---
title: Encaminhando mensagens com o Hub IoT do Azure (Python) | Microsoft Docs
description: "Como processar mensagens do dispositivo para nuvem do Hub IoT do Azure usando regras de direcionamento e pontos de extremidade personalizados para enviar mensagens para outros serviços de back-end."
services: iot-hub
documentationcenter: python
author: msebolt
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo
ms.openlocfilehash: f467437afb4bf89e77668cfd3e8a824bfbde9e10
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
---
# <a name="routing-messages-with-iot-hub-python"></a>Encaminhando mensagens com o Hub IoT (Python)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Este tutorial baseia-se no tutorial [Introdução ao Hub IoT] .  O tutorial:

* Mostra como usar regras de roteamento do para enviar mensagens de dispositivo à nuvem de maneira fácil, com base em configuração.
* Ilustra como isolar mensagens interativas que exigem ação imediata no back-end da solução para processamento adicional.  Por exemplo, um dispositivo pode enviar uma mensagem de alarme que dispara e insere um tíquete em um sistema CRM.  Por outro lado, as mensagens de ponto de dados, como telemetria de temperatura, são enviadas ao mecanismo de análise.

Ao final deste tutorial, você executará três aplicativos de console do Python:

* **SimulatedDevice.py**, uma versão modificada do aplicativo criado no tutorial [Introdução ao Hub IoT], envia mensagens de ponto de dados do dispositivo para nuvem a cada segundo e mensagens interativas do dispositivo para nuvem em um intervalo aleatório. O aplicativo usa o protocolo MQTT para se comunicar com o Hub IoT.
* **ReadCriticalQueue.py** remove as mensagens críticas da fila do Barramento de Serviço anexada ao hub IoT.

> [!NOTE]
> O Hub IoT tem suporte de SDK para várias plataformas de dispositivo e linguagens, incluindo C, Java e JavaScript. Para obter instruções sobre como substituir o dispositivo neste tutorial por um dispositivo físico e como conectar dispositivos a um Hub IoT, confira o [Centro de Desenvolvedores do Azure IoT].

Para concluir este tutorial, você precisará do seguinte:

* Uma versão de trabalho completa do tutorial [Introdução ao Hub IoT] .
* [Python 2.x or 3.x][lnk-python-download]. Certifique-se de usar a instalação de 32 bits ou 64 bits conforme exigido pelo seu programa de instalação. Quando solicitado durante a instalação, certifique-se de adicionar Python à variável de ambiente específica da plataforma. Se você estiver usando o Python 2. x, talvez seja necessário [instalar ou atualizar o *pip*, o sistema de gerenciamento de pacotes do Python][lnk-install-pip].
* Se você estiver usando o sistema operacional Windows, então o [Pacote redistribuível do Visual C++][lnk-visual-c-redist] permite o uso de DLLs nativas do Python.
* [Node.js 4.0 ou posterior][lnk-node-download]. Certifique-se de usar a instalação de 32 bits ou 64 bits conforme exigido pelo seu programa de instalação. Isso é necessário para instalar a [ferramenta do Gerenciador de Hub IoT][lnk-iot-hub-explorer].
* Uma conta ativa do Azure. (Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

Também é recomendável ler sobre o [Armazenamento do Azure] e o [Barramento de Serviço do Azure].


## <a name="send-interactive-messages-from-a-device-app"></a>Enviar mensagens interativas de um aplicativo de dispositivo
Nesta seção, você modificará o aplicativo de dispositivo criado no tutorial [Introdução ao Hub IoT] para enviar ocasionalmente mensagens que exigem processamento imediato.

1. Use um editor de texto para abrir o arquivo **SimulatedDevice.py**. Este arquivo contém o código do aplicativo **SimulatedDevice** que você criou no tutorial [Introdução ao Hub IoT].

2. Substitua a função **iothub_client_telemetry_sample_run** pelo seguinte código:

    ```python
    def iothub_client_telemetry_sample_run():

    try:
        client = iothub_client_init()
        print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
        message_counter = 0

        while True:
            random_seed = random.random()
            msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random_seed * 4 + 2))
            # messages can be encoded as string or bytearray
            if (message_counter & 1) == 1:
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))
            else:
                message = IoTHubMessage(msg_txt_formatted)
            # optional: assign ids
            message.message_id = "message_%d" % message_counter
            message.correlation_id = "correlation_%d" % message_counter
            # optional: assign properties
            prop_map = message.properties()
            if random_seed > .5:
                if random_seed > .8:
                    prop_map.add("level", 'critical')
                else:
                    prop_map.add("level", 'storage')

            client.send_event_async(message, send_confirmation_callback, message_counter)
            print ( "IoTHubClient.send_event_async accepted message [%d] for transmission to IoT Hub." % message_counter )

            status = client.get_send_status()
            print ( "Send status: %s" % status )
            time.sleep(30)

            status = client.get_send_status()
            print ( "Send status: %s" % status )

            message_counter += 1

    except IoTHubError as iothub_error:
        print ( "Unexpected error %s from IoTHub" % iothub_error )
        return
    except KeyboardInterrupt:
        print ( "IoTHubClient sample stopped" )
    ```
   
    Esse método adiciona aleatoriamente as propriedades `"level": "critical"` e `"level": "storage"` a mensagens enviadas pelo dispositivo, que simula uma mensagem que exige ação imediata do back-end do aplicativo ou de um que precise ser armazenado permanentemente. O aplicativo fornece suporte para mensagens de roteamento baseado no corpo da mensagem.
   
   > [!NOTE]
   > Você pode usar as propriedades a fim de direcionar as mensagens para vários cenários, incluindo processamento de ampliação, além do exemplo de afunilamento mostrado aqui.

1. Salve e feche o arquivo **SimulatedDevice.py**.

    > [!NOTE]
    > Para simplificar, esse tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição, como uma retirada exponencial, como sugerido no artigo [Tratamento de falhas transitórias]do MSDN.


## <a name="add-queues-to-your-iot-hub-and-route-messages-to-them"></a>Adicionar filas ao hub IoT e encaminhar mensagens para elas

Nesta seção, você cria uma fila do Barramento de Serviço e uma Conta de armazenamento, conecta-as ao seu hub IoT e configura o hub IoT para enviar mensagens para a fila de acordo com a presença de uma propriedade na mensagem e todas as mensagens para a Conta de armazenamento. Para obter mais informações sobre como processar mensagens de filas do Barramento de Serviço, consulte [Introdução às filas][lnk-sb-queues-node] e como gerenciar o armazenamento, consulte [Introdução ao Armazenamento do Azure][Armazenamento do Azure].

1. Crie uma fila do Barramento de Serviço conforme descrito em [Introdução às filas][lnk-sb-queues-node]. Anote o namespace e o nome da fila.

    > [!NOTE]
    > As filas do Barramento de Serviço e os tópicos utilizados como pontos de extremidade do Hub IoT não devem ter **Sessões** nem **Detecção Duplicada** habilitadas. Se qualquer uma dessas opções estiver habilitada, o ponto de extremidade aparecerá como **Inacessível** no Portal do Azure.

1. No Portal do Azure, abra o Hub IoT e clique em **Pontos de extremidade**.

    ![Pontos de extremidade no Hub IoT][30]

1. Na folha **Pontos de extremidade**, clique em **Adicionar** na parte superior para adicionar a fila ao Hub IoT. Chame o ponto de extremidade de **CriticalQueue** e use o menu suspenso para selecionar **Fila do Barramento de Serviço**, o namespace do Barramento de Serviço no qual suas filas estão e o nome da sua fila. Quando terminar, clique em **OK** na parte inferior.  

    ![Adicionando um ponto de extremidade][31]

1. Agora clique em **Rotas** no Hub IoT. Clique em **Adicionar** na parte superior da folha para criar uma regra que encaminhe mensagens para a fila que você acabou de adicionar. 

    ![Adicionando uma rota][34]

    Insira um nome e selecione **Mensagens do Dispositivo** como a fonte de dados. Escolha **CriticalQueue** como um ponto de extremidade personalizado para o ponto de extremidade de regra de roteamento e insira `level="critical"` como a cadeia de consulta. Clique em **Salvar** na parte inferior.

    ![Detalhes da rota][32]

    Verifique se a rota de fallback está definida como **ATIVADA**. Essa é a configuração padrão de um Hub IoT.

    ![Rota de fallback][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Opcional) Ler no ponto de extremidade da fila

Nesta seção, você cria um aplicativo de console do Python que lê mensagens críticas do Barramento de Serviço do IoT. Veja mais informações em [Introdução às filas][lnk-sb-queues-node]. 

1. Abra um novo prompt de comando e instale o SDK do Dipositivo do Azure IoT Hub para Python conforme a seguir. Feche o prompt de comando após a instalação.

    ```cmd/sh
    pip install azure-servicebus
    ```

    > [!NOTE]
    > Para resolver problemas de instalação do pacote **azure-servicebus** ou para obter outras opções de instalação, consulte o [pacote azure-servicebus do Python][lnk-python-service-bus].

1. Crie um arquivo chamado **ReadCriticalQueue.py**. Abra-o em um editor/IDE do Python de sua escolha (por exemplo, OCIOSO).

1. Adicione o seguinte código para importar os módulos necessários do SDK do dispositivo:

    ```python
    from azure.servicebus import ServiceBusService, Message, Queue
    ```

1. Adicione o seguinte código e substitua os espaços reservados pelos dados de conexão do barramento de serviço:

    ```python
    SERVICE_BUS_NAME = {serviceBusName}
    SHARED_ACCESS_POLICY_NAME = {sharedAccessPolicyName}
    SHARED_ACCESS_POLICY_KEY_VALUE = {sharedAccessPolicyKeyValue}
    QUEUE_NAME = {queueName}    
    ```

5. Adicione o seguinte código para se conectar e ler o barramento de serviço: 

    ```python
    def setup_client():
        bus_service = ServiceBusService(
        service_namespace=SERVICE_BUS_NAME,
        shared_access_key_name=SHARED_ACCESS_POLICY_NAME,
        shared_access_key_value=SHARED_ACCESS_POLICY_KEY_VALUE)

        while True:
            msg = bus_service.receive_queue_message(QUEUE_NAME, peek_lock=False)
            print(msg.body)
    ```

1. Finalmente, adicione a função principal. 

    ```python
    if __name__ == '__main__':
        setup_client()
    ```

1. Salve e feche o arquivo **ReadCriticalQueue.py**. Agora você está pronto para executar os aplicativos.


## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. Abra um prompt de comando e instale o Gerenciador de Hub IoT. 

    ```cmd/sh
    npm install -g iothub-explorer
    ```

1. Execute o seguinte comando no prompt de comando, para começar a monitorar as mensagens do dispositivo para a nuvem a partir do seu dispositivo. Use a cadeia de conexão do seu hub IoT no espaço reservado após `--login`.

    ```cmd/sh
    iothub-explorer monitor-events [deviceId] --login "[IoTHub connection string]"
    ```

1. Abra um novo prompt de comando e navegue até o diretório que contém o arquivo **SimulatedDevice.py**.

1. Execute o arquivo **SimulatedDevice.py**, que envia periodicamente os dados de telemetria para o seu hub IoT. 
   
    ```cmd/sh
    python SimulatedDevice.py
    ```

1. Para executar o aplicativo **ReadCriticalQueue**, em um prompt de comando ou shell, procure o arquivo **ReadCriticalQueue.py** e execute o seguinte comando:

   ```cmd/sh
   python ReadCriticalQueue.py
   ```

1. Observe as mensagens de dispositivo no prompt de comando executando o Gerenciador de Hub IoT da seção anterior. Observe as mensagens `critical` no aplicativo **ReadCriticalQueue**.

    ![Mensagens do dispositivo para a nuvem de Python][2]


## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Opcional) Adicionar contêiner de armazenamento ao Hub IoT e rotear mensagens para ele

Nesta seção, você cria uma conta de Armazenamento, conecta-a ao Hub IoT e configura o Hub IoT para enviar mensagens à conta baseada na presença de uma propriedade na mensagem. Para saber mais sobre como gerenciar o armazenamento, consulte [Introdução ao Armazenamento do Azure][Armazenamento do Azure].

 > [!NOTE]
   > As contas do Hub IoT criadas na camada _F1 Gratuita_ são limitadas a um **Ponto de extremidade**. Caso não haja limite de um **Ponto de extremidade**, é possível configurar o **StorageContainer**, além do **CriticalQueue**, e executar ambos simultaneamente.

1. Crie uma conta de armazenamento, conforme descrito na [Documentação do Armazenamento do Azure][lnk-storage]. Anote o nome da conta.

2. No Portal do Azure, abra o Hub IoT e clique em **Pontos de extremidade**.

3. Na folha **Pontos de Extremidade**, selecione o ponto de extremidade **CriticalQueue** e clique em **Excluir**. Clique em **Sim** e depois em **Adicionar**. Nomeie o ponto de extremidade **StorageContainer**, use os menus suspensos para selecionar o **Contêiner do Armazenamento do Azure** e crie uma **Conta de armazenamento** e um **Contêiner de armazenamento**.  Anote os nomes.  Quando terminar, clique em **OK** na parte inferior. 

 > [!NOTE]
   > As contas do Hub IoT criadas na camada _F1 Gratuita_ são limitadas a um **Ponto de extremidade**. Caso tenha mais de um **Ponto de Extremidade**, não é necessário excluir **CriticalQueue**.

4. Clique em **Rotas** no Hub IoT. Clique em **Adicionar** na parte superior da folha para criar uma regra que encaminhe mensagens para a fila que você acabou de adicionar. Selecione **Mensagens de Dispositivo** como a fonte de dados. Insira `level="storage"` como a condição e escolha **StorageContainer** como um ponto de extremidade personalizado para o ponto de extremidade da regra de roteamento. Clique em **Salvar** na parte inferior.  

    Verifique se a rota de fallback está definida como **ATIVADA**. Essa é a configuração padrão de um Hub IoT.

1. Verifique se o aplicativo anterior **SimulatedDevice.py** ainda está em execução. 

1. No Portal do Azure, acesse sua conta de armazenamento, em **Serviço de Blob**, e clique em **Procurar blobs...**.  Selecione o contêiner, navegue até o arquivo JSON e clique nele, depois clique em **Baixar** para exibir os dados.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a expedir confiavelmente mensagens do dispositivo para nuvem usando a funcionalidade de roteamento de mensagens do Hub IoT.

Para ver exemplos de soluções completas que usam o Hub IoT, veja [Azure IoT Suite][lnk-suite].

Para saber mais sobre como desenvolver soluções com o Hub IoT, consulte o [Guia do desenvolvedor do Hub IoT].

Para saber mais sobre o roteamento de mensagens no Hub IoT, confira [Enviar e receber mensagens com o Hub IoT][lnk-devguide-messaging].

<!-- Images. -->
[2]: ./media/iot-hub-python-python-process-d2c/output.png

[30]: ./media/iot-hub-python-python-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-python-python-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-python-python-process-d2c/route-creation.png
[33]: ./media/iot-hub-python-python-process-d2c/fallback-route.png
[34]: ./media/iot-hub-python-python-process-d2c/click-routes.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-sb-queues-node]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md

[Armazenamento do Azure]: https://azure.microsoft.com/documentation/services/storage/
[Barramento de Serviço do Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[Guia do desenvolvedor do Hub IoT]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Introdução ao Hub IoT]: iot-hub-python-getstarted.md
[Centro de Desenvolvedores do Azure IoT]: https://azure.microsoft.com/develop/iot

[Tratamento de falhas transitórias]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
[lnk-storage]: https://docs.microsoft.com/en-us/azure/storage/
[lnk-python-service-bus]: https://pypi.python.org/pypi/azure-servicebus