### <a name="create-a-nodejs-application"></a>Criar um aplicativo do Node.js
* Crie um novo arquivo JavaScript chamado `sender.js`.

### <a name="add-the-relay-npm-package"></a>Adicionar o pacote NPM de retransmissão
* Execute `npm install hyco-ws` em um prompt de comando do Node na pasta do projeto.

### <a name="write-some-code-to-send-messages"></a>Escrever código para enviar mensagens
1. Adicione a `constants` a seguir à parte superior do arquivo `sender.js`.
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
    ```
2. Adicione a retransmissão `constants` a seguir a `sender.js` para os detalhes da Conexão Híbrida. Substitua os espaços reservados nos colchetes pelos valores adequados que foram obtidos na criação da Conexão Híbrida.
   
   1. `const ns` - o namespace de retransmissão
   2. `const path` - o nome da Conexão Híbrida
   3. `const keyrule` - o nome da chave SAS
   4. `const key` - o valor da chave SAS
3. Adicione o seguinte código ao arquivo `sender.js`:
   
    ```js
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```
    É assim que deve ser o seu listener.js:
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```



<!--HONumber=Nov16_HO2-->


