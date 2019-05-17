CRUD passo-a-passo
==========================

# Criando User

```shell
mix phx.gen.schema Auth.User users name email password_hash
* creating lib/blog/auth/user.ex
* creating priv/repo/migrations/20190517182821_create_users.exs

Remember to update your repository by running migrations:

    $ mix ecto.migrate
```

Na migration adicionar a restrição unique na coluna do email:

```elixir
defmodule Blog.Repo.Migrations.CreateUsers do
  use Ecto.Migration

  def change do
    create table(:users) do
      add :name, :string, unique: true
      add :email, :string
      add :password_hash, :string

      timestamps()
    end

  end
end
```

No schema adicionar a restrição das constraint

```elixir
defmodule Blog.Auth.User do
  use Ecto.Schema
  import Ecto.Changeset
  alias Blog.Post.Postagem

  schema "users" do
    field :email, :string
    field :name, :string
    field :password_hash, :string
    has_many :postagens, Postagem

    timestamps()
  end

  @doc false
  def changeset(user, attrs) do
    user
    |> cast(attrs, [:name, :email, :password_hash])
    |> validate_required([:name, :email, :password_hash])
    |> unique_constraint(:email)
  end
end

```

# Associando as Postagens

```shell
mix ecto.gen.migration add_user_id_posts
* creating priv/repo/migrations/20190517183531_add_user_id_posts.exs
```

```elixir
defmodule Blog.Repo.Migrations.AddUserIdPosts do
  use Ecto.Migration

  def change do
    alter table(:postagens) do
      add :user_id, references(:users)
    end
  end
end

```

## Migrar o banco de dados
```shell
mix ecto.migrate
```

## Associar os schemas

```elixir
defmodule Blog.Auth.User do
  use Ecto.Schema
  import Ecto.Changeset
  alias Blog.Post.Postagem

  schema "users" do
    field :email, :string
    field :name, :string
    field :password_hash, :string
    has_many :postagens, Postagem

    timestamps()
  end

  @doc false
  def changeset(user, attrs) do
    user
    |> cast(attrs, [:name, :email, :password_hash])
    |> validate_required([:name, :email, :password_hash])
    |> unique_constraint(:email)
  end
end
```

```elixir
defmodule Blog.Post.Postagem do
  use Ecto.Schema
  import Ecto.Changeset
  alias Blog.Auth.User

  schema "postagens" do
    field :texto, :string
    field :titulo, :string
    belongs_to :user, User

    timestamps()
  end

  @doc false
  def changeset(postagem, attrs) do
    postagem
    |> cast(attrs, [:titulo, :texto, :user_id])
    |> validate_required([:titulo, :texto, :user_id])
  end
end
```

# Testando no iex

```shell
iex -S mix
```

```shell
iex(1)> alias Blog.Repo
Blog.Repo
iex(2)> alias Blog.Auth.User 
Blog.Auth.User
iex(3)> Repo.insert(%User{name: "Ohashi", email: "ohashi@gmail.com", password_hash: "123"})
[debug] QUERY OK db=0.6ms decode=1.4ms queue=1.1ms
INSERT INTO `users` (`email`,`name`,`password_hash`,`inserted_at`,`updated_at`) VALUES (?,?,?,?,?) ["ohashi@gmail.com", "Ohashi", "123", ~N[2019-05-17 18:49:24], ~N[2019-05-17 18:49:24]]
{:ok,
 %Blog.Auth.User{
   __meta__: #Ecto.Schema.Metadata<:loaded, "users">,
   email: "ohashi@gmail.com",
   id: 1,
   inserted_at: ~N[2019-05-17 18:49:24],
   name: "Ohashi",
   password_hash: "123",
   postagens: #Ecto.Association.NotLoaded<association :postagens is not loaded>,
   updated_at: ~N[2019-05-17 18:49:24]
 }}
```
 
```elixir 
iex(4)> alias Blog.Post.Postagem 
Blog.Post.Postagem
iex(5)> Repo.insert(%Postagem{titulo: "Elixir", texto: "bla bla bla", user_id: 1})
[debug] QUERY OK db=1.0ms queue=1.1ms
INSERT INTO `postagens` (`texto`,`titulo`,`user_id`,`inserted_at`,`updated_at`) VALUES (?,?,?,?,?) ["bla bla bla", "Elixir", 1, ~N[2019-05-17 18:50:17], ~N[2019-05-17 18:50:17]]
{:ok,
 %Blog.Post.Postagem{
   __meta__: #Ecto.Schema.Metadata<:loaded, "postagens">,
   id: 1,
   inserted_at: ~N[2019-05-17 18:50:17],
   texto: "bla bla bla",
   titulo: "Elixir",
   updated_at: ~N[2019-05-17 18:50:17],
   user: #Ecto.Association.NotLoaded<association :user is not loaded>,
   user_id: 1
 }}
```

```elixir
iex(6)> Repo.get(User, 1)
[debug] QUERY OK source="users" db=15.1ms queue=1.2ms
SELECT u0.`id`, u0.`email`, u0.`name`, u0.`password_hash`, u0.`inserted_at`, u0.`updated_at` FROM `users` AS u0 WHERE (u0.`id` = ?) [1]
%Blog.Auth.User{
  __meta__: #Ecto.Schema.Metadata<:loaded, "users">,
  email: "ohashi@gmail.com",
  id: 1,
  inserted_at: ~N[2019-05-17 18:49:24],
  name: "Ohashi",
  password_hash: "123",
  postagens: #Ecto.Association.NotLoaded<association :postagens is not loaded>,
  updated_at: ~N[2019-05-17 18:49:24]
}
```

```elixir
iex(7)> Repo.get(User, 1) |> Repo.preload(:postagens)
[debug] QUERY OK source="users" db=0.5ms
SELECT u0.`id`, u0.`email`, u0.`name`, u0.`password_hash`, u0.`inserted_at`, u0.`updated_at` FROM `users` AS u0 WHERE (u0.`id` = ?) [1]
[debug] QUERY OK source="postagens" db=7.2ms queue=0.7ms
SELECT p0.`id`, p0.`texto`, p0.`titulo`, p0.`user_id`, p0.`inserted_at`, p0.`updated_at`, p0.`user_id` FROM `postagens` AS p0 WHERE (p0.`user_id` = ?) ORDER BY p0.`user_id` [1]
%Blog.Auth.User{
  __meta__: #Ecto.Schema.Metadata<:loaded, "users">,
  email: "ohashi@gmail.com",
  id: 1,
  inserted_at: ~N[2019-05-17 18:49:24],
  name: "Ohashi",
  password_hash: "123",
  postagens: [
    %Blog.Post.Postagem{
      __meta__: #Ecto.Schema.Metadata<:loaded, "postagens">,
      id: 1,
      inserted_at: ~N[2019-05-17 18:50:17],
      texto: "bla bla bla",
      titulo: "Elixir",
      updated_at: ~N[2019-05-17 18:50:17],
      user: #Ecto.Association.NotLoaded<association :user is not loaded>,
      user_id: 1
    }
  ],
  updated_at: ~N[2019-05-17 18:49:24]
}
```

## Nested Resources

```elixir
defmodule BlogWeb.Router do
  use BlogWeb, :router

  pipeline :browser do
    plug :accepts, ["html"]
    plug :fetch_session
    plug :fetch_flash
    plug :protect_from_forgery
    plug :put_secure_browser_headers
  end

  pipeline :api do
    plug :accepts, ["json"]
  end

  scope "/", BlogWeb do
    pipe_through :browser

    resources "/users", UserController do
      resources "/postagens", PostagemController
    end

    get "/", PageController, :index
  end

  # Other scopes may use custom stacks.
  # scope "/api", BlogWeb do
  #   pipe_through :api
  # end
end
```

## Rotas

```shell
mix phx.routes
         user_path  GET     /users                                 BlogWeb.UserController :index
         user_path  GET     /users/:id/edit                        BlogWeb.UserController :edit
         user_path  GET     /users/new                             BlogWeb.UserController :new
         user_path  GET     /users/:id                             BlogWeb.UserController :show
         user_path  POST    /users                                 BlogWeb.UserController :create
         user_path  PATCH   /users/:id                             BlogWeb.UserController :update
                    PUT     /users/:id                             BlogWeb.UserController :update
         user_path  DELETE  /users/:id                             BlogWeb.UserController :delete
user_postagem_path  GET     /users/:user_id/postagens              BlogWeb.PostagemController :index
user_postagem_path  GET     /users/:user_id/postagens/:id/edit     BlogWeb.PostagemController :edit
user_postagem_path  GET     /users/:user_id/postagens/new          BlogWeb.PostagemController :new
user_postagem_path  GET     /users/:user_id/postagens/:id          BlogWeb.PostagemController :show
user_postagem_path  POST    /users/:user_id/postagens              BlogWeb.PostagemController :create
user_postagem_path  PATCH   /users/:user_id/postagens/:id          BlogWeb.PostagemController :update
                    PUT     /users/:user_id/postagens/:id          BlogWeb.PostagemController :update
user_postagem_path  DELETE  /users/:user_id/postagens/:id          BlogWeb.PostagemController :delete
         page_path  GET     /                                      BlogWeb.PageController :index
         websocket  WS      /socket/websocket                      BlogWeb.UserSocket
``` 

## Alterando as rotas

Rota quebrada
http://localhost:4000/postagens

Nova rota
http://localhost:4000/users/1/postagens

Rotas antigas precisam ser atualizadas





