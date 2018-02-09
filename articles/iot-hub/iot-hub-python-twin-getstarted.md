---
title: "Introdução aos dispositivos gêmeos do Hub IoT do Azure (Python) | Microsoft Docs"
description: "Como usar dispositivos gêmeos do Hub IoT do Azure para adicionar marcas e usar uma consulta do Hub IoT. Use os SDKs do IoT do Azure para Python para implementar o aplicativo de dispositivo simulado e um aplicativo de serviço que adiciona as marcações e executa a consulta do Hub IoT."
services: iot-hub
documentationcenter: python
author: msebolt
manager: timlt
editor: 
ms.assetid: 314c88e4-cce1-441c-b75a-d2e08e39ae7d
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/04/2017
ms.author: v-masebo
ms.openlocfilehash: 20c1eeee6ca690ddcf0b9489b88689213b79488e
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2018
---
# <a name="get-started-with-device-twins-python"></a>Introdução aos dispositivos gêmeos (Python)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Ao final deste tutorial, você terá dois aplicativos de console do Python:

* **AddTagsAndQuery.py**, um aplicativo de back-end do Python, que adiciona marcações e consulta os dispositivos gêmeos.
* **ReportConnectivity.py**, um aplicativo do Python, que simula um dispositivo que se conecta ao hub IoT com a identidade do dispositivo criada anteriormente e relata sua condição de conectividade.

> [!NOTE]
> O artigo [SDKs do IoT do Azure][lnk-hub-sdks] fornece informações sobre os SDKs do IoT do Azure que você pode usar para compilar dispositivos e aplicativos back-end.
> 
> 

Para concluir este tutorial, você precisará do seguinte:

* [Python 2.x or 3.x][lnk-python-download]. Certifique-se de usar a instalação de 32 bits ou 64 bits conforme exigido pelo seu programa de instalação. Quando solicitado durante a instalação, certifique-se de adicionar Python à variável de ambiente específica da plataforma. Se você estiver usando o Python 2. x, talvez seja necessário [instalar ou atualizar o *pip*, o sistema de gerenciamento de pacotes do Python][lnk-install-pip].
* Se você estiver usando o sistema operacional Windows, então o [Pacote redistribuível do Visual C++][lnk-visual-c-redist] permite o uso de DLLs nativas do Python.
* Uma conta ativa do Azure. (Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

> [!NOTE]
> Os pacotes *pip* para `azure-iothub-service-client` e `azure-iothub-device-client` atualmente estão disponíveis apenas para o sistema operacional Windows. Para Linux/Mac OS, confira as seções do Linux e Mac OS específicas na publicação [Preparação do seu ambiente de desenvolvimento para o Python][lnk-python-devbox].
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-service-app"></a>Criar o aplicativo do serviço
Nesta seção, você cria um aplicativo de console do Python que adiciona metadados de local ao dispositivo gêmeo associado à **{Device Id}**. Em seguida, ele consulta os dispositivos gêmeos armazenados no hub IoT selecionando os dispositivos localizados em Redmond e, depois, aqueles que relatam uma conexão celular.

1. Abra um prompt de comando e instale o **SDK de Serviço do Azure IoT Hub para Python**. Feche o prompt de comando depois de instalar o SDK.

    ```
    pip install azure-iothub-service-client
    ```

1. Usando um editor de texto, crie um novo arquivo **AddTagsAndQuery.py**.

3. Adicione o código a seguir para importar os módulos necessários do SDK de serviço:

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubError
    ```
2. Adicione o código a seguir, substituindo o espaço reservado de `[IoTHub Connection String]` e `[Device Id]` pela cadeia de conexão do hub IoT e da ID do dispositivo criado nas seções anteriores.
   
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

1. Adicione o seguinte código ao arquivo **AddTagsAndQuery.py**:
   
     ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
        
            iothub_registry_statistics = iothub_registry_manager.get_statistics()
            print ( "Total device count                       : {0}".format(iothub_registry_statistics.totalDeviceCount) )
            print ( "Enabled device count                     : {0}".format(iothub_registry_statistics.enabledDeviceCount) )
            print ( "Disabled device count                    : {0}".format(iothub_registry_statistics.disabledDeviceCount) )
            print ( "" )

            number_of_devices = iothub_registry_statistics.totalDeviceCount
            dev_list = iothub_registry_manager.get_device_list(number_of_devices)
        
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            for device in range(0, number_of_devices):
                if dev_list[device].deviceId == DEVICE_ID:
                    twin_info = iothub_twin_method.update_twin(dev_list[device].deviceId, UPDATE_JSON)
        
            print ( "Devices in Redmond: " )        
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)
         
                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    print ( dev_list[device].deviceId )
        
            print ( "" )
        
            print ( "Devices in Redmond using cellular network: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)
                
                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    if twin_info.find(UPDATE_JSON_CLIENT_SEARCH) > -1:
                        print ( dev_list[device].deviceId )

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "IoTHub sample stopped" )
    ```
   
    O objeto **Registry** expõe todos os métodos necessários para interagir com gêmeos de dispositivo do serviço. O código primeiro inicializa o objeto **Registry**, em seguida, atualiza o dispositivo gêmeo para **deviceId** e, por fim, executa duas consultas. A primeira seleciona somente os dispositivos gêmeos de dispositivos localizados na fábrica de **Redmond43**, enquanto a segunda refina a consulta para selecionar somente os dispositivos que também estão conectados por meio da rede de celular.
   
1. Adicione o seguinte código ao final de **AddTagsAndQuery.py** para implementar a função **iothub_service_sample_run**:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

1. Execute o aplicativo com:
   
    ```cmd/sh
    python AddTagsAndQuery.py
    ```
   
    Você deve ver um dispositivo nos resultados da consulta que pergunta sobre todos os dispositivos localizados em **Redmond43**, e nenhum para a consulta que restringe os resultados para dispositivos que usam uma rede de celular.
   
    ![primeira consulta][1]

Na seção seguinte, você cria um aplicativo de dispositivo que reporta as informações de conectividade e altera o resultado da consulta na seção anterior.

## <a name="create-the-device-app"></a>Criar o aplicativo do dispositivo
Nesta seção, você cria um aplicativo de console do Python que se conecta ao seu hub como **{Device Id}** e, em seguida, atualiza as propriedades relatadas de seu dispositivo gêmeo para que elas contenham as informações indicando que ele está conectado usando uma rede de celular.

1. Abra um prompt de comando e instale o **SDK de Serviço do Azure IoT Hub para Python**. Feche o prompt de comando depois de instalar o SDK.

    ```
    pip install azure-iothub-device-client
    ```

1. Usando um editor de texto, crie um novo arquivo **ReportConnectivity.py**.

3. Adicione o código a seguir para importar os módulos necessários do SDK de serviço:

    ```python
    import time
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError
    ```

2. Adicione o código a seguir, substituindo o espaço reservado de `[IoTHub Device Connection String]` pela cadeia de conexão do dispositivo de hub IoT criado nas seções anteriores.
   
    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"

    # choose HTTP, AMQP, AMQP_WS or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT

    TIMER_COUNT = 5
    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0
    ```

1. Adicione o seguinte código ao arquivo **ReportConnectivity.py** para implementar a funcionalidade de dispositivo gêmeo:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "" )
        print ( "Twin callback called with:" )
        print ( "    updateStatus: %s" % update_state )
        print ( "    payload: %s" % payload )

    def send_reported_state_callback(status_code, user_context):
        print ( "" )
        print ( "Confirmation for reported state called with:" )
        print ( "    status_code: %d" % status_code )

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        if client.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            client.set_device_twin_callback(
                device_twin_callback, TWIN_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            if client.protocol == IoTHubTransportProvider.MQTT:
                print ( "Sending data as reported property..." )

                reported_state = "{\"connectivity\":\"cellular\"}"

                client.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            while True:
                print ( "Press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= TIMER_COUNT:
                    status = client.get_send_status()
                    time.sleep(10)
                    status_counter += 1 
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```   

    O objeto **Client** expõe todos os métodos necessários para você interagir com dispositivos gêmeos a partir do dispositivo. O código anterior, depois de inicializar o objeto **Client**, recupera o dispositivo gêmeo para o dispositivo e atualiza suas propriedades relatadas com as informações de conectividade.

1. Adicione o seguinte código ao final de **ReportConnectivity.py** para implementar a função **iothub_client_sample_run**:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )

        iothub_client_sample_run()
    ```

1. Executar o aplicativo do dispositivo
   
    ```cmd/sh
    python ReportConnectivity.js
    ```
   
    Você deve ver a confirmação de que os dispositivos gêmeos foram atualizados.

    ![atualizar gêmeos][2]

6. Agora que o dispositivo divulgou suas informações de conectividade, ele deve aparecer em ambas as consultas. Volte e execute as consultas novamente:
   
    ```cmd/sh
    python AddTagsAndQuery.js
    ```
   
    Desta vez, **{Device Id}** deve aparecer em ambos os resultados da consulta.
   
    ![segunda consulta][3]

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você configurou um novo hub IoT no portal do Azure e depois criou uma identidade do dispositivo no Registro de identidade do Hub IoT. Você adicionou os metadados do dispositivo como marcações de um aplicativo de back-end e criou um aplicativo de dispositivo simulado para relatar informações de conectividade no dispositivo gêmeo. Você também aprendeu a consultar essas informações usando o registro.

Veja os recursos a seguir para saber como:

* Enviar telemetria de dispositivos com o tutorial [Introdução ao Hub IoT][lnk-iothub-getstarted].
* Configurar dispositivos usando as propriedades desejadas do dispositivo gêmeo com o tutorial [Usar as propriedades desejadas para configurar dispositivos][lnk-twin-how-to-configure].
* Controlar dispositivos interativamente (como ativar uma ventoinha de um aplicativo controlado pelo usuário) com o tutorial [Usar métodos diretos][lnk-methods-tutorial].

<!-- images -->
[1]: media/iot-hub-python-twin-getstarted/1.png
[2]: media/iot-hub-python-twin-getstarted/2.png
[3]: media/iot-hub-python-twin-getstarted/3.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-python-devbox]: https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-python-getstarted.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
