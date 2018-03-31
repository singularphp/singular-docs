## Alterar o tamanho da sidebar

Para alterar o tamanho da sidebar da interface de usuário:

### Como fazer

1. Altere o valor da propriedade __@app-aside-width__ no arquivo _web/assets/less/app.variables.less_ do seu projeto;
2. Apague o arquivo __app_less.build.css__ no diretório _web/deploy/_ do seu projeto;
3. Recarregue o sistema no seu navegador.

### Exemplo

```less
@app-aside-width:                310px;

```