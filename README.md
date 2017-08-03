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
