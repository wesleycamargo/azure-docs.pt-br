---
title: Mensagens da nuvem para dispositivo com o Hub IoT do Azure (Python) | Microsoft Docs
description: "Como enviar mensagens da nuvem para um dispositivo em um hub IoT do Azure usando os SDKs de IoT do Azure para Python. Modifique um aplicativo de dispositivo simulado para receber mensagens da nuvem para o dispositivo e modificar um aplicativo de back-end para enviá-las."
services: iot-hub
documentationcenter: python
author: msebolt
manager: timlt
editor: 
ms.assetid: 3ca8a78f-ade2-46e8-8a49-d5d599cdf1f1
ms.service: iot-hub
ms.devlang: javascript
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo
ms.openlocfilehash: 592e0cd858d16715f95955194eca4217d9914b05
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2018
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Enviar mensagens da nuvem para dispositivo com o Hub IoT (Python)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]


## <a name="introduction"></a>Introdução
O Hub IoT do Azure é um serviço totalmente gerenciado que ajuda a permitir comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end de solução. O tutorial [Introdução ao Hub IoT] mostra como criar um Hub IoT, provisionar uma identidade do dispositivo nele e codificar um aplicativo do dispositivo simulado que envia mensagens do dispositivo para a nuvem.

Esse tutorial se baseia na [Introdução ao Hub IoT]. Ele mostra como:

* Da sua solução de back-end, envie mensagens da nuvem para o dispositivo em um único dispositivo por meio do Hub IoT.
* Receber mensagens da nuvem para o dispositivo em um dispositivo.
* Da sua solução de back-end, solicite confirmação de entrega (*comentários*) para as mensagens enviadas a um dispositivo do Hub IoT.

É possível encontrar mais informações sobre as mensagens da nuvem para o dispositivo no [Guia do Desenvolvedor do Hub IoT][IoT Hub developer guide - C2D].

Ao final deste tutorial, você executará dois aplicativos de console do Python:

* **SimulatedDevice.py**, uma versão modificada do aplicativo criado em [Introdução ao Hub IoT], que se conecta ao hub IoT e recebe mensagens da nuvem para dispositivo.
* **SendCloudToDeviceMessage.py**, que envia uma mensagem da nuvem para dispositivo ao aplicativo de dispositivo simulado por meio do Hub IoT e, em seguida, recebe sua confirmação de entrega.

> [!NOTE]
> O Hub IoT tem suporte a SDK para várias plataformas de dispositivo e linguagens (incluindo C, Java e Javascript) nos SDKs do dispositivo IoT do Azure. Para obter instruções passo a passo sobre como conectar seu dispositivo ao código deste tutorial e, em geral, ao Hub IoT do Azure, veja o [Centro de Desenvolvedores do IoT do Azure].
> 

Para concluir este tutorial, você precisará do seguinte:

* [Python 2.x or 3.x][lnk-python-download]. Certifique-se de usar a instalação de 32 bits ou 64 bits conforme exigido pelo seu programa de instalação. Quando solicitado durante a instalação, certifique-se de adicionar Python à variável de ambiente específica da plataforma. Se você estiver usando o Python 2. x, talvez seja necessário [instalar ou atualizar o *pip*, o sistema de gerenciamento de pacotes do Python][lnk-install-pip].
* Se você estiver usando o sistema operacional Windows, então o [Pacote redistribuível do Visual C++][lnk-visual-c-redist] permite o uso de DLLs nativas do Python.
* Uma conta ativa do Azure. (Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

> [!NOTE]
> Os pacotes *pip* para `azure-iothub-service-client` e `azure-iothub-device-client` atualmente estão disponíveis apenas para o sistema operacional Windows. Para o Linux/Mac OS, veja as seções específicas ao Linux e Mac OS na postagem [Preparar o ambiente de desenvolvimento para o Python] [lnk-python-devbox].
> 


## <a name="receive-messages-in-the-simulated-device-app"></a>Receber mensagens no aplicativo do dispositivo simulado
Nesta seção, você cria um aplicativo de console do Python para simular o dispositivo e receber mensagens da nuvem para dispositivo do hub IoT.

1. Usando um editor de texto, crie um arquivo **SimulatedDevice.py**.

1. Adicione as seguintes instruções `import` e variáveis ao início do arquivo **SimulatedDevice.py**:
   
    ```python
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError

    RECEIVE_CONTEXT = 0
    WAIT_COUNT = 10
    RECEIVED_COUNT = 0
    RECEIVE_CALLBACKS = 0
    ```

1. Adicione o código a seguir ao arquivo **SimulatedDevice.py**. Substitua o valor do espaço reservado "{deviceConnectionString}" pela cadeia de conexão do dispositivo criado no tutorial [Introdução ao Hub IoT]:
   
    ```python
    # choose AMQP or AMQP_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.AMQP
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Adicione a seguinte função para imprimir as mensagens recebidas para o console:
   
    ```python
    def receive_message_callback(message, counter):
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "Received Message [%d]:" % counter )
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        counter += 1
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        return IoTHubMessageDispositionResult.ACCEPTED

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def print_last_message_time(client):
        try:
            last_message = client.get_last_message_receive_time()
            print ( "Last Message: %s" % time.asctime(time.localtime(last_message)) )
            print ( "Actual time : %s" % time.asctime() )
        except IoTHubClientError as iothub_client_error:
            if iothub_client_error.args[0].result == IoTHubClientResult.INDEFINITE_TIME:
                print ( "No message received" )
            else:
                print ( iothub_client_error )
    ```

1. Adicione o seguinte código para inicializar o cliente e aguardar para receber a mensagem da nuvem para dispositivo:
   
    ```python
    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    status = client.get_send_status()
                    print ( "Send status: %s" % status )
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

        print_last_message_time(client)
    ```

1. Adicione a seguinte função principal:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. Salve e feche o arquivo **SimulatedDevice.py**.


## <a name="send-a-cloud-to-device-message"></a>Envie uma mensagem da nuvem para o dispositivo
Nesta seção, você cria um aplicativo do console do Python que envia mensagens da nuvem para dispositivo ao aplicativo de dispositivo simulado. Você precisa da ID do dispositivo que você adicionou no tutorial [Introdução ao Hub IoT]. A cadeia de conexão do Hub IoT também é necessária e você pode encontrá-la no [Portal do Azure].

1. Usando um editor de texto, crie um arquivo **SendCloudToDeviceMessage.py**.

1. Adicione as seguintes instruções `import` e variáveis ao início do arquivo **SendCloudToDeviceMessage.py**:
   
    ```python
    import random
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubMessaging, IoTHubMessage, IoTHubError

    OPEN_CONTEXT = 0
    FEEDBACK_CONTEXT = 1
    MESSAGE_COUNT = 1
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. Adicione o código a seguir ao arquivo **SendCloudToDeviceMessage.py**. Substitua o valor do espaço reservado "{IoTHubConnectionString}" pela cadeia de conexão do Hub IoT do hub criado no tutorial [Introdução ao Hub IoT]. Substitua o espaço reservado "{deviceId}" pela ID do dispositivo adicionado no tutorial [Introdução ao Hub IoT]:
   
    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Adicione a seguinte função para imprimir os resultados da operação no console:
   
    ```python
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

1. Adicione a seguinte função para imprimir mensagens de comentários para o console:
   
    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

1. Adicione o seguinte código para enviar uma mensagem ao dispositivo e lidar com a mensagem de comentário quando o dispositivo reconhecer a mensagem da nuvem para o dispositivo:
   
    ```python
    def iothub_messaging_sample_run():
        try:
            iothub_messaging = IoTHubMessaging(CONNECTION_STRING)

            iothub_messaging.open(open_complete_callback, OPEN_CONTEXT)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))

                # optional: assign ids
                message.message_id = "message_%d" % i
                message.correlation_id = "correlation_%d" % i
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % i
                prop_map.add("Property", prop_text)

                iothub_messaging.send_async(DEVICE_ID, message, send_complete_callback, i)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

            iothub_messaging.close()

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubMessaging sample stopped" )
    ```

1. Adicione a seguinte função principal:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

1. Salve e feche o arquivo **SendCloudToDeviceMessage.py**.


## <a name="run-the-applications"></a>Executar os aplicativos
Agora você está pronto para executar os aplicativos.

1. Abra um prompt de comando e instale o **SDK do Dispositivo Hub IoT do Azure para Python**.

    ```
    pip install azure-iothub-device-client
    ```

1. No prompt de comando, execute o seguinte comando para escutar mensagens da nuvem para dispositivo:
   
    ```shell
    python SimulatedDevice.py 
    ```
   
    ![Executar um aplicativo de dispositivo simulado][img-simulated-device]

1. Abra um novo prompt de comando e instale o **SDK de Serviço do Hub IoT do Azure para Python**.

    ```
    pip install azure-iothub-service-client
    ```

1. No prompt de comando, execute o seguinte comando para enviar uma mensagem da nuvem para dispositivo e aguarde os comentários da mensagem:
   
    ```shell
    python SendCloudToDeviceMessage.py 
    ```
   
    ![Execute o aplicativo para enviar o comando da nuvem para o dispositivo][img-send-command]
   
1. Observe a mensagem recebida pelo dispositivo.

    ![Mensagem recebida][img-message-recieved]


## <a name="next-steps"></a>Próximas etapas
Neste tutorial você aprendeu a enviar e receber mensagens da nuvem para o dispositivo. 

Para ver exemplos de soluções completas que usam o Hub IoT, consulte [Azure IoT Suite].

Para saber mais sobre como desenvolver soluções com o Hub IoT, consulte o [Guia do desenvolvedor do Hub IoT].

<!-- Images -->
[img-simulated-device]: media/iot-hub-python-python-c2d/simulated-device.png
[img-send-command]:  media/iot-hub-python-python-c2d/send-command.png
[img-message-recieved]: media/iot-hub-python-python-c2d/message-recieved.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[Introdução ao Hub IoT]: iot-hub-node-node-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[Guia do desenvolvedor do Hub IoT]: iot-hub-devguide.md
[Centro de Desenvolvedores do IoT do Azure]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Portal do Azure]: https://portal.azure.com
[Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
