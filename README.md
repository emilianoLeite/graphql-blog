[Blog](https://emilianoleite.github.io/toy-graphql/)

[Core - Cloud9 IDE](https://ide.c9.io/emilianoleite/cerc-core-graphql)


# Daily Progress Blog
I'll update this blog daily, recording my progress and success in trying to make a funcional front/back integration with GraphQL.

## Day 0 (01/08)
* Created Repository
* Created blog

## Day 1 (02/08)
Tentando pensar se faz sentido criar um projeto Rails Api do zero. Se uma vez que eu consiga fazer o graphQL funcionar nesse projeto "virgem" eu realmente terei menos trabalho/mais facilidade de migrar o projeto real da CERC pra graphQL. Tudo isso porque não posso jogar o fonte do cliente num repositório público como este.

Estou quase decidido a tentar jogar o projeto do bitbucket e trabalhar por lá, junto com c9 IDE. O downside disso é que o projeto e o "blog" ficarão em repositórios separados.

----
Depois de pensar um pouco sobre o problema lembrei que o principal objetivo do exercício é testar quão facil é migrar uma aplicação já funcional para GraphQL. Não sei se a aplicação da CERC será um bom exemplo, se deixará tudo mais fácil ou mais difícil, mas como é a aplicação com a qual trabalho no dia-a-dia, me parece um ponto sensato para começar.

Baixei a aplicação em `.zip` na minha máquina de casa (Windows) e depois upei pra Cloud9 IDE.

Prevejo que o maior problema vai ser instalar/setar o mongo na máquina da Cloud9, que é o que estou tentando fazer agora.

---- 
Aparentemente não houveram problemas pra instalar o mongo (awyiss).

Os próximos passos são:
- [ ] Instalar alguma gem graphql e expor algum model através do GraphQL
- [ ] Teste automatizado pra ver se tá funcionando mesmo

## Day 2 (03/08)
Acho que conseguiria instalar a gem e ir tentando até ter algo minimamente funcional, mas se a base teórica por trás, não conseguiria explicar pra ninguém as motivações e considerações a se tomar numa migração para GraphQL. Portanto nos dias seguintes pretendo estar a std-doc do GraphQL pra me familiarizar com os termos e entender um pouco mais sobre as motivações e objetivos do GraphQL, antes de sair codando.

----
Depois de ver os primeiros 3 vídeos de introdução do site [How To GraphQL](https://www.howtographql.com/), aprendi algumas coisas interessantes
* REST pode os problemas:
  * Overfetching: Você precisa do nome do usuário, dá um `/users/:id` e o endpoint te retorna **todos** os campos do usuário (nome, idade, email...);
  * Underfetching: Você precisa do nome de todos posts de um autor, mas ao fazer `/authors/:id`, o endpoint apenas te retorna `post_ids`(ou nem isso). Consequentemente você precisa fazer um novo request para `/authors/:id/posts` para pegar os respectivos nomes.

Também aprendi que o Schema do GraphQL tem 3 **Root Types**:
  * Query: o **R** do C**R**UD;
  * Mutation: serve para **C**R**UD**;
  * Subscription: (me surpreendeu) parece ser um canal aberto onde o server pusha novos eventos para o cliente. O vídeo não explicou como isso funciona por debaixo dos panos

Já que falei de Schema, é bom explicar um pouco mais:    
GraphQL depende de Schemas, que funcionam como contratos, para definir quais são as operações possíveis/permitidas. Um schema é fortemente tipado, ou seja, ele é composto de exclusivamente de Tipos bem definidos. Os 3 Tipos especiais já foram citados acima, de resto, qualquer coisa pode ser um Tipo, e deduzo que isso se mapeia direta mas não exclusivamente com os models/collections da sua aplicação.

## Day 3 (04/08)
Ouvindo o podcast [GraphQL Radio](https://graphqlradio.com/), aprendi que a migração incremental REST -> GraphQL pode ser ainda mais granular do que eu pensava: A implementação do GraphQL pode, inicialmente, ser feita exclusivamente no frontend, deixando o back completamente REST. Para isso, basta criar **Resolvers** no frontend que fazem requests para a API REST.    
Pelo que eu entendo, **Resolvers** são wrappers que armazenam o conhecimento de onde os Tipos do sistema são armazenados fisicamente; em outras palavras: **Resolvers** sabem onde buscar a informação sobre dado Tipo.

## Day 4 (05/08)
Nenhum conhecimento no adquirido.

## Day 5 (06/08)
Lendo um pouco da doc oficial, entendi que **Resolvers** geralmente mapeiam 1-1 com os campos de uma query, ou seja: um **Resolver** sabe como gerar apenas um campo de um Tipo, e não um Tipo inteiro. Isso abre possibilidades para que campos de um Tipo possam ficar em diferentes DBs, mas não sei quão prático/útil isso seria. 

**Resolvers** são funções com a seguinte assinatura:
```graphql
human(obj, args, context) {
   ...
}
```
Os três argumentos que a função recebem são:
* `obj` The previous object, which for a field on the root Query type is often not used.
* `args` The arguments provided to the field in the GraphQL query.
* `context` A value which is provided to every resolver and holds important contextual information like the currently logged in user, or access to a database.

### Trivial Resolvers
Para query simples como:
```graphql
{
  human(id: 1002) {
    name
  }
}
```

o resolver de `name` seria algo assim:
```graphql
name(obj, args, context) {
  return obj.name
}
```
o que é bem simples/trivial, então muitas libs de GraphQL deixam você omitir esses *trivial resolvers*

## Day 6 (07/08)
Aprendi um pouco mais sobre GraphQL clients, e quais são suas propostas/objetivos. No geral os GraphQL clients se propõe a:
* Abstrair a obtenção e armazenamento dos dados
* Prover um serviço inteligente de caching
  * Para fazer isso, os clients (pelo menos o [Apollo](http://dev.apollodata.com/core/how-it-works.html#query-benefits)) normalizam os resultados da query, geralmente usando o `id` retornado como identificador único de cada objeto da resposta. Mas me pergunto como isso funciona quando a query enviada não pede o `id` do objeto.
* Otimizar queries e identificar erros de construção e sintaxe

## Day 7 (08/08)
Aprendi sobre alguns mecanismos de segurança para evitar que queries maldosas derrubem ou atrasem o servidor GraphQL. Como os schemas GraphQL geralmente são gráficos cíclicos (ainda não sei exatamente o que isso quer dizer), é possível montar queries infinitas como:

```graphql
query IAmEvil {
  author(id: "abc") {
    posts {
      author {
        posts {
          author {
            posts {
              author {
                # that could go on as deep as the client wants!
              }
            }
          }
        }
      }
    }
  }
}
```
Além dessas, muita queries aparentemente "simples" podem causar uma grande carga no server.

Para evitar isso, alguns mecanismos de defesa geralmente utilizados são:
* `Timeout`: Cancelar uma query que demora mais de X `ms`
* `Maximum Query Depth`: Limite de quantos níveis de profundidade podem haver numa query
* `Query Complexity`: Os desenvolvedores da API determinam qual a complexidade (custo) de cada campo de cada tipo, dando a eles uma pontuação normalizada. Uma query que excede a pontuação máxima determinada é bloqueada.
* `Throttling`: Geralmente consiste em dar um "saldo" para os clientes e bloquear qualquer query feita depois que o saldo foi zerado. Esse saldo é restituído ao longo do tempo

Esses mecanismos geralmente podem e devem ser utilizados em composição, para fornecer uma solução robusta e flexível.

Alguns exemplos são:
* `Throttling` **+** `Timeout` (na verdade Server Response Time): Dar um saldo de tempo ("x `ms`") para o cliente. Assim o cliente pode fazer algumas queries demoradas, ou várias queries rápidas. Essa composição não é ideal, pois é muito difícil pro cliente saber antecipadamente quanto cada query vai demorar.
* `Throttling` **+** `Query Complexity`: Dar um saldo de pontos para o cliente. Assim o cliente pode fazer algumas queries complexas, ou várias queries simples. Essa composição é melhor que a anterior, pois o cliente consegue pré-calcular o custo de um query, podendo assim melhor administrar seu saldo. O [GitHub usa essa composição](https://developer.github.com/v4/guides/resource-limitations/).

## Day 8 (09/08)
Na site (que eu acho que é o) oficial do GraphQL, na página de [Schemas & Types](http://graphql.org/learn/schema/#type-system) não há menção ao tipo especial `subscription`; é apenas falado sobre `query` e `mutation`. Isso me leva a crer que a feature de `subscription` ainda não esteja completa, ou pelo menos ainda está em testes.

A partir de amanhã pretendo colocar a mão na massa e tentar o começo de uma implementação GraphQL no projeto CERC, já que acabei a (curta) parte teórica do tutorial [How to GraphQL](https://www.howtographql.com/) e prefiro ficar experimentando com um projeto real em vez de um *[greenfield](https://en.wikipedia.org/wiki/Greenfield_project)*.

Os passos pendentes (do [Day 1](#day-1-0208)) são:
- [ ] Instalar alguma gem graphql e expor algum model através do GraphQL
  * pretendo usar a [gem graphql](http://graphql-ruby.org/getting_started)
- [ ] Teste automatizado pra ver se tá funcionando mesmo

## Day 9 (10/08)
Nenhum conhecimento no adquirido.

## Day 10 (11/08)
- [X] Instalar alguma gem graphql e expor algum model através do GraphQL
  * pretendo usar a [gem graphql](http://graphql-ruby.org/getting_started)
- [X] Teste automatizado pra ver se tá funcionando mesmo

Depois de instalar a `gem graphql` e seguir os passos do [Getting Started](http://graphql-ruby.org/getting_started), tive um pouco de dificuldade em criar um campo `nfeValidations` no meu `QueryType`, mas no final acabei conseguindo. Também, depois de mais tentativas-e-erros,  consegui criar um teste de request automatizado pra garantir o funcionamento da query `nfeValidations`.   
Mas aí fiquei com a dúvida se realmente é necessário criar testes para todas a queries criadas, ou se é possível confiar que o sistema de tipagem e schema do GraphQL são o suficiente pra acusar qualquer problema/inconsistência com as queries.

## Day 11 (12/08)
Instalei a [gem graphql-client](https://github.com/github/graphql-client] no `painel Admin` e, usando o [ngrok](https://ngrok.com), consegui fazer a query `nfeValidations` previamente criada no `core`.     
Próximos passos: 
- [ ] `Core` - criar uma query para representar o show de uma `NfeValidation`;
- [ ] `Admin` - Na action `Admin::NfeValidationsController#show`, em vez usar o `CERCClient`, obter os dados da `NfeValidation` atráves da `graphql-client`.

## Day 12 (13/08)
Talvez o endpoint de show de uma `NfeValidation` não seja o melhor lugar pra começar, **muitos** dados são devolvidos. Mas vou me ater a ele mesmo, porque me dará a chance de trabalhar com as  `Interfaces` do GraphQL (acho).
Tive um pouco de dificuldade para criar a query `nfeValidation(id: ID)` :
* Na query anterior (`nfeValidations`) eu declarava o tipo dela como um segundo argumento, antes de passar o bloco:
```ruby
 field :nfeValidations, types[Types::NfeValidationType] do
   ...
 end
```

* Mas como a nova query (`nfeValidation(id: ID)`) retorna apenas 1 record, me baseei na query gerada no scaffold:
```ruby
field :testField, types.String do
  description "An example field added by the generator"
  resolve ->(obj, args, ctx) {
    "Hello World!"
  }
end
```
e tentei usar a assinatura:
```ruby
 field :nfeValidation, types.Types::NfeValidationType do
   ...
 end
 ``` 
 mas ao tentar executar a query, tomava esse erro:
 ```ruby
 NoMethodError: undefined method `Types' for #<GraphQL::Define::TypeDefiner:0x0000000933ca80>
 ```
 
então passei a declaração do tipo pra dentro do bloco, que nem um exemplo do [Getting Started](http://graphql-ruby.org/getting_started):
```ruby
field :nfeValidation do
  type Types::NfeValidationType
  argument :id, !types.ID
  resolve ->(obj, args, ctx) { NfeValidation.find(args['id']) }
end
```
e finalmente funcionou;
* Mas aí, ao tentar executar a query:
```ruby
"
{
  nfeValidation(id:#{nfe_validation.id.to_s}) {
    id
  }
}"
```
eu tomava o seguinte erro:

```ruby
{"errors"=>[{"message"=>"Parse error on \")\" (RPAREN) at [2, 52]", "locations"=>[{"line"=>2, "column"=>52}]}]}
```

eu pensei que ` \")\" (RPAREN)` queria dizer que eu não tinha fechado corretamente os parentesis ou algo assim, mas depois de um triple-check não achei nada de errado.    
Então resolvi copiar a query de exemplo do [Getting Started](http://graphql-ruby.org/getting_started) e funcionou: em vez de tomar esse erro de parsing, tomei o `Mongoid::Errors::DocumentNotFound` para o id `1`.     
Então o problema era com o id, não com os parentesis.   
Pensei que talvez o `types.ID` da gem `graphql` não funcionasse com os ids do mongo, então tentei mudar o argumento da query pra ser `types.String`.     
Tentei passar o `nfe_validation.id.to_s`, mas tomei o mesmo erro `"Parse error on \")\" (RPAREN)`.   
Passei então `"{nfeValidation(id: abc) {id}}"` e tomei o erro `"Argument 'id' on Field 'nfeValidation' has an invalid value. Expected type 'String!'."`.    
**Bingo**.     
Tentei `"{nfeValidation(id: 'abcjnh') {id}}"`, mas tomei`"Parse error on \"'\" (error)`.     
Tentei `"{nfeValidation(id: \"abcjnh\") {id}}"` e finalmente tomei `Mongoid::Errors::DocumentNotFound`.    
Reverti o tipo do argumento da query pra `types.ID` e tomei o mesmo erro; e uma vez que eu passei um `id` válido, a query funcionou (feelsgoodman).

### TL;DR
Pra `ids` não-decimais, é necessário envolver o `id` com aspas; aspas simples não servem, apenas aspas duplas.

## Day 13 (14/08)
Definitivamente começar pelo endpoint do show da Validação não foi uma boa idéia. O endpoint devolve uma quantidade massiva de dados, o que vai demorar muito pra ser convertido em GraphQL Schemas. 

Muitos dos dados devolvidos estão contidos em campos do tipo `Hash`. Como os Trivial Resolvers da gem `graphql` tentam acessar os campos como métodos em um objeto

```ruby
# Exemplo:

Types::NfeValidationType = GraphQL::ObjectType.define do
  name "NfeValidation"
  field :id, !types.ID # => Tentará acessar nfe_validation.id
end
```
eu pensei que teria que criar os resolvers pra acessar chaves do `Hash` na mão:

```ruby
# Exemplo:

Types::RecebivelType = GraphQL::ObjectType.define do
  name "Recebivel"
  field :tipo, !types.String do
    resolve ->(obj, args, ctx) { obj[:tipo] }
  end
end
```

mas depois de dar uma pesquisada na [API da gem](http://www.rubydoc.info/gems/graphql/1.6.6/), achei como [declarar chaves de um `Hash` como Trivial Resolvers](http://www.rubydoc.info/gems/graphql/1.6.6/GraphQL/Field):
```ruby
# Exemplo:

Types::RecebivelType = GraphQL::ObjectType.define do
  name "Recebivel"
  field :tipo, !types.String, hash_key: :tipo
end
```

## Day 14 (15/08)
Nenhum conhecimento no adquirido.

## Day 15 (16/08)
Nenhum conhecimento no adquirido.
