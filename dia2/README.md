Introdução ao Phoenix 
========================

# Criando projeto Phoenix

```shell
mix phx.new blog --database mysql
```

Saída esperada:

```bash
* creating blog/assets/css/app.css
* creating blog/assets/css/phoenix.css
* creating blog/assets/js/app.js
* creating blog/assets/js/socket.js
* creating blog/assets/package.json
* creating blog/assets/static/robots.txt
* creating blog/assets/static/images/phoenix.png
* creating blog/assets/static/favicon.ico

Fetch and install dependencies? [Yn] Y       
* running mix deps.get
* running cd assets && npm install && node node_modules/webpack/bin/webpack.js --mode development
* running mix deps.compile

We are almost there! The following steps are missing:

    $ cd blog

Then configure your database in config/dev.exs and run:

    $ mix ecto.create

Start your Phoenix app with:

    $ mix phx.server

You can also run your app inside IEx (Interactive Elixir) as:

    $ iex -S mix phx.server
```

## Entrar na pasta do projeto

```shell
cd blog
```

## Criar o banco de dados, o que são migrations?

```shell
mix ecto.create
The database for Blog.Repo has been created
```

## Subindo o servidor

```shell
mix phx.server
```

### Testando no browser

```html
localhost:4000
```

# O que acabamos de criar?

# Gerar um scaffold completo.

```shell
mix phx.gen.html Post Postagem postagens titulo texto:text
```

resultado:

```shell
* creating lib/blog/post.ex
* injecting lib/blog/post.ex
* creating test/blog/post/post_test.exs
* injecting test/blog/post/post_test.exs

Add the resource to your browser scope in lib/blog_web/router.ex:

    resources "/postagens", PostagemController


Remember to update your repository by running migrations:

    $ mix ecto.migrate
```

## Acessar a url: http://localhost:4000/postagens

Realizar um CRUD... 

## Rotas

```shell
mix phx.routes

postagem_path  GET     /postagens                             BlogWeb.PostagemController :index
postagem_path  GET     /postagens/:id/edit                    BlogWeb.PostagemController :edit
postagem_path  GET     /postagens/new                         BlogWeb.PostagemController :new
postagem_path  GET     /postagens/:id                         BlogWeb.PostagemController :show
postagem_path  POST    /postagens                             BlogWeb.PostagemController :create
postagem_path  PATCH   /postagens/:id                         BlogWeb.PostagemController :update
               PUT     /postagens/:id                         BlogWeb.PostagemController :update
postagem_path  DELETE  /postagens/:id                         BlogWeb.PostagemController :delete
    page_path  GET     /                                      BlogWeb.PageController :index
    websocket  WS      /socket/websocket                      BlogWeb.UserSocket
```

## Explicar o passo a passo da requisição

quadro e terminal.

router > controller > schema > templates

### 1 - Baseado na URL definindo a rota

GET /postagens 

### 2 - A regra do router encaminha para o controller/função

controller: postagemController 

função: index

### 3 - Dentro da função acessamos o schema e carregamos informações do banco de dados, por ex.

acesso ao schema: Post.list_postagens()

### 4 - Renderizar o template (atenção aos nomes)

templates: template/postagem/index.eex

## Testando o IEX

No terminal executar:

```shell
iex -S mix
```

## Consultar todos os Posts

```elixir
iex(1)> alias Blog.Repo
Blog.Repo
iex(2)> alias Blog.Post.Postagem
Blog.Post.Postagem
iex(3)> Repo.all(Postagem)
[debug] QUERY OK source="postagens" db=5.4ms decode=1.4ms queue=13.0ms
SELECT p0.`id`, p0.`texto`, p0.`titulo`, p0.`inserted_at`, p0.`updated_at` FROM `postagens` AS p0 []
[
  %Blog.Post.Postagem{
    __meta__: #Ecto.Schema.Metadata<:loaded, "postagens">,
    id: 2,
    inserted_at: ~N[2019-04-11 19:28:52],
    texto: "Conteúdo....",
    titulo: "Primeiro post",
    updated_at: ~N[2019-04-11 19:28:52]
  }
]
```

## Criar um post 
```elixir
iex(4)> Repo.insert(%Postagem{titulo: "Novo Post", texto: "blalba  balba"})
[debug] QUERY OK db=18.5ms queue=8.1ms
INSERT INTO `postagens` (`texto`,`titulo`,`inserted_at`,`updated_at`) VALUES (?,?,?,?) ["blalba  balba", "Novo Post", ~N[2019-05-06 18:43:33], ~N[2019-05-06 18:43:33]]
{:ok,
 %Blog.Post.Postagem{
   __meta__: #Ecto.Schema.Metadata<:loaded, "postagens">,
   id: 3,
   inserted_at: ~N[2019-05-06 18:43:33],
   texto: "blalba  balba",
   titulo: "Novo Post",
   updated_at: ~N[2019-05-06 18:43:33]
 }}
```

## Consultar um post especifico, by id

```elixir
iex(6)> Repo.get(Postagem, 2)    
[debug] QUERY OK source="postagens" db=20.5ms queue=21.7ms
SELECT p0.`id`, p0.`texto`, p0.`titulo`, p0.`inserted_at`, p0.`updated_at` FROM `postagens` AS p0 WHERE (p0.`id` = ?) [2]
%Blog.Post.Postagem{
  __meta__: #Ecto.Schema.Metadata<:loaded, "postagens">,
  id: 2,
  inserted_at: ~N[2019-04-11 19:28:52],
  texto: "Conteúdo....", 
  titulo: "Primeiro post",
  updated_at: ~N[2019-04-11 19:28:52]
}
```

## Consultar por título

```elixir
iex(8)> Repo.get_by(Postagem, titulo: "primeiro post")
[debug] QUERY OK source="postagens" db=0.8ms
SELECT p0.`id`, p0.`texto`, p0.`titulo`, p0.`inserted_at`, p0.`updated_at` FROM `postagens` AS p0 WHERE (p0.`titulo` = ?) ["primeiro post"]
%Blog.Post.Postagem{
  __meta__: #Ecto.Schema.Metadata<:loaded, "postagens">,
  id: 2,
  inserted_at: ~N[2019-04-11 19:28:52],
  texto: "Conteúdo....", 
  titulo: "Primeiro post",
  updated_at: ~N[2019-04-11 19:28:52]
}
```

## Atualizar um post

```elixir
iex(3)> Blog.Repo.get(Postagem, 2) 
            |> Ecto.Changeset.change(%{titulo: "ohashi"})
            |> Blog.Repo.update
[debug] QUERY OK source="postagens" db=7.0ms
SELECT p0.`id`, p0.`texto`, p0.`titulo`, p0.`inserted_at`, p0.`updated_at` FROM `postagens` AS p0 WHERE (p0.`id` = ?) [2]
[debug] QUERY OK db=36.8ms queue=10.2ms
UPDATE `postagens` SET `titulo` = ?, `updated_at` = ? WHERE `id` = ? ["ohashi", ~N[2019-05-06 19:04:35], 2]
{:ok,
 %Blog.Post.Postagem{
   __meta__: #Ecto.Schema.Metadata<:loaded, "postagens">,
   id: 2,
   inserted_at: ~N[2019-04-11 19:28:52],
   texto: "Conteúdo....",
   titulo: "ohashi",
   updated_at: ~N[2019-05-06 19:04:35]
 }}

```

## Deletar um post

```elixir
iex(5)> a = Repo.get(Postagem, 2)                                               [debug] QUERY OK source="postagens" db=3.4ms
SELECT p0.`id`, p0.`texto`, p0.`titulo`, p0.`inserted_at`, p0.`updated_at` FROM `postagens` AS p0 WHERE (p0.`id` = ?) [2]
%Blog.Post.Postagem{
  __meta__: #Ecto.Schema.Metadata<:loaded, "postagens">,
  id: 2,
  inserted_at: ~N[2019-04-11 19:28:52],
  texto: "Conteúdo....", 
  titulo: "ohashi",
  updated_at: ~N[2019-05-06 19:04:35]
}

iex(6)> Repo.delete(a)
[debug] QUERY OK db=6.2ms queue=2.8ms
DELETE FROM `postagens` WHERE `id` = ? [2]
{:ok,
 %Blog.Post.Postagem{
   __meta__: #Ecto.Schema.Metadata<:deleted, "postagens">,
   id: 2,
   inserted_at: ~N[2019-04-11 19:28:52],
   texto: "Conteúdo....",
   titulo: "ohashi",
   updated_at: ~N[2019-05-06 19:04:35]
 }}
```


fonte: https://hexdocs.pm/phoenix/Mix.Tasks.Phx.Gen.Html.html

fonte: https://hexdocs.pm/phoenix/up_and_running.html