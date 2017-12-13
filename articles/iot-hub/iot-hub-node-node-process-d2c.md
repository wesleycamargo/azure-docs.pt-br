---
title: "Roteando mensagens com o Hub IoT do Azure (Nó)| Microsoft Docs"
description: "Como processar mensagens do dispositivo para nuvem do Hub IoT do Azure usando regras de direcionamento e pontos de extremidade personalizados para enviar mensagens para outros serviços de back-end."
services: iot-hub
documentationcenter: node
author: msebolt
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2017
ms.author: v-masebo
ms.openlocfilehash: 5a80195dd474414626cc54623945393c6f88093d
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2017
---
# <a name="routing-messages-with-iot-hub-node"></a>Roteamento de mensagens com o Hub IoT (nó)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Este tutorial baseia-se no tutorial [Introdução ao Hub IoT] .  O tutorial:

* Mostra como usar regras de roteamento do para enviar mensagens de dispositivo à nuvem de maneira fácil, com base em configuração.
* Ilustra como isolar mensagens interativas que exigem ação imediata no back-end da solução para processamento adicional.  Por exemplo, um dispositivo pode enviar uma mensagem de alarme que dispara e insere um tíquete em um sistema CRM.  Por outro lado, as mensagens de ponto de dados, como telemetria de temperatura, são enviadas ao mecanismo de análise.

No fim deste tutorial, você executa três aplicativos de console do Node.js:

* **SimulatedDevice.js**, uma versão modificada do aplicativo criado no tutorial [Introdução ao Hub IoT], que envia mensagens de ponto de dados do dispositivo para a nuvem a cada segundo e mensagens interativas do dispositivo para a nuvem em um intervalo aleatório. O aplicativo usa o protocolo MQTT para se comunicar com o Hub IoT.
* **ReadDeviceToCloudMessages** exibe a telemetria enviada pelo aplicativo de dispositivo.
* **ReadCriticalQueue.js** retira as mensagens críticas da fila do Barramento de Serviço conectada ao Hub IoT.

> [!NOTE]
> O Hub IoT tem suporte de SDK para várias plataformas de dispositivo e linguagens, incluindo C, Java e JavaScript. Para obter instruções sobre como substituir o dispositivo neste tutorial por um dispositivo físico e como conectar dispositivos a um Hub IoT, confira o [Centro de Desenvolvedores do Azure IoT].

Para concluir este tutorial, você precisará do seguinte:

* Uma versão de trabalho completa do tutorial [Introdução ao Hub IoT] .
* Node.js versão 4.0.x ou posterior.
* Uma conta ativa do Azure. (Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

Também é recomendável ler sobre o [Armazenamento do Azure] e o [Barramento de Serviço do Azure].

## <a name="send-interactive-messages-from-a-device-app"></a>Enviar mensagens interativas de um aplicativo de dispositivo
Nesta seção, você modificará o aplicativo de dispositivo criado no tutorial [Introdução ao Hub IoT] para enviar ocasionalmente mensagens que exigem processamento imediato.

1. Use um editor de texto para abrir o arquivo **simulateddevice\SimulatedDevice.js**. Este arquivo contém o código do aplicativo **SimulatedDevice** que você criou no tutorial [Introdução ao Hub IoT].

2. Substitua a função **connectCallback** pelo código abaixo:

    ```nodejs
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
    
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
        var data, message;
        if (Math.random() > 0.7) {
            if (Math.random() > 0.5) {
                data = "This is a critical message.";
                message = new Message(data);
                message.properties.add("level", "critical");
            } else {
                data = "This is a storage message.";
                message = new Message(data);
                message.properties.add("level", "storage");
            }
            console.log("Sending message: " + message.getData());
        }
        else {
                var temperature = 20 + (Math.random() * 15);
                var humidity = 60 + (Math.random() * 20);            
                data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
                message = new Message(data);
                message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
                console.log("Sending message: " + message.getData());
            }
        client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
   
    Esse método adiciona aleatoriamente as propriedades `"level": "critical"` e `"level": "storage"` a mensagens enviadas pelo dispositivo, que simula uma mensagem que exige ação imediata do back-end do aplicativo ou de um que precise ser armazenado permanentemente. O aplicativo passa essas informações nas propriedades da mensagem, e não no corpo da mensagem, de modo que o Hub IoT pode rotear a mensagem para o destino apropriado.
   
   > [!NOTE]
   > Você pode usar as propriedades a fim de direcionar as mensagens para vários cenários, incluindo processamento de ampliação, além do exemplo de afunilamento mostrado aqui.

2. Salve e feche o arquivo **simulateddevice\SimulatedDevice.js**.

    > [!NOTE]
    > É fortemente recomendado implementar uma política de repetição, como uma retirada exponencial, conforme sugerido no artigo [Tratamento de falhas transitórias] do MSDN.

## <a name="add-service-bus-queue-to-your-iot-hub-and-route-messages-to-it"></a>Adicionar uma fila do Barramento de Serviço ao seu Hub IoT e rotear mensagens para ela

Nesta seção, você cria uma fila do Barramento de Serviço, conecta-a ao Hub IoT e configura o Hub IoT para enviar mensagens à fila com base na presença de uma propriedade na mensagem. Para saber mais sobre como processar mensagens das filas do Barramento de Serviço, confira [Introdução às filas][lnk-sb-queues-node].

1. Crie uma fila do Barramento de Serviço conforme descrito em [Introdução às filas][lnk-sb-queues-node]. Anote o namespace e o nome da fila.

2. No Portal do Azure, abra o Hub IoT e clique em **Pontos de extremidade**.

    ![Pontos de extremidade no Hub IoT][30]

3. Na folha **Pontos de extremidade**, clique em **Adicionar** na parte superior para adicionar a fila ao Hub IoT. Chame o ponto de extremidade de **CriticalQueue** e use o menu suspenso para selecionar **Fila do Barramento de Serviço**, o namespace do Barramento de Serviço no qual suas filas estão e o nome da sua fila. Quando terminar, clique em **OK** na parte inferior.  

    ![Adicionando um ponto de extremidade][31]

4. Agora clique em **Rotas** no Hub IoT. Clique em **Adicionar** na parte superior da folha para criar uma regra que encaminhe mensagens para a fila que você acabou de adicionar. Selecione **Mensagens de Dispositivo** como a fonte de dados. Insira `level="critical"` como a condição e escolha **CriticalQueue** como um ponto de extremidade personalizado para o ponto de extremidade de regra do direcionamento. Clique em **Salvar** na parte inferior.  

    ![Adicionando uma rota][32]

    Verifique se a rota de fallback está definida como **ATIVADA**. Essa é a configuração padrão de um Hub IoT.

    ![Rota de fallback][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Opcional) Ler no ponto de extremidade da fila

Nesta seção, você cria um aplicativo de console do Node.js que lê as mensagens críticas do Barramento de Serviço do IoT. Veja mais informações em [Introdução às filas][lnk-sb-queues-node]. 

1. Crie uma pasta vazia chamada `readcriticalqueue`. Na pasta `readcriticalqueue`, crie um arquivo package.json usando o comando a seguir no seu prompt de comando. Aceite todos os padrões:

    ```cmd/sh
    npm init
    ```

2. No prompt de comando na pasta `readcriticalqueue`, execute o seguinte comando para instalar o pacote **azure**:

    ```cmd/sh
    npm install azure --save
    ```

3. Usando um editor de texto, crie um arquivo **ReadCriticalQueue.js** na pasta `readcriticalqueue`.

4. Adicione as seguintes instruções `require` no início do arquivo **ReadCriticalQueue.js** :

    ```nodejs
    'use strict';

    var azure = require('azure');
    ```

5. Adicione a seguinte declaração de variável e substitua os valores de espaço reservado pela cadeia de conexão do Barramento de Serviço do IoT e pelo nome da fila:

    ```nodejs
    var connectionString = '{iotservicebus connection string}';
    var queueName = '{iotservice bus queue name}';
    ```

6. Adicione as duas seguintes funções que imprimem a saída no console:

    ```nodejs
    var serviceBusService = azure.createServiceBusService(connectionString);

    setInterval(function() {serviceBusService.receiveQueueMessage(queueName, function(error, receivedMessage) {
        if(!error){
            // Message received and deleted
            console.log(receivedMessage);
        } else { console.log(error); }
        });
    }, 3000);
    ```

7. Salve e feche o arquivo **ReadCriticalQueue.js**.

## <a name="run-the-applications"></a>Executar os aplicativos

Agora, você está pronto para executar os três aplicativos.

1. Para executar o aplicativo **ReadDeviceToCloudMessages.js**, em um prompt ou shell de comando, navegue até a pasta readdevicetocloudmessages e execute o seguinte comando:

   ```cmd/sh
   node ReadDeviceToCloudMessages.js
   ```

   ![Executar read-d2c-messages][readd2c]

2. Para executar o aplicativo **ReadCriticalQueue.js**, navegue até a pasta readcriticalqueue em um prompt ou shell de comando e execute o seguinte comando:

   ```cmd/sh
   node ReadCriticalQueue.js
   ```
   
   ![Executar read-critical-messages][readqueue]

3. Para executar o aplicativo **SimulatedDevice.js**, em um prompt de comando ou shell, navegue até a pasta simulateddevice e execute o seguinte comando:

   ```cmd/sh
   node SimulatedDevice.js
   ```
   
   ![Executar simulated-device][simulateddevice]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Opcional) Adicionar contêiner de armazenamento ao Hub IoT e rotear mensagens para ele

Nesta seção, você cria uma conta de Armazenamento, conecta-a ao Hub IoT e configura o Hub IoT para enviar mensagens à conta baseada na presença de uma propriedade na mensagem. Para saber mais sobre como gerenciar o armazenamento, consulte [Introdução ao Armazenamento do Azure][Armazenamento do Azure].

 > [!NOTE]
   > Caso não haja limite de um **Ponto de extremidade**, é possível configurar o **StorageContainer**, além do **CriticalQueue**, e executar ambos simultaneamente.

1. Crie uma conta de armazenamento, conforme descrito na [Documentação do Armazenamento do Azure][lnk-storage]. Anote o nome da conta.

2. No Portal do Azure, abra o Hub IoT e clique em **Pontos de extremidade**.

3. Na folha **Pontos de Extremidade**, selecione o ponto de extremidade **CriticalQueue** e clique em **Excluir**. Clique em **Sim** e depois em **Adicionar**. Nomeie o ponto de extremidade **StorageContainer**, use os menus suspensos para selecionar o **Contêiner do Armazenamento do Azure** e crie uma **Conta de armazenamento** e um **Contêiner de armazenamento**.  Anote os nomes.  Quando terminar, clique em **OK** na parte inferior. 

 > [!NOTE]
   > Caso tenha mais de um **Ponto de Extremidade**, não é necessário excluir **CriticalQueue**.

4. Clique em **Rotas** no Hub IoT. Clique em **Adicionar** na parte superior da folha para criar uma regra que encaminhe mensagens para a fila que você acabou de adicionar. Selecione **Mensagens de Dispositivo** como a fonte de dados. Insira `level="storage"` como a condição e escolha **StorageContainer** como um ponto de extremidade personalizado para o ponto de extremidade da regra de roteamento. Clique em **Salvar** na parte inferior.  

    Verifique se a rota de fallback está definida como **ATIVADA**. Essa é a configuração padrão de um Hub IoT.

1. Verifique se seu aplicativo anterior **SimulatedDevice.js** ainda está em execução. 

1. No Portal do Azure, acesse sua conta de armazenamento, em **Serviço de Blob**, e clique em **Procurar blobs...**.  Selecione o contêiner, navegue até o arquivo JSON e clique nele, depois clique em **Baixar** para exibir os dados.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a expedir confiavelmente mensagens do dispositivo para nuvem usando a funcionalidade de roteamento de mensagens do Hub IoT.

O tutorial [Como enviar mensagens da nuvem para o dispositivo com o Hub IoT][lnk-c2d] mostra como enviar mensagens da solução de back-end para seus dispositivos.

Para ver exemplos de soluções completas que usam o Hub IoT, veja [Azure IoT Suite][lnk-suite].

Para saber mais sobre como desenvolver soluções com o Hub IoT, consulte o [Guia do desenvolvedor do Hub IoT].

Para saber mais sobre o roteamento de mensagens no Hub IoT, confira [Enviar e receber mensagens com o Hub IoT][lnk-devguide-messaging].

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-node-node-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-node-node-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-node-node-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-node-node-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-node-node-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-node-node-process-d2c/route-creation.png
[33]: ./media/iot-hub-node-node-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-node]: ../service-bus-messaging/service-bus-nodejs-how-to-use-queues.md

[Armazenamento do Azure]: https://azure.microsoft.com/documentation/services/storage/
[Barramento de Serviço do Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[Guia do desenvolvedor do Hub IoT]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Introdução ao Hub IoT]: iot-hub-node-node-getstarted.md
[Centro de Desenvolvedores do Azure IoT]: https://azure.microsoft.com/develop/iot
[Tratamento de falhas transitórias]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Tratamento de Falhas Transitórias]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-node-node-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
[lnk-storage]: https://docs.microsoft.com/en-us/azure/storage/