## Instalação

### Pré-requisitos

Antes de iniciar, certifique-se de que seu ambiente possui instalados os seguintes pré-requisitos:

+ PHP >= 5.6;
+ MySQL >= 5.6;
+ Composer;

### Criando um novo projeto


Para criar um novo projeto com o singular-project, basta executar o comando:

```shell
composer create-project singular/singular-project nomedoprojeto --stability=dev
```

!!! Onde: 
    + <span>nomedoprojeto</span> deverá ser substituído pelo nome do seu projeto.
    + Ao utilizar a opção <span>--stability=dev</span>, você estará garantindo que seu projeto irá conter todas as atualizações mais recentes do Singular Framework.

---

## Estrutura do projeto

Após criar o projeto, será criado um diretório nomeado com o <destak>nomedoprojeto</destak> com a seguinte
estrutura de arquivos:

```shell
> app
> db
> src
> vendor
> views
> web
  composer.json
  composer.lock
  phinx.yml
  README.md
  singular  
```

### O Diretório app

O diretório <destak>app</destak> é onde estão armazenados todos os arquivos de configuração e inicialização do projeto. 
Ele contém 6 subdiretórios.

+ __config__: Diretório onde são armazenados todos os arquivos de configuração do projeto;
+ __filters__: Diretório onde são armazenados os [middlewares](https://silex.symfony.com/doc/2.0/middlewares.html) da aplicação;
+ __packs__: Diretório onde são armazenados os provedores de serviço dos pacotes habilitados na aplicação;
+ __providers__: Diretório onde são armazenados os [provedores de serviço de terceiros](https://silex.symfony.com/doc/2.0/providers.html);
+ __services__: Diretório onde são armazenados os scritps de definição de [serviços](https://silex.symfony.com/doc/2.0/services.html) globais da aplicação;
+ __tmp__: Diretório onde são armazenados os arquivos temporários de log e gerenciamento de sessão da aplicação;

### Diretório db

Para gerenciar as migrações de banco de dados da aplicação, o Singular Project utiliza o [Phinx](https://phinx.org). 

O diretório <destak>db</destak> é onde estão armazenadas as migrations do projeto, dentro do subdiretório <destak>migrations</destak>. 
Esse diretório também poderá vir a armazenar os arquivos de <destak>seed</destak> para testes no banco de dados. 
Entretanto, para alimentar o banco de dados com registros necessários ao projeto, deverão ser utilizadas as migrations 
convencionais através dos métodos <destak>up</destak> e <destak>down</destak>.

Quando um novo projeto é criado, algumas migrations já encontram-se presentes neste diretório, à frente veremos como 
executá-las para poder começar a utilizar o projeto funcional do singular project.

### Diretório src

O diretório <destak>src</destak> é onde encontram-se armazenados os scripts de código fonte php do projeto. 
Cada pacote criado para a aplicação, deverá ter um subdiretório dentro do diretório src. 
Mais adiante veremos como criar um novo pacote e como um pacote encontra-se estruturado. 

### Diretório vendor

O diretório <destak>vendor</destak> é criado automaticamente pelo [Composer](https://getcomposer.org/doc/00-intro.md), 
gerenciador de pacotes e dependências php da aplicação.

### Diretório views

O diretório <destak>views</destak> é onde são armazenados os scripts html que são os templates [Twig](http://twig.symfony.com) 
das páginas do projeto. Esse diretório já possui dois arquivos principais de templates:

+ <destak>auth.html</destak> Template da view exibida quando o usuário ainda não está logado na aplicação;
+ <destak>secure.html</destak> Template da view exibida quando o usuário já está logado no sistema;

Poderão ser criadas tantos templates quanto necessário, por exemplo, podem ser criados templates html para criação 
de relatórios pdf.

Visto que o Singular Framework emprega 100% do conceito [AJAX](https://pt.wikipedia.org/wiki/AJAX_(programa%C3%A7%C3%A3o)``) 
através do [AngularJS](https://angularjs.org/), o diretório views, não deverá ser utilizado para armazenar templates 
de interface dos módulos da aplicação, mas templates que fornecem renderização aos módulos do AngularJS.

### Diretório web

O diretório web é onde encontram-se armazenados todos os scripts de front-end da aplicação. É nele que você irá criar 
seus scripts javascript de módulos, controladores, serviços e templates do AngularJS. 

Este diretório contém:

+ <destak>assets</destak> diretório onde são armazenados scripts css, fontes, imagens e bibliotecas front-end de 
terceiros;
+ <destak>src</destak> diretório onde são armazenados os scripts de código fonte javascript do seu projeto;
+ <destak>holder.js</destak> script javascript que gera imagens placeholders, que podem ser utilizados em telas e 
interfaces do seu projeto;
+ <destak>index.php</destak> script php principal da aplicação. Ele é o ponto de entrada da sua aplicação, nele a 
sua aplicação é iniciada e todas as chamadas, AJAX ou convencionais passam por este script.

### Arquivo composer.json

Arquivo que contém a relação das dependências das bibliotecas php do projeto. 

### Arquivo phinx.yml

Como mencionado anteriormente, o projeto baseado no Singular utiliza o Phinx como gerenciador  de migrations do banco 
de dados da aplicação. O Phinx por sua vez, é configurado através deste arquivo.

### Arquivo singular

O Singular project utiliza o Singular Command para fornecer tarefas de linha de comando que automatizam a maioria 
dos processos repetitivos do desenvolvimento de um projeto. Mais adiante iremos ver em detalhes como utilizar essa 
ferramenta e quais comandos estão disponíveis para utilização.

___

## Configuração

### Configurando o Virtual host

Para que seu projeto rode de forma simples na sua máquina, é necessário primeiramente configurar um virtual host no 
servidor web. Na Net On, utilizamos o <destak>MAMP</destak> em nosso ambiente de desenvolvimento, por isso, vamos 
ver como configurar o virtual host para este ambiente. 

A utilização do virtual host permitirá que você realize seus testes durante o desenvolvimento de forma muito mais 
fácil, ao invés de acessar no navegador um endereço como [http://localhost/nomedoprojeto/web]() podemos fazer algo do 
tipo [http://nomedoprojeto.local/](#)

#### 1. Crie uma entrada no arquivo hosts

Pelo terminal da sua máquina, abra o arquivo /etc/host

```shell
sudo vim /etc/hosts
```
Adicione a seguinte linha no arquivo, lembrando sempre de trocar o <destak>nomedoprojeto</destak> pelo nome do seu 
projeto.

```shell
...
127.0.0.1 sng.local # trocar sng pelo nome do seu projeto
...
```
Salve e feche o arquivo.

#### 2. Crie o arquivo de configuração do virtual host no MAMP

Abra o arquivo de configurações de virtual hosts pelo terminal:

```shell
sudo vim /Applications/MAMP/conf/apache/extra/httpd-vhosts.conf 
```
E adicione o seguinte bloco de código ao final do arquivo:

```shell
<VirtualHost *:80>
    DocumentRoot "/htdocs/sng/web" # trocar pelo caminho do diretório web completo do seu projeto
    ServerName sng.local # trocar sng pelo nome do seu projeto
</VirtualHost>
```
Salve e feche o seu arquivo e, em seguida, reinicie o serviço do apache do seu MAMP. Agora, deverá ser possível 
acessar o projeto através do seu navegador web. Para isso basta digitar na barra de endereços [http://sng.local/](). 
Se você se deparar com alguma mensagem de erro relacionado à permissão em diretórios, basta conceder as permissões 
de escrita no diretório e tentar novamente. 

Agora você deve visualizar em seu navegador uma tela semelhante a esta:
![Tela de login do Singular](http://www.neton.com.br/singular/login.png)
Entretanto, ainda não é possível acessar o sistema nem mesmo começar a desenvolver seus próprios módulos. Para isso, 
precisamos seguir com a configuração.

### Criando o banco de dados

No seu SGBD, crie um novo banco de dados banco de dados com o nome definido para a base de dados do seu projeto.

### Configurando o PHINX

Para gerenciar as migrations de banco de dados, o Singular Project utiliza o Phinx. Ele já está incluído no projeto 
como dependência, no entanto, é necessário configurá-lo. 

Abra o arquivo <destak>phinx.yml</destak> localizado no diretório raiz do seu projeto e, em seguida:

+ Duplique o bloco de configuração <destak>base</destak> e altere o nome desse novo bloco para <destak>dev-seunome</destak> 
substituíndo o <destak>seunome</destak> pelo seu primeiro nome, sem acentuação e todo em minúsculo. 

+ No seu bloco de configuração, altere as configurações de endereço da base de dados, senha, usuário e nome da base 
de dados;

O resultado final deverá ser algo como:

```yml
...
 dev-otavio:
        adapter: mysql
        host: localhost
        name: sng
        user: root
        pass: 'root'
        port: 3306
        charset: utf8

```

Salve e feche o arquivo. Agora é necessário configurar o ambiente padrão de execução do Phinx no seu ambiente. 
Abra o seu terminal e digite o seguinte código:

```shell
 export PHINX_ENVIRONMENT=dev-seunome # substituindo seunome pelo nome do bloco que você criou.
```
Finalmente, pelo terminal, dentro da pasta do seu projeto, execute o comando:

```shell
vendor/bin/phinx status
```
O resultado deverá ser algo semelhante a:

```shell
 Status  [Migration ID]  Started              Finished             Migration Name 
----------------------------------------------------------------------------------
   down  20170630140758                                            CriaTabelaSingularAplicacao
   down  20170630144050                                            CriaTabelaSingularModulo
   down  20170701171751                                            InsereRegistrosIniciaisSingular
   down  20170701173743                                            CriaTabelaSingularPerfil
   down  20170701174500                                            CriaTabelaSingularComponente
   down  20170701175748                                            CriaTabelaSingularPermissao
   down  20170703224706                                            CriaTabelaSingularUsuario
   down  20170827183316                                            InsereRegistroPerfilUsuario
   down  20170918230051                                            InsereRegistroModuloUsuario
   down  20171002180438                                            InsereRegistroModuloComponente
   down  20171004103414                                            InsereRegistrosComponentes1507124054
   down  20171006084220                                            InsereRegistrosComponentes1507290140
```
Por hora, é o suficiente, ainda não iremos executar as migrations.

### Configurando o banco de dados

Crie uma cópia do arquivo [app/config/database.json]() e a nomeie de [app/config/local.json]() dentro do mesmo diretório. 
Agora, altere as configurações do banco de dados neste arquivo de acordo com seu ambiente e adicione a propriedade 
<destak>priority</destak> com o valor 100. O resultado final será semelhante a:

```json
{
  "_priority": 100,
  "db.driver": "mysql",
  "db.name": "sng", 
  "db.host": "localhost",
  "db.user": "root",
  "db.pass": "root",
  "db.charset": "utf8",
  "db.collation": "utf8_unicode_ci"
}
```

### Inicializando a aplicação

Pronto, agora, todo o ambiente está pronto para iniciar o desenvolvimento, vamos então executar nossas migrations e 
começar a trabalhar. Para isso, no terminal, digite:

```shell
vendor/bin/phinx migrate
```

Se tudo correu bem, o resultado será parecido com:

```shell

neton-alpha:sng oaugustus$ vendor/bin/phinx migrate
Phinx by Rob Morgan - https://phinx.org. 0.8.1

using config file ./phinx.yml
using config parser yaml
using migration paths 
 - /htdocs/sng/db/migrations
using seed paths 
warning no environment specified, defaulting to: dev-otavio
using adapter mysql
using database sng

 == 20170630140758 CriaTabelaSingularAplicacao: migrating
 == 20170630140758 CriaTabelaSingularAplicacao: migrated 0.0215s

 == 20170630144050 CriaTabelaSingularModulo: migrating
 == 20170630144050 CriaTabelaSingularModulo: migrated 0.0217s

 == 20170701171751 InsereRegistrosIniciaisSingular: migrating
 == 20170701171751 InsereRegistrosIniciaisSingular: migrated 0.0053s

 == 20170701173743 CriaTabelaSingularPerfil: migrating
 == 20170701173743 CriaTabelaSingularPerfil: migrated 0.0196s

 == 20170701174500 CriaTabelaSingularComponente: migrating
 == 20170701174500 CriaTabelaSingularComponente: migrated 0.0180s

 == 20170701175748 CriaTabelaSingularPermissao: migrating
 == 20170701175748 CriaTabelaSingularPermissao: migrated 0.0169s

 == 20170703224706 CriaTabelaSingularUsuario: migrating
 == 20170703224706 CriaTabelaSingularUsuario: migrated 0.0212s

 == 20170827183316 InsereRegistroPerfilUsuario: migrating
 == 20170827183316 InsereRegistroPerfilUsuario: migrated 0.0049s

 == 20170918230051 InsereRegistroModuloUsuario: migrating
 == 20170918230051 InsereRegistroModuloUsuario: migrated 0.0032s

 == 20171002180438 InsereRegistroModuloComponente: migrating
 == 20171002180438 InsereRegistroModuloComponente: migrated 0.0023s

 == 20171004103414 InsereRegistrosComponentes1507124054: migrating
 == 20171004103414 InsereRegistrosComponentes1507124054: migrated 0.0034s

 == 20171006084220 InsereRegistrosComponentes1507290140: migrating
 == 20171006084220 InsereRegistrosComponentes1507290140: migrated 0.0047s

All Done. Took 0.1754s

```
Ainda no terminal, digite o comando:

```shell
./singular component:grant-full-access 1
```
E a mensagem:

```shell
Acesso garantido para o perfil 1!
```
Irá aparecer. Agora, podemos acessar nosso novo sistema. Acesse a tela de login pelo endereço [http://nomeprojeto.local/]() 
e digite o usuário e senha de acesso:

+ <destak>Login</destak>: singular
+ <destak>Senha</destak>: singular

Você será autenticado no sistema e redirecionado para o acesso restrito. A interface agora será a seguinte:

![Interface da aplicação](http://www.neton.com.br/singular/secure.png) 

Perfeito! Agora podemos começar a codificar nosso novo sistema. No restante da documentação iremos aprender a criar 
módulos, adicionar funcionalidades, restringir o acesso de perfis de usuário, e a utilizar as classes utilitárias para 
agilizar nosso serviço de desenvolvimento.
