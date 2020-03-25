---
title: Reatividade em Profundidade
type: guide
order: 601
---

Agora é hora de um mergulho mais profundo! Uma das características mais distintas do Vue é seu sistema de reatividade não obstrusivo. Modelos de dados são simplesmente objetos JavaScript puros. Quando você os modifica, a camada visual se atualiza. Isto torna o gerenciamento de estado simples e intuitivo, mas também é importante entender como funciona para evitar algumas pegadinhas. Nesta seção, estaremos nos aprofundando em alguns detalhes de baixo-nível do sistema de reatividade do Vue.

<div class="vue-mastery"><a href="https://www.vuemastery.com/courses/advanced-components/build-a-reactivity-system" target="_blank" rel="sponsored noopener" title="Reatividade no Vue">Assista a uma lição em vídeo gratuita na Vue Mastery</a></div>

## Como as Alterações são Monitoradas

Quando passamos um objeto JavaScript puro à instância Vue através da opção `data`, Vue itera através de todas as suas propriedades e as converte em [getter/setters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Working_with_Objects#Defining_getters_and_setters) através do método [`Object.defineProperty`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Working_with_Objects#Defining_getters_and_setters). Este é um recurso exclusivo do ES5 e impossível de simular em versões anteriores, sendo este o motivo do Vue não suportar IE8 e anteriores.

Os _getter_/_setters_ são invisíveis para o usuário, mas internamente habilitam o Vue a executar o rastreamento de dependências e notificações de alteração quando propriedades são acessadas ou modificadas. Note que o _console_ dos navegadores formata _getter_/_setters_ de forma diferente quando registram alterações em objetos de dados, então é provável que você prefira instalar o [vue-devtools](https://github.com/vuejs/vue-devtools) para uma interface de inspeção mais amigável.

Cada instância de componente tem um **observador** de instância correspondente (em inglês, um _instance watcher_), o qual registra qualquer propriedade "tocada" durante a renderização do componente e suas dependências. Mais tarde, quando o _setter_ de uma dependência é acionado, este notifica o observador, o qual causa a re-renderização do componente.

![Ciclo de Reatividade](/images/data.png)

## Limitações na Detecção de Alterações

Devido às limitações do JavaScript, existem tipos de mudanças que o Vue **não pode** detectar. No entanto, existem maneiras de contornar isso para preservar a reatividade.

### Para Objetos

Vue **não pode** detectar adição ou remoção de propriedades. Uma vez que o processo de conversão em _getter_/_setters_ ocorre durante a inicialização da instância, propriedades já devem estar presentes no objeto `data` para que o Vue o torne reativo. Por exemplo:

``` js
var vm = new Vue({
  data: {
    a: 1
  }
})
// `vm.a` é reativo

vm.b = 2
// `vm.b` NÃO é reativo
```

Vue não permite dinamicamente adicionar novas propriedades reativas em nível raiz para uma instância já criada. Entretanto, é possível adicionar propriedades reativas a objetos internos usando o método `Vue.set(object, propertyName, value)`. Por exemplo, dado o código:

``` js
var vm = new Vue({
  data: {
    userProfile: {
      name: 'Zé Ninguém'
    }
  }
})
```

Você poderia adicionar uma nova propriedade `age` ao objeto interno `userProfile` com:

``` js
Vue.set(vm.userProfile, 'age', 30)
```

Também é possível usar o método de instância [`vm.$set`](../api/#vm-set), um atalho para o global `Vue.set`:

``` js
vm.$set(vm.userProfile, 'age', 27)
```

Às vezes, você pode querer adicionar várias novas propriedades a um objeto existente, utilizando por exemplo `Object.assign()` ou `_.extend()`. No entanto, novas propriedades adicionadas posteriormente não vão disparar mudanças. Nestes casos, você pode criar um objeto novo com propriedades mescladas do objeto original e das novidades:

``` js
vm.userProfile = Object.assign({}, vm.userProfile, {
  age: 31,
  favoriteColor: 'Verde Vue'
})
```

### Para Arrays

Vue não pode detectar as seguintes mudanças em um Array:

1. Quando se define diretamente um item em um índice: `vm.items[indexOfItem] = newValue`
2. Quando se modifica diretamente o tamanho do Array: `vm.items.length = newLength`

Por exemplo:

``` js
var vm = new Vue({
  data: {
    items: ['a', 'b', 'c']
  }
})
vm.items[1] = 'x' // NÃO é reativo
vm.items.length = 2 // NÃO é reativo
```

Para contornar a limitação 1, há duas alternativas ao `vm.items[indexOfItem] = newValue` que causam atualização de estado no sistema de reatividade:

``` js
// Vue.set
Vue.set(vm.items, indexOfItem, newValue)
```
``` js
// Array.prototype.splice
vm.items.splice(indexOfItem, 1, newValue)
```

Também é possível usar o método de instância [`vm.$set`](https://vuejs.org/v2/api/#vm-set), um atalho para o global `Vue.set`:

``` js
vm.$set(vm.items, indexOfItem, newValue)
```

Para lidar com a limitação 2, é possível usar `splice`:

``` js
vm.items.splice(newLength)
```

## Declarando Propriedades Reativas

Como Vue não permite adicionar dinamicamente propriedades reativas ao nível raiz, você deve inicializar instâncias Vue declarando todos os dados com valores iniciais, mesmo que usando valores vazios:

``` js
var vm = new Vue({
  data: {
    // declare `message` com um valor vazio
    message: ''
  },
  template: '<div>{{ message }}</div>'
})
// aí pode setar `message` depois
vm.message = 'Hello!'
```

Se a propriedade `message` não estivesse declarada em `data`, Vue iria avisar que a função de renderização tentou acessar uma propriedade que não existe.

Há razões técnicas para essa restrição - elimina uma gama de casos extremos no sistema de rastreamento de dependências, além de permitir a instâncias Vue trabalhar melhor com sistemas de verificação de tipos. Mas há também uma consideração importante em termos de manutenibilidade: o objeto `data` é como um esquema para o estado de seu componente. Declarar todas as propriedades reativas antecipadamente torna o código mais fácil de entender quando revisitado posteriormente ou lido por outro desenvolvedor.

## Fila de Atualização Assíncrona

Caso não tenha reparado ainda, Vue executa atualizações do DOM de forma **assíncrona**. Sempre que uma alteração de dados for observada, será enfileirada juntamente com todas as alterações de dados que ocorrerem em um mesmo laço de eventos. Se um mesmo observador for acionado várias vezes, ele será empurrado para a fila apenas uma vez. Esta ação é importante para evitar cálculos duplicados e manipulações DOM desnecessárias. Então, no próximo "tique-taque" do laço eventos, Vue libera a fila e executa o trabalho atual. Internamente, Vue tenta utilizar os nativos `Promise.then`, `MutationObserver` e `setImmediate` para o enfileiramento assíncrono e retrocede para `setTimeout(fn, 0)` se necessário.

Por exemplo, quando você define `vm.someData = 'novo valor'`, o componente não será re-renderizado imediatamente. Ele será atualizado no próximo "tique-taque" em que a fila for liberada. Raramente precisamos nos preocupar com isso, mas a situação pode se complicar se precisar fazer algo que depende do estado do DOM após a atualização. Embora o Vue encoraje os desenvolvedores a pensarem de forma dirigida aos dados, evitando tocar no DOM diretamente, em alguns casos pode ser necessário sujar as mãos. Para aguardar até que o Vue tenha terminado de atualizar o DOM após alterações de dados, utilize o `Vue.nextTick(callback)` imediatamente após a alteração dos dados. A função _callback_ será executada após o término da atualização do DOM. Por exemplo:

``` html
<div id="example">{{ message }}</div>
```

``` js
var vm = new Vue({
  el: '#example',
  data: {
    message: '123'
  }
})
vm.message = 'novo valor' // modifique o dado
vm.$el.textContent === 'novo valor' // => false
Vue.nextTick(function () {
  vm.$el.textContent === 'novo valor' // => true
})
```

Há também o método de instância `vm.$nextTick()`, especialmente útil no interior de componentes pois não depende da variável global `Vue`, sendo que o contexto de `this` em seu _callback_ é automaticamente vinculado à instância Vue atual:

``` js
Vue.component('example', {
  template: '<span>{{ message }}</span>',
  data: function () {
    return {
      message: 'não atualizado'
    }
  },
  methods: {
    updateMessage: function () {
      this.message = 'atualizado'
      console.log(this.$el.textContent) // => 'não atualizado'
      this.$nextTick(function () {
        console.log(this.$el.textContent) // => 'atualizado'
      })
    }
  }
})
```

Como `$nextTick()` retorna uma promessa, você pode conseguir o mesmo que o acima, usando a nova sintaxe [async/await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) do ES2017:

``` js
  methods: {
    updateMessage: async function () {
      this.message = 'updated'
      console.log(this.$el.textContent) // => 'not updated'
      await this.$nextTick()
      console.log(this.$el.textContent) // => 'updated'
    }
  }
```
