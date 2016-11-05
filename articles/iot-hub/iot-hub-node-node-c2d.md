---
title: Enviar mensagens da nuvem para o dispositivo com o Hub IoT| Microsoft Docs
description: Siga este tutorial para aprender a enviar mensagens da nuvem para o dispositivo usando o Hub IoT do Azure com Java.
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: javascript
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2016
ms.author: dobett

---
# <a name="tutorial:-how-to-send-cloud-to-device-messages-with-iot-hub-and-node.js"></a>Tutorial: Como enviar mensagens de nuvem para o dispositivo com o Hub IoT e Node.js
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introdução
O Hub IoT do Azure é um serviço totalmente gerenciado que ajuda a permitir comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end de aplicativo. O tutorial [Introdução ao Hub IoT] mostra como criar um hub IoT, provisionar uma identidade do dispositivo e codificar um dispositivo simulado que envia mensagens do dispositivo para a nuvem.

Esse tutorial se baseia na [Introdução ao Hub IoT]. Ele mostra como:

* Do seu aplicativo de nuvem de back-end, envie mensagens da nuvem para o dispositivo em um único dispositivo por meio do Hub IoT.
* Receber mensagens da nuvem para o dispositivo em um dispositivo.
* Do seu aplicativo de nuvem de back-end, solicitar confirmação de entrega (*comentários*) para as mensagens enviadas a um dispositivo do Hub IoT.

Encontre mais informações sobre as mensagens da nuvem para o dispositivo no [Guia do Desenvolvedor do Hub IoT][IoT Hub Developer Guide - C2D].

Ao fim deste tutorial, você executará dois aplicativos de console do Node.js:

* **SimulatedDevice**, uma versão modificada do aplicativo criado na [Introdução ao Hub IoT], que se conecta a seu hub IoT e recebe mensagens da nuvem para o dispositivo.
* **SendCloudToDeviceMessage**, que envia uma mensagem da nuvem ao dispositivo simulado por meio do Hub IoT e recebe sua confirmação de entrega.

> [!NOTE]
> O Hub IoT tem suporte a SDK para várias plataformas de dispositivo e linguagens (incluindo C, Java e Javascript) nos SDKs do dispositivo IoT do Azure. Para obter instruções passo a passo sobre como conectar seu dispositivo ao código deste tutorial e, em geral, ao Hub IoT do Azure, veja o [Centro de Desenvolvedores do IoT do Azure].
> 
> 

Para concluir este tutorial, você precisará do seguinte:

* Node.js versão 0.10.x ou posterior.
* Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, confira [Avaliação Gratuita do Azure][lnk-free-trial]).

## <a name="receive-messages-on-the-simulated-device"></a>Receber mensagens no dispositivo simulado
Nesta seção, você modificará o aplicativo do dispositivo simulado criado na [Introdução ao Hub IoT] para receber mensagens da nuvem para o dispositivo do hub IoT.

1. Usando um editor de texto, abra o arquivo SimulatedDevice.js.
2. Modifique a função **connectCallback** para lidar com mensagens enviadas do Hub IoT. Neste exemplo, o dispositivo sempre chama a função **complete** para notificar o Hub IoT de que ele processou a mensagem. A nova versão da função **connectCallback** tem esta aparência:
   
    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
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
   
   > [!NOTE]
   > Se você usar HTTP/1 em vez de AMQP ou MQTT como transporte, a instância **DeviceClient** verificará se há mensagens do Hub IoT com pouca frequência (menos de a cada 25 minutos). Para saber mais sobre as diferenças entre o suporte do AMQP, MQTT e HTTP/1 e a limitação do Hub IoT, confira o [Guia do desenvolvedor do Hub IoT][IoT Hub Developer Guide - C2D].
   > 
   > 

## <a name="send-a-cloud-to-device-message"></a>Envie uma mensagem da nuvem para o dispositivo
Nesta seção, você criará um aplicativo do console do Node.js que envia mensagens da nuvem ao dispositivo para o aplicativo do dispositivo simulado. Você precisa da Id do dispositivo que você adicionou no tutorial [Introdução ao Hub IoT] . Você também precisa da cadeia de conexão do Hub IoT, que você pode encontrar no [Portal do Azure].

1. Crie uma pasta vazia denominada **sendcloudtodevicemessage**. Na pasta **sendcloudtodevicemessage** , crie um arquivo package.json usando o comando a seguir no prompt de comando. Aceite todos os padrões:
   
    ```
    npm init
    ```
2. No prompt de comando, na pasta **sendcloudtodevicemessage**, execute o seguinte comando para instalar o pacote **azure-iothub**:
   
    ```
    npm install azure-iothub --save
    ```
3. Usando um editor de texto, crie um novo arquivo **SendCloudToDeviceMessage.js** na pasta **sendcloudtodevicemessage**.
4. Adicione as seguintes instruções `require` no início do arquivo **SendCloudToDeviceMessage.js** :
   
    ```
    'use strict';
   
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```
5. Adicione o seguinte código ao arquivo **SendCloudToDeviceMessage.js** . Substitua o valor do espaço reservado da cadeia de pela cadeia de conexão do hub IoT criado no tutorial [Introdução ao Hub IoT] . Substitua o espaço reservado de destino do dispositivo pela Id do dispositivo que você adicionou no tutorial [Introdução ao Hub IoT] :
   
    ```
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';
   
    var serviceClient = Client.fromConnectionString(connectionString);
    ```
6. Adicione a seguinte função para imprimir os resultados da operação no console:
   
    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```
7. Adicione a seguinte função para imprimir mensagens de comentários de entrega para o console:
   
    ```
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```
8. Adicione o seguinte código para enviar uma mensagem ao dispositivo e lidar com a mensagem de comentário quando o dispositivo reconhecer a mensagem da nuvem para o dispositivo:
   
    ```
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```
9. Salve e feche o arquivo **SendCloudToDeviceMessage.js** .

## <a name="run-the-applications"></a>Executar os aplicativos
Agora você está pronto para executar os aplicativos.

1. No prompt de comando na pasta **simulateddevice** , execute o seguinte comando para enviar telemetria ao Hub IoT e escutar mensagens da nuvem para o dispositivo:
   
    ```
    node SimulatedDevice.js 
    ```
   
    ![Executar um aplicativo de dispositivo simulado][img-simulated-device]
2. No prompt de comando na pasta **sendcloudtodevicemessage** , execute o seguinte comando para enviar uma mensagem da nuvem para o dispositivo e esperar os comentários de confirmação:
   
    ```
    node SendCloudToDeviceMessage.js 
    ```
   
    ![Executar o aplicativo para enviar o comando c2d][img-send-command]
   
   > [!NOTE]
   > Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), conforme sugestão no artigo [Tratamento de Falhas Transitórias]do MSDN.
   > 
   > 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial você aprendeu a enviar e receber mensagens da nuvem para o dispositivo. 

Para ver exemplos de soluções completas que usam o Hub IoT, consulte [Azure IoT Suite].

Para saber mais sobre como desenvolver soluções com o Hub IoT, consulte o [Guia do desenvolvedor do Hub IoT].

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[Introdução ao Hub IoT]: iot-hub-node-node-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md
[Guia do Desenvolvedor do Hub IoT]: iot-hub-devguide.md
[Centro de Desenvolvedores do IoT do Azure]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[Tratamento de Falhas Transitórias]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Portal do Azure]: https://portal.azure.com
[Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/


<!--HONumber=Oct16_HO2-->


