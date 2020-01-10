# br.vuejs.org

Este é o repositório oficial da tradução em português brasileiro do _site_ [vuejs.org](http://www.vuejs.org/). O _site_ foi construído com [hexo](http://hexo.io/) e o conteúdo escrito em formato _Markdown_ na pasta `src`. _Pull requests_ são bem-vindos!

## Desenvolvendo

```
$ npm install
$ npm start # servidor executando em http://localhost:4000
```

## Publicando

[![Netlify Status](https://api.netlify.com/api/v1/badges/67c69642-3235-4dc1-bafc-5eaac44e09c3/deploy-status)](https://app.netlify.com/sites/br-vuejs-org/deploys)

O _site_ é automaticamente publicado quando _commits_ chegam em `master`, via [Netlify](https://www.netlify.com/).

O processo é disparado somente por um grupo seleto de contribuidores que podem atualizar o _branch_ `master`, a fim de manter a organização. Você pode colaborar enviando _pull requests_ para o _branch_ `dev` e, depois de revisados e aceitos, serão mesclados em `master` pela equipe, para que o _deploy_ automático ocorra.

## Como colaborar?

Problemas pontuais nas traduções já realizadas, como erros de grafia ou frases confusas, podem ser reportados apenas enviando _issues_ neste repositório. Deixe claro sobre qual página se refere o problema e, preferencialmente, ofereça uma sugestão para a correção desejada.

Se você se sente à vontade para traduzir/revisar conteúdos por conta, os passos são:

- Verifique o que está em aberto nas _issues_,
  - Se necessário, crie uma _issue_ neste _repo_ para informar o que está fazendo;
- Faça um _fork_ do _branch_ `dev` deste _repo_ para sua própria conta,
  - Como o _branch_ `dev` é utilizado para sincronizar as atualizações do [repositório original em inglês](https://github.com/vuejs/vuejs.org), muitas vezes existem conflitos de _merge_ para corrigir! Se estiver em dúvida se há onde ajudar no momento, procure por conflitos de _merge_ no _branch_ `dev`.
- Faça as traduções/revisões nos arquivos que se propôs, usando o editor que quiser;
- Ao finalizar, faça um _pull request_ para `dev` **com a descrição do _commit_ em inglês**;
- Não se esqueça de obter as alterações mais recentes antes de recomeçar o processo.

Caso não consiga terminar por completo algum arquivo, mas queira enviar o trabalho parcial, deixe isso claro na _issue_ de seu trabalho após a submissão, para informar quais problemas persistem no arquivo enviado.

## Considerações para padronização

Para uma documentação padronizada, seguem recomendações do que fazer:

- Traduzir os comentários dos códigos-fonte;
- Traduzir textos informativos dos códigos-fonte, por exemplo: `<div id="level-1">Nível 1</div>`;
- Seguir maiúsculas e minúsculas conforme o original em inglês sempre que possível;
- Utilizar alguma extensão para ortografia e gramática, para evitar que erros deste tipo;
- Usar _itálico_ em expressões sem tradução (por exemplo, _view layer_);
- Quando for submeter, sempre escreva os comentários do _commit_ em inglês.

E algumas recomendações do que **não** fazer:

- Não traduzir nomes de variáveis, métodos, dados, _ids_, classes etc. nos códigos-fonte.
- Não grafar em itálico tipos de dados ou valores primitivos;
- Não faça _pull request_ diretamente para `vuejs/master`, a tradução deve passar por esse repositório.
- Não faça _pull_ diretamente de `vuejs/master`, apenas deste repositório para seu _fork_ pessoal.

### Traduções padronizadas

Alguns termos recorrentes no guia devem ser traduzidos sempre da mesma maneira, desde que a tradução não atrapalhe o contexto da frase:

- *Bundle* = Pacote
- *Debug* = Depuração
- *Handling* = Manipulação
- *Event Listening* = Escuta de Eventos
- *Render Function* = Função de Renderização
- *Computed Properties* = Dados Computados
- *Single-File Components* = Componentes Single-File
- *Custom Elements* = Elementos Personalizados
- *Performance* = Desempenho
- *Watchers* = Observadores
- *Under the hood* = Em seu interior
- *Server-Side Rendering* = Renderização no Lado do Servidor
- *Hooks* = Gatilhos
- *Bind* = Vínculo (ou Interligação)

### Termos não traduzidos

Atualmente recomenda-se que estes termos sejam mantidos em inglês:

- _getter_
- _setter_
- _standalone_
- _runtime_
- _store_
- _scaffolding_
- _loader_
- _loop_
- _template_
- _wrapper_
- _hot-reload_
- true
- false
- Number
- String
- Boolean
- Array

## Como se envolver com a comunidade?

Se você não chegou até aqui para colaborar, mas sim para pedir ajuda sobre alguma coisa da documentação oficial do Vue, a Comunidade Brasileira criou [uma lista](https://github.com/vuejs-br/comunidades) com todos locais oficialmente reconhecidos. Saiba que a comunidade Vue é muito receptiva (no Brasil e no mundo), então não se acanhe para se envolver mais.

## Estou com dúvidas sobre o Vue, onde perguntar?

Nós temos o nosso fórum em português sobre o Vue, basta [clicar aqui](https://github.com/vuejs-br/forum/). Este fórum está no próprio GitHub, e usamos o gerenciamento de _issues_ como um sistema de fórum. Também temos um [chat no Slack](https://vuejs-brasil.slack.com/) destinado especificamente à comunidade brasileira, caso você queira uma interação mais direta com outros adeptos do Vue.
