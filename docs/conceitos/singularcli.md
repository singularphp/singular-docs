Um projeto criado com o Singular Project inclui o <destak>Singular Command Line Interface</destak>. O <destak>Singular
Cli</destak> é uma ferramenta utilitária que automatiza muitas tarefas no processo de desenvolvimento de aplicações.

### Comandos disponíveis

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
