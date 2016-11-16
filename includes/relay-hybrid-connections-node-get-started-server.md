### <a name="create-a-nodejs-application"></a>Criar um aplicativo do Node.js
* Crie um novo arquivo JavaScript chamado `listener.js`.

### <a name="add-the-relay-npm-package"></a>Adicionar o pacote NPM de retransmissão
* Execute `npm install hyco-ws` em um prompt de comando do Node na pasta do projeto.

### <a name="write-some-code-to-receive-messages"></a>Escrever código para receber mensagens
1. Adicione a `constant` a seguir à parte superior do arquivo `listener.js`.
   
    ```js
    const WebSocket = require('hyco-ws');
    ```
2. Adicione a retransmissão `constants` a seguir a `listener.js` para os detalhes da Conexão Híbrida. Substitua os espaços reservados nos colchetes pelos valores adequados que foram obtidos na criação da Conexão Híbrida.
   
   1. `const ns` - o namespace de retransmissão
   2. `const path` - o nome da Conexão Híbrida
   3. `const keyrule` - o nome da chave SAS
   4. `const key` - o valor da chave SAS
3. Adicione o seguinte código ao arquivo `listener.js`:
   
    ```js
    var wss = WebSocket.createRelayedServer(
    {
        server : WebSocket.createRelayListenUri(ns, path),
        token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(event.data);
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```
    É assim que deve ser o seu listener.js:
   
    ```js
    const WebSocket = require('hyco-ws');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var wss = WebSocket.createRelayedServer(
        {
            server : WebSocket.createRelayListenUri(ns, path),
            token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
        }, 
        function (ws) {
            console.log('connection accepted');
            ws.onmessage = function (event) {
                console.log(event.data);
            };
            ws.on('close', function () {
                console.log('connection closed');
            });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```



<!--HONumber=Nov16_HO2-->


