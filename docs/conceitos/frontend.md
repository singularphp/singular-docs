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

!!!info "Parâmetros ocultos"
    O método load envia ao backend parâmetros que são transparentes ao usuário. Mas podem ser definidos e alterados conforme
    a necessidade. [Leia mais]() 

+ __save(data: object, callback: function)__: método crud que faz a chamada do método __save__ no backend, para criar/atualizar um registro. É passado um objeto com a representação do registro que será inserido/atualizado e a função de callback é acionada com o resultado da ação de salvamento do registro no backend.
+ __remove(id: integer, callback: function, config: object)__: método crud que faz a chamada do método __remove__ no backend. Recebe como parâmetro o ID do registro que será excluído, e a função de callback que será acionada após a execução da ação no backend. Pode ainda receber um objeto de configuração com as propriedades (title, text e success) para definir respectivamente o título da modal de confirmação da exclusão, o texto de confirmação da modal de confirmação da exclusão e a mensagem exibida em caso de sucesso da exclusão. Caso este último parâmetro não seja passado, utilizará os valores padrão para a modal e mensagem de confirmação.

### Parâmetros do método load

Como mencionado acima, quando chamamos o método __load__ de um service store, ele implicitamente inclui nos parâmetros da chamada do método __find__ no backend as propriedades *filter*, *sort* e *paging*. Vamos analisar como esses parâmetros são definidos e como podem ser alterados, de acordo com nossa necessidade.

#### Paginação

A paginação do método load acontece pela configuração de um objeto com duas propriedades do Service Store, imagine que você tem acesso há um Service Store chamado UserStore.

```javascript
UserStore.paging.pageSize = 10;
UserStore.paging.currentPage = 0;
UserStore.load();
```
Ao utilizar uma __list view__ essas propriedades estarão vinculadas aos atributos md-limit e md-page da diretiva __md-table-pagination__, e podem ser manipuladas e alteradas diretamente pelo usuário na tela. 

#### Ordenação

Assim como a paginação, a ordenação do método load acontece também através da configuração de um objeto, __sort__. Em uma __list view__ essa propriedade é atribuída à diretiva __md-order__ no cabeçalho de uma tabela, e em conjunto com a diretiva md-order-by utilizada nas colunas do cabeçalho, definem a ordem da listagem de resultados:

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

#### Filtros

Apenas chamar um método que retorna uma relação de registros, sem poder aplicar um filtro aos resultados que queremos obter seria algo inútil. Por isso, ao chamar o método __load__ podemos definir filtros que serão enviados como parâmetro e servirão para filtrar os registros que desejamos obter. 

A forma mais simples de aplicar um filtro, é atribuir um valor ao atributo da tabela que queremos filtrar:

```javascript
UserStore.filter.ativo = 1;
UserStore.load();
```
Esse simples filtro será aplicado ao método find no backend, e ele diz para trazer todos os registros onde a propriedade ativo seja igual a 1. 

Algumas vezes, podemos desejar que os filtros sejam aplicados por similaridade, por exemplo:

```javascript
UserStore.filter.email = '%:gmail.com';
```
Esse filtro irá retornar todos os usuários que possuam um email que possua a expressão __gmail.com__ em alguma parte do valor. 

Podemos passar para um filtro qualquer condição de comparação:

```javascript
UserStore.filter.idade = '>=:10'; // idade maior igual há 10
UserStore.filter.peso = '<=:30'; // peso menor igual há 30
UserStore.filter.reprovado = '<>:1'; // reprovado diferente de 1
```
Ao utilizar filtros simples, o Singular irá automaticamente vincular a propriedade do __filter__ à tabela principal vinculada ao Store do backend. Entretanto, quando utilizamos joins, podemos querer vincular uma propriedade há um atributo de uma outra tabela na relação de joins. Para isso, precisamos criar um mapa na definição do  Service Store:

```javascript
        /* Script UserStore.js */
        me.filterMap = {
            perfil: {
                property: 'p.perfil',
                operation: '%'
            }
        };
```
Utilizando o filterMap, podemos definir qual deve ser o atributo equivalente em uma tabela de um atributo de filtro no frontend. 

Também podemos utilizar o filter map para determinar o tipo de operação que desejamos que seja aplicada há um atributo, dessa forma não precisaremos ficar informando a toda chamada a operação:

```javascript
// Ao invés de
UserStore.filter.idade = '>:10';

// Equivale a utilizar um filter map
UserStore.filterMap.idade = '>';
UserStore.filter.idade = 10;
UserStore.load();
```
Uma outra utilidade do filterMap é aplicar funções de conversão em um atributo antes que ele seja enviado como filtro para a função find no backend. 

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

Embora, a maioria da comunicação entre o frontend e backend seja a chamada de métodos CRUD, eles não resolvem 100% dos problemas, e, frequentemente é necessário fazer a chamada de métodos customizados no controlador do backend anotados com __@Route__. Isso pode ser feito utilizando a chamada ao serviço $http, mas o Service store possui um método auxiliar que simplifica e torna fácil a chamada e recuperação de resultado de métodos customizados no backend: o método __call__.

O método call recebe quatro parâmetros:

+ __method (string)__: nome do método que será chamado no backend;
+ __params (object)__: objeto que contém a relação de chaves e valor dos parâmetros que serão enviados para o método customizado no backend, pode ser enviado um objeto vazio;
+ __callback (function)__: função de callback que será executada quando a chamada ao método do backend retornar;
+ __config (object: opcional)__: objeto que contém a configuração da chamada remota. Por padrão, ao chamar o método call, sempre é executada uma chamada ao método __post__, mas isso pode ser alterado mudando o valor da propriedade method do objeto de configuração para __get__, também podem ser passados parâmetros na URL através da propriedade urlParams no formato string, por exemplo: 

```javascript
me.call('meuMetodoGet', {}, function(response){...}, {method: 'get', urlParams: '/10'});
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

