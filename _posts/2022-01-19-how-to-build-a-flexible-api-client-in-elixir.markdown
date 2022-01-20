---
layout: post
title:  "How to build a flexible API client in Elixir"
categories: elixir
---

Normally, to talk with an external API we will search some library to do the work for us. Sometimes, this search is infructuous for some of the next reasons:

- Exist one or more libraries but are unmaintained.
- There is no library at all.
- Existing libraries do not fit our requirements.

At this point, you could take the following paths.

## 1 Build a service module in your app
Maybe you just need to consume some endpoints and already use some HTTP client like Tesla. In this case, you can use the usual path:

```elixir
defmodule GitHub do
  use Tesla

  plug Tesla.Middleware.BaseUrl, "https://api.github.com"
  plug Tesla.Middleware.Headers, [{"authorization", "token xyz"}]
  plug Tesla.Middleware.JSON

  def user_repos(login) do
    get("/users/" <> login <> "/repos")
  end
end
```

## 2 Build a custom API client

Sometimes you want/need to do it yourself in a library way :). In this case you can follow the pattern mentioned in the [Goth redesign](https://dashbit.co/blog/goth-redesign).

### Use an HTTP client contract:
Using behaviours you can define a contract for the HTTP client. With this contract you can implement the contract and use the HTTP client library that you want:

```elixir
defmodule API.HTTPClient do

  @type method() :: atom()

  @type url() :: binary()

  @type status() :: non_neg_integer()

  @type header() :: {binary(), binary()}

  @type body() :: binary()

  @doc """
  Callback to make an HTTP request.
  """
  @callback request(method(), url(), [header()], body(), opts :: keyword()) ::
              {:ok, %{status: status, headers: [header()], body: body()}}
              | {:error, Exception.t()}

  @doc false
  def request(module, method, url, headers, body, opts) do
    module.request(method, url, headers, body, opts)
  end
end
```

## Implement the HTTP Client contract

Hackney is a popular HTTP client library, so this can be the default implementation.

```elixir
defmodule API.HTTPClient.Hackney do
  @behaviour API.HTTPClient

  @impl true
  @spec request(atom(), String.t(), list(), String.t(), Keyword.t()) ::
          {:ok, map()} | {:error, any()}
  def request(method, url, headers, body, opts) do
    with {:ok, status, headers, body_ref} <- :hackney.request(method, url, headers, body, opts),
         {:ok, body} <- :hackney.body(body_ref) do
      {:ok, %{status: status, headers: headers, body: body}}
    end
  end
end
```

Then you can set this as default in the api client config:
```elixir
config :api, http_client: API.HTTPClient.Hackney
```

### Using the HTTP client

```elixir
defmodule API.Resource do

  def get_resource() do
    url = "https://#{Application.fetch_env!(:api, :base_url)}/api/<resource>"

    headers = build_headers()

    result =
      :api
      |> Application.fetch_env!(:http_client)
      |> HTTPClient.request(:get, url, headers, "", [])
      |> handle_response()

    case result do
      {:ok, response} -> {:ok, response}
      {:error, error} -> {:error, error}
    end
  end

  defp build_headers() do
    [
      {"content-type", "application/json"}
    ]
  end

  defp handle_response({:ok, %{status: status, body: body}}) when status in [200, 201] do
    case Jason.decode(body) do
      {:ok, attrs} -> {:ok, attrs}
      {:error, reason} -> {:error, reason}
    end
  end

  defp handle_response({:ok, response}) do
    {:error, Jason.decode!(response.body)}
  end

  defp handle_response({:error, exception}) do
    {:error, exception}
  end
end
```

### Custom implementation

If you don't use hackney and you use another client like Finch or Mint instead, you can implement the contract yourself:

```elixir
defmodule MyApp.Extensions.API.FinchClient do
  @behaviour API.HTTPClient

  @impl true
  def request(method, url, headers, body, opts, initial_state) do
    opts = Keyword.merge(initial_state.default_opts, opts)

    Finch.build(method, url, headers, body)
    |> Finch.request(initial_state.name, opts)
  end
end
```

Then you can use the new implementation in your app config:

```elixir
config :api,
  http_client: MyApp.Extensions.API.FinchClient,
  base_url: "..."
```



### Advantages:

You can make the default http client dependency optional in your API client: `{:hackney, "~> 1.7", optional: true}`

Who use the API client can pick their HTTP client, exists a lot of options to choose. Read some at: [The State of Elixir HTTP Clients](https://blog.appsignal.com/2020/07/28/the-state-of-elixir-http-clients.html)

Avoid duplication of dependencies. If you're already using hackney great! But if you are already using another client, you are not forced to download and use Hackney additionally.

Who use the API client can use the http client contract to test inside their app using [Mox](https://github.com/dashbitco/mox). More about this in: [Mocks and explicit contracts](http://blog.plataformatec.com.br/2015/10/mocks-and-explicit-contracts/)

Share code between the organization projects. Preventing writing the same functionality multiple times


Finally, you can see another example in the Tesla library:
- [Tesla.Adapter](https://github.com/teamon/tesla/blob/c1e0f2d031eb87a207db33333b8d4afc58384e87/lib/tesla.ex#L127) (behaviour)
- [Tesla.Adapter.Hackney](https://github.com/teamon/tesla/blob/master/lib/tesla/adapter/hackney.ex) (contract implementation)
