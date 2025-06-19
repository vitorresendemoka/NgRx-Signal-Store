# Padrão Redux no Gerenciamento de Estado em Aplicações Frontend

## Índice

- [Padrão Redux no Gerenciamento de Estado em Aplicações Frontend](#padrão-redux-no-gerenciamento-de-estado-em-aplicações-frontend)
  - [Índice](#índice)
  - [Introdução ao Padrão Redux](#introdução-ao-padrão-redux)
  - [Quais problemas o Redux resolve](#quais-problemas-o-redux-resolve)
  - [Quando usar o Redux](#quando-usar-o-redux)
  - [NgRx Signal Store - Padrão Redux em Aplicações Angular](#ngrx-signal-store---padrão-redux-em-aplicações-angular)
    - [Introdução ao NgRx Signal Store](#introdução-ao-ngrx-signal-store)
      - [O que o NgRx Signal Store resolve](#o-que-o-ngrx-signal-store-resolve)
    - [Como usar o NgRx Signal Store?](#como-usar-o-ngrx-signal-store)
      - [Criar um store com estado inicial](#criar-um-store-com-estado-inicial)
      - [Adicionar valores derivados](#adicionar-valores-derivados)
      - [Criar métodos para atualizar o estado](#criar-métodos-para-atualizar-o-estado)
      - [Executar ações automáticas](#executar-ações-automáticas)
      - [Usar o store em algum componente.](#usar-o-store-em-algum-componente)
    - [Quando usar o NgRx Signal Store dentro do Moka Web](#quando-usar-o-ngrx-signal-store-dentro-do-moka-web)
    - [Vantagens e Desvantagens do NgRx Signal Store](#vantagens-e-desvantagens-do-ngrx-signal-store)
      - [Vantagens](#vantagens)
      - [Desvantagens](#desvantagens)
  - [Conclusão](#conclusão)

## Introdução ao Padrão Redux

O Redux é um padrão/biblioteca para o gerenciamento de estado para frameworks javascript para frontend.

Seu principal objetivo é fornecer um modelo de gerenciamento de estado com maior controle, organização e consistência para o fluxo de dados da aplicação.

Esse modelo se baseia em 3 pilares:

- Store: é um obejto único que guarda todo o estado da aplicação em um único objeto.
- Actions: objetos que descrevem o que aconteceu, representam eventos ou intenções de mudança.
- Reducers: funções para determinar como o estado deve mudar com base na action recebida.

Esse padrão segue um fluxo unidirecional de dados, as atualizações de estado seguem um caminho definido e claro: uma action é disparada, o reducer calcula um novo estado e a store é atualizada.

Esse padrão garante transparência, previsibilidade e manutenabilidade.

## Quais problemas o Redux resolve

Quando as aplicações frontend se tornam mais interativas e robustas, e o Redux foi criado para centralizar o estado de uma aplicação, com isso padronizar como ele é alterado e garantir um fluxo de dados melhor estruturadi.

- Estado distribuido entre componentes: Em aplicações com muitos componentes reutilizaveis, manter os dados sincronizados entre eles sem uma estrutura central (Redux), normalmente gera inconsistências e códigos duplicados.
- Fluxo de dados dificil de rastrear: quando vários eventos podem alterar o estado em diferentes partes da aplicação, entender onde e o que gerou alguma mudança pode ser dificil.
- Problemas de previsibilidade: sem um padrão de modificação do estado, esse estado pode ser modificado de forma inesperada, o que dificulta os testes ou encontrar erros.
- Compartilhamento de dados entre diferentes partes da aplicação: dados globais da aplicação devem ser acessíveis em todas as partes da aplicação, como por exmeplo, em um eccomerce, o carrinho de compras deve ser mostrado em todas as páginas.
- Integração com APIs: gerenciar chamadas assíncronas, como requisições http para APIs sem impactar a lógica do estado.

## Quando usar o Redux

O Redux, por ser uma ferramenta muito poderosa, é indica a ser usada em aplicações onde gerenciamento de estado complexos são necessários.

As principais situações que se encaixa são:

- quando muitos componentes dependem do mesmo estado.
- a lógica de alteração do estado é complexa e/ou envolve efeitos colaterais assincronos.
- a aplicação precisa de ter o controle geral sobre como o estado muda.

## NgRx Signal Store - Padrão Redux em Aplicações Angular

### Introdução ao NgRx Signal Store

O NgRx Signal Store é uma ferramenta para gerenciamente do estado, que usa o padrão Redux no Angular. Ela aproveita nativamente da nova API reativa de signals do Angular para simplificar o gerenciamento de estado nas aplicações Angular.

O Signal Store na prática, permite definir um store (como um repositório de estado) como um service Angular, onde cada propriedade do estado armazenado se torna um Signal.

O que ele faz:

- Armazena o estado do objeto de forma centralizada na aplicação.
- Expõe esse estado usando Signals.
- Permite definir valores derivados com `computed()`.
- Fornece metodos para alterar o estado com segurança.

#### O que o NgRx Signal Store resolve

Estado espalhado em vários lugares: quando um estado é compartilhado entre componentes, se não tiver o estado centralizado, cada componente poode tratar esse estado da forma que quiser, e isso causa inconsistências. Com o Signal Store, esse estado fica centralizado e sua alteração definida, assim só se muda o estado da maneira que ele foi criado para mudar.

### Como usar o NgRx Signal Store?

De maneira simples é apenas: definir o estado da aplicação, criar métodos para alterar o estado, e o Angular cuida da parte de reagir automaticamente às mudanças desse estado.

Abaixo um exemplo simples de um contador usando o NgRx Signal Store, para entender como implementar.

#### Criar um store com estado inicial

Primeiro, devemos definir o estado armazenado, usando a função `signalStore()` e `withState()`. No exemplo, é criado um store com apenas a propriedade `count`, e esse valor é um Signal, e quando ele mudar, o Angular atualiza automaticamente qualquer componente que estiver usando esse valor.

```
import { signalStore, withState } from '@ngrx/signals';

export const CounterStore = signalStore(
  { providedIn: 'root' },
  withState({ count: 0 })
);
```

#### Adicionar valores derivados

É possivel adicionar valores que são derivados desse estado com a função `withComputed()`, com issso, o Store também terá `doubleCount` como "atributo", que sempre será o dobro do `count`.

```
import { computed } from '@angular/core';

withComputed(({ count }) => ({
  doubleCount: computed(() => count() * 2)
}))
```

#### Criar métodos para atualizar o estado

Definir as funções para alterar o estado da forma que for definida nessas funções com a função `withMethods()`. Esses métodos podem ser usados em qualquer componente para alterar o estado da forma correta.

```
import { patchState } from '@ngrx/signals';

withMethods((store) => ({
  increment: () => patchState(store, { count: store.count() + 1 }),
  decrement: () => patchState(store, { count: store.count() - 1 }),
  reset: () => patchState(store, { count: 0 })
}))
```

#### Executar ações automáticas

Para executar algo automaticamente quando por exemplo, o store for criado ou destruído com a função `withHooks()`. É interessante para exemplos onde é necessário carregar dados da API quando o store foi incializado.

```
withHooks({
  onInit: (store) => {
    console.log('Store inicializado com count:', store.count());
  },
  onDestroy: () => {
    console.log('Store foi destruído');
  }
})
```

#### Usar o store em algum componente.

```
import { Component, inject } from '@angular/core';
import { CounterStore } from './counter.store';

@Component({
  selector: 'app-counter',
  template: `
    <p>Valor atual: {{ count() }}</p>
    <p>Dobro do valor: {{ doubleCount() }}</p>
    <button (click)="store.increment()">+</button>
    <button (click)="store.decrement()">-</button>
    <button (click)="store.reset()">Reset</button>
  `
})
export class CounterComponent {
  store = inject(CounterStore);
  count = this.store.count;
  doubleCount = this.store.doubleCount;
}
```

### Quando usar o NgRx Signal Store dentro do Moka Web

Situações em que seria interessante usar o NgRx dentro do Moka Web:

1. Para gerenciar o estado de autenticação e dados do usuário, principalmente agora com a nova features de exibir o nome do usuário no header da aplicação.
2. Para gerenciar estilos e cores de acordo com o usuário, por exemplo, quando um usuário Itaú entrar, trazer via API suas cores, e usar nos compontes esses dados.
3. Para gerenciar os dados de filtros, como por exemplo, os filtros de Locais, que usam os mesmo dados, e podem ser gerenciados de forma centralizada na aplicação.
4. Com a adição das novas features de relatórios inteligentes, que é um componente reutilizavel, que normalmente usa dados de filtros de prédios seria uma boa usar o NgRx também.
5. Para alguns dashboards, como eventos e alarmes, que tem a paginação implementadas, pode ser uma boa usar o Signals Store para guardar e gerenciar os dados das tabelas, que podem mudar facilmente à uma interação do usuário, como mudar a página ou ordenar alguma coluna.

### Vantagens e Desvantagens do NgRx Signal Store

#### Vantagens

1. Menos códigos e configuração: só cria o estado, as actions e selectors uma vez no mesmo arquivo.
2. Reatividade automática: por usar Signals, tem a reatividade do angular automática na interface.
3. Facilidade para criar valores derivados com `computed()`: sem seletores manuais, é possível calcular totais, listas filtradas dentro da store.
4. Integração com ciclo de vida: usando o `withHooks()` é possível carregar dados ou iniciar ações automaticamente dentro do store.
5. Melhor performance em comparação aos observables: os Signals são atualizados apenas quando usados, e isso gera menos renderizações desnecessárias, como fazem os observables.

#### Desvantagens

1. Falta de estrutura robusta para lógicas assincronas complexas: em casos com muitas requisições encadeadas ou cancelamentos, o Signal Store pode não dar conta sozinho.
2. Sobrecarga em aplicações simples.

## Conclusão

O padrão Redux ajuda no gerenciamento de estado e usando o NgRx Signal Store, que aplica esse padrão de forma simples e reativa no angular, é possível melhorar ainda mais a aplicação frontend do Moka Web, usando em casos onde o estado é compartilhado ou precisa reagir rápido às mudanças.
