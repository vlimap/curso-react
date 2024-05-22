
# Aula 01: Introdução ao React

## O que é React?
React é uma biblioteca JavaScript de código aberto, mantida pelo Facebook, para a construção de interfaces de usuário (UI). Ela é usada principalmente para desenvolver aplicações web de página única (single-page applications) e permite criar interfaces de usuário de forma eficiente e flexível por meio do uso de componentes.

### Componentes
A ideia central do React é o uso de componentes. Um componente em React é uma pequena, auto-contida e reutilizável peça de código que define a aparência e o comportamento de uma parte da interface do usuário. Componentes podem ser aninhados dentro de outros componentes para construir interfaces complexas a partir de blocos menores e mais simples. Isso promove a reutilização de código e facilita a manutenção e a escalabilidade da aplicação.

### Virtual DOM
Outro conceito fundamental no React é o Virtual DOM. O Document Object Model (DOM) é uma interface de programação para documentos HTML e XML que representa a estrutura da página e permite a manipulação de seu conteúdo e estilo. Manipular o DOM diretamente pode ser lento e ineficiente, especialmente quando há muitas mudanças frequentes na interface do usuário. O React aborda este problema com o Virtual DOM, que é uma representação leve e in-memory do DOM real. Quando o estado de um componente muda, o Virt...

### Declarativo
O React é declarativo, o que significa que você descreve como a interface deve parecer com base no estado atual dos dados, e o React cuida de atualizar a UI para refletir esses dados. Isso é diferente de uma abordagem imperativa, onde você precisa escrever código passo a passo para manipular o DOM diretamente.

### JSX
JSX é uma extensão de sintaxe para JavaScript que parece com HTML. Ele é usado com o React para descrever o que a interface do usuário deve parecer. Embora o JSX não seja necessário para usar o React, ele facilita a visualização da estrutura da UI e é amplamente adotado na comunidade React.

### Ecossistema e Comunidade
Além da biblioteca principal, o React possui um vasto ecossistema de ferramentas e bibliotecas auxiliares, como React Router para gerenciamento de rotas, Redux para gerenciamento de estado global e muitos outros. A comunidade ao redor do React é grande e ativa, com muitos recursos, tutoriais e suporte disponíveis para desenvolvedores.

## História e Evolução do React

### Origens e Criação
O React foi inicialmente desenvolvido por Jordan Walke, um engenheiro de software do Facebook, em 2011. A ideia surgiu da necessidade de criar interfaces de usuário mais eficientes e fáceis de manter. Antes do React, o Facebook enfrentava desafios significativos com a manutenção de sua base de código front-end, que se tornava cada vez mais complexa com o crescimento da plataforma.

### Primeira Implementação
A primeira implementação do React foi usada internamente no Facebook em sua funcionalidade de feed de notícias. A abordagem inovadora do React para manipulação do DOM e a introdução do conceito de componentes ajudou a simplificar o desenvolvimento e a manutenção da interface de usuário.

### Lançamento Público
Em 2013, o React foi lançado como um projeto de código aberto na conferência de desenvolvedores JavaScript, JSConf US. Esse lançamento marcou o início da sua adoção pela comunidade de desenvolvedores e o começo de sua evolução como uma das bibliotecas JavaScript mais populares para a construção de interfaces de usuário.

### React Native
Em 2015, a equipe do React lançou o React Native, uma extensão do React para o desenvolvimento de aplicações móveis. O React Native permite que desenvolvedores criem aplicativos nativos para iOS e Android usando a mesma abordagem baseada em componentes do React. Esse desenvolvimento ampliou significativamente o alcance e a utilidade do React, permitindo a criação de aplicativos multiplataforma com uma base de código compartilhada.

### Evolução Contínua e Lançamento de Novos Recursos
Desde seu lançamento, o React passou por várias atualizações importantes, cada uma introduzindo novos recursos e melhorias:
- **React 0.14 (2015)**: Introdução da separação entre componentes baseados em classes e funções, e melhorias na interoperabilidade com outras bibliotecas.
- **React 15 (2016)**: Foco em melhorias de performance e correção de bugs.
- **React 16 (2017)**: Introdução do Fiber, uma reescrita interna do mecanismo de renderização que melhorou a capacidade do React de lidar com atualizações complexas de UI. Também trouxe suporte para o novo método createPortal e melhor tratamento de erros com o componentDidCatch.
- **React 16.3 (2018)**: Introdução de novos métodos de ciclo de vida, como getDerivedStateFromProps, e do Context API aprimorado para melhor gerenciamento de estado global.
- **React 16.8 (2019)**: Introdução dos Hooks, uma das maiores mudanças na forma como os componentes funcionais lidam com estado e outros recursos do React. Os Hooks permitiram que componentes funcionais utilizassem estado e efeitos colaterais, tornando-os mais poderosos e flexíveis.

### Atualizações Recentes e Futuro
- **React 17 (2020)**: Foco em melhorias de compatibilidade e atualização progressiva, facilitando a atualização de grandes aplicações React.
- **React 18 (2022)**: Introdução do Concurrent Mode e da nova API de renderização Server-Side, que trouxe melhorias significativas de performance e experiência do usuário.

### Comunidade e Ecossistema
A comunidade do React cresceu exponencialmente desde seu lançamento. Hoje, é uma das bibliotecas mais usadas no desenvolvimento web, com um vasto ecossistema de ferramentas e bibliotecas complementares. A conferência React Conf, eventos comunitários e inúmeros recursos de aprendizado online ajudam a manter a comunidade ativa e engajada.

## Configuração do Ambiente de Desenvolvimento
Para iniciar o desenvolvimento com React, é essencial configurar corretamente o ambiente de desenvolvimento. Esse processo inclui a instalação de ferramentas básicas, configuração do editor de código, e criação de um novo projeto React. Vamos detalhar cada um desses passos a seguir.

### 1. Instalação do Node.js e npm
O Node.js é uma plataforma JavaScript que permite executar JavaScript fora do navegador. O npm (Node Package Manager) é um gerenciador de pacotes que acompanha o Node.js e é utilizado para instalar bibliotecas e dependências do projeto.
- **Passo 1**: Baixe e instale o Node.js a partir do site oficial: [Node.js](https://nodejs.org/).
- **Passo 2**: Verifique se a instalação foi bem-sucedida abrindo um terminal e digitando os comandos:

```sh
node -v
npm -v
```

Esses comandos devem retornar a versão instalada do Node.js e do npm, respectivamente.

### 2. Escolha do Editor de Código
O Visual Studio Code (VS Code) é um dos editores mais populares para desenvolvimento em JavaScript e React. Ele oferece suporte a uma ampla gama de extensões que facilitam o desenvolvimento.
- **Passo 1**: Baixe e instale o Visual Studio Code a partir do site oficial: [VS Code](https://code.visualstudio.com/).
- **Passo 2**: Instale as extensões recomendadas para desenvolvimento em React:
    - **ESLint**: Para linting do código JavaScript.
    - **Prettier**: Para formatação de código.
    - **vscode-icons**: Para ícones de arquivos.
    - **Reactjs code snippets**: Para snippets de código React.

### 3. Criando o Primeiro Projeto React com Create React App
O Create React App é uma ferramenta oficial do React que configura rapidamente um novo projeto React com uma configuração moderna de build. Ele cuida da configuração inicial, permitindo que você se concentre no código.
- **Passo 1**: Abra o terminal e execute o comando para instalar o Create React App globalmente:

```sh
npm install -g create-react-app
```

- **Passo 2**: Crie um novo projeto React executando o comando:

```sh
npx create-react-app meu-primeiro-projeto
```

Esse comando cria uma nova pasta chamada `meu-primeiro-projeto` e configura automaticamente a estrutura do projeto.

- **Passo 3**: Navegue até a pasta do projeto e inicie o servidor de desenvolvimento:

```sh
cd meu-primeiro-projeto
npm start
```

O comando `npm start` inicia o servidor de desenvolvimento e abre o projeto no navegador padrão. Você deve ver a página de boas-vindas do Create React App.

### 4. Estrutura de um Projeto React
A estrutura de um projeto criado com Create React App é organizada da seguinte forma:

```
meu-primeiro-projeto/
├── node_modules/
├── public/
│   ├── index.html
│   └── favicon.ico
├── src/
│   ├── App.js
│   ├── App.css
│   ├── index.js
│   └── index.css
├── .gitignore
├── package.json
└── README.md
```

### Descrição dos Diretórios e Arquivos

#### `node_modules/`
Este diretório contém todas as dependências e pacotes instalados via npm ou yarn. Ele é gerado automaticamente quando você executa o comando `npm install` ou `yarn install`. Você não precisa modificar nada dentro deste diretório, pois ele é gerenciado pelo gerenciador de pacotes.

#### `public/`
Este diretório contém arquivos públicos que serão servidos diretamente pelo servidor web. Os principais arquivos aqui são:

- **`index.html`**: Este é o arquivo HTML principal. Ele contém um contêiner `<div>` com o id `root`, onde a aplicação React será montada.
- **`favicon.ico`**: Este é o ícone que aparece na aba do navegador quando a aplicação está aberta.

#### `src/`
Este diretório contém todo o código-fonte da aplicação React. Os principais arquivos aqui são:

- **`App.js`**: Componente principal da aplicação. Normalmente, você começa a desenvolver sua aplicação aqui.
- **`App.css`**: Arquivo CSS para estilizar o componente `App`.
- **`index.js`**: Ponto de entrada da aplicação. Este arquivo é responsável por renderizar o componente `App` dentro do contêiner `<div>` com o id `root` no `index.html`.
- **`index.css`**: Arquivo CSS global para estilizações gerais da aplicação.

#### `.gitignore`
Arquivo que especifica quais arquivos e diretórios devem ser ignorados pelo Git. Normalmente, este arquivo inclui `node_modules/`, arquivos de build e outros arquivos temporários.

#### `package.json`
Arquivo de configuração que contém metadados sobre o projeto, como o nome, versão, dependências, scripts, etc. Este arquivo é crucial para gerenciar as dependências e os scripts do projeto.


# Mas... 
## Antes de iniciar, vamos revisar 
## JavaScript: Funções e Arrow Functions

## Introdução
JavaScript é uma linguagem de programação versátil usada para criar interatividade em páginas web. Um dos conceitos fundamentais em JavaScript é o uso de funções. 
Instale extensões que podem ajudar a escrever e executar código JavaScript de forma mais eficiente.

- **ESLint:** Ajuda a identificar e corrigir problemas no código JavaScript.
- **Prettier - Code Formatter:** Para formatar seu código automaticamente.
- **JavaScript (ES6) code snippets:** Fornece snippets úteis para JavaScript.

## Funções Tradicionais

### Declaração de Função
Uma função em JavaScript pode ser declarada usando a palavra-chave `function`. Abaixo está um exemplo de uma função simples que adiciona dois números:

```javascript
function soma(a, b) {
    return a + b;
}
```

### Expressão de Função
Uma função também pode ser definida como uma expressão e atribuída a uma variável:

```javascript
const soma = function(a, b) {
    return a + b;
};
```

### Funções de Ordem Superior
Funções podem ser passadas como argumentos para outras funções. Estas são chamadas de funções de ordem superior.

```javascript
function soma(a, b) {
    return a + b;
}

function executarOperacao(a, b, operacao) {
    return operacao(a, b);
}

const resultado = executarOperacao(5, 3, soma);
console.log(resultado);
// saida: 8
```

### Funções Aninhadas
Funções podem ser aninhadas dentro de outras funções. As funções internas têm acesso às variáveis das funções externas.

```javascript
function externa() {
    const mensagem = "Olá, ";

    function interna(nome) {
        return mensagem + nome;
    }

    return interna("Mundo");
}

console.log(externa()); // Olá, Mundo
```

## Arrow Functions

### Sintaxe Básica
Arrow functions foram introduzidas no ES6 (ECMAScript 2015) e oferecem uma sintaxe mais curta para escrever funções. A sintaxe básica é a seguinte:

```javascript
const soma = (a, b) => {
    return a + b;
};
```

Para funções de uma única linha, as chaves e a palavra-chave `return` podem ser omitidas:

```javascript
const soma = (a, b) => a + b;
```

### Contexto `this`
Arrow functions não têm seu próprio `this` context; em vez disso, elas herdam o `this` do escopo envolvente. Isso é útil em métodos de objetos e em callbacks.

```javascript
function Timer() {
    this.segundos = 0;

    setInterval(() => {
        this.segundos++;
        console.log(this.segundos);
    }, 1000);
}

const meuTimer = new Timer();
```

### Sem `arguments`
Arrow functions não possuem o objeto `arguments`, que é uma característica das funções tradicionais. Se você precisar do objeto `arguments`, pode usar uma função tradicional ou o operador rest.
`arguments` está disponível apenas em funções tradicionais.
`...args` está disponível em todas as funções, incluindo arrow functions.

### Operador de Rest (...args)
O operador de rest (...) foi introduzido no ES6 (ECMAScript 2015) e permite que uma função receba um número indefinido de argumentos como um array. Isso é especialmente útil em arrow functions, onde arguments não está disponível.

```javascript
const funcaoTradicional = function() {
    console.log(arguments);
};

const arrowFunction = (...args) => {
    console.log(args);
};

funcaoTradicional(1, 2, 3); // [1, 2, 3]
arrowFunction(1, 2, 3); // [1, 2, 3]
```

### Retorno Implícito
Se a função retorna um objeto, você deve envolvê-lo entre parênteses para evitar ambiguidades de sintaxe.

```javascript
const criarPessoa = (nome, idade) => ({ nome, idade });

console.log(criarPessoa("Alice", 25));
// { nome: 'Alice', idade: 25 }
```

## Conclusão
Funções são uma parte essencial de JavaScript, permitindo a modularização e reutilização de código. As arrow functions introduzem uma sintaxe mais curta e um comportamento de escopo `this` que pode simplificar o código, especialmente em callbacks. Compreender quando usar funções tradicionais versus arrow functions é crucial para escrever código JavaScript eficaz e eficiente.
