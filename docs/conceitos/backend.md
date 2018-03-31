No backend é onde é realizado todo o processamento da lógica de negócio, acesso à base de dados, geração de relatórios, 
envios de email, e outras tarefas semelhantes. Como mencionado anteriormente, o Singular Framework foi construído sobre 
o Silex, mas de forma totalmente orientada a objetos. 

Todo o código backend de um projeto no Singular reside dentro do diretório <destak>src</destak>. 

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
___

## Serviços

Serviços são classes que expõe métodos e funções utilitárias que permitem a execução de algumas lógicas e regras de negócio para outras partes da aplicação (outros serviços e controladores). 

  
### Criando um Serviço


Para criar um serviço, basta utilizar o Singular Cli:

```shell
./singular backend:create-service NomeServico Pacote
```

!!! Onde 
    <span>NomeServico</span> deve ser substituído pelo nome do serviço que se deseja criar<br>
    <span>Pacote</span> deve ser substituído pelo nome do pacote onde se deseja criar o novo serviço

Todo serviço tem acesso ao container da aplicação, que pode ser acessado através da propriedade $app do serviço. 
Pelo container da aplicação, você pode acessar qualquer outro serviço definido na aplicação. 

Assim como com os controladores, um serviço no Singular Framework precisa ser anotado para estar registrado no container 
da aplicação como um serviço. Para isso, a anotação <destak>@Service</destak> deve ser utilizada. 

### Acessando serviços do container

Frequentemente, faz-se necessário acessar serviços a partir de um controlador, ou mesmo de outro serviço. 
Entende-se por serviço, qualquer classe que estiver dentro de um pacote da aplicação. Suponha que você precise acessar 
um serviço chamado <destak>CalculoSalario</destak> que está definido dentro do pacote <destak>Financeiro</destak>, para
acessá-lo, basta pegar uma referência do container de serviços da aplicação, como é feito no pimple:

```php
$servico = $this->app['cadastro.store.calculo_salario'];
```

É possível acessar qualquer serviço criado, a partir de qualquer pacote, controlador ou serviço, simplesmente usando 
a anotação de namespace de serviços com três partes. Onde a primeira parte identifica o pacote, a segunda, o diretório 
do pacote (Command, Controller, Service, Store) e a terceira o nome da classe em <destak>snake_case</destak>.

## Stores

Classes Stores são a camada de acesso ao banco de dados da sua aplicação. Cada store, pode representar uma tabela, e 
disponibiliza uma API de métodos que estarão disponíveis para serviços e controladores que executam consultas e 
manipulações específicas no banco de dados retornando resultados, alterando ou manipulando registros.

### Criando um Store

Para criar um store, você pode utilizar o Singular Cli:

```shell
./singular backend:create-store Store Pacote tabela
```

!!! Onde
    <destak>Store</destak> deve ser substituído pelo nome da classe do store<br>
    <destak>Pacote</destak> deve ser substituído pelo nome do pacote onde você deseja armazenar o novo controlador<br>
    <destak>tabela</destak> nome da tabela no banco de dados vinculada ao store

### Métodos do Store

Todo store, estende a classe <destak>Singular\SingularStore</destak>, com isso ela herda alguns métodos utilitários do 
da classe mãe:

!!! note "find($id: integer): array"
    O método find localiza um registro na tabela do banco de dados pelo seu <destak>id</destak>.
    > __PARÂMETROS__:<br>
       > <span>$id</span> Chave primária de localização do registro<br>
    > __RETORNO__:<br>
       > <span>array</span> representando um registro único da tabela
          
          
Exemplo: 

```php
$usuario = $app['cadastro.store.usuario']->find(1);
```

!!! note "findOneBy($filters: array):array"
    Localiza um registro de uma tabela através de um <span>array de filtros</span> aplicados nessa tabela. 
    > __PARÂMETROS__: <br>
      > <span>$filters</span> array de filtros a serem aplicados na consulta<br>
    > __RETORNO__:<br> 
      > <span>array</span> representando um registro único da tabela.
   
Exemplo: 
      
```php
$usuario = $app['cadastro.store.usuario']->findBy([
      'login' => '%:Otavio', 
      'email' => 'otavio@neton.com.br'
]);
```

!!! note "findBy($filters: array, $pageOpts: array, $sort: array):array" 
    Localiza um conjunto de registros que combinam com um grupo de filtros aplicado na tabela. 
    > __PARÂMETROS__:<br>
      > <span>$filters</span> array de filtros a serem aplicados na consulta<br>
      > <span>$pageOpts</span> array de configuração da paginação dos resultados<br>
      > <span>$sort</span> array de configuração da ordenação dos resultados<br>
    > __RETORNO__: <br>
      > <span>array</span> de resultados representando 1 ou mais registros retornados pela consulta
     
Exemplo: 
      
```php
$usuarios = $app['cadastro.store.usuario']->findBy(
     ['email' => '%:gmail.com'],
     ['start' => 0,'limit'=> 5], 
     ['nome' => 'asc']
);
```

!!! note "save($record:array):integer"
    Cria/atualiza um registro na tabela e retorna o seu <span>id</span>
    > __PARÂMETROS__:<br>
      > <span>$record</span> array representando o registro a ser salvo na tabela<br>
    > __RETORNO__:<br>
      > <span>integer</span> id do registro inserido/atualizado na tabela.

Exemplo:
       
```php
$usuario = ['nome' => 'Otávio', 'login' => 'otavio', 'senha'=> '123];
$id = $app['cadastro.store.usuario']->save($usuario);
```

!!! note "remove($id: integer): boolean"
    Remove um registro da tabela através do seu <span>id</span>
    > __PARÂMETROS__:<br>
      > <span>$id</span> inteiro representando o id do registro que se deseja remover<br>
    > __RETORNO__:<br>
      > <span>boolean</span> true se o registro foi excluído e false se não foi possível excluir o registro

Exemplo:
      
```php
$excluido = $app['cadastro.store.usuario']->remove(1);
```

!!! note "removeBy($filter: array): boolean"
    Remove um ou mais registros da tabela através da combinação de filtros aplicados na tabela.
    > __PARÂMETROS__:<br>
      > <span>$filter</span> array de filtros a serem aplicados na consulta de exclusão<br>
    > __RETORNO__:<br>
      > <span>boolean</span> true se o registro foi excluído e false se não foi possível excluir o registro
      
Exemplo:      

```php
$excluido = $app['cadastro.store.usuario']->removeBy(['email' => '%:gmail.com']);
```

### Operadores de filtros

Os métodos <destak>findOneBy</destak>, <destak>findBy</destak> e <destak>removeBy</destak> recebem como parâmetro uma
propriedade <destak>$filter</destak> que é um array de filtros a serem aplicados nas consultas.

Por padrão, um filtro desse array aplica o operador de igualdade. Ou seja, sempre que o operador é omitido, é aplicado
o operador de igualdade. Mas é possível adicionar outros operadores utilizando a sintaxe <destak>operador</destak> <destak>:</destak> <destak>valor</destak>

Exemplo:

```php

['idade' => '>=:10']
```  

Os seguintes operadores estão disponíveis

| Operador | Descrição | Comportamento 
|----------|-----------|---------------
| =        |  Igualdade| Filtra o valor exatamente igual
| >        | Maior que | Filtra valor maior que
| >=       | Maior igual que | Filtra valor maior igual que
| <        | Menor que | Filtra valor menor que
| <=       | Menor igual que | Filtra valor menor igual que 
| %        | Contém | Filtra valor contendo uma parte do texto
| in       | Entre     | Filtra valor dentro de uma lista de valores
| notin    | Não entre | Filtra valor que não está dentro de uma lista de valores
| isnull   | Nulo      | Filtra valor que é nulo
| isnotnull| Não nulo  | Filtra valor que não é nulo

Exemplo de uso dos operadores:

```php
  $filter = [
    'idade'    => '25',   // onde a idade é igual a 25
    'id'       => '>:10', // id maior que 10
    'numero'   => '>=30', // número maior igual a 30
    'registro' => '<20',  // registro menor que 20
    'contador' => '<=90', // contador menor igual a 90
    'nome'     => '%joão',// nome contém joão
    'status'   => 'in:2,3,4', // status entre (2,3,4)
    'pending'  => 'notin:1,2', // pendind não está entre (1,2)
    'deleted'  => 'isnull', // deleted é nulo
    'active'   => 'isnotnull', // active não é nulo
  ]
```
### Conexão de dados

Todo store utiliza o <destak>Doctrine Database Abstraction Layer</destak> <destak>DBAL</destak> para se conectar ao banco 
de dados e executar as operações de consulta e manipulação de registros. 

Por padrão, um store utiliza a conexão <destak>default</destak> do __DBAL__. Mas é possível configurar um store para 
utilizar outra conexão, caso mais de uma conexão tenham sido definidas no registro do <destak>DoctrineServiceProvider</destak>.

Para utilizar uma outra conexão, basta definir a propriedade <destak>$conn<destak> do store:

```php
class MeuStore extends Singular\SingularStore
{
   protected $conn = "mssql_leitura";
   
   protected $table = "tabela1";
}
```

### Identificador único da tabela

Assim como toda tabela possui uma chave primária, um store também possui um identificador único que representa essa 
chave primária da tabela. Por padrão, um identificador de um store procura pelo campo <destak>id</destak> da tabela. 
Entretanto, esse campo pode ser alterado, caso a chave primária da tabela com a qual você estiver trabalhando não se 
chame id.

Para alterar o nome do campo que representa a chave primária, basta alterar a propriedade <destak>$id</destak> do seu 
store.

```php
class MeuStore extends Singular\SingularStore
{
   protected $table = "tabela1";
   
   protected $id = "minhachave"
}
```
### Sequences

Alguns gerenciadores de banco de dados, utilizam <destak>sequences</destak> para substituir o recurso de <destak>
autoincrement keys</desak>. 

Para trabalhar com sequences no store, basta definir a propriedade <destak>$sequence</destak> do store.

```php
class MeuStore extends Singular\SingularStore
{
   protected $table = "tabela1";
   
   protected $sequence = "meustore_sequence"
}
```

### SoftDelete

2.1

### Registro automático de criação

2.1

### Registro automático de atualização

2.1

### Perfis de consulta

Todas as funções de consulta herdadas do SingularStore, por padrão, retornam determinados campos, aplicando joins, 
groupings e filtros padrão. Isso é ótimo, pois evita a criação de consultas complexas desnecessárias agilizando o 
processo. Entretanto, pode haver a necessidade de trazer informações diferentes, com filtros e joins diferentes, 
dependendo do contexto. A versão 2 do Singular implementa uma funcionalidade chamada <destak>Perfis de consulta (profiles)</destak>
que permite criar vários perfis de consulta (campos do select, filtros, joins, groupings), dessa forma é possível de 
acordo com o contexto, selecionar um ou outro perfil padrão, eliminando ainda mais a necessidade de criar querys 
complexas para atividades triviais.

Quando você cria um novo store, por padrão ele já define o padrão <destak>default</destak> de consulta:

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

Um perfil de consulta nada mais é que um array de arrays estruturados. Para criar um novo perfil, basta criar um 
elemento na propriedade <destak>$profiles</destak> atribuindo-lhe um nome e igualando seu valor a um outro array com as 
propriedades:

<destak>select</destak> Array com a lista dos campos que serão selecionadas, por padrão o alias da tabela vinculada ao store é __t__<br>

```php
    [
        'select'=> ['t.nome','t.id as codigo']
    ]
```
    
<destak>joins</destak> um array de definição de um relacionamento com outra tabela. Para cada elemento do join é necessário
 definir:

+ __Primeiro parâmetro__:<destak>obrigatório</destak> nome da tabela relacionada 
+ __Segundo parâmetro__:<destak>obrigatório</destak> o alias da nova tabela
+ __Terceiro parâmetro__:<destak>obrigatório</destak>  a condição de igualdade do relacionamento 
+ __Quarto parâmetro__: <destak>opcional</destak> o tipo de relacionamento (left ou join), se for omitido assume o valor 'join'.

```php
[
   'joins' => [
      ['perfil','p','p.id = u.perfil_id', 'left']
   ]
]
```

<destak>filters</destak> um array de definição dos filtros padrão a serem aplicados na consulta.

```php
[
   'filters' => [
      'ativo' => '1'
   ]
]
```

<destak>groupings</destak> um array de definição dos agrupamentos padrão aplicados na consulta

```php
[
   'grouping' => [
      't.id'
   ]
]
```

Para fazer uma consulta utilizando um perfil de consulta, basta acionar o método <destak>setProfile</destak> antes de
 executar uma das funções (find, findBy, findOneBy).

```php
$app['cadastro.store.usuario']->setProfile('perfil')->findBy(['id' => '>:10']);
```

### Criando consultas customizadas

Embora os métodos <destak>findBy</destak> e <destak>findOneBy</destak> possam ser muito úteis e flexíveis através da 
utilização dos perfis de consulta, algumas vezes se faz necessário criar consultas customizadas e complexas para 
realizar tarefas específicas. Esses métodos podem ser facilmente criados nas classes store. 

A classe store, possui uma propriedade <destak>$db</destak> que fornece acesso à classe de conexão <destak>DBAL</destak>
 do Doctrine para o Silex. Para facilitar a criação de consultas, e ter consultas que sejam mais legíveis, é 
 extremamente importante utilizar o <destak>QueryBuilder</destak> do Doctrine. Para recuperar uma nova instância do 
 query builder, basta:

```php
$qb = $this->db->createQueryBuilder();
```

Ao criar uma consulta customizada, geralmente, precisamos passar filtros para essa consulta. Por questões de segurança,
 __SEMPRE__ utilize a vinculação de parâmetros para definir filtros. __NÃO UTILIZE__ a vinculação da entrada do usuário
diretamente na montagem da sua consulta. Vamos ver uma aplicação de certo e errado:

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
