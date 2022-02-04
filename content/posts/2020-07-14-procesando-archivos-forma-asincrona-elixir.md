---
author: Me
title: Procesando archivos de forma asíncrona con Elixir
date: 2020-07-14
description: Como cargar y procesar archivos en segundo plano con Elixir/Phoenix
math: true
categories: ["elixir"]
---

## Escenario

A veces, no todo es un escenario perfecto y puede que para alimentar de información tu aplicación  necesitas; no conectarte a una API, si no subir archivos CSV's.

Si es el caso, no quieres que quien sea responsable de esta tarea tenga que subir de 100 en 100 registros por que si sube un archivo con 101, el servidor tarda demasiado en responder, el navegador muestra un error de timeout y la carga se interrumpe.

Es decir, lo que buscamos es procesar archivos CSV's grandes, con miles de registros.

## Proceso general

Podemos resumir el proceso en los siguientes pasos

1. El usuario envía un archivo al servicio
2. El servicio recibe el archivo y llamas un código similar a este

```elixir
Task.start(fn -> Modulo.procesar_csv(plug_upload) end)
```

3. El servicio contesta el request con algo como: "archivo recibido"

La propuesta tiene 2 inconvenientes:

- Si el Task falla estamos perdidos, no tenemos forma de recuperarnos
- Phoenix utiliza el plug [Plug.Upload](https://hexdocs.pm/plug/Plug.Upload.html), un GenServer para manejar los archivos cargados.

El problema es que este archivo únicamente vive durante el ciclo de vida de la petición, por lo que si usamos algo como [Streams](https://hexdocs.pm/elixir/Stream.html), el archivo terminara despareciendo sin haber concluido el proceso.

Debido a la limitación anterior el flujo se vería modificado de la siguiente manera:

1. El usuario envía un archivo al servidor
2. El servicio recibe el archivo y lo copia a una carpeta del sistema
3. Se ejecuta el procesamiento del archivo de forma asíncrona con una tarea supervisada (utilizaremos `Task.Supervisor.async_nolink`)
4. El servicio responde la petición
5. Al terminar de procesar el archivo, este es borrado del servidor

### Configuración

Primero necesitamos agregar `Task.Supervisor` al supervisor de nuestra aplicación. Si usamos Phoenix lo encontraremos en el archivo `my_app/lib/app/application.ex`:

```elixir
def start(_type, _args) do
    # List all child processes to be supervised
    children = [
      # Start the Ecto repository
      App.Repo,
      # Start the PubSub system
      {Phoenix.PubSub, name: App.PubSub},
      # Start the endpoint when the application starts
      AppWeb.Endpoint,
      {Task.Supervisor, name: App.TaskSupervisor},
    ]

    # See https://hexdocs.pm/elixir/Supervisor.html
    # for other strategies and supported options
    opts = [strategy: :one_for_one, name: App.Supervisor]
    Supervisor.start_link(children, opts)
  end

```

### Guardar el archivo en el servidor

Como dijimos que queremos guardar el archivo en el servidor para poder procesarlo mas tarde sin mantener la conexión con el cliente abierta, creamos un pequeño modulo para esto:

```elixir
defmodule MyApp.Files do
	@spec move_to_server(Plug.Upload.t()) :: {:ok, String.t())
	def move_to_server(%Plug.Upload{} = upload_params) do
		tmp_dir = System.tmp_dir!() # Nos da un directorio temporal de escritura

    extension = Path.extname(upload_params.filename)
    tmp_file = Path.join(tmp_dir, "#{Ecto.UUID.generate()}#{extension}")

		File.cp(upload_params.path, tmp_file)
	end
end
```

### Server

Ahora crearemos un server, este se encargara de llamar a la tarea  y verificar su estatus

```elixir
defmodule MyApp.FileServer do
  use GenServer

	alias MyApp.Files

  def start_task do
    GenServer.call(__MODULE__, :start_task)
  end

  # In this case the task is already running, so we just return :ok.
  def handle_call(:start_task, _from, %{ref: ref} = state) when is_reference(ref) do
    {:reply, :ok, state}
  end

  # The task is not running yet, so let's start it.
  def handle_call({:process_file, upload_params}, _from, %{ref: nil} = state) do
		{:ok, path_to_file} = Files.move_to_server(upload_params)
    task =
      Task.Supervisor.async_nolink(MyApp.TaskSupervisor, fn ->
        Modulo.procesar_csv(path_to_file)
      end)

    # We return :ok and the server will continue running
    {:reply, :ok, %{state | ref: task.ref}}
  end

  # The task completed successfully
  def handle_info({ref, answer}, %{ref: ref} = state) do
    # We don't care about the DOWN message now, so let's demonitor and flush it
    Process.demonitor(ref, [:flush])
    # Do something with the result and then return
    {:noreply, %{state | ref: nil}}
  end

  # The task failed
  def handle_info({:DOWN, ref, :process, _pid, _reason}, %{ref: ref} = state) do
    # Log and possibly restart the task...
    {:noreply, %{state | ref: nil}}
  end
end
```

Este server, ejecuta una tarea, guarda en el estado la referencia de esta tarea. Si ya tiene una tarea en el estado y recibe una nueva llamada responde un `:ok` pero no toma la nueva tarea.

Este comportamiento es útil en mi caso, ya que los registros van a la base de datos, procesar un solo archivo a la vez evita tener problemas de recursos (por ejemplo).

Cuando la tarea termina o falla, se recibe el mensaje `:DOWN`, si la tarea se completo `reason` será `:normal`. En caso contrario podemos notificar o manejar el caso según lo necesitemos cuando sabemos que la tarea fallo.

En ambos casos, limpia el estado y ahora puede recibir un nuevo archivo para ser procesado.

En mi caso particular, notifico por email cuando la tarea finalizo y si hubo errores los recolecto en un CSV que se adjunta al email.

Links útiles:

[https://dockyard.com/blog/2019/02/13/understanding-test-concurrency-in-elixir](https://dockyard.com/blog/2016/05/02/phoenix-tips-and-tricks)

[https://hexdocs.pm/elixir/Task.Supervisor.html#async_nolink/3-compatibility-with-otp-behaviours](https://hexdocs.pm/elixir/Task.Supervisor.html#async_nolink/3-compatibility-with-otp-behaviours)
