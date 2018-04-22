# Quick knowledge Redux

O Redux é um contêiner de estado previsível para aplicativos JavaScript.

Como os requisitos para aplicativos de página única do JavaScript se tornaram cada vez mais complicados, nosso código deve gerenciar mais estados do que nunca. Esse estado pode incluir respostas do servidor e dados em cache, bem como dados criados localmente que ainda não foram persistidos no servidor. O estado da interface do usuário também está aumentando em complexidade, já que precisamos gerenciar rotas ativas, guias selecionadas, spinners, controles de paginação e assim por diante.

Gerenciar esse estado em constante mudança é difícil. Se um modelo puder atualizar outro modelo, uma exibição poderá atualizar um modelo, que atualizará outro modelo, e isso, por sua vez, poderá fazer com que outra visão seja atualizada. Em algum momento, você não entende mais o que acontece no seu aplicativo, pois perdeu o controle sobre quando, por que e como está. Quando um sistema é opaco e não determinístico, é difícil reproduzir erros ou adicionar novos recursos.

Como se isso não fosse ruim o suficiente, considere os novos requisitos se tornando comuns no desenvolvimento de produtos front-end. Como desenvolvedores, espera-se que lidemos com atualizações otimistas, renderização do lado do servidor, buscando dados antes de realizar transições de rotas e assim por diante. Nós nos encontramos tentando administrar uma complexidade com a qual nunca tivemos que lidar antes, e inevitavelmente fazemos a pergunta: é hora de desistir? A resposta é não.

Essa complexidade é difícil de lidar, pois estamos misturando dois conceitos que são muito difíceis para a mente humana pensar: mutação e assincronia. Eu os chamo de Mentos e Coca-Cola. Ambos podem ser ótimos na separação, mas juntos eles criam uma bagunça. Bibliotecas como o React tentam resolver esse problema na camada de visualização, removendo tanto a assincronia quanto a manipulação direta do DOM. No entanto, gerenciar o estado de seus dados é deixado para você. É aqui que o Redux entra.

Seguindo as etapas do Flux, CQRS e Event Sourcing, o Redux tenta tornar as mutações de estado previsíveis, impondo certas restrições sobre como e quando as atualizações podem acontecer. Essas restrições são refletidas nos três princípios do Redux.


* Conceitos Básicos
* Ecosystem
* Examples
* Introduction
* Motivation
* Prior Art
* Three Principles

## Conceitos Básicos

O Redux em si é muito simples.

Imagine que o estado do seu aplicativo é descrito como um objeto simples. Por exemplo, o estado de um aplicativo todo pode ter esta aparência:
```js
{
  todos: [{
    texto: 'comer comida',
    completado: true
  }, {
    texto: 'Exercício',
    completed: false
  }],
  visibilityFilter: 'SHOW_COMPLETED'
}
```
Este objeto é como um "modelo", exceto que não há setters. Isso acontece para que partes diferentes do código não alterem o estado arbitrariamente, causando bugs difíceis de reproduzir.

Para mudar algo no estado, você precisa despachar uma ação. Uma ação é um objeto JavaScript simples (observe como não introduzimos mágica?) Que descreve o que aconteceu. Aqui estão algumas ações de exemplo:
```js
{type: 'ADD_TODO', texto: 'Ir para a piscina'}
{type: 'TOGGLE_TODO', índice: 1}
{type: 'SET_VISIBILITY_FILTER', filtro: 'SHOW_ALL'}
```
A aplicação de que todas as alterações são descritas como uma ação nos permite ter uma compreensão clara do que está acontecendo no aplicativo. Se algo mudou, sabemos porque mudou. Ações são como migalhas de pão do que aconteceu. Finalmente, para amarrar estados e ações juntos, escrevemos uma função chamada redutor. Novamente, nada de mágico - é apenas uma função que toma o estado e a ação como argumentos e retorna o próximo estado do aplicativo. Seria difícil escrever uma função desse tipo para um aplicativo grande, por isso escrevemos funções menores gerenciando partes do estado:
```js
function visibilityFilter (state = 'SHOW_ALL', ação) {
  if (action.type === 'SET_VISIBILITY_FILTER') {
    return action.filter
  } outro {
    estado de retorno
  }
}

function todos (state = [], ação) {
  switch (action.type) {
    case 'ADD_TODO':
      retornar state.concat ([{text: action.text, completed: false}])
    caso 'TOGGLE_TODO':
      return state.map (
        (todo, índice) =>
          action.index === index
            ? {text: todo.text, concluído:! todo.completado}
            : façam
      )
    padrão:
      estado de retorno
  }
}
```
E nós escrevemos outro redutor que gerencia o estado completo do nosso aplicativo chamando esses dois redutores para as chaves de estado correspondentes:
```js
função todoApp (state = {}, action) {
  Retorna {
    todos: todos (state.todos, ação),
    visibilityFilter: visibilityFilter (state.visibilityFilter, ação)
  }
}
```
Esta é basicamente a ideia do Redux. Observe que não usamos nenhuma API do Redux. Ele vem com alguns utilitários para facilitar esse padrão, mas a idéia principal é que você descreva como seu estado é atualizado ao longo do tempo em resposta a objetos de ação, e 90% do código que você escreve é ​​simplesmente JavaScript, sem uso do Redux. em si, suas APIs ou qualquer mágica.
