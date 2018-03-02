---
title: Agendar trabalhos com o Hub IoT do Azure (Python) | Microsoft Docs
description: "Como agendar um trabalho do Hub IoT do Azure para invocar um método direto em vários dispositivos. Você pode usar os SDKs do IoT do Azure para Python para implementar os aplicativos do dispositivo simulado e um aplicativo de serviço para executar o trabalho."
services: iot-hub
documentationcenter: .net
author: msebolt
manager: timlt
editor: 
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2018
ms.author: v-masebo
ms.openlocfilehash: 3b6daf1ce1116ea3787550f9dac8d90b3751f9af
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="schedule-and-broadcast-jobs-python"></a>Agendar e difundir trabalhos (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

O Hub IoT do Azure é um serviço completamente gerenciado que permite que um aplicativo back-end crie e controle trabalhos que agendam e atualizam milhões de dispositivos.  Os trabalhos podem ser usados para as seguintes ações:

* Atualizar as propriedades desejadas
* Marcas de atualização
* Chamar métodos diretos

Conceitualmente, um trabalho encapsula uma dessas ações e rastreia o progresso da execução com relação a um conjunto de dispositivos, definido por uma consulta do dispositivo gêmeo.  Por exemplo, um aplicativo de back-end pode usar um trabalho para invocar um método de reinicialização em 10.000 dispositivos, especificado por uma consulta do dispositivo gêmeo e agendado no futuro.  Esse aplicativo pode acompanhar o progresso à medida que cada um desses dispositivos recebem e executam o método de reinicialização.

Saiba mais sobre cada um desses recursos nestes artigos:

* Dispositivo gêmeo e propriedades: [Introdução os dispositivos gêmeos][lnk-get-started-twin] e [Tutorial: Como usar as propriedades do dispositivo gêmeo][lnk-twin-props]
* Métodos diretos: [Guia do desenvolvedor do Hub IoT – métodos diretos][lnk-dev-methods] e [Tutorial: métodos diretos][lnk-c2d-methods]

Este tutorial mostra como:

* Criar um aplicativo do dispositivo simulado Python que tem um método direto para habilitar o **lockDoor** que pode ser chamado pelo back-end da solução.
* Crie um aplicativo de console Python que chama o método direto **lockDoor** no aplicativo de dispositivo simulado usando um trabalho e atualiza as propriedades desejadas usando um trabalho de dispositivo.

No fim deste tutorial, você tem dois aplicativos de Python:

**simDevice.py**, que se conecta ao seu hub IoT com a identidade do dispositivo e recebe um método direto **lockDoor**.

**scheduleJobService.py**, que chama um método direto no aplicativo do dispositivo simulado e atualiza as propriedades desejadas do dispositivo gêmeo usando um trabalho.

Para concluir este tutorial, você precisará do seguinte:

* [Python 2.x or 3.x][lnk-python-download]. Certifique-se de usar a instalação de 32 bits ou 64 bits conforme exigido pelo seu programa de instalação. Quando solicitado durante a instalação, certifique-se de adicionar Python à variável de ambiente específica da plataforma. Se você estiver usando o Python 2. x, talvez seja necessário [instalar ou atualizar o *pip*, o sistema de gerenciamento de pacotes do Python][lnk-install-pip].
* Se você estiver usando o sistema operacional Windows, então o [Pacote redistribuível do Visual C++][lnk-visual-c-redist] permite o uso de DLLs nativas do Python.
* Uma conta ativa do Azure. (Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

> [!NOTE]
> O **SDK do IoT do Azure para Python** não dá suporte diretamente à funcionalidade **Trabalhos**. Em vez disso, este tutorial oferece uma solução alternativa usando temporizadores e segmentos assíncronos. Para obter informações mais atualizadas, confira a lista de recursos do **SDK de cliente do serviço** na página [SDK do IoT do Azure para Python](https://github.com/Azure/azure-iot-sdk-python). 
> 
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]


## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado
Nesta seção, você cria um aplicativo de console do Python que responde a um método direto chamado pela nuvem, que aciona um método **lockDoor** simulado.

1. No prompt de comando, execute o seguinte comando para instalar o pacote **azure-iot-device-client**:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Usando um editor de texto, crie um novo arquivo **simDevice.py** no diretório de trabalho.

1. Adicione as instruções `import` e variáveis a seguir ao início do arquivo **simDevice.py**. Substitua `deviceConnectionString` pela cadeia de conexão do dispositivo criada acima:
   
    ```python
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    METHOD_CONTEXT = 0
    TWIN_CONTEXT = 0
    WAIT_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Adicione o seguinte retorno de chamada de função para manipular o método **lockDoor**:
   
    ```python
    def device_method_callback(method_name, payload, user_context):
        if method_name == "lockDoor":
            print ( "Locking Door!" )

            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"lockDoor called successfully\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

1. Adicione outra retorno de chamada de função para manipular atualizações de dispositivos gêmeos:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "")
        print ( "Twin callback called with:")
        print ( "payload: %s" % payload )
    ```

1. Adicione o seguinte código para registrar o manipulador do método **lockDoor**. Também inclui a rotina `main`:
   
    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
            client.set_device_method_callback(device_method_callback, METHOD_CONTEXT)
            client.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

            print ( "Direct method initialized." )
            print ( "Device twin callback initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )
        
            while True:
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
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_jobs_sample_run()
    ```

1. Salve e feche o arquivo **simDevice.py**.

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), como sugerido no artigo [Tratamento de falhas transitórias][lnk-transient-faults] do MSDN.
> 
> 


## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Agendar trabalhos para chamar um método direto e atualizar as propriedades do dispositivo gêmeo
Nesta seção, é possível criar um aplicativo de console do Python que inicia um **lockDoor** remoto em um dispositivo usando um método direto e atualizar as propriedades do dispositivo gêmeo.

1. No prompt de comando, execute o seguinte comando para instalar o pacote **azure-iot-service-client**:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. Usando um editor de texto, crie um novo arquivo **scheduleJobService.py** no diretório de trabalho.

1. Adicione as seguintes instruções `import` e variáveis ao início do arquivo **scheduleJobService.py**:
   
    ```python
    import sys
    import time
    import threading
    import uuid

    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_JSON = "{\"properties\":{\"desired\":{\"building\":43,\"floor\":3}}}"
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

1. Adicione a seguinte função que é usada para consultar dispositivos:
   
    ```python
    def query_condition(device_id):
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
    
        number_of_devices = 10
        dev_list = iothub_registry_manager.get_device_list(number_of_devices)
    
        for device in range(0, number_of_devices):
            if dev_list[device].deviceId == device_id:
                return 1

        print ( "Device not found" )
        return 0
    ```

1. Adicione os seguintes métodos para executar os trabalhos que chamam o método direto e dispositivo gêmeo:
   
    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)
    
        if query_condition(device_id):
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)
    
            response = iothub_device_method.invoke(device_id, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)
        
            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )
        
    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)
    
        if query_condition(device_id):
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
    
            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)
        
            print ( "" )
            print ( "Device twin updated." )
    ```

1. Adicione o código a seguir para agendar os trabalhos e atualizar o status do trabalho. Também inclui a rotina `main`:
   
    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()
        
            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )
        
            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()
        
            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )
        
            while True:
                print ( "" )
            
                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )
            
                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )
                
                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
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
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

1. Salve e feche o arquivo **scheduleJobService.py**.


## <a name="run-the-applications"></a>Executar os aplicativos
Agora você está pronto para executar os aplicativos.

1. No prompt de comando do seu diretório de trabalho, execute o seguinte comando para começar a escutar o método direto de reinicialização:
   
    ```cmd/sh
    python simDevice.py
    ```

1. Em outro prompt de comando no seu diretório de trabalho, execute o seguinte comando para disparar os trabalhos para bloquear a porta e atualizar o gêmeo:
   
    ```cmd/sh
    python scheduleJobService.py
    ```

1. Veja as respostas de dispositivo para o método direto e dispositivos gêmeos na atualização no console.

    ![saída do dispositivo][1]

    ![saída do serviço][2]


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você usou um trabalho para agendar um método direto para um dispositivo e a atualização das propriedades do twin do dispositivo.

Para continuar com a introdução ao Hub IoT e aos padrões de gerenciamento de dispositivo como remoto por meio da atualização de firmware de ar, consulte:

[Tutorial: Como realizar uma atualização de firmware][lnk-fwupdate]

Para continuar a introdução ao Hub IoT, consulte [Introdução ao Azure IoT Edge][lnk-iot-edge].

[lnk-get-started-twin]: iot-hub-python-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-python-python-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-transient-faults]: https://docs.microsoft.com/azure/architecture/best-practices/transient-faults

[1]: ./media/iot-hub-python-python-schedule-jobs/1.png
[2]: ./media/iot-hub-python-python-schedule-jobs/2.png
