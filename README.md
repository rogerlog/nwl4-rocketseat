# NLW#4 :rocket:

<br>

<br>

<br>

## Segunda (22/02/2021)

- **Instalação do ambiente de desenvolvimento**

  - Elixir

  - Phoenix

    ```shell
    mix archive.install hex phx_new 1.5.7
    ```

  - Docker

    ```docker
    sudo systemctl start docker
    sudo systemctl enable docker
    docker version
    ```

  - Postgres

  ```shell
  sudo docker run --name postgres -e POSTGRES_PASSWORD=postgres -p 5432:5432 -d postgres
  ```

  

- Mão na massa

```shell 
mix phx.new rocketpay --no-webpack --no-html
```

Resultado

```shell
We are almost there! The following steps are missing:

    $ cd rocketpay

Then configure your database in config/dev.exs and run:

    $ mix ecto.create

Start your Phoenix app with:

    $ mix phx.server

You can also run your app inside IEx (Interactive Elixir) as:

    $ iex -S mix phx.server

```

- Configurações do ElixirLS *(Extensão do Code)*

Desabilitar o *Dialyzer*

Arquivo mix.exs, add do Credo

```elixir
defp deps do
    [
      {:phoenix, "~> 1.5.7"},
      {:phoenix_ecto, "~> 4.1"},
      {:ecto_sql, "~> 3.4"},
      {:postgrex, ">= 0.0.0"},
      {:phoenix_live_dashboard, "~> 0.4"},
      {:telemetry_metrics, "~> 0.4"},
      {:telemetry_poller, "~> 0.4"},
      {:gettext, "~> 0.11"},
      {:jason, "~> 1.0"},
      {:plug_cowboy, "~> 2.0"},
      {:credo, "~> 1.5", only: [:dev, :test], runtime: false}
    ]
  end
```

- Configurações da aplicação

/config/dev.exs

- Verificando o banco de dados

```shell
 mix ecto.setup
```

- Baixar todas as dependencias da aplicação

```shell
mix deps.get
```

- Validar Credo

```shell
mix credo gen.config
```

- No arquivo `.credo.exs`

```shell
{Credo.Check.Readability.ModuleDoc, []},
```

mudar para

```shell
{Credo.Check.Readability.ModuleDoc, false},
```

- Rodar o projeto. Executa o servidor da aplicação

```shell
mix phx.server
```

- No navegador: http://localhost:4000/dashboard/home

![](.img/rocket1.png)

- Divisão de pastas e conteúdo no projeto

Dentro da pasta **lib** possui duas outras pastas com a separação do projeto em rotas e conexões com o banco e regras de negócio.

> ​	rocketpay
>
> ​	rocketpay_web

- Criação de Rotas

No arquivo router.ex

```elixir
 scope "/api", RocketpayWeb do
    pipe_through :api
    
    get "/", WelcomeController, :index
 end
```

**Controller**

/lib/rocketpay_web/controllers/welcome_controller.ex

```elixir
defmodule RocketpayWeb.WelcomeController do
  use RocketpayWeb, :controller

  def index(conn, _params) do
    text(conn, "Welcome to the Rocketpay API")
  end

end
```

- Módulo para ler os dados de um arquivo e exibir

Na raiz do projeto criar arquivo `numbers.csv`

- Para a lógica de negócios

Pasta rocketpay, criar arquivo numbers.ex

```
\lib\rocketpay\numberx.ex
```

```elixir
defmodule Rocketpay.Numbers do
  def sum_from_file(filename) do
    file = File.read("#{filename}.csv")
  end
end
```



Comando `iex` - Interactive Elixir

Dentro do projeto

```shell 
iex -S mix

Rocketpay.Numbers.sum_from_file("numbers")
```

Para recompilar o código

```shell 
recompile
```

- Pattern Matching no Elixir

https://elixir-lang.org/getting-started/pattern-matching.html

```elixir
defmodule Rocketpay.Numbers do
  def sum_from_file(filename) do
    file = File.read("#{filename}.csv")
    handle_file(file)
  end

  defp handle_file({:ok, file}), do: file
  defp handle_file({:error, _reason}), do: {:error, "Invalid File"}
end
```

*para...*

```elixir
defmodule Rocketpay.Numbers do
  def sum_from_file(filename) do
    "#{filename}.csv"
    |> File.read()
    |> handle_file()
  end

  defp handle_file({:ok, file}), do: file
  defp handle_file({:error, _reason}), do: {:error, "Invalid File"}
end
```

https://hexdocs.pm/elixir/Kernel.html#%7C%3E/2

- Separando Strings

`String.split("1, 2, 3, 4, 8, 9, 10", ",")`

Percorrer todos os elementos de uma lista e executar uma função

Para consultar as funções dentro do Elixir, no caso **String** `String` + `.` + `Tecla espaço`

Para documentação colocar o `h` antes

```elixir
iex(3)> h String 
```

- Utilizando o map `%{}`

```elixir
Enum.map(lista, fn number -> String.to_integer(number) end)
```

 ```elixir
Enum.sum()
 ```

Fica assim...

```elixir
defmodule Rocketpay.Numbers do
  def sum_from_file(filename) do
    "#{filename}.csv"
    |> File.read()
    |> handle_file()
  end

  defp handle_file({:ok, result}) do
    result =
      result
      |> String.split(",")
      |> Enum.map(fn number -> String.to_integer(number) end)
      |> Enum.sum()
    {:ok, %{result: result}}
  end

  defp handle_file({:error, _reason}), do: {:error, "Invalid File"}
end
```

Resumindo... 

Com o pipe operator

```elixir
Rocketpay.Number.sum_from_file("numbers")
```

fica assim

```elixir
"numbers" |> Rocketpay.Number.sum_from_file()
```

- Alias

```elixir 
alias Rocketpay.Numbers
```

a função fica só `Numbers`

- Utilizando Stream

```elixir
  defp handle_file({:ok, result}) do
    result =
      result
      |> String.split(",")
      |> Stream.map(fn number -> String.to_integer(number) end)
      |> Enum.sum()
    {:ok, %{result: result}}
  end
```

- Teste Automatizado

`/test/rocketpay/numbers_test.exs`

```elixir
defmodule Rocketpay.NumbersTest do
  use ExUnit.Case
  alias Rocketpay.Numbers

describe "sum_from_file/1" do
    test "when there is a file with the given name, returns the sum of numbers" do
      response = Numbers.sum_from_file("numbers")
      expected_response = "banana"
      assert response == expected_response
    end
  end
end
```

No terminal, arrumar as variáveis de retorno.

```shell
mix test
```

```elixir
defmodule Rocketpay.NumbersTest do
  use ExUnit.Case

  alias Rocketpay.Numbers

  describe "sum_from_file/1" do
    test "when there is a file with the given name, returns the sum of numbers" do
      response = Numbers.sum_from_file("numbers")
      expected_response = {:ok, %{result: 37}}
      assert response == expected_response
    end

    test "when there is no file with the given name, returns the sum of numbers" do
      response = Numbers.sum_from_file("banana")
      expected_response = {:error, %{message: "Invalid File"}}
      assert response == expected_response
    end

  end
end
```

- Código da aula

#rumoaoproximonivel



<br>

<br>

<br>

## Terça (23/02/2021)



<br>

<br>

<br>

## Quarta (24/02/2021)





<br>

<br>

<br>

## Quinta (25/02/2021)



<br>

<br>

<br>

## Sexta (26/02/2021)

