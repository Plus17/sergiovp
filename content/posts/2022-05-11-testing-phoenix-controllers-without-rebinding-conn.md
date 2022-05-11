---
author: Me
title: Testing the Phoenix controllers without rebinding the conn
date: 2022-05-11
description: An easy way to improve readability in phoenix controllers tests
math: true
categories: ["elixir"]
---

Maybe, for readability purposes, you prefer to avoid [variable rebinding](https://hexdocs.pm/credo/Credo.Check.Refactor.VariableRebinding.html).

For example, take this generated test code from `mix phx.gen.auth` task:

```elixir
test "renders log in page", %{conn: conn} do
  conn = get(conn, Routes.user_session_path(conn, :new))
  response = html_response(conn, 200)
  assert response =~ "Log in"
  assert response =~ "Register</a>"
  assert response =~ "Forgot password?</a>"
end
```

It's completely ok. But, you can prefer something like this:

```elixir
test "renders log in page", %{conn: conn} do
  response =
    conn
    |> get(Routes.user_session_path(conn, :new))
    |> html_response(200)

  assert response =~ "Log in"
  assert response =~ "Register</a>"
  assert response =~ "Forgot password?</a>"
end
```

In this way, you don't rebind the conn and take advantage of the pipe operator.
