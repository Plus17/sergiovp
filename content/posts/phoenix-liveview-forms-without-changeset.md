---
author: Me
title: "Formularios sin Changeset en Phoenix LiveView"
date: 2023-08-30T23:09:34-06:00
description: "Como crear formularios sin changeset en Phoenix LiveView"
categories: ["elixir", "web", "liveview"]
---

La forma estándar de crear [formularios en LiveView](https://hexdocs.pm/phoenix_live_view/form-bindings.html) es utilizar el helper `to_form/1` pasándole una estructura changeset, por ejemplo:

```elixir
to_form(Accounts.change_user(%User{})))
```

Sin embargo, pueden surgir situaciones donde tengamos un formulario sin tener que respaldarlo con un changeset.

Porque saltarse el changeset?

`Ecto.Changeset` es una herramienta para trabajar con información de distintas fuentes. Proporciona validaciones, castings y manejo de errores. Normalmente creamos formularios basados en un schema de Ecto, es decir que tienen una tabla en nuestra base de datos. Sin embargo, hay situaciones en las que generar un changeset puede ser excesivo o innecesario, ya sea basado en un schema o en su forma schemaless. 

Si simplemente queremos capturar datos sin necesidad de validaciones complejas, es posible que quieras evitar usar un changeset.

Vamos a poner como ejemplo un formulario de contacto (asumiendo que ya tienes un proyecto de Phoenix corriendo):

```elixir
defmodule MyAppWeb.ContactLive do
  use MyAppWeb, :live_view

  @impl true
  def mount(_params, _session, socket) do
    params = %{"name" => "", "subject" => "", "body" => "", "email" => ""}
    {:ok, assign(socket, :form, to_form(params))}
  end

  @impl true
  def render(assigns) do
    ~H"""
    <div>
      <.header>
        <:subtitle>Contact</:subtitle>
      </.header>

      <.simple_form for={@form} id="contact_form" phx-submit="save">
        <.input field={@form[:name]} label="Name" />
        <.input field={@form[:subject]} label="Subject" />
        <.input field={@form[:body]} type="textarea" label="Body" />
        <.input field={@form[:email]} type="email" label="Email" />

        <:actions>
          <.button phx-disable-with="Saving...">Send</.button>
        </:actions>
      </.simple_form>
    </div>
    """
  end

  @impl true
  def handle_event("save", params, socket) do
    {:noreply, socket}
  end
end
```

Y en el `router.ex`:

```elixir
 scope "/", MyAppWeb do
    pipe_through :browser

    get "/", PageController, :home

    live "/contact", ContactLive # <-- add this
  end
```

Si entramos a http://localhost:4000/contact tendremos nuestro formulario de contacto:

![Formulario de contacto sin changeset en LiveView](https://res.cloudinary.com/escribocodigo/image/upload/v1693589107/phoenix_liveivew_contact_form_kg4srs.png)

Como vemos la única diferencia es que al helper `to_form/1` le pasaremos un mapa en lugar de un changeset:

```elixir
# with changeset
changeset = Accounts.change_user(%User{}))
to_form(changeset)

# without changeset
params = %{"name" => "", "subject" => "", "body" => "", "email" => ""}
to_form(params)
```

Si probamos el formulario podremos ver lo siguiente en los logs:

![Logs recibiendo los parámetros del formulario](https://res.cloudinary.com/escribocodigo/image/upload/v1693589447/logs_handle_event_save_form_without_changeset_udt2id.png)

De esta forma podemos seguir aprovechando el poder de los helpers y los nuevos componentes del modulo `CoreComponents`.
