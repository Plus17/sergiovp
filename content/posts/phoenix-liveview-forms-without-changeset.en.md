---
author: Me
title: "Phoenix Liveview Forms Without Changeset"
date: 2023-08-30T23:09:34-06:00
description: "How to build forms without changeset in Phoenix Liveview"
categories: ["elixir", "web", "liveview"]
---

The standard way to create [LiveView forms](https://hexdocs.pm/phoenix_live_view/form-bindings.html) is to use the `to_form/1` helper by passing it a changeset structure, for example:

```elixir
to_form(Accounts.change_user(%User{})))
```

However, situations can arise where we have a form without having to back it up with a changeset.

Why skip the changeset?

`Ecto.Changeset` is a tool for working with information from various sources. It provides validations, castings, and error handling. Typically, we create forms based on an Ecto schema, meaning they have a table in our database. However, there are situations where generating a changeset can be excessive or unnecessary, either based on a schema or in its schemaless form.

If we simply want to capture data without the need for complex validations, you might want to avoid using a changeset.

Let's use a contact form as an example (assuming you already have a Phoenix project running):

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

And in the `router.ex`:

```elixir
 scope "/", MyAppWeb do
    pipe_through :browser

    get "/", PageController, :home

    live "/contact", ContactLive # <-- add this
  end
```

If we go to http://localhost:4000/contact, we'll have our contact form:

![Contact form withouth changeset in Liveview](https://res.cloudinary.com/escribocodigo/image/upload/v1693589107/phoenix_liveivew_contact_form_kg4srs.png)

As we can see, the only difference is that the helper to_form/1 will be passed a map instead of a changeset:

```elixir
# with changeset
changeset = Accounts.change_user(%User{})
to_form(changeset)

# without changeset
params = %{"name" => "", "subject" => "", "body" => "", "email" => ""}
to_form(params)
```

If we test the form, we can see the following in the logs:

![Logs handling form params](https://res.cloudinary.com/escribocodigo/image/upload/v1693589447/logs_handle_event_save_form_without_changeset_udt2id.png)

In this way we can continue taking advantage of the power of the helpers and the new components of the `CoreComponents` module.
