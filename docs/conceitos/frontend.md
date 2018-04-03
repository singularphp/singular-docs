O Singular Framework foi projetado para ser um framework Ajax de baixo acoplamento entre as camadas de frontend e 
backend, ou seja, a interface dos módulos que serão construídos na aplicação, é definida totalmente no frontend, 
sem nenhuma vinculação com a renderização no backend. Enquanto no backend, o <destak>Silex</destak> é utilizado como base do framework, 
no frontend por sua vez é o <destak>AngularJS</destak> quem fornece a estrutura de trabalho para a construção da 
interface de usuário.

## Views renderizadas pelo Twig

Em um projeto criado a partir do Singular Project, há inicialmente, apenas 2 views que são renderizadas pelo backend. 
Através do <destak>Twig</destak>. Entretanto essas views não possuem código de interface de usuário, elas são apenas 
páginas HTML que ativam uma aplicação AngularJS. Essas duas views são <destak>auth.html</destak> e <destak>secure.html</destak>,
ambas estão localizadas no diretório <destak>views</destak> na raiz do projeto. 

A renderização dessas views ocorre através do controlador <destak>Sessao\Controller\Main</destak> através dos métodos 
<detak>showSecure</destak> e <destak>showAuth</destak>. Eles utilizam o provedor de serviços do <destak>Twig</destak> 
para recuperar o template das páginas html processá-lo e renderizá-los no navegador. 

Algumas vezes, pode ser necessário criar algumas rodas adicionais para serem renderizadas pelo backend, por exemplo, 
suponha que a aplicação que você está construindo terá uma tela que será exibida em um monitor e que para o recurso 
acessado, ela não precise que o usuário esteja autenticado. Então, seria necessário criar uma outra view, para isso, 
crie o arquivo do template da view dentro do diretório views. Por exemplo: <destak>monitor.html</destak>. Essa view irá 
fazer referência a uma aplicação própria do AngularJS. 

Após criar o seu template no diretório views, o próximo passo é criar um novo método que será responsável por renderizar a view no 
controlador <destak>Main</destak> no pacote <destak>Sessao</destak>. 

```php
...
    /**
     * Renderiza a interface que será exibida no monitor.
     * 
     * @param Request $request
     * 
     * @return JsonResponse
     */
    public function showMonitor(Request $request)
    {
        $app = $this->app;

        // rederiza a página de autenticação
        return $app['twig']->render("monitor.html");
    }
...
```
O método render do serviço <destak>Twig</destak> pode ainda receber parâmetros adicionais que serão interpolados ao 
template, para maiores informações, consulte a documentação do [Provedor de serviços do Twig](https://silex.symfony.com/doc/2.0/providers/twig.html). 

Para que a view possa ser renderizada, ainda é necessário realizar uma última configuração que irá criar uma rota 
get para o método de renderização da view. Para isso, adicione uma entrada para o mapeamento da rota no método connect 
da classe <destak>SessaoServiceProvider</destak> no pacote <destak>Sessao</destak>.

```php
    /**
     * Registra as rotas de inicialização da aplicação.
     *
     * @param Application $app
     */
    public function connect(Application $app)
    {
        // define o serviço do controlador principal
        $app['sessao.controller.main'] = function() use ($app) {
            return new Main($app, $app['singular.packs'][$this->pack]);
        };

        // define a rota de acesso autenticado da aplicação
        $app->get('/secure.app', 'sessao.controller.main:showSecure')->bind('secure');

        // define a rota de visualização da interface de registro e autenticação do sistema
        $app->get('/auth.app', 'sessao.controller.main:showAuth')->bind('auth');

        // define a rota default do sistema
        $app->get('/', 'sessao.controller.main:index');

        // define a rota para a view do monitor
        $app->get('/monitor', 'sessao.controller.main:showMonitor');
    }
```

Como se trata de uma definição de rota convencional do Silex, você pode utilizar qualquer funcionalidade ou recurso 
disponíveis para isso, como por exemplo, utilizar rotas dinâmicas com parâmetros, validação de parâmetros, etc. 

Pronto, feito isso, você poderá agora acessar a view diretamente através do seu navegador.

Apesar de termos definido a nova rota no pacote <destak>Sessao</destak>, qualquer pacote pode ser utilizado para a 
definição dessa rota.

## Módulos

Se no backend as funcionalidades são agrupadas em pacotes, no frontend elas são agrupadas por módulos, esses são módulos
do AngularJS, esses módulos podem ser dependentes de outros módulos menores ou submódulos, se assim quiser chamá-los. 

Os módulos de frontend são armazenados dentro do diretório <destak>web/src</destak>. Uma aplicação criada a partir do 
Singular Project já vem com 3 módulos principais:

!!!info "Módulos" 
    + <destak>login</destak> Módulo que define a aplicação utilizada na view de autenticação do sistema. Ele é instanciado 
    na renderização do template <destak>auth.html</destak> no diretório <destak>views</destak>. Ele é responsável por 
    fornecer funções para autenticação do usuário;

    + <destak>secure</destak> Módulo que define a aplicação utilizada na view segura do sistema. Ele é instanciado na 
    renderização do template <destak>secure.html</destak>. Geralmente novos módulos serão adicionados como dependência 
    para este módulo, de forma que, quando o usuário logar, seus módulos estarão disponíveis na aplicação.
 
    + <destak>ui</destak> Módulo padrão que define serviços, controladores, views e diretivas utilizadas na interface do 
    sistema.
 

### A estrutura de um módulo

Um módulo de frontend é composto por um script de definição e vários scripts de (controladores, diretivas, serviços, 
views e filtros) que são organizados dentro de diretórios. A idéia é seguir a definição do [Jhon Papa]() para melhor 
organização do código. 

Ao abrir o diretório de um módulo geralmente tem-se a seguinte estrutura:

!!!info "Arquitetura do módulo"
    + <span>controllers</span> Diretório onde são armazenados os scripts dos controladores AngularJS do módulo. 
    Cada view deverá ter seu próprio controlador. Podem ser criados tantos controladores quanto necessários e quanto 
    menor o controlador mais organizado e bem estruturado será o seu código
    
    + <span>services</span> Diretório onde são armazenados os scripts dos serviços AngularJS do módulo. Podem ser 
    criados tantos serviços quanto se fizer necessário. Toda comunicação com o backend se dará por meio dos serviços 
    que implementam o [Service Store]
    
    + <span>directives (opcional)</span> Diretório onde podem ser armazenadas diretivas específicas do módulo em questão
    
    + <span>filters (opcional)</span> Diretório onde podem ser armazenados filtros específicos do módulo em questão
    
    + <span>modulo.js</span> Script que define o módulo AngularJS. É nessa definição que são criados os states para as 
    views do módulo. Vale lembrar que se o módulo se chamar login.registro, o nome do script será <span>registro.js</span>.

### Criando um novo módulo

Para criar um novo módulo, basta utilizar o Singular Cli:

```shell
./singular frontend:create-module app.teste
```
Ao executar este comando, será criado um novo módulo no diretório <destak>web/src</destak> chamado <destak>app.teste</destak>. 
No entanto, frequentemente, você precisará criar um submódulo, ou seja, um módulo que fica dentro de outro módulo como 
uma dependência. Para isso, o Singular utiliza o padrão de namespaces para nomeação dos módulos. No caso, o módulo 
<destak>app.teste</destak> está dentro do namespace <destak>app</destak> que representa a nossa aplicação. 

Suponha que seja necessário criar um submódulo <destak>registro</destak> dentro do módulo de login, para isso, no 
Singular Cli utilizaríamos:

```php
./singular frontend:create-module login.registro --dir=login
```

Ao executar este comando, um novo diretório do módulo será criado dentro do diretório do módulo <destak>login</destak>. 
No nome <destak>login.registro</destak> o namespace é facilmente identificado pelo <destak>login</destak>. Por padrão, 
quando você cria um submódulo com o Singular Cli, o mesmo já é registrado como uma dependência no módulo pai.

### Definindo states para o módulo

O Singular Project utiliza o ui-router como mecanismo de roteamento padrão no frontend. Cada módulo define seus 
próprios states. Por este motivo, todas as rotas são definidas através de states, convencionalmente como é feito no 
ui-router, dentro da função config do módulo:

```javascript
...
        $stateProvider.state('usuario.cadastro', {
            url: '/usuario/cadastro',
            acl: 'f-cadastro-create',
            controller: 'cadastro.CreateCtrl',
            templateUrl: getView('create')
        })
...
```
Mas ao observar o bloco de código, você deve ter percebido que existe um parâmetro que não é comum ao ui-router. 
O parâmetro <destak>acl</destak> faz parte do Singular e ele é utilizado para restringir o acesso de um usuário ao state. 
Ao utilizar esse parâmetro, você garante que apenas usuários que possuam no seu perfil o acesso à funcionalidade 
<destak>f-cadastro-create</destak> poderão acessá-lo.

## Controladores

Assim como na definição do AngularJS, a principal funcão de um controlador é integrar o modelo à view. Cada módulo tem seu próprio conjunto de controladores, e eles se encontram no diretório __controllers__ do módulo. 

### Criando um novo controlador

Para criar um novo controlador, basta utilizar o Singular Cli:

```shell
./singular frontend:create-controller namespacemodulo.MeuControlador modulo diretorio/do/submodulo --tipo=list
```

!!! Onde
    + <span>namespacemodulo.MeuControlador</span> deve ser substituído pelo nome do seu controlador com o namespace.<br> 
    Por exemplo: usuario.ListCtrl;
    + <span>modulo</span> deve ser substituído pelo nome com namepsace completo do módulo onde o controlador será criado.<br> 
    Por exemplo: cadastro.usuario;
    + <span>diretorio/do/modulo</span> deve ser substituído pelo caminho completo do diretório do módulo a partir do 
    diretório web/src;
    + <span>tipo</span> se omitido, o valor default é normal.<br> 
    Pode assumir os valores (list, filter, modal, create, edit, normal);

## Service Stores

Service Stores são serviços angular que são criados a partir de uma StoreFactory. Eles disponibilizam métodos CRUD e de 
comunicação com o backend encapsulando as chamadas do serviço $http.

### Criando um novo service store

Para criar um novo service store, basta utilizar o Singular Cli:

```shell
./singular frontend:create-store NomedoStore modulo pack controlador dir
```

!!! Onde
    + <span>NomedoStore</span> deverá ser substituído pelo nome do service store que será criado
    + <span>modulo</span> deverá ser substituído pelo nome completo com namepsace do módulo onde o service store será criado
    + <span>pack</span> deverá ser substituído pelo nome do pacote no backend, onde se encontra o controlador com o qual o service store irá se comunicar
    + <span>controlador</span> deverá ser substituído pelo nome do controlador no backend com o qual o service store irá se comunicar
    + <span>dir</span> deverá ser substituído pelo caminho completo do módulo/submódulo a partir de web/src

Exemplo:

```shell
./singular frontend:create-store ContatoStore cadastro.usuario Cadastro Contato cadastro/usuario 
```

### Métodos CRUD disponíveis 

Como mencionado, um service store é criado a partir de um StoreFactory, com isso ele consegue acesso há alguns métodos 
CRUD já implementados pelo StoreFactory que possibilitam comunicação e chamada dos métodos CRUD no backend. 

!!!note "get(id: integer, callback: function)"
    Método CRUD que faz a chamada do método <span>get</span> no controlador do backend.<br>
    __PARÂMETROS__:<br>
    <span>id</span> id do registro que você deseja recuperar no backend;<br>
    <span>callback</span> função de __callback__ que será executada quando a chamada do backend retornar;<br>
    __RETORNO__:<br>
    NULL

Exemplo:

```javascript
UsuarioStore.get(12, function(record){
    $scope.usuario = record;
});
```

!!!note "load(callback: function, method: string)"
    Método CRUD que faz a chamada de métodos <span>find</span> no controlador do backend.<br>
    __PARÂMETROS__:<br>
    <span>callback</span> função de callback que é executada quando a função do backend retornar o resultado.<br>
    <span>method</span> método find do backend que será executado. Por padrão, executa o método __find__<br>
    __RETORNO__:<br>
    NULL

Exemplo:

```javascript
UsuarioStore.load(function(response){
    $scope.usuarios = response.results;
}, 'findWithContatos')
```

!!!info "Parâmetros implícitos"
    O método load envia ao backend parâmetros que são transparentes ao usuário. Mas podem ser definidos e alterados conforme
    a necessidade. [Leia mais](frontend.md#parametros-implicitos-do-metodo-load) 

!!!note "save(data: object, callback: function)"
    Método crud que faz chamada ao método <span>save</span> no backend, para criar/atualizar um registro.<br>
    __PARÂMETROS__:<br>
    <span>data</span> objeto que representa o registro que será criado/atualizado<br>
    <span>callback</span> função de retorno que será acionada quando o método remoto for executado<br>
    __RETORNO__:<br>
    NULL

Exemplo:

```javascript
var record = {nome: "Otávio", email:"otavio@teste.com", senha: "123"};

UsuarioStore.save(record, function(response){
    if (response.success) {
        alert("Registro de id: " + response.record + " criado com sucesso!");
    }
})
```     

!!!note "remove(id: integer, callback: function, config: object)"
    Método crud que faz a chamada ao método <span>remove</span> no backend.<br> 
    __PARÂMETROS__:<br>
    <span>id</span> inteiro representando o identificador único do registro a ser excluído<br>
    <span>callback</span> função de retorno a ser acionada quando o método de remoção no backend for executado<br>
    <span>config</span> objeto de configuração das propriedades da mensagem de confirmação da exclusão (title, text, success)

Exemplo:

```javascript
UsuarioStore.remove(1, function(removed){
    if (removed) {
        console.log('removeu');
    }
}, {
    title: "Atenção", 
    text: "Deseja apagar o registro selecionado?", 
    success: "Registro excluído com sucesso!"
});
```

### Parâmetros implícitos do método load

Como mencionado anteriormente, quando chamamos o método <span>load</span> de um service store, ele implicitamente inclui 
nos parâmetros da chamada do método <destak>find</destak> no backend as propriedades <destak>filter</destak>, 
<destak>sort</destak> e <destak>paging</destak>. Esses parâmetros permitem realizar o filtro, ordenação e paginação dos
resultados da consulta. 

#### __Paginação__

A paginação do método load acontece pela configuração de um objeto <destak>paging</destak> de um Service Store. Essas 
propriedades são:

<destak>pageSize</destak> define a quantidade de registros em cada página

<destak>currentPage</destak> define a página atual da consulta

Exemplo: 
```javascript
UsuarioStore.paging.pageSize = 10;      // 10 registros por página
UsuarioStore.paging.currentPage = 0;    // página inicial é a primeira página de resultados
UsuarioStore.load();                    // aciona o recarregamento dos registros 
```
Ao utilizar uma <destak>list view</destak> essas propriedades estarão vinculadas aos atributos <destak>md-limit</destak> 
e <destak>md-page</destak> da diretiva <destak>md-table-pagination</destak>, e podem ser manipuladas e alteradas 
diretamente pelo usuário na tela. 

Exemplo:
```html
<md-table-pagination 
    md-limit="DataStore.paging.pageSize" 
    md-limit-options="[10, 30, 50, 100]"
    md-page="DataStore.paging.currentPage" 
    md-total="{{DataStore.total}}" 
    md-on-paginate="reloadData" 
    md-page-select>
</md-table-pagination>
```
#### __Ordenação__

A ordenação do método load acontece através da propriedade, <destak>sort</destak> de um Service Store. Essa propriedade
é uma string, onde cada campo pelo qual se deseja ordenar está separado por vírgula.  

Exemplo:
```javascript
UsuarioStore.sort = "nome,-idade";
UsuarioStore.load();
``` 

No exemplo acima a consulta será ordenada pelo nome de forma crescente e pela idade de forma descrescente.

Em uma <destak>list view</destak> essa propriedade é atribuída à diretiva <destak>md-order</destak> no cabeçalho de uma 
tabela, e em conjunto com a diretiva <destak>md-order-by</destak> utilizada nas colunas do cabeçalho, definem a ordem da 
listagem de resultados:

```html
<table md-table md-row-select="DataStore.enableRowSelection" ng-model="DataStore.selected">
   <thead md-head md-order="DataStore.sort" md-on-reorder="reloadData">
      <tr md-row>
         <th class="col-sm-1"></th>
         <th md-column md-order-by="perfil"><span>Perfil</span></th>
         <th md-column md-order-by="nome"><span>Nome</span></th>
         <th md-column md-order-by="login"><span>Login</span></th>
         <th md-column md-order-by="ativo"><span>Ativo</span></th>
         <th md-column><span></span></th>
      </tr>
   </thead>
...
</table>
```

!!!warning "Atenção"
    Ao aplicar a ordenação através do md-order na interface, não é possível utilizar múltiplas ordenações.

#### __Filtros__

Apenas chamar um método que retorna uma relação de registros, sem poder aplicar um filtro aos resultados que queremos 
obter seria inútil. Por isso, ao chamar o método <destak>load</destak> podemos definir filtros que serão enviados como 
parâmetro e servirão para filtrar os registros que desejamos obter. 

Para filtrar os resultados do método load, é necessário configurar o objeto <destak>filter</destak> do Service Store, com 
a condição de pesquisa que desejamos aplicar. 

```javascript
UsuarioStore.filter.ativo = 1;   // todos os registros de usuário onde a coluna ativo seja igual a 1 
UsuarioStore.load();             // força o carregamento dos registros
```

Todos os operadores de comparação aceitos pelo [Store do backend](backend.md#stores) podem ser utilizados para filtrar resultados.

Exemplo:
```javascript
UsuarioStore.filter.email = '%:gmail.com';      // email contém email.com
UsuarioStore.filter.idade = '>=:10';            // e idade maior igual há 10
UsuarioStore.filter.peso = '<=:30';             // e peso menor igual há 30
UsuarioStore.filter.reprovado = '<>:1';         // e reprovado diferente de 1
```

Ao utilizar filtros simples, o Singular irá automaticamente vincular a propriedade do <destak>filter</destak> à tabela
principal vinculada ao Store do backend. Entretanto, quando utilizamos joins, podemos querer vincular uma propriedade 
há um atributo de uma outra tabela na relação de joins. Para isso, precisamos criar um mapa na definição do  
Service Store.

Configurando o objeto <destak>filterMap</destak> de um Service Store, podemos definir qual deve ser o atributo 
equivalente em uma tabela de um  atributo de filtro no frontend. 


```javascript
/* Script UsuarioStore.js */
me.filterMap = {
    perfil: {
        property: 'p.perfil',
        operation: '%'
    }
};
```


Também podemos utilizar o filter map para determinar o operador de comparação que desejamos que seja aplicado há um 
atributo, dessa forma não precisaremos passar essa informação em toda chamada do método load:

```javascript
// Ao invés de
UsuarioStore.filter.idade = '>:10';

// Podemos utilizar um filter map
UsuarioStore.filterMap.idade = '>';
UsuarioStore.filter.idade = 10;
UsuarioStore.load();
```
Uma outra utilidade do filterMap é aplicar funções de conversão em um atributo antes que ele seja enviado como filtro 
para a função find no backend. 

```javascript
/* Script UserStore.js */
me.filterMap = {
   status: {
      property: 'en.status',
      operation: '=',
      convert: function(v){
         if (v > 1) {
            return 1;
         }
         return 0;
      }
   }
}
```

### Fazendo a chamada de métodos customizados

Embora, a maioria da comunicação entre o frontend e backend seja a chamada de métodos CRUD, eles não resolvem 100% dos 
problemas, e, frequentemente é necessário fazer a chamada de métodos customizados no controlador do backend anotados com 
<destak>@Route</destak>. Isso pode ser feito utilizando a chamada ao serviço <destak>$http</destak> do AngularJS, mas o 
Service store possui um método auxiliar que simplifica e torna fácil a chamada e recuperação de resultado de métodos 
customizados no backend. O método <destak>call</destak>.

!!!note "Parâmetros do método call"
    <span>method (string)</span> nome do método que será chamado no backend<br>
    <span>params (object)</span> objeto que contém a relação de chaves e valor dos parâmetros que serão enviados para 
    o método customizado no backend, pode ser enviado um objeto vazio<br>
    <span>callback (function)</span> função de callback que será executada quando a chamada ao método do backend retornar<br>
    <span>config (object: opcional)</span> objeto que contém a configuração da chamada remota. Por padrão, ao chamar o 
    método call, sempre é executada uma chamada ao método <span>post</span>, mas isso pode ser alterado mudando o valor 
    da propriedade method do objeto de configuração para <span>get</span>, também podem ser passados parâmetros na URL 
    através da propriedade urlParams no formato string: 

Exemplo:

```javascript
// POST
UsuarioStore.call('metodoCustomizadoPost', {id: 1}, function(response){
    if (response.success){
        $scope.data = response.results;
    }
});

// GET
UsuarioStore.call('metodoCustomizadoGet', {}, function(response){
    
}, {method: 'get', urlParams: '/10'});
```

## Views

As views do AngularJS são o que tornam possível a utilização do padrão SPA (Single Page Aplication). Assim como nos controladores, cada módulo tem seu próprio conjunto de views, e elas se encontram no diretório __views__ do módulo. 

### Criando uma nova view

Para criar uma nova view, basta utilizar o Singular Cli:

```shell
./singular frontend:create-view view dir tipo
```

Onde:
+ __view__: deve ser substituído pelo nome da view que se está criando. Exemplo: usuario.cadastro;
+ __diretorio/do/modulo__: deve ser substituído pelo caminho completo do diretório do módulo onde a view será criada a partir do diretório web/src;
+ __tipo__: o tipo de view a ser criada (list, filter, modal, form, tab);

Tipos de view:

+ __list__: view do tipo listagem de resultados de um service store, com uma tabela com recursos de paginação e botões de ação para criação, exclusão, edição e visualização de um registro;
+ __filter__: view do tipo filtro de resultados de uma list view, é a view exibida na sidebar lateral de filtros do módulo;
+ __modal__: view do tipo modal que será exibida através do serviço $modal;
+ __form__: view do tipo formulário que inclui tabs do cadastro do registro. Também possui botões de ação para salvamento do registro e retorno à tela de listagem;
+ __tab__: view do tipo tab, que pode ser incluída em uma view form.

