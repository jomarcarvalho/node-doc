# Sinopse

<!--type=misc-->

Um exemplo de um [Servidor Web](http.html) escrito em Node .js que responde com 'Olá
Mundo':

    var http = require('http');

    http.createServer(function (request, response) {
      response.writeHead(200, {'Content-Type': 'text/plain'});
      response.end('Hello World\n');
    }).listen(8124);

    console.log('Server running at http://127.0.0.1:8124/');

Para executar o servidor, colocar o código em um arquivo chamado `example.js` e executar-lo com o node

    > node example.js
    Server running at http://127.0.0.1:8124/

Todos os exemplos na documentação podem ser executados da mesma forma usada no exemplo.
