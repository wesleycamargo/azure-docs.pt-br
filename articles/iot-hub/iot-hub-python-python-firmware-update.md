---
title: "Atualização de firmware do dispositivo com o Hub IoT do Azure (Python) | Microsoft Docs"
description: "Como usar o gerenciamento de dispositivos no Hub IoT do Azure para iniciar uma atualização de firmware do dispositivo. Use os SDKs do IoT do Azure para Python para implementar um aplicativo de dispositivo simulado e um aplicativo de serviço que dispara a atualização do firmware."
services: iot-hub
documentationcenter: .net
author: msebolt
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2018
ms.author: v-masebo
ms.openlocfilehash: e7ca0d95da3fd36e6dad081bad0aa5a178436159
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-pythonpython"></a>Use o gerenciamento de dispositivos para iniciar uma atualização de firmware do dispositivo (Python/Python)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

No tutorial [Introdução ao gerenciamento de dispositivo][lnk-dm-getstarted], você viu como usar os primitivos [dispositivo gêmeo][lnk-devtwin] e [métodos diretos][lnk-c2dmethod] para reiniciar remotamente um dispositivo. Este tutorial usa os mesmos primitivos do Hub IoT, fornece orientações e mostra como fazer uma atualização de firmware simulada de ponta a ponta.  Esse padrão é usado na implementação da atualização de firmware para o dispositivo de exemplo Intel Edison.

Este tutorial mostra como:

* Criar um aplicativo de console Python que chama o método direto firmwareUpdate no aplicativo de dispositivo simulado por meio do Hub IoT.
* Criar um aplicativo de dispositivo simulado que implementa um método direto **firmwareUpdate**. Esse método inicia um processo de várias etapas que aguarda o download, baixa e aplica a imagem do firmware. Durante cada etapa da atualização, o dispositivo usa as propriedades relatadas para informar sobre o progresso.

Ao final deste tutorial, você terá dois aplicativos de console do Python:

**dmpatterns_fwupdate_service.py**, que chama um método direto no aplicativo do dispositivo simulado, exibe a resposta e periodicamente (a cada 500 ms) exibe as propriedades relatadas atualizadas.

**dmpatterns_fwupdate_device.py**, que se conecta ao seu Hub IoT com a identidade do dispositivo criada anteriormente, recebe um método direto firmwareUpdate, é executado por meio de um processo de vários estados para simular uma atualização de firmware, incluindo: aguardar o download da imagem, baixar a nova imagem e aplicar a imagem.

Para concluir este tutorial, você precisará do seguinte:

* [Python 2.x or 3.x][lnk-python-download]. Certifique-se de usar a instalação de 32 bits ou 64 bits conforme exigido pelo seu programa de instalação. Quando solicitado durante a instalação, certifique-se de adicionar Python à variável de ambiente específica da plataforma. Se você estiver usando o Python 2. x, talvez seja necessário [instalar ou atualizar o *pip*, o sistema de gerenciamento de pacotes do Python][lnk-install-pip].
* Se você estiver usando o sistema operacional Windows, então o [Pacote redistribuível do Visual C++][lnk-visual-c-redist] permite o uso de DLLs nativas do Python.
* Uma conta ativa do Azure. (Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Disparar uma atualização de firmware remoto no dispositivo usando um método direto
Nesta seção, você criará um aplicativo do console Python que inicia uma atualização remota de firmware em um dispositivo. O aplicativo utiliza um método direto para iniciar a atualização e usa consultas em dispositivos gêmeos para receber periodicamente o status da atualização do firmware ativo.

1. No prompt de comando, execute o seguinte comando para instalar o pacote **azure-iothub-service-client**:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. Usando um editor de texto, no seu diretório de trabalho, crie um arquivo **dmpatterns_getstarted_service.py**.

1. Adicione as seguintes instruções e variáveis de ‘importar’ no início do arquivo **dmpatterns_getstarted_service.py**. Substitua `IoTHubConnectionString` e `deviceId` por seus valores observados anteriormente:
   
    ```python
    import sys
    import time

    import iothub_service_client
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "firmwareUpdate"
    METHOD_PAYLOAD = "{\"fwPackageUri\":\"test.com\"}"
    TIMEOUT = 60
    MESSAGE_COUNT = 5
    ```

1. Adicione a seguinte função para chamar o método direto e exibir o valor da propriedade relatada firmwareUpdate. Também adicione a rotina `main`:
   
    ```python
    def iothub_firmware_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            print ( "" )
            print ( "Direct Method called." )
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)
        
            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)
            print ( response.payload )
        
            print ( "" )
            print ( "Device Twin queried, press Ctrl-C to exit" )
            while True:
                twin_info = iothub_twin_method.get_twin(DEVICE_ID)

                if "\"firmwareStatus\":\"standBy\"" in twin_info:
                    print ( "Waiting on device..." )
                elif "\"firmwareStatus\":\"downloading\"" in twin_info:
                    print ( "Downloading firmware..." )
                elif "\"firmwareStatus\":\"applying\"" in twin_info:
                    print ( "Download complete, applying firmware..." )
                elif "\"firmwareStatus\":\"completed\"" in twin_info:
                    print ( "Firmware applied" )
                    print ( "" )
                    print ( "Get reported properties from device twin:" )
                    print ( twin_info )
                    break
                else:
                    print ( "Unknown status" )

                status_counter = 0
                while status_counter <= MESSAGE_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub firmware update Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_firmware_sample_run()
    ```

1. Salve e feche o arquivo **dmpatterns_fwupdate_service.py**.


## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado
Nesta seção, você:

* Criar um aplicativo de console do Python que responde a um método direto chamado pela nuvem
* Disparar uma atualização de firmware simulada
* Usar as propriedades relatadas para habilitar consultas de dispositivo gêmeo para identificar dispositivos e quando foi a última atualização de firmware concluída

1. No prompt de comando, execute o seguinte comando para instalar o pacote **azure-iothub-device-client**:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Usando um editor de texto, crie um arquivo **dmpatterns_fwupdate_device.py**.

1. Adicione as seguintes instruções e variáveis de ‘importar’ no início do arquivo **dmpatterns_fwupdate_device.py**. Substitua `deviceConnectionString` pela cadeia de conexão do dispositivo do Hub IoT:
   
    ```python
    import time, datetime
    import sys
    import threading

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    SEND_REPORTED_STATE_CONTEXT = 0
    METHOD_CONTEXT = 0

    MESSAGE_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)
    ```

1. Adicione as seguintes funções usadas para fornecer atualizações de propriedades relatadas e implementar o método direto:
   
    ```python
    def send_reported_state_callback(status_code, user_context):
        print ( "Reported state updated." )

    def device_method_callback(method_name, payload, user_context):
        if method_name == "firmwareUpdate":
            print ( "Starting firmware update." )
            image_url = payload
            thr = threading.Thread(target=simulate_download_image, args=([payload]), kwargs={})
            thr.start()
    
            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"Firmware update started\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

1. Adicione as seguintes funções que simulam baixar e aplicar a imagem do firmware:
   
    ```python
    def simulate_download_image(image_url):
        time.sleep(15)
        print ( "Downloading image from: " + image_url )

        reported_state = "{\"firmwareStatus\":\"downloading\", \"downloadComplete\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        time.sleep(15)
    
        simulate_apply_image(image_url)
    
    def simulate_apply_image(image_url):
        print ( "Applying image from: " + image_url )

        reported_state = "{\"firmwareStatus\":\"applying\", \"startedApplyingImage\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        time.sleep(15)
    
        simulate_complete_image()

    def simulate_complete_image():
        print ( "Image applied." )

        reported_state = "{\"firmwareStatus\":\"completed\", \"lastFirmwareUpdate\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
    ```

8. Adicione a função a seguir que inicializa as propriedades relatadas do dispositivo gêmeo e aguarde até o método ser chamado. Também adicione a rotina `main`:
   
    ```python
    def iothub_firmware_sample_run():
        try:
            CLIENT.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

            reported_state = "{\"firmwareStatus\":\"standBy\", \"logTime\":\"" + str(datetime.datetime.now()) + "\"}"
            CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
            print ( "Device twins initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )
        
            while True:
                status_counter = 0
                while status_counter <= MESSAGE_COUNT:
                    time.sleep(10)
                    status_counter += 1
            
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python firmware update sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_firmware_sample_run()
    ```

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), conforme sugerido no artigo [Tratamento de falhas transitórias](https://msdn.microsoft.com/library/hh675232.aspx) do MSDN.
> 


## <a name="run-the-apps"></a>Executar os aplicativos
Agora você está pronto para executar os aplicativos.

1. No prompt de comando, execute o seguinte comando para começar a escutar o método direto de reinicialização.
   
    ```cmd/sh
    python dmpatterns_fwupdate_device.py
    ```

1. Em outro prompt de comando, execute o seguinte comando para disparar a reinicialização e a consulta remotas para que o dispositivo gêmeo localize o tempo de reinicialização mais recente.
   
    ```cmd/sh
    python dmpatterns_fwupdate_service.py
    ```

1. Você verá a resposta do dispositivo para o método direto no console. Observe a alteração nas propriedades relatadas durante a atualização de firmware.

    ![saída do programa][1]


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você usou um método direto para disparar uma atualização remota de firmware em um dispositivo e utilizou as propriedades relatadas para acompanhar o andamento da atualização do firmware.

Para saber como estender sua solução de IoT e agendar chamadas de método em vários dispositivos, confira o tutorial [Agendar e difundir trabalhos][lnk-tutorial-jobs].

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[1]: ./media/iot-hub-python-python-firmware-update/1.png
