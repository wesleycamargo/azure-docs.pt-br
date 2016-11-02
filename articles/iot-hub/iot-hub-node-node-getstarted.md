<properties
    pageTitle="Introdução ao Hub IoT do Azure para Node.js | Microsoft Azure"
    description="Tutorial de introdução ao Hub IoT do Azure para Node.js. Use o Hub IoT do Azure e Node.js com SDKs de IoT do Microsoft Azure para implementar uma solução de Internet das Coisas."
    services="iot-hub"
    documentationCenter="nodejs"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/12/2016"
     ms.author="dobett"/>


# <a name="get-started-with-azure-iot-hub-for-node.js"></a>Introdução ao Hub IoT do Azure para Node.js

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

No fim deste tutorial, você tem três aplicativos de console do Node.js:

* **CreateDeviceIdentity.js**, que cria uma identidade do dispositivo e uma chave de segurança associada para conectar o dispositivo simulado.
* **ReadDeviceToCloudMessages.js**, que exibe a telemetria enviada pelo dispositivo simulado.
* **SimulatedDevice.js**, que se conecta ao Hub IoT com a identidade do dispositivo criada anteriormente e envia uma mensagem de telemetria a cada segundo usando o protocolo AMQPS.

> [AZURE.NOTE] O artigo [SDKs do Hub IoT][lnk-hub-sdks]fornece informações sobre vários SDKs que você pode usar para criar aplicativos para executar nos dispositivos e no back-end da solução.

Para concluir este tutorial, você precisará do seguinte:

+ Node.js versão 0.10.x ou posterior.

+ Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação Gratuita do Azure][lnk-free-trial].)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Você criou seu Hub IoT. Você tem o nome de host do Hub IoT e a cadeia de conexão do Hub IoT de que precisa para concluir o restante deste tutorial.

## <a name="create-a-device-identity"></a>Criar uma identidade do dispositivo

Nesta seção, você cria um aplicativo de console do Node.js que cria uma identidade do dispositivo no registro de identidade em seu hub IoT. Um dispositivo não pode se conectar ao Hub IoT, a menos que ele tenha uma entrada no registro de identidade do dispositivo. Consulte a seção **Registro de Identidade do Dispositivo** do [Guia do Desenvolvedor do Hub IoT][lnk-devguide-identity] para obter mais informações. Quando você executa esse aplicativo de console, ele gera uma ID e chave do dispositivo exclusivas com as quais seu dispositivo poderá identificar-se ao enviar mensagens entre o dispositivo e a nuvem para o Hub IoT.

1. Crie uma nova pasta vazia denominada **createdeviceidentity**. Na pasta **createdeviceidentity** , crie um arquivo package.json usando o comando a seguir no prompt de comando. Aceite todos os padrões:

    ```
    npm init
    ```

2. No prompt de comando na pasta **createdeviceidentity**, execute o seguinte comando para instalar o pacote SDK de Serviço **azure-iothub**:

    ```
    npm install azure-iothub --save
    ```

3. Usando um editor de texto, crie um arquivo **CreateDeviceIdentity.js** na pasta **createdeviceidentity**.

4. Adicione a seguinte instrução `require` no início do arquivo **CreateDeviceIdentity.js** :

    ```
    'use strict';
    
    var iothub = require('azure-iothub');
    ```

5. Adicione o seguinte código ao arquivo **CreateDeviceIdentity.js** e substitua o valor do espaço reservado pela cadeia de conexão para o hub IoT que você criou na seção anterior: 

    ```
    var connectionString = '{iothub connection string}';
    
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. Adicione o código a seguir para criar uma definição do dispositivo no registro de identidade do dispositivo no hub IoT. Esse código criará um dispositivo se a id do dispositivo não existir no registro. Caso contrário, retornará a chave do dispositivo existente:

    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstNodeDevice';
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

7. Salve e feche o arquivo **CreateDeviceIdentity.js** .

8. Para executar o aplicativo **createdeviceidentity** , execute o comando a seguir no prompt de comando na pasta createdeviceidentity:

    ```
    node CreateDeviceIdentity.js 
    ```

9. Anote a **Id do dispositivo** e a **Chave do dispositivo**. Você precisará desses valores mais tarde quando criar um aplicativo que conecta o Hub IoT como um dispositivo.

> [AZURE.NOTE] O registro de identidade do Hub IoT armazena apenas as identidades de dispositivo para habilitar o acesso seguro ao hub. Ele armazena as IDs e as chaves do dispositivo a usar como credenciais de segurança e um sinalizador habilitado/desabilitado que você poderá usar para desabilitar o acesso de um dispositivo individual. Se seu aplicativo precisar armazenar outros metadados específicos do dispositivo, ele deverá usar um repositório específico do aplicativo. Consulte o [Guia do Desenvolvedor do Hub IoT][lnk-devguide-identity] para obter mais informações.

## <a name="receive-device-to-cloud-messages"></a>Receber mensagens do dispositivo para a nuvem

Nesta seção, você cria um aplicativo de console do Node.js que lê as mensagens do dispositivo para a nuvem a partir do Hub IoT. Um hub IoT expõe um ponto de extremidade compatível com os [Hubs de Eventos][lnk-event-hubs-overview]para que você possa ler as mensagens do dispositivo para a nuvem. Para simplificar, este tutorial cria um leitor básico que não é adequado para uma implantação de alta taxa de transferência. O tutorial [Processar mensagens do dispositivo para a nuvem][lnk-process-d2c-tutorial] mostra como processar as mensagens do dispositivo para a nuvem em escala. O tutorial [Introdução aos Hubs de Eventos][lnk-eventhubs-tutorial] fornece informações adicionais sobre como processar as mensagens dos Hubs de Eventos e é aplicável aos pontos de extremidade compatíveis com os Hubs de Eventos do Hub IoT.

> [AZURE.NOTE] O ponto de extremidade compatível com os Hubs de Eventos para ler mensagens do dispositivo para a nuvem sempre usa o protocolo AMQPS.

1. Crie uma nova pasta vazia denominada **readdevicetocloudmessages**. Na pasta **readdevicetocloudmessages** , crie um arquivo package.json usando o comando a seguir no prompt de comando. Aceite todos os padrões:

    ```
    npm init
    ```

2. No prompt de comando na pasta **readdevicetocloudmessages**, execute o seguinte comando para instalar o pacote **azure-event-hubs**:

    ```
    npm install azure-event-hubs --save
    ```

3. Usando um editor de texto, crie um arquivo **ReadDeviceToCloudMessages.js** na pasta **readdevicetocloudmessages**.

4. Adicione as seguintes instruções `require` no início do arquivo **ReadDeviceToCloudMessages.js** :

    ```
    'use strict';

    var EventHubClient = require('azure-event-hubs').Client;
    ```

5. Adicione a seguinte declaração de variável e substitua o valor de espaço reservado pela cadeia de conexão do Hub IoT:

    ```
    var connectionString = '{iothub connection string}';
    ```

6. Adicione as duas seguintes funções que imprimem a saída no console:

    ```
    var printError = function (err) {
      console.log(err.message);
    };

    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```

7. Adicione o seguinte código para criar o **EventHubClient**, abra a conexão com o Hub IoT e crie um receptor para cada partição. Esse aplicativo usa um filtro ao criar um receptor para que o receptor leia apenas as mensagens enviadas para o Hub IoT após o início da execução do receptor. Esse filtro é útil em um ambiente de teste para ver apenas o conjunto atual de mensagens. Em um ambiente de produção, seu código deve verificar se ele processa todas as mensagens - consulte o tutorial [Como processar as mensagens do dispositivo para a nuvem do Hub IoT][lnk-process-d2c-tutorial] para obter mais informações:

    ```
    var client = EventHubClient.fromConnectionString(connectionString);
    client.open()
        .then(client.getPartitionIds.bind(client))
        .then(function (partitionIds) {
            return partitionIds.map(function (partitionId) {
                return client.createReceiver('$Default', partitionId, { 'startAfterTime' : Date.now()}).then(function(receiver) {
                    console.log('Created partition receiver: ' + partitionId)
                    receiver.on('errorReceived', printError);
                    receiver.on('message', printMessage);
                });
            });
        })
        .catch(printError);
    ```

8. Salve e feche o arquivo **ReadDeviceToCloudMessages.js** .

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado

Nesta seção, você cria um aplicativo de console do Node.js que simula um dispositivo que envia mensagens do dispositivo para a nuvem para um hub IoT.

1. Crie uma nova pasta vazia denominada **simulateddevice**. Na pasta **simulateddevice** , crie um arquivo package.json usando o comando a seguir no prompt de comando. Aceite todos os padrões:

    ```
    npm init
    ```

2. No prompt de comando na pasta **simulateddevice**, execute o seguinte comando para instalar o pacote SDK do Dispositivo **azure-iot-device** e o pacote **azure-iot-device-amqp**:

    ```
    npm install azure-iot-device azure-iot-device-amqp --save
    ```

3. Usando um editor de texto, crie um novo arquivo **SimulatedDevice.js** na pasta **simulateddevice**.

4. Adicione as seguintes instruções `require` no início do arquivo **SimulatedDevice.js** :

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. Adicione uma variável **connectionString** e use-a para criar um cliente do dispositivo. Substitua **{seunomehostiot}** pelo nome do hub IoT criado na seção *Criar um Hub IoT* . Substitua **{suachavedispositivo}** pelo valor da chave do dispositivo gerado na seção *Criar uma identidade de dispositivo* :

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myFirstNodeDevice;SharedAccessKey={yourdevicekey}';
    
    var client = clientFromConnectionString(connectionString);
    ```

6. Adicione a seguinte função para exibir a saída do aplicativo:

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Crie um retorno de chamada e use a função **setInterval** para enviar uma nova mensagem ao hub IoT a cada segundo:

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```

8. Abra a conexão com o Hub IoT e comece a enviar mensagens:

    ```
    client.open(connectCallback);
    ```

9. Salve e feche o arquivo **SimulatedDevice.js** .

> [AZURE.NOTE] Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição (como retirada exponencial), conforme sugerido no artigo do MSDN [Tratamento de Falhas Transitórias][lnk-transient-faults].


## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. Em um prompt de comando na pasta **readdevicetocloudmessages** , execute o seguinte comando para iniciar o monitoramento de seu hub IoT:

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![Aplicativo de cliente do serviço do Hub IoT do Node.js para monitorar mensagens do dispositivo para a nuvem][7]

2. Em um prompt de comando, na pasta **simulateddevice** , execute o seguinte comando para iniciar o envio dos dados de telemetria para seu hub IoT:

    ```
    node SimulatedDevice.js
    ```

    ![Aplicativo de cliente do dispositivo do Hub IoT do Node.js para enviar mensagens do dispositivo para a nuvem][8]

3. O bloco **Uso** no [portal do Azure][lnk-portal] mostra o número de mensagens enviadas para o hub:

    ![Bloco de Uso do Portal do Azure mostrando o número de mensagens enviadas ao Hub IoT][43]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um novo hub IoT no portal e depois criou uma identidade do dispositivo no registro de identidade do hub. Você usou essa identidade do dispositivo para habilitar o aplicativo do dispositivo simulado para enviar as mensagens entre o dispositivo e a nuvem para o hub. Você também criou um aplicativo que exibe as mensagens recebidas pelo hub. 

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, confira:

- [Conectar o dispositivo][lnk-connect-device]
- [Introdução ao gerenciamento de dispositivos][lnk-device-management]
- [Introdução ao SDK do Gateway][lnk-gateway-SDK]

Para saber como estender sua solução de IoT e processar as mensagens do dispositivo para a nuvem em escala, consulte o tutorial [Processar mensagens do dispositivo para a nuvem][lnk-process-d2c-tutorial].

<!-- Images. -->
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/



<!--HONumber=Oct16_HO2-->


