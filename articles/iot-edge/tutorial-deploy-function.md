---
title: Implantar função do Azure com o Azure IoT Edge | Microsoft Docs
description: Implantar função do Azure em um dispositivo de borda como um módulo
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3d7dd0986878c747f92afc712301453bc8772ef2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="deploy-azure-function-as-an-iot-edge-module---preview"></a>Implantar função do Azure como um módulo IoT Edge - versão prévia
Use o Azure Functions para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial orienta você pela criação e implantação de uma função do Azure que filtra dados de sensor no dispositivo IoT Edge simulado criado nos tutoriais Implantar o Azure IoT Edge em um dispositivo simulado no [Windows][lnk-tutorial1-win] ou no [Linux][lnk-tutorial1-lin]. Neste tutorial, você aprenderá como:     

> [!div class="checklist"]
> * Usar o Visual Studio Code para criar uma função do Azure
> * Usar o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no registro 
> * Implantar o módulo no dispositivo IoT Edge
> * Exibir os dados gerados


A função do Azure criada neste tutorial filtra os dados de temperatura gerados pelo dispositivo e envia apenas mensagens upstream para o Hub IoT do Azure quando a temperatura está acima do limite especificado. 

## <a name="prerequisites"></a>pré-requisitos

* O dispositivo do Azure IoT Edge criado no guia de início rápido ou no tutorial anterior.
* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# para extensão do Visual Studio Code (com OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Extensão do Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Docker](https://docs.docker.com/engine/installation/). A CE (Community Edition) para sua plataforma é suficiente para este tutorial. 
* [SDK 2.0 do .NET Core](https://www.microsoft.com/net/core#windowscmd). 

## <a name="create-a-container-registry"></a>Criar um registro de contêiner
Neste tutorial, você utiliza a extensão do Azure IoT Edge do Visual Studio Code para compilar um módulo e criar uma **imagem de contêiner** dos arquivos. Em seguida, você efetua push dessa imagem para um **registro** que armazena e gerencia suas imagens. Finalmente, você implanta a imagem do seu registro para executar no dispositivo IoT Edge.  

Você pode usar qualquer registro compatível com o Docker neste tutorial. Dois serviços de registro do Docker populares disponíveis na nuvem são o [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) e o [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Este tutorial utiliza o Registro de Contêiner do Azure. 

1. No [Portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Contêineres** > **Registro de Contêiner do Azure** .
2. Nomeie seu registro, escolha uma assinatura, selecione um grupo de recursos e defina a SKU para **Básica**. 
3. Selecione **Criar**.
4. Depois que o registro de contêiner for criado, navegue até ele e selecione **Chaves de acesso**. 
5. Alterne **Usuário administrador** para **Ativar**.
6. Copie os valores para **Servidor de logon**, **Nome de usuário** e **Senha**. Você usará esses valores mais tarde no tutorial. 

## <a name="create-a-function-project"></a>Criar um projeto de função
As etapas a seguir mostram como criar uma função do IoT Edge usando o Visual Studio Code e a extensão do Azure IoT Edge.
1. Abra o Visual Studio Code.
2. Para abrir o terminal integrado do VS Code, selecione **Exibir** > **Terminal integrado**.
3. Para instalar ou atualizar o modelo **AzureIoTEdgeFunction** em dotnet, execute o seguinte comando no terminal integrado:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. Criar um projeto para o novo módulo. O comando a seguir cria a pasta do projeto, **FilterFunction**, com o seu repositório de contêiner. O segundo parâmetro deve estar na forma de `<your container registry name>.azurecr.io` se você estiver usando o registro de contêiner do Azure. Digite o seguinte comando na pasta de trabalho atual:

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction -r <your container registry address>/filterfunction
    ```

3. Selecione **Arquivo** > **Abrir Pasta**, procure a pasta **FilterFunction** pasta e abra o projeto no VS Code.
4. No VS Code explorer, expanda a pasta **EdgeHubTrigger-Csharp** e abra o arquivo **run.csx**.
5. Substitua o conteúdo do arquivo pelo código a seguir:

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
        const int temperatureThreshold = 25;
        byte[] messageBytes = messageReceived.GetBytes();
        var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

        if (!string.IsNullOrEmpty(messageString))
        {
            // Get the body of the message and deserialize it
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }
                // Add a new property to the message to indicate it is an alert
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message        
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
            }
        }
    }

    //Define the expected schema for the body of incoming messages
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
       public double temperature {get; set;}
       public double pressure {get; set;}         
    }
    class Ambient
    {
       public double temperature {get; set;}
       public int humidity {get; set;}         
    }
   ```

11. Salve o arquivo.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Criar uma imagem do Docker e publicá-la no registro

1. Entre no Docker, inserindo o seguinte comando no terminal integrado do Visual Studio Code: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Para localizar o nome de usuário, a senha e o servidor de logon, vá para o [Azure portal] (https://portal.azure.com). Em **Todos os recursos**, clique no bloco do seu registro de contêiner do Azure para abrir suas propriedades; em seguida, clique em **Chaves de acesso**. Copie os valores nos campos **Nome de usuário**, **Senha** e **Servidor de logon**. 

2. No código explorador do VS Code clique com o botão direito no arquivo **module.json** e clique no **módulo de Docker do módulo Azure IoT Edge de Push**. Na caixa suspensa pop-up na parte superior da janela de Código do VS, selecione sua plataforma de contêiner, ou **amd64** para contêiner Linux ou **windows-amd64** para o contêiner do Windows. O Visual Studio Code coloca os códigos da função em contêiner e o envia por push para o registro de contêiner especificado.


3. Você pode obter o endereço de imagem de contêiner completo com marca no terminal integrado de VS Code. Para obter mais informações sobre a definição de compilação e enviar por push, você pode consultar o `module.json` arquivo.

## <a name="add-registry-credentials-to-your-edge-device"></a>Adicionar credenciais de registro ao dispositivo Edge
Adicione as credenciais do seu registro ao tempo de execução do Edge no computador em que você está executando o dispositivo Edge. Isso dá acesso ao tempo de execução para efetuar pull no contêiner. 

- Para Windows, execute o seguinte comando:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- Para Linux, execute o seguinte comando:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Executar a solução

1. No **portal do Azure**, navegue para o hub IoT.
2. Vá para **IoT Edge (versão prévia)** e selecione o dispositivo IoT Edge.
1. Selecione **Definir Módulos**. 
2. Se você já implantou o módulo **tempSensor** nesse dispositivo, ele pode ser preenchido automaticamente. Do contrário, siga estas etapas para adicioná-lo:
    1. Selecione **Adicionar Módulo do IoT Edge**.
    2. No campo **Nome**, insira `tempSensor`.
    3. No campo **URI da Imagem**, insira `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Deixe as outras configurações inalteradas e clique em **Salvar**.
1. Adicione o módulo **filterFunction**.
    1. Selecione **Adicionar Módulo do IoT Edge** novamente.
    2. No campo **Nome**, insira `filterFunction`.
    3. No campo **URI de Imagem** digite o endereço da imagem; por exemplo `<your container registry address>/filterfunction:0.0.1-amd64`. O endereço de imagem completo pode ser encontrado na seção anterior.
    74. Clique em **Salvar**.
2. Clique em **Próximo**.
3. Na etapa **Especificar Rotas**, copie o JSON abaixo na caixa de texto. A primeira rota transporta mensagens do sensor de temperatura para o módulo de filtro por meio do ponto de extremidade "input1". A segunda rota transporta as mensagens do módulo de filtro para o Hub IoT. Nessa rota, `$upstream` é um destino especial que manda o Hub do Edge enviar mensagens para o Hub IoT. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterFunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterFunction/outputs/* INTO $upstream"
       }
    }
    ```

4. Clique em **Próximo**.
5. Na etapa **Examinar Modelo**, clique em **Enviar**. 
6. Volte para a página de detalhes do dispositivo IoT Edge e clique em **Atualizar**. Você deverá ver o novo módulo **filterfunction** em execução junto com o módulo **tempSensor** e o **tempo de execução do IoT Edge**. 

## <a name="view-generated-data"></a>Exibir os dados gerados

Para monitorar mensagens de dispositivo para nuvem enviadas do seu dispositivo IoT Edge para o seu hub IoT:
1. Configure a extensão Kit de Ferramentas do Azure IoT com a cadeia de conexão para o hub IoT: 
    1. No portal do Azure, navegue até o hub IoT e selecione **Políticas de acesso compartilhado**. 
    2. Selecione **iothubowner** e copie o valor de **Chave primária da cadeia de conexão**.
    3. No VS Code explorer, clique em **IOT HUB DEVICES** e em **...**. 
    4. Selecione **Definir Cadeia de Conexão do IoT Hub** e insira a cadeia de conexão do Iot Hub na janela pop-up. 

2. Para monitorar os dados recebidos do Hub IoT, selecione **Exibir** > **Paleta de Comandos...** e procure **IoT: Iniciar o monitoramento de mensagem D2C**. 
3. Para interromper o monitoramento de dados, use o comando **IoT: Parar o monitoramento de mensagem D2C** na Paleta de Comandos. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma função do Azure que contém código para filtrar os dados brutos gerados pelo seu dispositivo IoT Edge. Para continuar explorando o Azure IoT Edge, aprenda a usar um dispositivo IoT Edge como gateway. 

> [!div class="nextstepaction"]
> [Criar um dispositivo de gateway IoT Edge](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
