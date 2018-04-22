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

## Ecossistema

O Redux é uma pequena biblioteca, mas seus contratos e APIs são cuidadosamente escolhidos para gerar um ecossistema de ferramentas e extensões.

Para uma extensa lista de tudo relacionado ao Redux, recomendamos o Awesome Redux. Ele contém exemplos, boilerplates, middleware, bibliotecas de utilitários e muito mais. O React / Redux Links contém tutoriais e outros recursos úteis para qualquer pessoa que esteja aprendendo o React ou o Redux, e o Redux Ecosystem Links lista muitas bibliotecas e addons relacionados ao Redux.

Nesta página, apenas apresentaremos alguns deles que os mantenedores do Redux examinaram pessoalmente. Não deixe que isso te desencoraje de tentar o resto deles! O ecossistema está crescendo rápido demais e temos um tempo limitado para analisar tudo. Considere estas “escolhas da equipe”, e não hesite em enviar um PR se você construiu algo maravilhoso com o Redux.

### Ligações
* react-redux - Reagir
* ng-redux - Angular
* ng2-redux - Angular 2
* backbone-redux - Backbone
* redux-falcor - Falcor
* deku-redux - Deku
* polímero-redux - Polímero
* ember-redux - Ember.js

### Middleware
* redux-thunk - A maneira mais fácil de escrever criadores de ações assíncronas
* redux-promise - middleware de promessa compatível com FSA
* redux-axios-middleware - Middleware Redux para buscar dados com o cliente HTTP axios
* redux-observable - middleware RxJS para efeitos colaterais de ação usando "Epics"
* redux-cycles - Manipula ações assíncronas do Redux usando Cycle.js
* redux-logger - Registra todas as ações do Redux e o próximo estado
* redux-immutable-state-invariant - Avisa sobre mutações de estado no desenvolvimento
* redux-unhandled-action - Avisa sobre ações que não produziram mudanças de estado no desenvolvimento
* redux-analytics - middleware do Analytics para Redux
* redux-gen - Generator middleware para Redux
* redux-saga - Um modelo alternativo de efeitos colaterais para aplicativos Redux
* redux-action-tree - Sinais de estilo Cerebral Composable para Redux
* apollo-client - Um cliente de caching simples para qualquer servidor GraphQL e framework de UI construído sobre o Redux

### Roteamento
* react-router-redux - Ligações impiedosamente simples para manter o React Router e Redux em sincronia
* redial - Busca universal de dados e gerenciamento do ciclo de vida de rotas para o React que funciona muito bem com o Redux

### Componentes
* redux-form - Manter o estado do formulário React no Redux
* react-redux-form - Crie formulários facilmente em React with Redux

### Intensificadores
* redux-batched-subscribe - Personalize as chamadas em lote e de discagem para os assinantes da loja
* redux-history-transitions - Transições de histórico baseadas em ações arbitrárias
* redux-optimist - Aplique otimisticamente ações que podem ser posteriormente confirmadas ou revertidas
* redux-optimistic-ui - Um aprimorador de redutor para ativar atualizações otimistas de tipo agnóstico
* redux-undo - Desfazer / refazer sem esforço e histórico de ações para seus redutores
* redux-ignore - Ignora ações de redux por array ou função de filtro
* redux-recycle - Redefine o estado de redux em certas ações
* redux-batched-actions - Despachar várias ações com uma única notificação de assinante
* redux-search - Indexa automaticamente recursos em um trabalhador da Web e pesquisa-os sem bloqueá-los
* redux-electron-store - Armazena potenciadores que sincronizam as lojas Redux através dos processos de Electron
* redux-loop - Sequencia efeitos puramente e naturalmente, retornando-os de seus redutores
* redux-side-effects - Utilize geradores para rendimento declarativo de efeitos colaterais de seus redutores puros

### Serviços de utilidade pública
* select - Seletores de dados derivados eficientes inspirados pelo NuclearJS
* normalizr - normaliza respostas aninhadas da API para facilitar o consumo dos redutores
* redux-actions - Reduz o clichê de redutores e criadores de ação por escrito
* redux-act - Uma biblioteca opinativa para fazer redutores e criadores de ação
* redux-transducers - Utilitários do transdutor para o Redux
* redux-immutable - Usado para criar uma função equivalente do Redux combineReducers que funciona com o estado Immutable.js.
* redux-tcomb - Estado e ações imutáveis ​​e com verificação de tipo para o Redux
* redux-mock-store - Mock loja redux para testar seu aplicativo
* redux-actions-assertions - Asserções para testes de ações do Redux
* redux-bootstrap - Função bootstrapping para aplicativos Redux

### DevTools
* Redux DevTools - Um logger de ação com interface de tempo de viagem, recarga quente e tratamento de erros para os redutores, primeiro demoed na React Europe
* Redux DevTools Extension - Uma extensão do Chrome que envolve o Redux DevTools e fornece funcionalidade adicional

### Monitores DevTools
* Log Monitor - O monitor padrão para Redux DevTools com uma visualização em árvore
* Dock Monitor - Um dock redimensionável e móvel para os monitores Redux DevTools
* Slider Monitor - Um monitor personalizado para o Redux DevTools para reproduzir ações gravadas do Redux
* Inspector - Um monitor personalizado para Redux DevTools que permite filtrar ações, inspecionar diffs e fixar caminhos profundos no estado para observar suas alterações
* Diff Monitor - Um monitor para o Redux Devtools que difere as mutações do armazenamento do Redux entre as ações
* Monitor de Log Filtrável - Monitor de exibição de árvore filtrável para Redux DevTools
* Chart Monitor - Um monitor gráfico para Redux DevTools
* Ações de filtro - Redux monitor composable DevTools com a capacidade de filtrar ações



