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

## Consultar um post especifico, by id

## Consultar por título

## Atualizar um post

## Criar um post 

## Deletar um post


fonte: https://hexdocs.pm/phoenix/Mix.Tasks.Phx.Gen.Html.html

fonte: https://hexdocs.pm/phoenix/up_and_running.html