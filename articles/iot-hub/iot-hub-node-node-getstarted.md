<properties
	pageTitle="Introdução ao Hub IoT do Azure para Node.js | Microsoft Azure"
	description="Tutorial de introdução ao Hub IoT do Azure para Node.js. Use o Hub IoT do Azure e Node.js com SDKs de IoT do Microsoft Azure para implementar uma solução de Internet das coisas."
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
     ms.date="03/22/2016"
     ms.author="dobett"/>

# Introdução ao Hub IoT do Azure para Node.js

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## Introdução

O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT (Internet das coisas) e um back-end da solução. Um dos maiores desafios enfrentados por projetos IoT é como conectar dispositivos de forma confiável e segura para a solução de back-end. Para enfrentar esse desafio, o hub IoT:

- Oferece, de forma confiável, mensagens em larga escala do dispositivo para a nuvem e da nuvem para o dispositivo.
- Permite proteger as comunicações usando credenciais de segurança e controle de acesso por dispositivo.
- Inclui bibliotecas de dispositivo para as plataformas e idiomas mais populares.

Este tutorial mostra como:

- Use o portal do Azure para criar um hub IoT.
- Crie uma identidade de dispositivo em seu hub IoT.
- Crie um dispositivo simulado que envie telemetria para o back-end de nuvem.

No fim deste tutorial, você terá três aplicativos de console do Node.js:

* **CreateDeviceIdentity.js**, que cria uma identidade do dispositivo e uma chave de segurança associada para conectar o dispositivo simulado.
* **ReadDeviceToCloudMessages.js**, que exibe a telemetria enviada pelo dispositivo simulado.
* **SimulatedDevice.js**, que se conecta ao Hub IoT com a identidade do dispositivo criada anteriormente e envia uma mensagem de telemetria a cada segundo usando o protocolo AMQPS.

> [AZURE.NOTE] O artigo [SDKs do hub IoT][lnk-hub-sdks] fornece informações sobre vários SDKs que você pode usar para criar aplicativos executados em dispositivos e no back-end da solução.

Para concluir este tutorial, você precisará do seguinte:

+ Node.js versão 0.12.x ou posterior. <br/> [Preparar o ambiente de desenvolvimento][lnk-dev-setup] descreve como instalar o Node.js para este tutorial no Windows ou no Linux.

+ Uma conta ativa do Azure. <br/>Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk-free-trial].

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Como uma etapa final, clique em **Configurações** na folha do Hub IoT, então, em **Mensagens** na folha **Configurações**. Na folha **Mensagens**, anote o **nome compatível com o Hub de Eventos** e o **ponto de extremidade compatível com o Hub de Eventos**. Você precisará desses valores ao criar o aplicativo **read-d2c-messages**.

![][6]

Você criou seu hub IoT e tem o nome de host do Hub IoT, a cadeia de conexão do Hub IoT, o nome compatível com o Hub de Eventos e os valores de ponto de extremidade compatíveis com o Hub de Eventos necessário para concluir o restante deste tutorial.

## Criar uma identidade do dispositivo

Nesta seção, você cria um aplicativo do console do Node.js que cria uma nova identidade do dispositivo no registro de identidade em seu hub IoT. Um dispositivo não pode se conectar ao Hub IoT, a menos que ele tenha uma entrada no registro de identidade do dispositivo. Veja a seção **Registro de identidade do dispositivo** do [Guia do Desenvolvedor do Hub IoT][lnk-devguide-identity] para obter mais informações. Quando você executa esse aplicativo de console, ele gera uma ID e uma chave de dispositivo exclusivas com que seu dispositivo poderá se identificar ao enviar mensagens de dispositivo para a nuvem para o Hub IoT.

1. Crie uma nova pasta vazia denominada **createdeviceidentity**. Na pasta **createdeviceidentity**, crie um novo arquivo package.json usando o comando a seguir no prompt de comando. Aceite todos os padrões:

    ```
    npm init
    ```

2. No prompt de comando, na pasta **createdeviceidentity**, execute o seguinte comando para instalar o pacote **azure-iothub**:

    ```
    npm install azure-iothub --save
    ```

3. Usando um editor de texto, crie um novo arquivo **CreateDeviceIdentity.js** na pasta **createdeviceidentity**.

4. Adicione a seguinte instrução `require` no início do arquivo **CreateDeviceIdentity.js**:

    ```
    'use strict';
    
    var iothub = require('azure-iothub');
    ```

5. Adicione o seguinte código ao arquivo **CreateDeviceIdentity.js**, substituindo o valor do espaço reservado pela cadeia de conexão para o hub IoT que você criou na seção anterior:

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

Nesta seção, você cria um aplicativo do console do Node.js que lê mensagens do dispositivo para a nuvem do Hub IoT. Um hub IoT expõe um ponto de extremidade compatível com os [Hubs de Eventos][lnk-event-hubs-overview] para permitir que você leia mensagens do dispositivo para a nuvem. Para simplificar, este tutorial cria um leitor básico que não é adequado para uma implantação de alta taxa de transferência. O tutorial [Processar mensagens do dispositivo para a nuvem][lnk-process-d2c-tutorial] mostra como processar as mensagens do dispositivo para a nuvem em escala. O tutorial [Introdução aos Hubs de Eventos][lnk-eventhubs-tutorial] oferece informações adicionais sobre como processar as mensagens dos Hubs de Eventos e é aplicável aos pontos de extremidade compatíveis com o Hub de Eventos do Hub IoT.

> [AZURE.NOTE] O ponto de extremidade compatível com os Hubs de Eventos para ler mensagens do dispositivo para a nuvem sempre usa o protocolo AMQPS.

1. Crie uma nova pasta vazia denominada **readdevicetocloudmessages**. Na pasta **readdevicetocloudmessages**, crie um novo arquivo package.json usando o comando a seguir no prompt de comando. Aceite todos os padrões:

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

5. Adicione as declarações de variáveis a seguir, substituindo os espaços reservados pelos valores que você anotou anteriormente. O valor do espaço reservado **{namespacecompatívelcom seuhubeventos}** é proveniente do campo **Ponto de extremidade compatível com o Hub de Eventos**: ele tem o formato **namespace.barramentoserviço.windows.net** (sem o prefixo **sb://*).

    ```
    var protocol = 'amqps';
    var eventHubHost = '{your event hub-compatible namespace}';
    var sasName = 'iothubowner';
    var sasKey = '{your iot hub key}';
    var eventHubName = '{your event hub-compatible name}';
    var numPartitions = 2;
    ```

    > [AZURE.NOTE] Esse código pressupõe que você tenha criado seu hub IoT na camada F1 (gratuita). Um hub IoT gratuito tem duas partições chamadas "0" e "1". Se você criou o hub IoT usando uma das outras camadas de preço, deverá ajustar o código para criar uma **MessageReceiver** para cada partição.

6. Adicione a definição de filtro a seguir. Esse aplicativo usa um filtro ao criar um receptor para que o receptor leia apenas as mensagens enviadas para o Hub IoT após o início da execução do receptor. Isso será útil em um ambiente de teste para que você possa ver o conjunto de mensagens atual, mas em um ambiente de produção, seu código deverá garantir o processamento de todas as mensagens - consulte o tutorial [Como processar as mensagens do dispositivo para a nuvem do Hub IoT][lnk-process-d2c-tutorial] para saber mais.

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

## Criar um aplicativo de dispositivo simulado

Nesta seção, você criará um aplicativo do console do Node.js que simula um dispositivo que envia mensagens do dispositivo para a nuvem para um hub IoT.

1. Crie uma nova pasta vazia denominada **simulateddevice**. Na pasta **simulateddevice**, crie um novo arquivo package.json usando o comando a seguir no prompt de comando. Aceite todos os padrões:

    ```
    npm init
    ```

2. No prompt de comando, na pasta **simulateddevice**, execute o seguinte comando para instalar o pacote **azure-iot-device-amqp**:

    ```
    npm install azure-iot-device azure-iot-device-amqp --save
    ```

3. Usando um editor de texto, crie um novo arquivo **SimulatedDevice.js** na pasta **simulateddevice**.

4. Adicione as seguintes instruções `require` no início do arquivo **SimulatedDevice.js**:

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. Adicione uma variável **connectionString** e use-a para criar um cliente do dispositivo. Substitua **{seunomehubiot}** pelo nome do hub IoT e **{suaiddispositivo}** e **{suachavedispositivo}** pelos valores do dispositivo gerados na seção *Criar uma identidade do dispositivo*:

    ```
    var connectionString = 'HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    
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
            var data = JSON.stringify({ deviceId: 'mydevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 2000);
      }
    };
    ```

8. Abra a conexão com o Hub IoT e comece a enviar mensagens:

    ```
    client.open(connectCallback);
    ```

9. Salve e feche o arquivo **SimulatedDevice.js**.

> [AZURE.NOTE] Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), como sugerido no artigo [Tratamento de falhas transitórias][lnk-transient-faults] do MSDN.


## Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. Em um prompt de comando na pasta **readdevicetocloudmessages**, execute o seguinte comando para iniciar o monitoramento de seu hub IoT:

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![][7]

2. Em um prompt de comando, na pasta **simulateddevice**, execute o seguinte comando para iniciar o envio dos dados de telemetria para seu hub IoT:

    ```
    node SimulatedDevice.js
    ```

    ![][8]

3. O bloco **Uso** no [portal do Azure][lnk-portal] mostra o número de mensagens enviadas para o hub:

    ![][43]

## Próximas etapas

Neste tutorial, você configurou um novo hub IoT no portal e depois criou uma identidade de dispositivo no registro de identidade do hub. Você usou essa identidade de dispositivo para habilitar o aplicativo do dispositivo simulado para enviar mensagens entre o dispositivo e a nuvem para o hub e criou um aplicativo que exibe as mensagens recebidas pelo hub. Você pode continuar a explorar os recursos do hub IoT e outros cenários de IoT nestes tutoriais:

- [Enviar mensagens da Nuvem para o Dispositivo com o Hub IoT][lnk-c2d-tutorial] mostra como enviar mensagens para dispositivos e como processar os comentários de entrega produzidos pelo Hub IoT.
- [Processar mensagens do Dispositivo para a Nuvem][lnk-process-d2c-tutorial] mostra como processar de forma confiável a telemetria e as mensagens interativas vindas dos dispositivos.
- [Carregando arquivos de dispositivos][lnk-upload-tutorial] descreve um padrão que usa mensagens da nuvem para o dispositivo para facilitar o carregamento de arquivo dos dispositivos.

<!-- Images. -->
[6]: ./media/iot-hub-node-node-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0615_2016-->