---
title: "Métodos diretos do Hub IoT do Azure (Python) | Microsoft Docs"
description: "Como usar os métodos diretos do Hub IoT do Azure. Use os SDKs do IoT do Azure para Python para implementar um aplicativo de dispositivo simulado que inclui um método direto e um aplicativo de serviço que invoca o método direto."
services: iot-hub
documentationcenter: 
author: msebolt
manager: timlt
editor: 
ms.assetid: ea9c73ca-7778-4e38-a8f1-0bee9d142f04
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo
ms.openlocfilehash: 9dac7b45894c2da0dcd32e456c8806faadf814e9
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2018
---
# <a name="use-direct-methods-on-your-iot-device-with-python"></a>Usar métodos diretos no dispositivo IoT com o Python
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

Ao final deste tutorial, você terá dois aplicativos de console do Python:

* **CallMethodOnDevice.py**, que chama um método no aplicativo de dispositivo simulado e exibe a resposta.
* **SimulatedDevice.py**, que se conecta ao hub IoT com a identidade do dispositivo criada anteriormente e responde ao método chamado pela nuvem.

> [!NOTE]
> O artigo [SDKs de IoT do Azure][lnk-hub-sdks] fornece informações sobre os SDKs de IoT do Azure que você pode usar para criar aplicativos executados em dispositivos e no back-end da solução.
> 

Para concluir este tutorial, você precisará do seguinte:

* [Python 2.x or 3.x][lnk-python-download]. Certifique-se de usar a instalação de 32 bits ou 64 bits conforme exigido pelo seu programa de instalação. Quando solicitado durante a instalação, certifique-se de adicionar Python à variável de ambiente específica da plataforma. Se você estiver usando o Python 2. x, talvez seja necessário [instalar ou atualizar o *pip*, o sistema de gerenciamento de pacotes do Python][lnk-install-pip].
* Se você estiver usando o sistema operacional Windows, então o [Pacote redistribuível do Visual C++][lnk-visual-c-redist] permite o uso de DLLs nativas do Python.
* Uma conta ativa do Azure. (Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]


## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado
Nesta seção, você cria um aplicativo de console do Python que responde a um método chamado pela nuvem.

1. Usando um editor de texto, crie um novo arquivo **SimulatedDevice.py**.

1. Adicione as seguintes instruções `import` e variáveis ao início do arquivo **SimulatedDevice.py**:
   
    ```python
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    
    WAIT_COUNT = 5
    METHOD_CONTEXT = 0
    METHOD_CALLBACKS = 0

    # chose MQTT or MQTT_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "<deviceConnectionString>"
    ```

1. Adicione as seguintes funções para implementar o retorno de chamada de método de dispositivo e o método direto no dispositivo:
   
    ```python
    def device_method_callback(method_name, payload, user_context):
        global METHOD_CALLBACKS
    
        if method_name == "DeviceMethod":
            onDeviceMethod()
        
        print ( "\nMethod callback called with:\nmethodName = %s\npayload = %s\ncontext = %s" % (method_name, payload, user_context) )
        METHOD_CALLBACKS += 1
    
        print ( "Total calls confirmed: %d\n" % METHOD_CALLBACKS )
        device_method_return_value = DeviceMethodReturnValue()
        device_method_return_value.response = "{ \"Response\": \"This is the response from the device\" }"
        device_method_return_value.status = 200
    
        return device_method_return_value

    def onDeviceMethod():
        print ( "Direct method called." )
    ```

1. Adicione a seguinte função para abrir a conexão com o hub IoT e inicializar o ouvinte do método:
   
    ```python
    def iothub_client_init():
        # prepare iothub client
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        if client.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            client.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

        return client
    ```

1. Adicione as funções principais:

    ```python
    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for direct method call, press Ctrl-C to exit" )

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
        print ( "Starting the IoT Hub Python direct methods sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. Salve e feche o arquivo **SimulatedDevice.py**.

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição (tais como tentar conexão novamente), conforme sugerido no artigo do MSDN [Tratamento de Falhas Transitórias][lnk-transient-faults].
> 


## <a name="call-a-method-on-a-device"></a>Chamar um método em um dispositivo
Nesta seção, você cria um aplicativo de console do Python que chama um método no aplicativo de dispositivo simulado e, em seguida, exibe a resposta.


1. Usando um editor de texto, crie um novo arquivo **CallMethodOnDevice.py**.

1. Adicione as seguintes instruções `import` e variáveis ao início do arquivo **CallMethodOnDevice.py**:
   
    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "<IoTHubConnectionString>"
    DEVICE_ID = "<deviceId>"

    METHOD_NAME = "DeviceMethod"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    ```

1. Adicione as seguintes funções para implementar o retorno de chamada de método de dispositivo e o método direto no dispositivo:
   
    ```python
    def iothub_devicemethod_sample_run():
        try:
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Device Method called" )
            print ( "Device Method name       : {0}".format(METHOD_NAME) )
            print ( "Device Method payload    : {0}".format(METHOD_PAYLOAD) )
            print ( "" )
            print ( "Response status          : {0}".format(response.status) )
            print ( "Response payload         : {0}".format(response.payload) )

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )
    ```

1. Adicione a função principal:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceMethod Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

1. Salve e feche o arquivo **CallMethodOnDevice.py**.

## <a name="run-the-apps"></a>Executar os aplicativos
Agora você está pronto para executar os aplicativos.

1. Em um prompt de comando, execute o seguinte comando para iniciar a escuta de chamadas de método de seu Hub IoT:
   
    ```cmd/sh
    python SimulatedDevice.py
    ```
   
    ![][7]

1. Em um prompt de comando, execute o seguinte comando para iniciar o monitoramento de seu Hub IoT:
   
    ```cmd/sh
    python CallMethodOnDevice.py
    ```
   
    ![][8]

1. Você verá o dispositivo reagir ao método imprimindo a mensagem e o aplicativo que chamou o método exibindo a resposta do dispositivo:
   
    ![][9]

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você configurou um novo hub IoT no portal do Azure e depois criou uma identidade do dispositivo no Registro de identidade do Hub IoT. Você usou essa identidade do dispositivo para habilitar o aplicativo do dispositivo simulado para reagir aos métodos invocados pela nuvem. Você também criou um aplicativo que invoca métodos no dispositivo e exibe a resposta do dispositivo. 

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, confira:

* [Introdução ao Hub IoT]
* [Agendar trabalhos em vários dispositivos][lnk-devguide-jobs]

Para saber como estender sua solução de IoT e agendar chamadas de método em vários dispositivos, confira o tutorial [Agendar e difundir trabalhos][lnk-tutorial-jobs].

<!-- Images. -->
[7]: ./media/iot-hub-python-python-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-python-python-direct-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-python-python-direct-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Introdução ao Hub IoT]: iot-hub-node-node-getstarted.md
