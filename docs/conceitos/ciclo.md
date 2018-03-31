O Singular Framework, foi projetado para ser um framework Ajax, ou seja, à exceção das views principais (autenticação e 
acesso seguro, além das que se fizerem necessárias), todo o front-end é codificado em HTML, CSS e JavaScript, com apoio 
do framework AngularJS. Sempre que é necessário um dado que venha do servidor (backend) é feita uma requisição 
assíncrona Ajax para uma classe em PHP, que por sua vez, após realizar o processamento necessário, retorna a resposta 
para o frontend no formato JSON.

Por este motivo, todo o ciclo de desenvolvimento se divide basicamente em dois ambientes: Backend e Frontend.
___
