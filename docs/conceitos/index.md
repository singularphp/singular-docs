O Singular Framework, foi projetado para ser um framework Ajax, ou seja, à exceção das views principais (autenticação e 
acesso seguro, além das que se fizerem necessárias), todo o front-end é codificado em HTML, CSS e JavaScript, com apoio 
do framework AngularJS. Sempre que é necessário um dado que venha do servidor (backend) é feita uma requisição 
assíncrona Ajax para uma classe em PHP, que por sua vez, após realizar o processamento necessário, retorna a resposta 
para o frontend no formato JSON.

Por este motivo, todo o ciclo de desenvolvimento se divide basicamente em dois ambientes: Backend e Frontend.

## Backend

No backend é onde são feitos todos os processamentos de lógica de negócio, acesso à base de dados, geração de relatórios, 
envios de email, e outras tarefas semelhantes. Como mencionado anteriormente, o Singular Framework foi construído sobre 
o Silex, mas de forma totalmente orientada a objetos. 

Todo o código backend de um projeto no Singular reside dentro do diretório <destak>src</destak>. 

___

## Singular CLI

Um projeto criado com o Singular Project inclui o <destak>Singular Command Line Interface</destak>. O <destak>Singular
Cli</destak> é uma ferramenta utilitária que automatiza muitas tarefas no processo de desenvolvimento de aplicações.

### Exibindo os comandos disponíveis

Para visualizar os comandos disponíveis no <destak>Singular Cli</destak>, na raiz do seu projeto, execute o comando:

```shell
./singular 
```

O resultado será algo como:

```shell
Available commands:
  help                         Displays help for a command
  list                         Lists commands
 backend
  backend:create-command       Cria um novo comando num pacote
  backend:create-controller    Cria um novo controlador num pacote
  backend:create-pack          Cria e habilita um novo pacote na aplicação
  backend:create-service       Cria um novo serviço num pacote
  backend:create-store         Cria um novo store num pacote
  backend:disable-pack         Desabilita um pacote da aplicação
  backend:enable-pack          Habilita um pacote da aplicação
component
  component:create-migration   Cria migrations para inclusão dos componentes no banco de dados
  component:grant-full-access  Concede permissão de acesso a todos os componentes para um perfil
 frontend
  frontend:create-controller   Cria um controlador de frontend na aplicação
  frontend:create-module       Cria um módulo de frontend na aplicação
  frontend:create-store        Cria um store de frontend na aplicação
  frontend:create-view         Cria uma view de frontend para um módulo na aplicação
 lint
  lint:yaml                    Lints a file and outputs encountered errors
 sessao
  sessao:cria-perfil           Cria um novo registro de perfil na aplicação
```

___

## Pacotes

Cada conjunto de funcionalidades é organizado dentro de um pacote no diretório src. Por exemplo, se o sistema a ser 
desenvolvido possui um conjunto de funcionalidades relacionadas a cadastros, poderá ser criado um pacote <destak>Cadastro</destak> 
que irá armazenar todas as classes relacionadas a essa funcionalidade de cadastro. 

Quando um novo projeto é criado com o Singular Project, já existe um pacote chamado <destak>Sessao</destak>. Esse pacote
possui controladores, serviços e stores relacionados às funcionalidades de controle de acesso e gerenciamento de sessão
da aplicação.

### Estrutura de um Pacote

Como dito anteriormente, um pacote é uma forma de agrupar um conjunto de funcionalidades relacionadas. 
Quando um pacote é criado, a seguinte estrutura de diretórios e arquivos é criada na raiz do diretório <destak>src</destak>:

+ <destak>Command</destak>: Diretório onde são armazenadas as classes das tarefas que serão adicionadas à linha de comando do singular para o pacote em questão;
+ <destak>Controller</destak>: Diretório onde são armazenadas as classes dos controladores, controladores são o canal de comunicação entre o frontend e o backend. Funcionam como uma API do backend disponibilizada para acesso no frontend;
+ <destak>Service</destak>: Diretório onde são armazenadas classes auxiliares que disponibilizam serviços para que o container de serviços do Silex possa disponibilizar para toda a aplicação;
+ <destak>Store</destak>: Diretório onde são armazenadas as classes que intermediam a comunicação da aplicação com o banco de dados. Cada tabela é representada no Singular por um store;
+ <destak>PacoteServiceProvider.php</destak>: Arquivo de definição do pacote, ele é que registra o provedor de serviços do pacote e o expõe para o container de serviços da aplicação.

O Singular CLI possui tarefas utilitárias para a criação de todos os recursos de um pacote, mais adiante iremos estudá-lo para conhecer as tarefas disponíveis.

### Criando um pacote

A maneira mais fácil de criar um pacote é utilizar o Singular CLI, para isso, no terminal, no diretório raiz do seu projeto digite o seguinte comando:

```shell
./singular backend:create-pack NomeDoPacote
```

Onde, <destak>NomeDoPacote</destak>, deverá ser substituído pelo nome do pacote que você deseja criar.

Ao executar este comando, um novo diretório nomeado de acordo com nome que você forneceu ao pacote será criado na 
raiz do diretório src, com a estrutura completa de um pacote.

Além disso, um arquivo chamado <destak>nomedopacote.php</destak> é criado dentro do diretório [app/packs](). Ao abrir 
este arquivo, você pode verificar a seguinte declaração:

```php
use NomeDoPacote\NomeDoPacoteServiceProvider;

$app->register(new NomeDoPacoteServiceProvider());
```

Uma vez que o Singular é criado sobre o <destak>Silex Framework</destak>, ele também utiliza a funcionalidade de provedores
de serviço para extender suas funcionalidades. Neste caso, cada novo pacote criado é um novo provedor de serviços, e 
cada funcionalidade desse pacote extende as funcionalidades do framework.

### Desabilitando um pacote

Algumas vezes, pode ser necessário, para efeitos de testes ou por outro motivo, desativar um pacote e todas as suas 
funcionalidades. Para fazer isso, basta apagar o arquivo [app/packs/nomedopacote.php](). Mas isso pode ser feito via 
<destak>Singular Cli</destak>

```shell
./singular backend:disable-pack NomeDoPacote
```

### Habilitando um pacote

Se um pacote foi desabilitado, ou criado manualmente, para que o mesmo seja novamente ativado, é necessário que ele seja
habilitado. Para isso, basta criar um arquivo chamado <destak>nomedopacote.php</destak> dentro do diretório [app/packs](). 
Dentro desse arquivo, você deve registrar o seu provedor de serviços.

```php
use NomeDoPacote\NomeDoPacoteServiceProvider;

$app->register(new NomeDoPacoteServiceProvider());
```

Entretanto, isso também pode ser alcançado de forma muito mais simples, através do <destak>Singular Cli</destak>

```shell
./singular backend:enable-pack NomeDoPacote
```
___

## Comandos

Comandos são tarefas que podem ser executadas pelo terminal através do Singular Cli. Comandos são úteis para disparar 
emails, executar tarefas de limpeza de tabelas, etc. Eles podem ser acionados manualmente, ou ser programados para 
executar automaticamente através de ferramentas como o <destak>cron</destak> do linux.

### Criando um comando


Cada pacote pode definir seus próprios comandos, e desta forma extender as funcionalidades do <destak>Singular Cli</destak>
para executar tarefas específicas do pacote. 

Para criar um comando, basta digitar a seguinte instrução no seu terminal:

```shell
./singular backend:create-command Comando Pacote
```

!!! Onde:
    <span>Comando</span> deve ser substituído pelo nome script de comando<br>
    <span>Pacote</span> deve ser substituído pelo nome do pacote onde você deseja armazenar o novo comando

Ao executar essa instrução, uma nova classe php, com o nome fornecido para o comando, será criado no diretório Command 
do seu pacote. 

A estrutura dessa classe é bem simples, ela possui apenas 2 métodos <destak>configure</destak> e <destak>execute</destak>, 
conforme veremos em detalhe abaixo:

```php 
 1.  class TesteCommandCommand extends Command
 2.  {
 6.      public function configure()
 7.      {
 8.         $this->setName('sessao:teste-command')
 9.             ->setDescription('Descrição do comando')
10.            ->setHelp('Ajuda para execução do comando')
11.            ->addArgument(
12.                'parametro',
13.                InputArgument::REQUIRED,
14.                'Descricao do parâmetro'
15.            );
16.      }
17.
24.      public function execute(InputInterface $input, OutputInterface $output)
25.      {
26.          $app = $this->getSilexApplication();
27.
28.          $output->writeln(sprintf('<info>%s</info>', 'Comando executado com sucesso!'));
29.      }
30.  }
```
No método <destak>configure</destak> é feita a configuração do parâmetro: 

!!! Entendendo:  
    <span>Linha 8</span> É definido o nome do comando que será exibido no Singular Cli, 
    é uma boa prática utilizar o nome do pacote como namespace de um comando 
    Por exemplo, se o comando chama-se teste e o pacote cadastro, o nome deveria ser <span>cadastro:teste</span>
    
    <span>Linha 9</span> É definido o texto de definição do comando, que exibe a ajuda ao usuário, sobre o que o 
    comando faz
    
    <span>Linha 10</span> É definido um texto de ajuda, que será exibido quando o usuário digitar a opção de ajuda 
    para o seu comando
    
    <span>Linha 11 a 14</span> São definidos os argumentos do comando, ou parâmetros, como costumamos chamá-los. 
    Podem ser criados tantos argumentos quanto necessários. Argumentos são obrigatórios, mas existe opções, que são 
    parâmetros opcionais 

O método <destak>execute</destak> é acionado quando o seu comando é executado pelo Singular Cli, ele pode acessar 
qualquer serviço da sua aplicação para executar uma determinada tarefas. 

Para maiores informações, acesse o site da documentação do [Componente Console do Symfony](http://symfony.com/doc/current/console.html).

## Controladores

Controladores são a interface de comunicação entre o frontend e o backend. Quando uma informação do backend é 
necessária no frontend, como por exemplo, uma lista de usuários, uma requisição é feita a partir da interface para um 
controlador, esse por sua vez, é responsável por realizar as chamadas aos serviços e stores e retornar essa informação 
para o frontend no formato json. 

### Criando um controlador


Para criar um novo controlador através do Singular Cli, basta executar o seguinte código:

```shell
./singular backend:create-controller Controlador Pacote
```

!!! Onde
    <span>Controlador</span> deve ser substituído pelo nome do controlador<br>
    <span>Pacote</span> deve ser substituído pelo nome do pacote onde você deseja armazenar o novo controlador

Ao executar essa instrução, uma nova classe php, com o nome fornecido para o controlador, será criado no diretório 
<destak>Controller</destak> do seu pacote.  A estrutura básica da classe de um controlador se parece com:

```php
1.  /**
2.   * Classe Perfil
3.   *
4.   * @Controller
5.   *
6.   * @author Otávio Fernandes <otavio@netonsolucoes.com.br>
7.   */
8.  class Perfil extends SingularController
9.  {
10.    use Crud;
11.
12.    /**
13.     * Defina o store padrão do controlador.
14.     *
15.     * @var $store
16.     */
17.    protected $store = 'perfil';
18. }
```

O que diferencia uma classe de um controlador de uma outra classe qualquer é a anotação <destak>@Controller</destak> no 
bloco de documentação da classe. Se essa anotação não existir, não será possível acessar nenhum de seus métodos através 
do frontend.

Outro ponto importante, é que, se você desejar que o controlador criado já implemente métodos básicos de funcionalidades
 <destak>CRUD</destak>, você pode utilizar o <destak>Trait Crud</destak>, como é feito na <destak>linha 10</destak>. 
 Por padrão, um controlador criado pelo Singular Cli, já implementa esse traço, mas você pode retirar esse comportamento, 
 removendo o <destak>use Crud</destak> da declaração da classe. 

!!!CRUD
    + <span>Método find</span> Função que recupera uma lista de registros vinculados há um store de acordo com parâmetros de 
    filtro, paginação e ordenação (<span>filter</span>, <span>paging</span> e <span>sort</span>);
    
    + <span>Método get</span> Função que recupera um registro específico vinculado há um store de acordo com o parâmetro 
    <span>id</span> desse registro;

    + <span>Método save</span> Função que cria/atualiza um registro específico vinculado há um store de acordo com 
    parâmetros de campos de uma tabela. Se o campo <span>id</span> for enviado na lista de parâmetros o registro é 
    atualizado, caso contrário, um novo registro é criado e o seu id é retornado para o método que fez a requisição no 
    frontend;

    + <span>Método remove</span> Função que exclui um registro vinculado há um store através do seu <span>id</span> 
    informado como parâmetro.

Outro ponto muito importante a considerar é a propriedade <destak>$store</destak> da classe do controlador. Essa 
propriedade faz referência direta a qual classe <destal>store</destak> o controlador está vinculado. Sem essa 
propriedade definida, nenhum método definido pelo traço <destak>Crud</destak> irá funcionar. Ela deve referenciar, 
no minúsculo, há uma classe store, criada no mesmo pacote do controlador. Caso não seja um controlador que use o 
traço Crud, ela não é necessária. 

### Expondo métodos para o frontend

O objetivo de uma classe de controlador é fornecer um método que permita comunicação entre o backend e o frontend. Para 
 que isso aconteça é extremamente necessário que os métodos criados no controlador sejam <destak>expostos</destak> para o frontend. 
 Veja como um novo método pode ser criado e exposto para o frontend:

```php
<?php
1.  namespace Sessao\Controller;

2.  use Symfony\Component\HttpFoundation\Request;
3.  use Singular\SingularController;
4.  use Singular\Crud;
6.  use Singular\Annotation\Controller;
7.  use Singular\Annotation\Route;
8.  use Singular\Annotation\Direct;
9.  use Singular\Annotation\Value;
10. use Singular\Annotation\Assert;
11. use Singular\Annotation\Convert;
12. use Singular\Annotation\After;
13. use Singular\Annotation\Before;

14. /**
15.  * Classe Perfil
16.  *
17.  * @Controller
18.  *
19.  * @author Otávio Fernandes <otavio@netonsolucoes.com.br>
20.  */
21. class Perfil extends SingularController
22. {
23.     use Crud;
24.
25.     /**
26.      * Defina o store padrão do controlador.
27.      *
28.      * @var $store
29.      */
30.    protected $store = 'perfil';
31.
32.    /**
33.     * Emite uma saudação para um nome completo.
34.     *
35.     * @Route(method="post")
36.     *
37.     * @param Request $request
38.     *
40.     * @return \Singular\Response\JsonResponse
41.     */
42.    public function saudacoesNomeCompleto(Request $request)
43.    {
47.        $app = $this->app;
48.
49.        $primeiroNome = $request->get('primeiro_nome');
50.        $segundoNome = $request->get('segundo_nome');
51.
52.        return $app->json([
53.            'mensagem' => 'Olá '.$primeiroNome.' '.$segundoNome.', seja bem vindo!'
54.        ]);
58.    }
59. }

```

O método <destak>saudacoesNomeCompleto</destak> do controlador <destak>Perfil</destak> do pacote <destak>Sessao</destak> 
precisa ser exposto para a camada de frontend. Para isso, utilizamos a anotação da <destak>linha 35</destak>

```php
@Route(method="post")
```
 Ao utilizarmos essa anotação, estamos tornando possível que uma requisição seja feita diretamente da interface por 
 exemplo através do serviço <destak>$http</destak> do __AngularJS__ ou qualquer outro framework:

```javascript
$http.post('./sessao/perfil/saudacoesNomeCompleto', {primeiro_nome: "Otavio", segundo_nome: "Fernandes"}, function(response){
   alert(response.mensagem);
});
```

Todos os métodos http estão disponíveis para serem utilizados na anotação <destak>@Route</destak> (get, post, put, 
delete, options). Algumas vezes, pode ser necessário expor uma função do controlador para mais de um método http, 
isso pode ser facilmente alcançado com:

```php
@Route(methods={"post","options"})
```

Por padrão, todos os parâmetros são recuperados na função do controlador através de um objeto da classe 
<destak>Symfony\Component\HttpFoundation\Request</destak> que é parâmetro da função. Entretanto, algumas vezes, 
você pode necessitar passar alguns parâmetros na própria URL, para isso basta adicionar o nome desses parâmetros na 
sua função:

```php
    /**
     * Emite uma saudação para um nome completo.
     *
     * @Route(method="get")
     *
     * @param Request $request
     * @param string  $primeiroNome
     * @param string  $segundoNome
     *
     * @return \Singular\Response\JsonResponse
     */
    public function saudacoesNomeCompleto(Request $request, $primeiroNome, $segundoNome)
    {
        $app = $this->app;

        $primeiroNome = $primeiroNome;
        $segundoNome = $segundoNome;

        return $app->json([
            'mensagem' => 'Olá '.$primeiroNome.' '.$segundoNome.', seja bem vindo!'
        ]);
    }
```

E a chamada http ficaria semelhante à:

```javascript
$http.get('./sessao/perfil/saudacoesNomeCompleto/Otavio/Fernandes', function(response){
   alert(response.mensagem);
});
```
### Middlewares de controladores

Assim como no Silex, o Singular permite que sejam definidos <destak>middlewares de rotas</destak> que podem ser 
executados antes, ou depois de um grupo de rotas ser executado. Cada controlador no Singular é mapeado para um grupo
de rota do Silex. Para criar middlares para um controlador, basta alterar a anotação <destak>@Controller</destak>, 
adicionando as anotações <destak>@After</destak> e <destak>@Before</destak>.

```php
1.  /**
2.   * Classe Perfil
3.   *
4.   * @Controller(
5.   *      @Before({"pacote.service.servico:metodoAntes"})
6.   *      @After({"pacote.service.servico:metodoDepois"})
7.   * )
8.   *
9.   */
10.  class Perfil extends SingularController
11.  {
12.  }

```
!!! Onde
    <span>pacote</span> é o nome do pacote onde está o serviço que será executado como middleware<br>
    <span>servico</span> é o nome da classe do serviço que será instanciado<br>
    <span>metodoAntes</span> é o método na classe serviço, que será executado antes de qualquer método no controlador 
    ser acionado<br>
    <span>metodoDepois</span> é o método na classe serviço, que será executado depois todo método no controlador ser 
    executado
    
No exemplo acima, estão sendo registrados dois middlewares <destak>@Before</destak> e <destak>@After</destak>. 

Antes que qualquer método exposto do controlador <destak>Perfil</destak> seja executado o método <destak>metodoAntes</destak> 
da classe <destak>Pacote\Service\Servico</destak> será executado, como um interceptador da requisição, e poderá manipular 
o objeto <destak>Request</destak> adicionando ou removendo parâmetros, ou mesmo curto-circuitar a resposta, impedindo que 
o método do controlador seja executado.

Após a execução de qualquer método do controlador Perfil. o método <destak>metodoDepois</destak> da classe <destak>
Pacote\Service\Servico</destak> será executado e receberá como parâmetro o objeto <destak>Response</destak> retornado
pelo método do controlador.

Exemplo de implementação do serviço middleware.

```php
1.  namespace Pacote\Service;
2.  
3.  class Servico
4.  {
5.      public function metodoAntes(Request $request)
6.      {
6.           $app = $this->app;
8.
9.           $request->set('user_id',$app['session']->get('user'));
10.     }
11.     
12.     public function metodoDepois(Request $request, Response $response)
13.     {
14.          $app = $this->app;
15.
16.          $response->set('user_id',$app['session']->get('user'));
17.     }
18.     
 
```
  
### Criando um Serviço

Serviços são classes que expõe métodos e funções utilitárias que permitem a execução de algumas lógicas e regras de negócio para outras partes da aplicação (outros serviços e controladores). 

Para criar um serviço, basta utilizar o Singular Cli:

```shell
./singular backend:create-service NomeServico Pacote
```

Onde: 
+ __NomeServico__: deve ser substituído pelo nome do serviço que se deseja criar;
+ __Pacote__: deve ser substituído pelo nome do pacote onde se deseja criar o novo serviço;

Todo serviço tem acesso ao container da aplicação, que pode ser acessado através da propriedade $app da aplicação. Pelo container da aplicação, você pode acessar qualquer outro serviço definido na aplicação. 

Assim como com os controladores, um serviço no Singular Framework precisa ser anotado para estar registrado no container da aplicação como um serviço. Para isso, a anotação __@Service__ deve ser utilizada. 

#### Acessando serviços do container

Frequentemente, faz-se necessário acessar serviços a partir de um controlador, ou mesmo de outro serviço. Entende-se por serviço, qualquer classe que estiver dentro de um pacote da aplicação. Suponha que você precise acessar um serviço chamado __CalculoSalario__ que está definido dentro do pacote __Financeiro__, para acessá-lo, basta pegar uma referência do container de serviços da aplicação, como é feito no pimple:

```php
$servico = $this->app['cadastro.store.calculo_salario'];
```
É possível acessar qualquer serviço criado, a partir de qualquer pacote, controlador ou serviço, simplesmente usando a anotação de namespace de serviços com três partes. Onde a primeira parte identifica o pacote, a segunda, o diretório do pacote (Command, Controller, Service, Store) e a terceira o nome da classe em minúsculo.

### Criando um Store

Classes Stores são a camada de acesso ao banco de dados da sua aplicação. Cada store, pode representar uma tabela, e disponibiliza uma API de métodos que estarão disponíveis para serviços e controladores que executam consultas e manipulações específicas no banco de dados retornando resultados, alterando ou manipulando registros.

Para criar um store, você pode utilizar o Singular Cli:

```shell
./singular backend:create-store Store Pacote tabela
```
+ __Store__: deve ser substituído pelo nome da classe do store;
+ __Pacote__: deve ser substituído pelo nome do pacote onde você deseja armazenar o novo controlador;
+ __tabela__: nome da tabela no banco de dados vinculada ao store;

#### Métodos CRUD herdados da classe SingularStore

Uma classe store faz herança da classe __Singular\SingularStore__, então ela herda alguns métodos utilitários do tipo crud padrão dessa classe:

+ __find($id: integer):array__: Retorna um registro de uma tabela do banco de dados pelo seu id. Recebe o __id__ do registro como parâmetro e retorna um array representando um registro único da tabela.
```php
$usuario = $app['cadastro.store.usuario']->find(1);
```
+ __findOneBy($filters: array):array__: Retorna um registro de uma tabela através de um array de filtros aplicados nessa tabela. Recebe um array de filtros como parâmetro e retorna um array representando um registro único da tabela.
```php
$usuario = $app['cadastro.store.usuario']->findBy([
      'login' => '%:Otavio', 
      'email' => 'otavio@neton.com.br'
]);
```
+ __findBy($filters: array, $pageOpts: array, $sort: array)__: Retorna um conjunto de registros que combinam com um grupo de filtros aplicado na tabela. Recebe um array de filtros como parâmetro, e também pode receber parâmetros de paginação e ordenação dos resultados e retorna um array contendo um conjunto de resultados que combinam com os filtros aplicados;
```php
$usuarios = $app['cadastro.store.usuario']->findBy(
     ['email' => '%:gmail.com'],
     ['start' => 0,'limit'=> 5], 
     ['nome' => 'asc']
);
```
+ __save($record:array):integer__: Cria/atualiza um registro na tabela e retorna o seu id;
```php
$usuario = ['nome' => 'Otávio', 'login' => 'otavio', 'senha'=> '123];
$id = $app['cadastro.store.usuario']->save($usuario);
```
+ __remove($id: integer): boolean__: Remove um registro da tabela através do seu id e retorna o booleano do sucesso da operação;
```php
$excluido = $app['cadastro.store.usuario']->remove(1);
```
+ __removeBy($filter: array)__: boolean: Remove um ou mais registros da tabela através da combinação de filtros aplicados na tabela;

```php
$excluido = $app['cadastro.store.usuario']->removeBy(['email' => '%:gmail.com']);
```

##### Perfis de consulta

Todas as funções de consulta herdadas do SingularStore, por padrão, retornam determinados campos, aplicando joins, groupings e filtros padrão. Isso é ótimo, pois evita a criação de consultas complexas desnecessárias agilizando o processo. Entretanto, pode haver a necessidade de trazer informações diferentes, com filtros e joins diferentes, dependendo do contexto. A versão 2 do Singular implementa uma funcionalidade chamada __Perfis de consulta (profiles)__ que permite criar vários perfis de consulta (campos do select, filtros, joins, groupings), dessa forma é possível de acordo com o contexto, selecionar um ou outro perfil padrão, eliminando ainda mais a necessidade de criar querys complexas para atividades triviais.

Quando você cria um novo store, por padrão ele já define o padrão __default__ de consulta:

```php
    ...
    /**
     * Perfis de consulta.
     *
     * @var array
     */
    protected $profiles = [
        'default' => [
            'select' => ['t.*'],
            'joins' => [],
            'filters' => [],
            'groupings' => []
        ]
    ];
    ...
```
Um perfil de consulta nada mais é que um array de arrays estruturados. Para criar um novo perfil, basta criar um elemento na propriedade $profiles atribuindo-lhe um nome e igualando seu valor a um outro array com as propriedades:
+ __select__: array com a lista dos campos que serão selecionadas, por padrão o alias da tabela vinculada ao store é __t__;
```php
[
   'select'=> ['t.nome','t.id as codigo']
]
````
+ __joins__: um array de definição de um relacionamento com outra tabela. Para cada elemento do join o primeiro parâmetro é o nome da tabela relacionada, o segundo o alias, o terceiro a condição de igualdade do relacionamento e o quarto e opcional, o tipo de relacionamento (left ou join), se for omitido assume o valor 'join'.
```php
[
   'joins' => [
      ['perfil','p','p.id = u.perfil_id', 'left']
   ]
]
```
+ __filters__: um array de definição dos filtros padrão a serem aplicados na consulta.
```php
[
   'filters' => [
      'ativo' => '1'
   ]
]
```
+ __groupings__: um array de definição dos agrupamentos padrão aplicados na consulta
```php
[
   'grouping' => [
      't.id'
   ]
]
```
Para criar um novo perfil de consulta, apenas crie um novo elemento no array $profiles e defina os valores que julgar necessários. Para selecionar um perfil de consulta, basta acionar o método __setProfile__ antes de executar uma função como (find, findBy, findOneBy).

```php
$app['cadastro.store.usuario']->setProfile('perfil')->findBy(['id' => '>:10']);
```
##### Criando consultas customizadas

Embora os métodos findBy e findOneBy possam ser muito úteis e flexíveis através da utilização dos perfis de consulta, algumas vezes se faz necessário criar consultas customizadas e complexas para realizar tarefas específicas. Esses métodos podem ser facilmente criados nas classes store. 

A classe store, possui uma propriedade __$db__ que fornece acesso à classe de conexão __DBAL__ do Doctrine para o Silex. Para facilitar a criação de consultas, e ter consultas que sejam mais legíveis, é extremamente importante utilizar o __QueryBuilder__ do Doctrine. Para recuperar uma nova instância do query builder, basta:

```php
$qb = $this->db->createQueryBuilder();
```

Ao criar uma consulta customizada, geralmente, precisamos passar filtros para essa consulta. Por questões de segurança, __SEMPRE__ utilize a vinculação de parâmetros para definir filtros. __NÃO UTILIZE__ a vinculação da entrada do usuário diretamente na montagem da sua consulta. Vamos ver uma aplicação de certo e errado:

```php
/* ERRADO */
$qb->select('u.*')
   ->from('usuarios','u')
   ->where('u.idade > '.$idade);
$rs = $this->db->fetchAll($qb->getSQL());

/* CORRETO */
$qb->select('u.*')
   ->from('usuarios','u')
   ->where('u.idade > :idade');

$rs = $this->db->fetchAll($qb->getSQL(), ['idade' => $idade]);

```

## Frontend

O Singular Framework foi projetado para ser um framework Ajax de baixo acoplamento entre as camadas de frontend e backend, ou seja, a interface dos módulos que serão construídos na aplicação, são definidos totalmente no frontend, sem nenhuma vinculação com a renderização no backend. Enquanto no backend, o Silex é utilizado como base do framework, no frontend por sua vez é o AngularJS quem fornece a estrutura de trabalho para a construção da interface de usuário.

### Views renderizadas pelo backend

Em um projeto criado a partir do Singular Project, há inicialmente, apenas 2 views que são renderizadas pelo backend. Entretanto essas views não possuem código de interface de usuário, elas são apenas páginas HTML que ativam uma aplicação AngularJS. Essas duas views são auth.html e secure.html, ambas estão localizadas no diretório __views__ na raiz do projeto. 

A renderização dessas views ocorre através do controlador __Sessao\Controller\Main__ através dos métodos __showSecure__ e __showAuth__. Eles utilizam o provedor de serviços do __Twig__ para pegar o template das páginas html e renderizá-las no navegador. 

Algumas vezes, pode ser necessário criar algumas rodas adicionais para serem renderizadas pelo backend, por exemplo, suponha que a aplicação que você está construindo terá uma tela que será exibida em um monitor e que para o recurso acessado, ela não precise que o usuário esteja autenticado. Então, seria necessário criar uma outra view, para isso, crie o arquivo do template da view dentro do diretório views. Por exemplo: __monitor.html__. Essa view irá fazer referência a uma aplicação própria do angular. 

Após criar o seu template, o próximo passo é criar um novo método que será responsável por renderizar a view no controlador __Main__ no pacote __Sessao__. 

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
O método render do serviço __twig__ pode ainda receber parâmetros adicionais que serão interpolados ao template, para maiores informações, consulte a documentação do [Provedor de serviços do Twig](https://silex.symfony.com/doc/2.0/providers/twig.html). 

Para que a view possa ser renderizada, ainda é necessário realizar uma última configuração que irá criar uma rota get para o método de renderização da view. Para isso, adicione uma entrada para o mapeamento da rota no método connect da classe SessaoServiceProvider no pacote Sessao.

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

Como se trata de uma definição de rota convencional do Silex, você pode utilizar qualquer funcionalidade ou recurso disponíveis para isso, como por exemplo, utilizar rotas dinâmicas com parâmetros, validação de parâmetros, etc. 

Pronto, feito isso, você poderá agora acessar a view diretamente através do seu navegador.

### Módulos

Se no backend as funcionalidades são agrupadas em pacotes, no frontend elas são agrupadas por módulos, esses são módulos do AngularJS, esses módulos podem ser dependentes de outros módulos menores ou submódulos, se assim quiser chamá-los. 

Os módulos de frontend são armazenados dentro do diretório __web/src__. Uma aplicação criada a partir do Singular Project já vem com 3 módulos principais:

+ __login__: Módulo que define a aplicação utilizada na view de autenticação do sistema (auth.html). Ele é responsável por fornecer funções para autenticação do usuário;
+ __secure__: Módulo que define a aplicação utilizada na view segura do sistema (secure.html). Geralmente seus novos módulos serão adicionados como dependência para este módulo, de forma que, quando o usuário logar, seus módulos estarão disponíveis na aplicação;
+ __ui__: Módulo padrão que define serviços, controladores, views e diretivas utilizadas na interface do sistema. 

#### A estrutura de um módulo

Um módulo de frontend é composto por um script de definição e vários scripts de (controladores, diretivas, serviços, views e filtros) que são organizados dentro de diretórios. A idéia é seguir a definição do *Jhon Papa* para melhor organização do código. 

Ao abrir o diretório de um módulo geralmente tem-se a seguinte estrutura:

+ __controllers__: Diretório onde são armazenados os scripts dos controladores AngularJS do módulo. Cada view deverá ter seu próprio controlador. Podem ser criados tantos controladores quanto necessários e quanto menor o controlador mais organizado e bem estruturado será o seu código;
+ __services__: Diretório onde são armazenados os scripts dos serviços AngularJS do módulo. Podem ser criados tantos serviços quanto se fizer necessário. Toda comunicação com o backend se dará por meio dos serviços que implementam o [Service Store];
+ __directives (opcional)__: Diretório onde podem ser armazenadas diretivas específicas do módulo em questão;
+ __filters (opcional)__: Diretório onde podem ser armazenados filtros específicos do módulo em questão;
+ __modulo.js__: Script que define o módulo AngularJS. É nessa definição que são criados os states para as views do módulo. Vale lembrar que se o módulo se chamar login.registro, o nome do script será registro.js.

#### Criando um novo módulo

Para criar um novo módulo, basta utilizar o Singular Cli:

```shell
./singular frontend:create-module app.teste
```
Ao executar este comando, será criado um novo módulo no diretório __web/src__ chamado __app.teste__. No entanto, frequentemente, você precisará criar um submódulo, ou seja, um módulo que fica dentro de outro módulo como uma dependência. Para isso, o Singular utiliza o padrão de namespaces para nomeação dos módulos. No caso, o módulo __app.teste__ está dentro do namespace __app__ que representa a nossa aplicação. 

Suponha que seja necessário criar um submódulo "registro" dentro do módulo de login, para isso, no Singular Cli utilizaríamos:

```php
./singular frontend:create-module login.registro --dir=login
```
Ao executar este comando, um novo diretório do módulo será criado dentro do diretório do módulo __login__. No nome __login.registro__ o namespace é facilmente identificado pelo __login.__. Por padrão, quando você cria um submódulo, o mesmo já é registrado como uma dependência no módulo pai.

#### Definindo states para o módulo

O Singular Project utiliza o ui-router como mecanismo de roteamento padrão. Cada módulo define seus próprios states. Por este motivo, todas as rotas são definidas através de states, convencionalmente como é feito no ui-router, dentro da função config do módulo:

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
Mas ao observar o bloco de código, você deve ter percebido que existe um parâmetro que não é comum ao ui-router. O parâmetro __"acl"__ faz parte do Singular e ele é utilizado para restringir o acesso de um usuário ao state. Ao utilizar esse parâmetro, você garante que apenas usuários que possuam no seu perfil o acesso à funcionalidade __f-cadastro-create__ poderão acessá-lo.

### Controladores

Assim como na definição do AngularJS, a principal funcão de um controlador é integrar o modelo à view. Cada módulo tem seu próprio conjunto de controladores, e eles se encontram no diretório __controllers__ do módulo. 

#### Criando um novo controlador

Para criar um novo controlador, basta utilizar o Singular Cli:

```shell
./singular frontend:create-controller namespacemodulo.MeuControlador modulo diretorio/do/submodulo --tipo=list
```

Onde:
+ __namespacemodulo.MeuControlador__: deve ser substituído pelo nome do seu controlador com o namespace. Por exemplo: usuario.ListCtrl;
+ __modulo__: deve ser substituído pelo nome com namepsace completo do módulo onde o controlador será criado. Por exemplo: cadastro.usuario;
+ __diretorio/do/modulo__: deve ser substituído pelo caminho completo do diretório do módulo a partir do diretório web/src;
+ __tipo__: se omitido, o valor default é normal. Mas pode assumir os valores (list, filter, modal, create, edit, normal);

### Service Stores

Service Stores são serviços angular que são criados a partir de uma StoreFactory. Eles disponibilizam métodos CRUD e de comunicação com o backend encapsulando as chamadas do serviço $http.

#### Criando um novo service store

Para criar um novo service store, basta utilizar o Singular Cli:

```shell
./singular frontend:create-store NomedoStore modulo pack controlador dir
```
Onde:
+ __NomedoStore__: deverá ser substituído pelo nome do service store que será criado;
+ __modulo__: deverá ser substituído pelo nome completo com namepsace do módulo onde o service store será criado;
+ __pack__: deverá ser substituído pelo nome do pacote no backend, onde se encontra o controlador com o qual o service store irá se comunicar;
+ __controlador__: deverá ser substituído pelo nome do controlador no backend com o qual o service store irá se comunicar;
+ __dir__: deverá ser substituído pelo caminho completo do módulo/submódulo a partir de web/src;

#### Métodos CRUD disponíveis 

Como mencionado, um service store é criado a partir de um StoreFactory, com isso ele consegue acesso há alguns métodos CRUD já implementados pelo StoreFactory que possibilitam comunicação e chamada dos métodos CRUD no backend. 

+ __get(id: integer, callback: function)__: método crud que faz a chamada do método __get__ no backend, onde um ID do registro é passado como parâmetro, a função de callback é acionada com o resultado da recuperação do registro no backend.
+ __load(callback: function)__: método crud que faz a chamada do método __find__ no backend. Este método utiliza implicitamente as propriedades *filter*, *sort*, e *paging* do store como parâmetros para o método find, a função de callback é acionada com o resultado da recuperação dos registros no backend.
+ __save(data: object, callback: function)__: método crud que faz a chamada do método __save__ no backend, para criar/atualizar um registro. É passado um objeto com a representação do registro que será inserido/atualizado e a função de callback é acionada com o resultado da ação de salvamento do registro no backend.
+ __remove(id: integer, callback: function, config: object)__: método crud que faz a chamada do método __remove__ no backend. Recebe como parâmetro o ID do registro que será excluído, e a função de callback que será acionada após a execução da ação no backend. Pode ainda receber um objeto de configuração com as propriedades (title, text e success) para definir respectivamente o título da modal de confirmação da exclusão, o texto de confirmação da modal de confirmação da exclusão e a mensagem exibida em caso de sucesso da exclusão. Caso este último parâmetro não seja passado, utilizará os valores padrão para a modal e mensagem de confirmação.

#### Parâmetros do método load

Como mencionado acima, quando chamamos o método __load__ de um service store, ele implicitamente inclui nos parâmetros da chamada do método __find__ no backend as propriedades *filter*, *sort* e *paging*. Vamos analisar como esses parâmetros são definidos e como podem ser alterados, de acordo com nossa necessidade.

##### Paginação

A paginação do método load acontece pela configuração de um objeto com duas propriedades do Service Store, imagine que você tem acesso há um Service Store chamado UserStore.

```javascript
UserStore.paging.pageSize = 10;
UserStore.paging.currentPage = 0;
UserStore.load();
```
Ao utilizar uma __list view__ essas propriedades estarão vinculadas aos atributos md-limit e md-page da diretiva __md-table-pagination__, e podem ser manipuladas e alteradas diretamente pelo usuário na tela. 

##### Ordenação

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

##### Filtros

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

#### Fazendo a chamada de métodos customizados

Embora, a maioria da comunicação entre o frontend e backend seja a chamada de métodos CRUD, eles não resolvem 100% dos problemas, e, frequentemente é necessário fazer a chamada de métodos customizados no controlador do backend anotados com __@Route__. Isso pode ser feito utilizando a chamada ao serviço $http, mas o Service store possui um método auxiliar que simplifica e torna fácil a chamada e recuperação de resultado de métodos customizados no backend: o método __call__.

O método call recebe quatro parâmetros:

+ __method (string)__: nome do método que será chamado no backend;
+ __params (object)__: objeto que contém a relação de chaves e valor dos parâmetros que serão enviados para o método customizado no backend, pode ser enviado um objeto vazio;
+ __callback (function)__: função de callback que será executada quando a chamada ao método do backend retornar;
+ __config (object: opcional)__: objeto que contém a configuração da chamada remota. Por padrão, ao chamar o método call, sempre é executada uma chamada ao método __post__, mas isso pode ser alterado mudando o valor da propriedade method do objeto de configuração para __get__, também podem ser passados parâmetros na URL através da propriedade urlParams no formato string, por exemplo: 

```javascript
me.call('meuMetodoGet', {}, function(response){...}, {method: 'get', urlParams: '/10'});
```

### Views

As views do AngularJS são o que tornam possível a utilização do padrão SPA (Single Page Aplication). Assim como nos controladores, cada módulo tem seu próprio conjunto de views, e elas se encontram no diretório __views__ do módulo. 

#### Criando uma nova view

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

