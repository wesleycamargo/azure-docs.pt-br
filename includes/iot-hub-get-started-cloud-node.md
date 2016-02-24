## Criar uma identidade do dispositivo

Nesta seção, você cria um aplicativo do console do Node.js que cria uma nova identidade do dispositivo no registro de identidade em seu hub IoT. Um dispositivo não pode se conectar ao Hub IoT, a menos que ele tenha uma entrada no registro de identidade do dispositivo. Veja a seção **Registro de identidade do dispositivo** do [Guia do Desenvolvedor do Hub IoT][lnk-devguide-identity] para obter mais informações. Quando você executa esse aplicativo de console, ele gera uma ID e uma chave de dispositivo exclusivas com que seu dispositivo poderá se identificar ao enviar mensagens de dispositivo para a nuvem para o Hub IoT.

1. Crie uma nova pasta vazia chamada **createdeviceidentity**. Na pasta **createdeviceidentity**, crie um novo arquivo package.json usando o comando a seguir no prompt de comando. Aceite todos os padrões:

    ```
    npm init
    ```

2. No prompt de comando na pasta **createdeviceidentity**, execute o seguinte comando para instalar o pacote **iothub azure**:

    ```
    npm install azure-iothub --save
    ```

3. Usando um editor de texto, crie um novo arquivo **CreateDeviceIdentity.js** na pasta **createdeviceidentity**.

4. Adicione a seguinte instrução `require` no início do arquivo **CreateDeviceIdentity.js**:

    ```
    'use strict';
    
    var iothub = require('azure-iothub');
    ```

5. Adicione o seguinte código ao arquivo **CreateDeviceIdentity.js**, substituindo o valor de espaço reservado pela cadeia de conexão para o hub IoT que você criou na seção anterior:

    ```
    var connectionString = '{iothub connection string}';
    
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. Adicione o código a seguir para criar uma nova definição de dispositivo no registro de identidade de dispositivo no hub IoT. Esse código criará um novo dispositivo se a ID do dispositivo não existir no registro. Caso contrário, ele retornará a chave do dispositivo existente:

    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstDevice';
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });

    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device id: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.SymmetricKey.primaryKey);
      }
    }
    ```

7. Salve e feche o arquivo **CreateDeviceIdentity.js**.

8. Para executar o aplicativo **createdeviceidentity**, execute o comando a seguir no prompt de comando na pasta createdeviceidentity:

    ```
    node CreateDeviceIdentity.js 
    ```

9. Anote a **Id do dispositivo** e a **Chave do dispositivo**. Você precisará delas mais tarde quando criar um aplicativo que se conecte ao Hub IoT como um dispositivo.

> [AZURE.NOTE] O registro de identidade do Hub IoT armazena apenas as identidades de dispositivo para habilitar o acesso seguro ao hub. Ele armazena as IDs e as chaves de dispositivo para usar como credenciais de segurança e um sinalizador habilitado/desabilitado que permite que você desabilite o acesso para um dispositivo individual. Se seu aplicativo precisar armazenar outros metadados específicos do dispositivo, ele deverá usar um repositório específico do aplicativo. Veja o [Guia do Desenvolvedor do Hub IoT][lnk-devguide-identity] para saber mais.

## Receber mensagens do dispositivo para a nuvem

Nesta seção, você cria um aplicativo do console do Node.js que lê mensagens do dispositivo para a nuvem do Hub IoT. Um hub IoT expõe um ponto de extremidade compatível com os [Hubs de Eventos][lnk-event-hubs-overview] para permitir que você leia mensagens do dispositivo para a nuvem. Para simplificar, este tutorial cria um leitor básico que não é adequado para uma implantação de alta taxa de transferência. O tutorial [Processar mensagens do dispositivo para a nuvem][lnk-processd2c-tutorial] mostra como processar mensagens do dispositivo para a nuvem em escala. O tutorial [Introdução aos Hubs de Eventos][lnk-eventhubs-tutorial] oferece informações adicionais sobre como processar mensagens de Hubs de Eventos e se aplica aos pontos de extremidade compatíveis com o Hub de Eventos Hub IoT.

1. Crie uma nova pasta vazia chamada **readdevicetocloudmessages**. Na pasta **readdevicetocloudmessages**, crie um novo arquivo package.json usando o comando a seguir no prompt de comando. Aceite todos os padrões:

    ```
    npm init
    ```

2. No prompt de comando na pasta **readdevicetocloudmessages**, execute o seguinte comando para instalar os pacotes **amqp10** e **bluebird**:

    ```
    npm install amqp10 bluebird --save
    ```

3. Usando um editor de texto, crie um novo arquivo **ReadDeviceToCloudMessages.js** na pasta **readdevicetocloudmessages**.

4. Adicione as seguintes instruções `require` no início do arquivo **ReadDeviceToCloudMessages.js**:

    ```
    'use strict';

    var AMQPClient = require('amqp10').Client;
    var Policy = require('amqp10').Policy;
    var translator = require('amqp10').translator;
    var Promise = require('bluebird');
    ```

5. Adicione as declarações de variáveis a seguir, substituindo os espaços reservados pelos valores que você anotou anteriormente. O valor do espaço reservado **{your event hub-compatible namespace}** é proveniente do **ponto de extremidade compatível com o Hub de Eventos**: ele tem o formato **xxxxnamespace.servicebus.windows.net**.

    ```
    var protocol = 'amqps';
    var eventHubHost = '{your event hub-compatible namespace}';
    var sasName = 'iothubowner';
    var sasKey = '{your iot hub key}';
    var eventHubName = '{your event hub-compatible name}';
    var numPartitions = 2;
    ```

    > [AZURE.NOTE] Esse código pressupõe que você tenha criado seu hub IoT na camada F1 (gratuita). Um hub IoT gratuito tem duas partições chamadas "0" e "1". Se você tiver criado o hub IoT usando um dos outros tipos de preços, deverá ajustar o código para criar um **MessageReceiver** para cada partição.

6. Adicione a definição de filtro a seguir. Esse aplicativo usa um filtro ao criar um receptor para que o receptor leia apenas as mensagens enviadas para o Hub IoT após o início da execução do receptor. Isso será útil em um ambiente de teste para que você possa ver o conjunto de mensagens atual, mas, em um ambiente de produção, seu código deverá garantir o processamento de todas as mensagens - confira o tutorial [Como processar mensagens do dispositivo para a nuvem do Hub IoT][lnk-processd2c-tutorial] para saber mais.

    ```
    var filterOffset = new Date().getTime();
    var filterOption;
    if (filterOffset) {
      filterOption = {
      attach: { source: { filter: {
      'apache.org:selector-filter:string': translator(
        ['described', ['symbol', 'apache.org:selector-filter:string'], ['string', "amqp.annotation.x-opt-enqueuedtimeutc > " + filterOffset + ""]])
        } } }
      };
    }
    ```

7. Adicione o seguinte código para criar o endereço de recebimento e um cliente do AMQP:

    ```
    var uri = protocol + '://' + encodeURIComponent(sasName) + ':' + encodeURIComponent(sasKey) + '@' + eventHubHost;
    var recvAddr = eventHubName + '/ConsumerGroups/$default/Partitions/';
    
    var client = new AMQPClient(Policy.EventHub);
    ```

8. Adicione as duas seguintes funções que imprimem a saída no console:

    ```
    var messageHandler = function (partitionId, message) {
      console.log('Received(' + partitionId + '): ', message.body);
    };
    
    var errorHandler = function(partitionId, err) {
      console.warn('** Receive error: ', err);
    };
    ```

9. Adicione a seguinte função que age como um receptor para determinada partição usando o filtro:

    ```
    var createPartitionReceiver = function(partitionId, receiveAddress, filterOption) {
      return client.createReceiver(receiveAddress, filterOption)
        .then(function (receiver) {
          console.log('Listening on partition: ' + partitionId);
          receiver.on('message', messageHandler.bind(null, partitionId));
          receiver.on('errorReceived', errorHandler.bind(null, partitionId));
        });
    };
    ```

10. Adicione o seguinte código para se conectar ao ponto de extremidade compatível com o Hub de Eventos e iniciar os receptores:

    ```
    client.connect(uri)
      .then(function () {
        var partitions = [];
        for (var i = 0; i < numPartitions; ++i) {
          partitions.push(createPartitionReceiver(i, recvAddr + i, filterOption));
        }
        return Promise.all(partitions);
    })
    .error(function (e) {
        console.warn('Connection error: ', e);
    });
    ```

11. Salve e feche o arquivo **ReadDeviceToCloudMessages.js**.

<!-- Links -->

[lnk-eventhubs-tutorial]: event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: event-hubs-overview.md
[lnk-processd2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

<!---HONumber=AcomDC_0128_2016-->