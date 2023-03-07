---
author: Me
title: Como crear un productor personalizado en Broadway
date: 2023-02-21
description: Como crear un custom producer para consumir información de otra fuente en Elixir Broadway
math: true
categories: ["elixir"]
---


[Broadway](https://hexdocs.pm/broadway/Broadway.html) es una herramienta construida sobre [GenStage](https://hexdocs.pm/gen_stage/1.0.0/GenStage.html), concurrente de varias etapas para crear pipelines de ingesta y procesamiento de datos.

Es decir, esta biblioteca nos permite consumir y procesar información de fuentes como un Message Broker (Kafka, RabbitMQ, etc.).

Por si fuera poco, viene con una serie de características integradas que nos permiten diseñar nuestros pipelines con una gran flexibilidad:
- Back-pressure
- Concurrencia y procesamiento por lotes
- Rate limiting
- Varios producers out of the box
- La posibilidad de crear custom producers
- Entre otras.

## Creando un custom producer

Si bien consumir información desde un *message broker* es, por decirlo de alguna manera, la opción por defecto de Broadway, esto no significa que esté limitado a estos como fuente de información.

Como ya se mencionó, nos permite crear y conectar nuestros propios [producers](https://hexdocs.pm/broadway/custom-producers.html) y estos pueden consumir información de cualquier fuente que queramos. Una base de datos, por ejemplo.

Lo primero es crear un módulo que utilice el behaviour de Broadway:

```elixir
defmodule MyBroadway do
  use Broadway

  require Logger

  @chunk_size 10

  def start_link(_opts) do
    options = [
      name: __MODULE__,
      producer: [
        module: {DBDummyProducer, []},
        transformer: {__MODULE__, :transform, []},
        concurrency: 1
      ],
      processors: [
        default: [
          max_demand: @chunk_size,
          concurrency: 1
        ]
      ]
    ]

	Broadway.start_link(__MODULE__, options)
  end
end
```


En las opciones le estamos diciendo a la biblioteca que nuestro *producer* es el módulo `DBDummyProducer`, ya que vamos a "realizar" consultas a la base de datos, usamos la opción `max_demand` para limitar el número de registros que vamos a cargar en memoria. Vamos a utilizar este valor en `DBDummyProducer`.

Además, le estamos diciendo que vamos a utilizar como *transformer* la función `transform` en el mismo módulo `MyBroadway`. Ya que los datos llegaran como maps o structs y deben incluirse en la estructura `Broadway.Message.t()`.

```elixir
def transform(event, _opts) do
    Logger.debug("[MyBroadway] transforming event: #{inspect(event)}")

    %Message{
      data: event,
      acknowledger: {__MODULE__, :trackings, :ack_data}
    }
end
```

Otra función que debemos implementar es `ack/3` . Esta función sirve para notificar que los mensajes recibidos se procesaron correctamente o fallaron.

```elixir
def ack(:ack_id, successful, failed) do
    Logger.debug(
      "[MyBroadway] ack successful: #{Enum.count(successful)}, failed: #{Enum.count(failed)}"
    )

    :ok
  end
```

Ahora creamos nuestro módulo `DBDummyProducer`, para efectos de simplificación vamos a generar datos en memoria, solo habría que sustituir esto por la llamada a nuestro contexto o módulo que genere la consulta.

```elixir
defmodule DBDummyProducer do
  @moduledoc """
  This module emulates DBProducer behavior producing in memory rows
  """
  use GenStage

  require Logger

  @impl GenStage
  def init(opts) do
    {:producer, opts}
  end

  def start_link(opts) do
    GenStage.start_link(__MODULE__, opts)
  end

  @impl true
  @spec handle_demand(integer, any) :: {:noreply, list, any}
  def handle_demand(demand, state) do
    Logger.debug(fn ->
      "[DBProducer] handling demand: #{demand}"
    end)

    # events = Users.list(limit: demand)
    events =
      Enum.map(1..demand, fn _number ->
        %{
          id: Enum.random(1..9_999_999),
        }
      end)

    {:noreply, events, state}
  end
end

```

Ahora nuestro pipeline puede recibir datos desde nuestro *producer*. Seguramente necesitamos procesar esta información de alguna manera. Para esto tenemos dos *callback* `handle_message/3` para procesar mensajes individuales y `handle_batch/4` para procesar lotes.

```elixir
def handle_message(_processor, message, _context) do
    Logger.debug(fn -> "[MyBroadway] Incoming Message: #{inspect(message)}" end)
	# perform business logic
    message
end

def handle_batch(_batcher, messages, _batch_info, _context) do
	Logger.debug("[MyBroadway] in default batcher")
	Logger.debug(fn -> "[MyBroadway] size: #{length(messages)}" end)

	messages
end
```

Sí queremos hacer uso de `handle_batch/4` debemos hacer dos cosas:
- Agregar la llave `batchers` a nuestras `options`
- Al transformar el mensaje debemos especificar en cual `batcher` se va a procesar

```elixir
def start_link(_opts) do
	 options = [
	  # ... previous options
	  batchers: [default: []]
	]
	Broadway.start_link(__MODULE__, options)
	end
```

```elixir
def transform(event, _state) do
    Logger.debug("[MyBroadway] transforming event: #{inspect(event)}")

    %Broadway.Message{
      data: event,
      acknowledger: {__MODULE__, :trackings, :ack_data}
    }
    |> Broadway.Message.put_batcher(:default)
  end
```

Por último, solo resta agregar `MyBroadway` al supervisor, normalmente en el archivo `application.ex`

```elixir
children = [
  {MyBroadway, []}
]

Supervisor.start_link(children, strategy: :one_for_one)
```

Si intentamos probar el comportamiento del código, no seremos capaces de apreciar los eventos que están sucediendo. Para configurar un poco este comportamiento podemos usar la configuración de *rate_limiting* en las opciones del *producer*:
```elixir
def start_link(_opts) do
	 options = [
	  # ... previous options
	  producer: [
      module: {DBDummyProducer, []},
      transformer: {__MODULE__, :transform, []},
      rate_limiting: [
        interval: 10_000,
        allowed_messages: 10
      ],
      concurrency: 1
    ],
    # .. next options
	]
	Broadway.start_link(__MODULE__, options)
end
```


Puedes ver el resultado final en el repositorio [broadway-custom-producer](https://github.com/Plus17/broadway-custom-producer). Pero nuestro código queda de la siguiente manera:
```elixir
defmodule MyBroadway do
  use Broadway

  require Logger

  @chunk_size 10

  @doc """
  Uses rate_limiting options to be able to see log messages in the console
  """
  def start_link(_opts) do
    options = [
      name: __MODULE__,
      producer: [
        module: {DBDummyProducer, []},
        transformer: {__MODULE__, :transform, []},
        rate_limiting: [
          interval: 10_000,
          allowed_messages: 10
        ],
        concurrency: 1
      ],
      processors: [
        default: [
          max_demand: @chunk_size,
          concurrency: 1
        ]
      ],
      batchers: [default: []]
    ]

    Broadway.start_link(__MODULE__, options)
  end

  def transform(event, _state) do
    Logger.debug("[MyBroadway] transforming event: #{inspect(event)}")

    %Broadway.Message{
      data: event,
      acknowledger: {__MODULE__, :trackings, :ack_data}
    }
    |> Broadway.Message.put_batcher(:default)
  end

  def ack(:trackings, successful, failed) do
    Logger.debug(
      "[MyBroadway] ack successful: #{length(successful)}, failed: #{length(failed)}"
    )

    :ok
  end

  def handle_message(_processor, message, _context) do
    Logger.debug(fn -> "[MyBroadway] Incoming Message: #{inspect(message)}" end)

    message
  end

  def handle_batch(_batcher, messages, _batch_info, _context) do
    Logger.debug("[MyBroadway] in default batcher")
    Logger.debug(fn -> "[MyBroadway] size: #{length(messages)}" end)

    messages
  end
end

```

```elixir
defmodule DBDummyProducer do
  @moduledoc """
  This module emulates database behavior by producing in-memory rows
  """
  use GenStage

  require Logger

  @impl GenStage
  def init(opts) do
    {:producer, opts}
  end

  def start_link(opts) do
    GenStage.start_link(__MODULE__, opts)
  end

  @impl true
  def handle_demand(demand, state) do
    Logger.debug(fn ->
      "[DBDummyProducer] handling demand: #{demand}"
    end)

    # events = Users.list_users(limit: demand)
    events =
      Enum.map(1..demand, fn _number ->
        %{
          id: Enum.random(1..9_999_999_999)
        }
      end)

    {:noreply, events, state}
  end
end

```

Ahora, si ingresamos a la consola interactiva de nuestra aplicación, veremos algo como los siguientes mensajes que mostramos mediante el `Logger`:

```shell
10:10:39.341 [debug] [DBDummyProducer] handling demand: 10

10:10:39.349 [debug] [MyBroadway] transforming event: %{id: 2730249896}

10:10:39.349 [debug] [MyBroadway] transforming event: %{id: 8277765695}

10:10:39.349 [debug] [MyBroadway] transforming event: %{id: 917212511}

10:10:39.349 [debug] [MyBroadway] transforming event: %{id: 2264493728}

10:10:39.349 [debug] [MyBroadway] transforming event: %{id: 4033891075}

10:10:39.349 [debug] [MyBroadway] transforming event: %{id: 2065156038}

10:10:39.349 [debug] [MyBroadway] transforming event: %{id: 4625753942}

10:10:39.349 [debug] [MyBroadway] transforming event: %{id: 2587137776}

10:10:39.349 [debug] [MyBroadway] transforming event: %{id: 6481119527}

10:10:39.349 [debug] [MyBroadway] transforming event: %{id: 6661902836}

10:10:39.353 [debug] [MyBroadway] Incoming Message: %Broadway.Message{data: %{id: 2730249896}, metadata: %{}, acknowledger: {MyBroadway, :trackings, :ack_data}, batcher: :default, batch_key: :default, batch_mode: :bulk, status: :ok}

10:10:39.353 [debug] [MyBroadway] Incoming Message: %Broadway.Message{data: %{id: 8277765695}, metadata: %{}, acknowledger: {MyBroadway, :trackings, :ack_data}, batcher: :default, batch_key: :default, batch_mode: :bulk, status: :ok}

10:10:39.353 [debug] [MyBroadway] Incoming Message: %Broadway.Message{data: %{id: 917212511}, metadata: %{}, acknowledger: {MyBroadway, :trackings, :ack_data}, batcher: :default, batch_key: :default, batch_mode: :bulk, status: :ok}

10:10:39.353 [debug] [MyBroadway] Incoming Message: %Broadway.Message{data: %{id: 2264493728}, metadata: %{}, acknowledger: {MyBroadway, :trackings, :ack_data}, batcher: :default, batch_key: :default, batch_mode: :bulk, status: :ok}

10:10:39.353 [debug] [MyBroadway] Incoming Message: %Broadway.Message{data: %{id: 4033891075}, metadata: %{}, acknowledger: {MyBroadway, :trackings, :ack_data}, batcher: :default, batch_key: :default, batch_mode: :bulk, status: :ok}

10:10:39.354 [debug] [MyBroadway] Incoming Message: %Broadway.Message{data: %{id: 2065156038}, metadata: %{}, acknowledger: {MyBroadway, :trackings, :ack_data}, batcher: :default, batch_key: :default, batch_mode: :bulk, status: :ok}

10:10:39.354 [debug] [MyBroadway] Incoming Message: %Broadway.Message{data: %{id: 4625753942}, metadata: %{}, acknowledger: {MyBroadway, :trackings, :ack_data}, batcher: :default, batch_key: :default, batch_mode: :bulk, status: :ok}

10:10:39.354 [debug] [MyBroadway] Incoming Message: %Broadway.Message{data: %{id: 2587137776}, metadata: %{}, acknowledger: {MyBroadway, :trackings, :ack_data}, batcher: :default, batch_key: :default, batch_mode: :bulk, status: :ok}

10:10:39.354 [debug] [MyBroadway] Incoming Message: %Broadway.Message{data: %{id: 6481119527}, metadata: %{}, acknowledger: {MyBroadway, :trackings, :ack_data}, batcher: :default, batch_key: :default, batch_mode: :bulk, status: :ok}

10:10:39.354 [debug] [MyBroadway] Incoming Message: %Broadway.Message{data: %{id: 6661902836}, metadata: %{}, acknowledger: {MyBroadway, :trackings, :ack_data}, batcher: :default, batch_key: :default, batch_mode: :bulk, status: :ok}

10:10:40.355 [debug] [MyBroadway] in default batcher

10:10:40.355 [debug] [MyBroadway] size: 10

10:10:40.355 [debug] [MyBroadway] ack successful: 10, failed: 0
```

Como pudimos ver, Broadway es una opción muy poderosa para consumir y procesar información, podemos diseñar nuestros pipelines incluyendo distintas fases en el proceso y contamos con múltiples opciones que nos permiten adecuar el comportamiento de acuerdo a nuestro caso de uso.

En este post apenas abarcamos lo básico, algunos recursos muy útiles para consultar:
- El libro [Concurrent Data Processing in Elixir](https://pragprog.com/titles/sgdpelixir/concurrent-data-processing-in-elixir/)
- La [documentación oficial](https://hexdocs.pm/broadway/introduction.html)
