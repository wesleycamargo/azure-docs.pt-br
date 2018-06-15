---
title: Módulo do Python do Azure IoT Edge | Microsoft Docs
description: Criar um módulo do IoT Edge com código em Python e implantá-lo em um dispositivo de borda
author: shizn
manager: ''
ms.author: xshi
ms.date: 03/18/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 88d772306cb9e67216b380aa885284ebedc77b5f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632101"
---
# <a name="develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device---preview"></a>Desenvolver e implantar um módulo do IoT Edge em Python em seu dispositivo simulado - versão prévia

Use os módulos do IoT Edge para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial o orienta através da criação e implantação de um módulo IoT Edge que filtra os dados do sensor. Você utilizará o dispositivo IoT Edge simulado que foi criado em Implantar Azure IoT Edge em um dispositivo simulado nos tutoriais do [Windows][lnk-tutorial1-win] ou [Linux][lnk-tutorial1-lin]. Neste tutorial, você aprenderá como:    

> [!div class="checklist"]
> * Use o Visual Studio Code para criar um módulo do IoT Edge Python
> * Usar o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no registro 
> * Implantar o módulo no dispositivo IoT Edge
> * Exibir os dados gerados


O módulo IoT Edge que criado neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Ele somente envia mensagens upstream se a temperatura estiver acima de um limite especificado. Este tipo de análise na borda é útil para reduzir a quantidade de dados comunicados e armazenados na nuvem. 

> [!IMPORTANT]
> Atualmente o módulo do Python pode ser executado somente em contêineres do Linux amd64; ele não pode ser executado em contêineres do Windows ou contêineres baseados em ARM. 

## <a name="prerequisites"></a>pré-requisitos

* O dispositivo do Azure IoT Edge criado no guia de início rápido ou no primeiro tutorial.
* A cadeia de caracteres de conexão de chave primária para o dispositivo IoT Edge.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extensão do Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Extensão do Python para o Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python). 
* [Docker](https://docs.docker.com/engine/installation/) no mesmo computador com o Visual Studio Code. A CE (Community Edition) é suficiente para este tutorial. 
* [Python](https://www.python.org/downloads/).
* [PIP](https://pip.pypa.io/en/stable/installing/#installation) para instalar os pacotes do Python (normalmente incluídos com a instalação do Python).

## <a name="create-a-container-registry"></a>Criar um registro de contêiner
Neste tutorial, você utiliza a extensão do Azure IoT Edge do Visual Studio Code para compilar um módulo e criar uma **imagem de contêiner** dos arquivos. Em seguida, você efetua push dessa imagem para um **registro** que armazena e gerencia suas imagens. Finalmente, você implanta a imagem do seu registro para executar no dispositivo IoT Edge.  

Você pode usar qualquer registro compatível com o Docker neste tutorial. Dois serviços de registro do Docker populares disponíveis na nuvem são o [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) e o [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Este tutorial utiliza o Registro de Contêiner do Azure. 

1. No [Portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Contêineres** > **Registro de Contêiner do Azure** .
2. Nomeie seu registro, escolha uma assinatura, selecione um grupo de recursos e defina a SKU para **Básica**. 
3. Selecione **Criar**.
4. Depois que o registro de contêiner for criado, navegue até ele e selecione **Chaves de acesso**. 
5. Alterne **Usuário administrador** para **Ativar**.
6. Copie os valores para **Servidor de logon**, **Nome de usuário** e **Senha**. Você usará esses valores mais tarde no tutorial. 

## <a name="create-an-iot-edge-module-project"></a>Criar um projeto de módulo do IoT Edge
As etapas a seguir mostram como criar uma função do IoT Edge Python usando o Visual Studio Code e a extensão do Azure IoT Edge.
1. No Visual Studio Code, selecione **Exibir** > **Terminal Integrado** para abrir o terminal integrado do Visual Studio Code.
2. No terminal integrado, digite o seguinte comando para instalar (ou atualizar) **cookiecutter** (é recomendável fazer isso em um ambiente virtual ou como instalação por usuário conforme mostrado abaixo):

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

3. Criar um projeto para o novo módulo. O comando a seguir cria a pasta do projeto, **FilterModule**, com o seu repositório de contêiner. O parâmetro de `image_repository`deve estar na forma de `<your container registry name>.azurecr.io/filtermodule` se você estiver usando o registro de contêiner do Azure. Digite o seguinte comando na pasta de trabalho atual:

    ```cmd/sh
    cookiecutter --no-input https://github.com/Azure/cookiecutter-azure-iot-edge-module module_name=FilterModule image_repository=<your container registry address>/filtermodule
    ```
 
4. Selecione **Arquivo** > **Abrir Pasta**.
5. Navegue para a pasta **FilterModule** e clique em **Selecionar Pasta** para abrir o projeto no Visual Studio Code.
6. No VS Code explorer, clique em **main.py** para abri-lo.
7. Na parte superior do **FilterModule** namespace, importe a `json` biblioteca:

    ```python
    import json
    ```

8. Adicione `TEMPERATURE_THRESHOLD`, `RECEIVE_CALLBACKS` e `TWIN_CALLBACKS` aos contadores globais. O limite de temperatura define o valor que a temperatura medida deve exceder para que os dados sejam enviados para o Hub IoT.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = RECEIVE_CALLBACKS = 0
    ```

9. Atualizar a função `receive_message_callback` com o conteúdo abaixo.

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we will forward this message onto the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print("    Data: <<<{}>>> & Size={:d}".format(message_text, size))
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print("    Properties: {}".format(key_value_pair))
        RECEIVE_CALLBACKS += 1
        print("    Total calls received: {:d}".format(RECEIVE_CALLBACKS))
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature {} exceeds threshold {}".format(data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

10. Adicionar uma nova função `device_twin_callback`. Essa função será invocada quando as propriedades desejadas forem atualizadas.

    ```python
    # device_twin_callback is invoked when twin's desired properties are updated.
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print("\nTwin callback called with:\nupdateStatus = {}\npayload = {}\ncontext = {}".format(update_state, payload, user_context))
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print("Total calls confirmed: {:d}\n".format(TWIN_CALLBACKS))
    ```

11. Na classe `HubManager`, adicione uma nova linha ao `__init__` método para inicializar a `device_twin_callback` função que você acabou de adicionar.

    ```python
    # sets the callback when a twin's desired properties are updated.
    self.client.set_device_twin_callback(device_twin_callback, self)
    ```


12. Salve o arquivo.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Criar uma imagem do Docker e publicá-la no registro

1. Entre no Docker, inserindo o seguinte comando no terminal integrado do Visual Studio Code: 
     
   ```csh/sh
   docker login -u <username> -p <password> <Login server>
   ```
        
   Use o nome de usuário, a senha e o servidor de logon que você copiou do seu registro de contêiner do Azure quando foi criado.

2. No código explorador do VS Code clique com o botão direito no arquivo **module.json** e clique no **módulo de Docker do módulo Azure IoT Edge de Push**. Na caixa suspensa pop-up na parte superior da janela do VS Code, selecione sua plataforma de contêiner, por exemplo, **amd64** para contêiner Linux. O VS Code compila seu código, coloca em contêiner o `main.py` e envia por push para o registro de contêiner especificado. Pode levar vários minutos para você compilar primeiro a imagem.

3. Você pode obter o endereço de imagem de contêiner completo com marca no terminal integrado de VS Code. Para obter mais informações sobre a definição de compilação e enviar por push, você pode consultar o `module.json` arquivo.

## <a name="add-registry-credentials-to-edge-runtime"></a>Adicionar as credenciais de registro ao tempo de execução do Edge
Adicione as credenciais do seu registro ao tempo de execução do Edge no computador em que você está executando o dispositivo Edge. Essas credenciais fornecem ao tempo de execução acesso para efetuar pull de contêiner. 

- Para Windows, execute o seguinte comando:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- Para Linux, execute o seguinte comando:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Executar a solução

1. No [portal do Azure](https://portal.azure.com), navegue para o hub IoT.
2. Vá para **IoT Edge (versão prévia)** e selecione o dispositivo IoT Edge.
3. Selecione **Definir Módulos**. 
2. Verifique se o módulo **tempSensor** é preenchido automaticamente. Se não, utilize as seguintes etapas para adicioná-lo:
    1. Selecione **Adicionar Módulo do IoT Edge**.
    2. No campo **Nome**, insira `tempSensor`.
    3. No campo **URI da Imagem**, insira `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Deixe as outras configurações inalteradas e clique em **Salvar**.
9. Adicionar o **filterModule** que você criou nas seções anteriores. 
    1. Selecione **Adicionar Módulo do IoT Edge**.
    2. No campo **Nome**, insira `filterModule`.
    3. No campo **URI de Imagem** digite o endereço da imagem; por exemplo `<your container registry address>/filtermodule:0.0.1-amd64`. O endereço de imagem completo pode ser encontrado na seção anterior.
    4. Marque a caixa **Habilitar** para que você possa editar o módulo gêmeo. 
    5. Substitua o JSON na caixa de texto para o gêmeo do módulo com o seguinte JSON: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Clique em **Salvar**.
10. Clique em **Próximo**.
11. Na etapa **Especificar Rotas**, copie o JSON abaixo na caixa de texto. Os módulos publicam todas as mensagens no tempo de execução do Edge. As regras declarativas no tempo de execução definem o local do fluxo de mensagens. Neste tutorial, você precisa de duas rotas. A primeira rota transporta mensagens de sensor de temperatura para o módulo de filtro pelo ponto de extremidade "input1", que é o ponto de extremidade configurado com o manipulador **FilterMessages**. A segunda rota transporta as mensagens do módulo de filtro para o Hub IoT. Nessa rota, `upstream` é um destino especial que manda o Hub do Edge enviar mensagens para o Hub IoT. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. Clique em **Próximo**.
5. Na etapa **Examinar Modelo**, clique em **Enviar**. 
6. Volte para a página de detalhes do dispositivo IoT Edge e clique em **Atualizar**. Você deverá ver o novo módulo **filtermodule** em execução junto com o módulo **tempSensor** e o **tempo de execução do IoT Edge**. 

## <a name="view-generated-data"></a>Exibir dados gerados

Para monitorar mensagens de dispositivo para nuvem enviadas do seu dispositivo IoT Edge para o seu hub IoT:
1. Configure a extensão Kit de Ferramentas do Azure IoT com a cadeia de conexão para o hub IoT: 
    1. Abra o gerenciador do Visual Studio Code, selecionando **Exibir** > **Explorer**. 
    3. No gerenciador, clique em **DISPOSITIVOS HUB IOT** e clique em **...**. Clique em **Definir Cadeia de Conexão do Hub IoT** e insira a cadeia de conexão para o hub IoT ao qual seu dispositivo IoT Edge se conecta na janela pop-up. 

        Para localizar a cadeia de conexão, clique no bloco para o hub IoT no portal do Azure e clique em **Políticas de acesso compartilhado**. Em **Políticas de acesso compartilhado**, clique na política **iothubowner** e copie a cadeia de conexão do Hub IoT na janela **iothubowner**.   

1. Para monitorar os dados recebidos do Hub IoT, selecione **Exibir** > **Paleta de Comandos** e procure o comando de menu **IoT: Iniciar o monitoramento de mensagem D2C**. 
2. Para parar de monitorar os dados, use o comando de menu **IoT: Parar o monitoramento de mensagem D2C**. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um módulo do IoT Edge que contém código para filtrar os dados brutos gerados pelo seu dispositivo IoT Edge. Continue com um dos tutoriais a seguir para saber mais sobre outras formas pelas quais o Azure IoT Edge pode ajudá-lo a transformar dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Implantar a função do Azure como um módulo](tutorial-deploy-function.md)
> [Implantar o Azure Stream Analytics como um módulo](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
