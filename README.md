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
* Instalar alguma gem graphql e expor algum model através do GraphQL
* Teste automatizado pra ver se tá funcionando mesmo

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
