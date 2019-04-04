# Introdução ao Elixir

- Elixir é uma linguagem de programação funcional, concorrente, linguagem de propósito geral que roda Erlang virtual machine (BEAM). Desenvolvida por um brasileiro "José Valim - Plataformatec".  

fonte: https://en.wikipedia.org/wiki/Elixir_(programming_language)

- Erlang é apenas a linguagem (VM) por trás do Whatsapp, 50 engenheiros para 90 milhões de usuários. 

fonte: 
https://www.wired.com/2015/09/whatsapp-serves-900-million-users-50-engineers/
https://www.fastcompany.com/3026758/inside-erlang-the-rare-programming-language-behind-whatsapps-success

- Erlang code - ugly

![](erlang_code.jpg)

- Elixir - sintaxe mais amigável

![](elixir_code.jpg)

- Phoenix "roubou" diversos desenvolvedores Rails. As similaridades de sintaxe do Elixir com o Ruby e as características de alta concorrência e escalabilidade providas pelo Erlang, tornaram o Elixir/Phoenix uma evolução natural para os desenvolvedores Ruby/Rails.

fonte: https://smashingboxes.com/blog/choosing-your-future-tech-stack-clojure-vs-elixir-vs-go/

- 2 milhões de WebSocket conexões com Phoenix

![](2mi_websockets.png)

fonte: https://phoenixframework.org/blog/the-road-to-2-million-websocket-connections

Este material é baseado https://elixirschool.com/en, versão em Português https://elixirschool.com/pt (pode estar desatualizada)

# Instalação

Seguir o guia da página oficial https://elixir-lang.org/install.html ou https://elixirgirls.com/install-guides/windows.html

# Shell - iex

```elixir
iex(1)> 2 + 3
5
iex(2)> 2 + 3 == 5
true
iex(3)> String.length("UFRA - ICIBE")         
12
```

# Tipos de dados: Integer, Float, Boolean, Atoms, Strings
```elixir
iex(4)> 25 
25
iex(5)> 5.6
5.6
iex(6)> true
true
iex(7)> false
false
iex(8)> :constante
:constante
iex(9)> :ufra
:ufra
iex(10)> is_atom(:ufra)
true
iex(11)> :ufra |> is_atom # passa :ufra como 1 parametro para a função seguinte
true
iex(12)> "ufra"
"ufra"
iex(13)> "ufra" == :ufra
false
iex(14)> String.to_atom("ufra") == :ufra
true
```

# Variáveis e operadores lógicos
```elixir
iex(15)> x = 12
12
iex(16)> x > 12 and x <= 20
false
iex(17)> x > 12 or x <= 20 
true
iex(18)> x == 12 
true
iex(19)> x === 12.0
false
iex(20)> x == 12.0 
true
iex(21)> x != 12
false
```

# Interpolação e concatenação de Strings
```elixir
iex(22)> x = "ICIBE"
"ICIBE"
iex(23)> "Ufra - #{x}"
"Ufra - ICIBE"
iex(24)> "Ufra -" <> x
"Ufra -ICIBE"
```

# Lists
Estrutura genérica, de tamanho variável que pode conter elementos dos mais variados tipos. 

```elixir
iex(32)> lista = [12, :ufra, "icibe"]
[12, :ufra, "icibe"]
```

# Concatenar Lists
```elixir
iex(34)> lista ++ [3.4]
[12, :ufra, "icibe", 3.4]
iex(35)> lista # não alterou a variável lista
[12, :ufra, "icibe"]
# guardando nova lista
iex(36)> lista2 = lista ++ [3.4]
[12, :ufra, "icibe", 3.4]
```

# Subtrair Lists
```elixir
iex(37)> [1,2,3,4,5] -- [1,2,6]
[3, 4, 5]
iex(38)> [1,1,2,2,3] --[1,2,3] 
[1, 2]
iex(39)> ["foo", :bar, 42] -- [42, "bar"]
["foo", :bar]
```

# Head | Tail (muito usado...)
```elixir
iex(40)> x = [1,2,3,4,5]
[1, 2, 3, 4, 5]
iex(41)> hd(x)
1
iex(42)> tl(x)
[2, 3, 4, 5]
iex(43)> [h|t] = x # pattern matching
[1, 2, 3, 4, 5]
iex(44)> h
1
iex(45)> t
[2, 3, 4, 5]
```

# Tuples
Tuples são estruturas que são armazenadas num espaço continuo de memória (diferente das Lists), o que torna o acesso to tamanho muito rápido e alterações custoso. Uma nova tupla tem que ser copiada inteira na memória. Muito utilizada em retorno de funções.

```elixir
iex(46)> {:ok, 12}
{:ok, 12}
iex> File.read("path/to/existing/file")
{:ok, "... contents ..."}
iex> File.read("path/to/unknown/file")
{:error, :enoent}    
```

# Keyword Lists

Keyword Lists é um tipo especial de List. Lista de tuplas onde o primeiro elemento da tupla é um atom. 

```elixir
iex> [foo: "bar", hello: "world"]
[foo: "bar", hello: "world"]
iex> [{:foo, "bar"}, {:hello, "world"}]
[foo: "bar", hello: "world"]
```

Características:

1 - Keys são atoms;
2 - Keys são ordenadas;
3 - Keys não precisam ser únicas.

Por causa destas características, são usualmente utilizadas para passar parâmetros para funções.

# Maps

Maps são estrutura Hash que permite uma coleção key-value. Permite que a key seja de qualquer tipo e não são ordenadas. 

```elixir
iex> map = %{:foo => "bar", "hello" => :world}
%{:foo => "bar", "hello" => :world}
iex> map[:foo]
"bar"
iex> map["hello"]
:world
```

- Não é permitido duplicação de chave.

```elixir
iex> %{:foo => "bar", :foo => "hello world"}
%{foo: "hello world"}
```

- sintax especial quando todas as key são atoms
```elixir
iex> %{foo: "bar", hello: "world"}
%{foo: "bar", hello: "world"}
iex> %{foo: "bar", hello: "world"} == %{:foo => "bar", :hello => "world"}
true
iex> map.hello
"world"
```

# Pattern Matching

O operador = é na verdade um operador de correspondência (match operator), seria o mesmo que o operador = na matemática. Elixir faz o match se o valor da esquerda é igual ao da direita. Se o Match der certo ele retorna o valor da operação, caso contrário retorna um erro.

```elixir
iex(47)> x = 1
1
iex(48)> 1 = x
1
iex(49)> 2 = x
** (MatchError) no match of right hand side value: 1
    (stdlib) erl_eval.erl:453: :erl_eval.expr/5
    (iex) lib/iex/evaluator.ex:257: IEx.Evaluator.handle_eval/5
    (iex) lib/iex/evaluator.ex:237: IEx.Evaluator.do_eval/3
    (iex) lib/iex/evaluator.ex:215: IEx.Evaluator.eval/3
    (iex) lib/iex/evaluator.ex:103: IEx.Evaluator.loop/1
    (iex) lib/iex/evaluator.ex:27: IEx.Evaluator.init/4
```

Matching com lista
```elixir
iex(49)> lista = [1, 2, 3]  
[1, 2, 3]
iex(50)> [1, 2, 3] = lista
[1, 2, 3]
iex(51)> [1, 2] = lista
** (MatchError) no match of right hand side value: [1, 2, 3]
    (stdlib) erl_eval.erl:453: :erl_eval.expr/5
    (iex) lib/iex/evaluator.ex:257: IEx.Evaluator.handle_eval/5
    (iex) lib/iex/evaluator.ex:237: IEx.Evaluator.do_eval/3
    (iex) lib/iex/evaluator.ex:215: IEx.Evaluator.eval/3
    (iex) lib/iex/evaluator.ex:103: IEx.Evaluator.loop/1
    (iex) lib/iex/evaluator.ex:27: IEx.Evaluator.init/4
iex(51)> [1, 2, aux] = lista
[1, 2, 3]
iex(52)> aux
3
iex(53)> [1, 2, _] = lista # _ ignora a variável
[1, 2, 3]
iex(54)> [1 | tail] = lista
[1, 2, 3]
iex(55)> tail
[2, 3]
```



# Estruturas de Controle

# Funções

# Modules

# Funções Enum


