---
title: "Usar as propriedades do dispositivo gêmeo do Hub IoT do Azure (Python) | Microsoft Docs"
description: "Como usar dispositivos gêmeos do Hub IoT do Azure para configurar dispositivos. Use os SDKs do IoT do Azure para Python para implementar um aplicativo de dispositivo simulado e um aplicativo de serviço que modifica a configuração de um dispositivo usando um dispositivo gêmeo."
services: iot-hub
documentationcenter: .net
author: msebolt
manager: timlt
editor: 
ms.assetid: d0bcec50-26e6-40f0-8096-733b2f3071ec
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2018
ms.author: v-masebo
ms.openlocfilehash: d0d5a30a76068eb3212124fd14e7ea1616b75708
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="use-desired-properties-to-configure-devices-python"></a>Usar as propriedades desejadas para configurar os dispositivos (Python)
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

Ao final deste tutorial, você terá dois aplicativos de console do Python:

* **SimulateDeviceConfiguration.py**, um aplicativo de dispositivo simulado que aguarda uma atualização da configuração desejada e reporta o status de um processo simulado de atualização de configuração.
* **SetDesiredConfigurationAndQuery.py**, um aplicativo de back-end que define a configuração desejada em um dispositivo e consulta o processo de atualização de configuração.

> [!NOTE]
> O artigo [SDKs do IoT do Azure][lnk-hub-sdks] fornece informações sobre os SDKs do IoT do Azure que você pode usar para compilar dispositivos e aplicativos back-end.
> 
> 

Para concluir este tutorial, você precisará do seguinte:

* [Python 2.x or 3.x][lnk-python-download]. Certifique-se de usar a instalação de 32 bits ou 64 bits conforme exigido pelo seu programa de instalação. Quando solicitado durante a instalação, certifique-se de adicionar Python à variável de ambiente específica da plataforma. Se você estiver usando o Python 2. x, talvez seja necessário [instalar ou atualizar o *pip*, o sistema de gerenciamento de pacotes do Python][lnk-install-pip].
* Se você estiver usando o sistema operacional Windows, então o [Pacote redistribuível do Visual C++][lnk-visual-c-redist] permite o uso de DLLs nativas do Python.
* Uma conta ativa do Azure. (Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

Caso tenha seguido o tutorial [Introdução aos dispositivos gêmeos][lnk-twin-tutorial], você já terá um Hub IoT e uma identidade de dispositivo chamada **myDeviceId**; você poderá pular para a seção [Criar o aplicativo do dispositivo simulado][lnk-how-to-configure-createapp].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-simulated-device-app"></a>Criar o aplicativo de dispositivo simulado
Nesta seção, você cria um aplicativo de console Python que se conecta ao seu hub como **myDeviceId**, aguarda uma atualização de configuração desejada e, em seguida, reporta atualizações sobre o processo simulado de atualização de configuração.

1. Instale o **SDK do Dispositivo Python do IoT do Azure** usando o seguinte comando no prompt de comando:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Usando um editor de texto, crie um novo arquivo **SimulateDeviceConfiguration.py**.

1. Adicione o seguinte código ao arquivo **SimulateDeviceConfiguration.py** e substitua o espaço reservado **{cadeia de conexão do dispositivo}** pela cadeia de conexão do dispositivo copiada quando você criou a identidade do dispositivo **myDeviceId**:

    ```python   
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.MQTT

    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    WAIT_COUNT = 5

    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0

    TWIN_CALLBACKS = 0
    SEND_REPORTED_STATE_CALLBACKS = 0

    CONFIG_ID = "1"
    TWIN_PAYLOAD = "{\"configId\":" + CONFIG_ID + ",\"sendFrequency\":\"24h\"}"
    ```

    O objeto **CLIENT** expõe todos os métodos necessários para interagir com os dispositivos gêmeos do dispositivo. Em outro código, você anexará um manipulador para a atualização nas propriedades desejadas e, em seguida, adicionará um manipulador adicional para verificar se há uma solicitação de alteração de configuração real, comparando os configIds, que então invoca um método que inicia a alteração de configuração. Por uma questão de simplicidade, o código anterior usa um padrão codificado para a configuração inicial. Um aplicativo real provavelmente carregaria essa configuração de um armazenamento local.
   
   > [!IMPORTANT]
   > Eventos de alteração de propriedade desejada são sempre emitidos uma vez no ato da conexão com o dispositivo, não deixe de verificar se há uma alteração real nas propriedades desejadas antes de executar qualquer ação.
   > 

1. Adicione o seguinte código ao final do **SimulateDeviceConfiguration.py**:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global CONFIG_ID

        desired_configId = payload[payload.find("configId")+11:payload.find("configId")+12]
        if CONFIG_ID != desired_configId:
            print ( "" )
            print ( "Reported pending config change: %s" % payload)
        
            desired_sendFrequency = payload[payload.find("sendFrequency")+17:payload.find("sendFrequency")+19]
            print ( "...desired configId: " + desired_configId)
            print ( "...desired sendFrequency: " + desired_sendFrequency)
            new_payload = "{\"configId\":" + desired_configId + ",\"sendFrequency\":\"" + desired_sendFrequency + "\"}"
            CLIENT.send_reported_state(new_payload, len(new_payload), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        
        CONFIG_ID = desired_configId
    
    def send_reported_state_callback(status_code, user_context):
        global SEND_REPORTED_STATE_CALLBACKS
    
        print ( "" )
        print ( "Device twins updated." )
    ```
   
    O método **device_twin_callback** atualiza as propriedades relatadas no objeto do dispositivo gêmeo local com a solicitação de atualização de configuração e define o _configId_. Após atualizar dispositivo gêmeo com êxito, ele imprime uma mensagem de atualização. Para economizar largura de banda, as propriedades reportadas são atualizadas especificando apenas as propriedades a serem modificadas (nomeadas **TWIN_PAYLOAD** no código acima), em vez de substituir o documento inteiro.
   
   > [!NOTE]
   > Este tutorial não simula nenhum comportamento para atualizações de configuração simultâneas. Alguns processos de atualização de configuração podem ser capazes de acomodar as alterações de configuração de destino enquanto a atualização está em execução, outros podem colocá-las em fila e outros poderiam ainda rejeitá-las com uma condição de erro. Certifique-se de considerar o comportamento desejado para o seu processo de configuração específico e adicione a lógica apropriada antes de iniciar a alteração de configuração.
   > 

1. Adicione o seguinte código ao final do **SimulateDeviceConfiguration.py**: 

    ```python
    def iothub_client_init():
        if CLIENT.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            CLIENT.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

    def iothub_client_sample_run():
        try:
            iothub_client_init()
        
            CLIENT.send_reported_state(TWIN_PAYLOAD, len(TWIN_PAYLOAD), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. Execute o aplicativo do dispositivo:
   
    ```cmd/sh
    node SimulateDeviceConfiguration.js
    ```
   
    Você deve ver a mensagem `Device twins updated.`. Mantenha o aplicativo em execução.


## <a name="create-the-service-app"></a>Criar o aplicativo do serviço
Nesta seção, você criará um aplicativo de console Python que atualiza as *propriedades desejadas* no dispositivo gêmeo associado à **myDeviceId** com um novo objeto de configuração de telemetria. Ele então consulta os dispositivos gêmeos armazenados no Hub IoT e mostra a diferença entre as configurações desejadas e reportadas do dispositivo.

1. Instale o **SDK do Serviço Python do IoT do Azure** usando o seguinte comando no prompt de comando:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. Usando um editor de texto, crie um novo arquivo **SetDesiredAndQuery.py**.

1. Adicione o código a seguir ao arquivo **SetDesiredAndQuery.py** e substitua o espaço reservado **{IoTHubConnectionString}** pela cadeia de conexão do Hub IoT que você copiou quando criou o hub e o espaço reservado **{deviceId}** pelo nome do seu dispositivo:

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubError, IoTHubDeviceTwin

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"configId\":3, \"sendFrequency\":\"" + sys.argv[1:][0] + "\"}}}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

1. Adicione o código a seguir ao final do arquivo **SetDesiredAndQuery.py**:

    ```python
    def iothub_devicetwin_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
        
            twin_info = iothub_twin_method.get_twin(DEVICE_ID)
            print ( "" )
            print ( "Device Twin before update    :" )
            print ( "{0}".format(twin_info) )

            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)
            print ( "" )
            print ( "Device Twin after update     :" )
            print ( "{0}".format(twin_info) )
        
            while True:
                time.sleep(10)

                twin_info = iothub_twin_method.get_twin(DEVICE_ID)
                print ( "" )
                print ( "Device Twin after client change initiated    :" )
                print ( "{0}".format(twin_info) )
        
                print ( "" )
                print ( "IoT Hub service sample complete, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(30)
                
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceTwins Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicetwin_sample_run()
    ```

1. Com **SimulateDeviceConfiguration.py** em execução, execute o aplicativo em um novo prompt de comando com:

    ```cmd/sh
    python SetDesiredAndQuery.py 5m
    ```
   
    Você deve ver a alteração da ID de configuração relatada de **1** a **2** com a nova frequência de envio ativa de cinco minutos em vez de 24 horas.


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você definiu uma configuração desejada como *propriedades desejadas* de um aplicativo de back-end e gravou um aplicativo de dispositivo simulado para detectar essa alteração e simulou um processo de atualização relatando seu status como *propriedades relatadas* ao dispositivo gêmeo.

Veja os recursos a seguir para saber como:

* Enviar telemetria de dispositivos com o tutorial [Introdução ao Hub IoT][lnk-iothub-getstarted].
* Para agendar ou executar operações em grandes conjuntos de dispositivos, consulte o tutorial[Agendar e difundir trabalhos][lnk-schedule-jobs].
* Controlar dispositivos interativamente (como ativar uma ventoinha de um aplicativo controlado pelo usuário) com o tutorial [Usar métodos diretos][lnk-methods-tutorial].

<!-- links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-python-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-iothub-getstarted]: iot-hub-python-getstarted.md
[lnk-methods-tutorial]: iot-hub-python-python-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app
