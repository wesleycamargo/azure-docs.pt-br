---
title: "Implantar função do Azure com o Azure IoT Edge | Microsoft Docs"
description: "Implantar função do Azure em um dispositivo de borda como um módulo"
services: iot-edge
keywords: 
author: JimacoMS2
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: cba901e8929d3626dc06e4600437b6d364e9bc44
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2017
---
# <a name="deploy-azure-function-as-an-iot-edge-module---preview"></a>Implantar função do Azure como um módulo IoT Edge - versão prévia
Use o Azure Functions para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial orienta você pela criação e implantação de uma função do Azure que filtra dados de sensor no dispositivo IoT Edge simulado criado nos tutoriais Implantar o Azure IoT Edge em um dispositivo simulado no [Windows][lnk-tutorial1-win] ou no [Linux][lnk-tutorial1-lin]. Neste tutorial, você aprenderá como:     

> [!div class="checklist"]
> * Usar o Visual Studio Code para criar uma função do Azure
> * Usar o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no registro 
> * Implantar o módulo no dispositivo IoT Edge
> * Exibir dados gerados


A função do Azure criada neste tutorial filtra os dados de temperatura gerados pelo dispositivo e envia apenas mensagens upstream para o Hub IoT do Azure quando a temperatura está acima do limite especificado. 

## <a name="prerequisites"></a>Pré-requisitos

* O dispositivo do Azure IoT Edge criado no guia de início rápido ou no tutorial anterior.
* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# para extensão do Visual Studio Code (com OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). (Você pode instalar a extensão do painel de extensões no Visual Studio Code.)
* [Extensão do Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). (Você pode instalar a extensão do painel de extensões no Visual Studio Code.)
* [Docker](https://docs.docker.com/engine/installation/). A CE (Community Edition) para sua plataforma é suficiente para este tutorial. 
* [SDK 2.0 do .NET Core](https://www.microsoft.com/net/core#windowscmd). 

## <a name="set-up-a-docker-registry"></a>Configurar um registro de Docker
Neste tutorial, você usa a extensão do Azure IoT Edge para o VS Code para criar uma função do Azure e um [imagem do Docker](https://docs.docker.com/glossary/?term=image) com ela. Em seguida, aplique essa imagem do Docker a um [repositório Docker](https://docs.docker.com/glossary/?term=repository) hospedado por um [registro Docker](https://docs.docker.com/glossary/?term=registry). Por fim, você implanta a imagem do Docker empacotada como um [contêiner Docker](https://docs.docker.com/glossary/?term=container) do registro para o dispositivo IoT Edge.  

Você pode usar qualquer registro compatível com o Docker neste tutorial. Dois serviços de registro do Docker populares disponíveis na nuvem são o **Registro de Contêiner do Azure** e o **Hub do Docker**:

- O [Registro de Contêiner do Azure](https://docs.microsoft.com/en-us/azure/container-registry/) está disponível nas [assinaturas pagas](https://azure.microsoft.com/en-us/pricing/details/container-registry/). Para este tutorial, a assinatura **Básica** é suficiente. 

- O [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) oferece um repositório privado gratuito se você se inscrever e obter uma ID de Docker (gratuita). 
    1. Para se inscrever e obter uma ID do Docker, siga as instruções em [Registrar-se para uma ID do Docker](https://docs.docker.com/docker-id/#register-for-a-docker-id) no site do Docker. 

    2. Para criar um repositório particular do Docker, siga as instruções em [Criando um novo repositório no Hub do Docker](https://docs.docker.com/docker-hub/repos/#creating-a-new-repository-on-docker-hub) no site do Docker.

Ao longo deste tutorial, quando for o caso, serão fornecidos comandos para o Registro de Contêiner do Azure e o Hub do Docker.

## <a name="create-a-function-project"></a>Criar um projeto de função
As etapas a seguir mostram como criar uma função do IoT Edge usando o Visual Studio Code e a extensão do Azure IoT Edge.
1. Abra o VS Code.
2. Use o comando de menu **Exibição | Terminal Integrado** para abrir o terminal integrado do VS Code.
3. No terminal integrado, digite o seguinte comando para instalar (ou atualizar) o modelo **AzureIoTEdgeFunction** no dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. No terminal integrado, digite o seguinte comando para criar um projeto para o novo módulo:

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction
    ```

    >[!NOTE]
    > O comando cria a pasta do projeto, **FilterFunction**, na pasta de trabalho atual. Se você deseja criá-lo em outro local, altere os diretórios antes de executar o comando.

3. Use o comando de menu **Arquivo | Abrir Pasta**, navegue até a pasta **FilterFunction** e clique em **Selecionar Pasta** para abrir o projeto no Visual Studio Code.
4. No gerenciador do Visual Studio Code, clique na pasta **EdgeHubTrigger Csharp** e clique no arquivo **run.csx** para abri-lo.
5. Adicione a seguinte instrução após a instrução `#r "Microsoft.Azure.Devices.Client"`:

    ```csharp
    #r "Newtonsoft.Json"
    ```

5. Adicione os itens abaixo usando instruções após as instruções `using`:

    ```csharp
    using Newtonsoft.Json;
    ```

1. Adicione as classes a seguir. Essas classes definem o esquema esperado para o corpo de mensagens de entrada.

    ```csharp
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

1. Substitua o corpo do método **Run** pelo código a seguir. Ele filtra as mensagens com base no valor de temperatura no corpo da mensagem e no valor de limite de temperatura.

    ```csharp
    const int temperatureThreshold = 25;

    byte[] messageBytes = messageReceived.GetBytes();
    var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

    if (!string.IsNullOrEmpty(messageString))
    {
        // Get the body of the message and deserialize it
        var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);
        
        if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
        {
            // We will send the message to the output as the temperature value is greater than the threashold
            var filteredMessage = new Message(messageBytes);
            // We need to copy the properties of the original message into the new Message object
            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
            {
                filteredMessage.Properties.Add(prop.Key, prop.Value);
            }
            // We are adding a new property to the message to indicate it is an alert
            filteredMessage.Properties.Add("MessageType", "Alert");
            // Send the message        
            await output.AddAsync(filteredMessage);
            log.Info("Received and transferred a message with temperature above the threshold");
        }
    }
    ```

11. Salve o arquivo.

## <a name="publish-a-docker-image"></a>Publicar uma imagem do Docker

1. Cria a imagem do Docker.
    1. No gerenciador do VS Code, clique na pasta **Docker** para abri-la. Selecione a pasta para a sua plataforma de contêiner: **linux-x64** ou **windows-nano**. 
    2. Clique com botão direito do mouse no arquivo **Dockerfile** e clique em **Criar imagem do Docker para o modulo IoT Edge**. 
    3. Na caixa **Selecionar pasta**, navegue até a pasta do projeto **FilterFunction** e clique em **Selecionar pasta como EXE_DIR**. 
    4. Na caixa de texto pop-up na parte superior da janela do VS Code, insira o nome da imagem. Por exemplo, `<docker registry address>/filterfunction:latest`, em que o *endereço de registro do docker* é sua ID do Docker se você está usando o Hub do Docker, ou é semelhante a `<your registry name>.azurecr.io` se você está usando o Registro de Contêiner do Azure.
 
4. Entre com o Docker. No terminal integrado, digite o seguinte comando: 

    - Hub do Docker (insira suas credenciais quando solicitado):
        
        ```csh/sh
        docker login
        ```

    - Registro de Contêiner do Azure:
        
        ```csh/sh
        docker login -u <username> -p <password> <Login server>
        ```
        
        Para localizar o nome de usuário, a senha e o servidor de logon, vá para o [portal do Azure] (https://portal.azure.com). Em **Todos os recursos**, clique no bloco do seu registro de contêiner do Azure para abrir suas propriedades; em seguida, clique em **Chaves de acesso**. Copie os valores nos campos **Nome de usuário**, **Senha** e **Servidor de logon**. O servidor de logon deve estar no formato: `<your registry name>.azurecr.io`.

3. Envie a imagem por push para o repositório do Docker. Use o comando de menu **Exibição | Paleta de comandos... | Edge: Enviar imagem do Docker para o módulo do IoT Edge por push** e digite o nome da imagem na caixa de texto pop-up na parte superior da janela do VS Code. Use o mesmo nome de imagem usado na etapa 1.c.

## <a name="add-registry-credentials-to-your-edge-device"></a>Adicionar credenciais de registro ao dispositivo Edge
Adicione as credenciais do seu registro ao tempo de execução do Edge no computador em que você está executando o dispositivo Edge. Isso dá acesso ao tempo de execução para efetuar pull no contêiner. 

- Para Windows, execute o seguinte comando:
    
    ```cmd/sh
    iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

- Para Linux, execute o seguinte comando:
    
    ```cmd/sh
    sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

## <a name="run-the-solution"></a>Executar a solução

1. No **portal do Azure**, navegue para o hub IoT.
2. Vá para **IoT Edge (versão prévia)** e selecione o dispositivo IoT Edge.
1. Selecione **Definir Módulos**. 
2. Adicione o módulo **tempSensor**. A etapa só é necessária se você ainda não tiver implantado o módulo **tempSensor** para o dispositivo IoT Edge.
    1. Selecione **Adicionar Módulo do IoT Edge**.
    2. No campo **Nome**, insira `tempSensor`.
    3. No campo **URI da Imagem**, insira `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Deixe as outras configurações inalteradas e clique em **Salvar**.
1. Adicione o módulo **filterfunction**.
    1. Selecione **Adicionar Módulo do IoT Edge** novamente.
    2. No campo **Nome**, insira `filterfunction`.
    3. No campo **imagem**, insira seu endereço de imagem, por exemplo, `<docker registry address>/filterfunction:latest`.
    74. Clique em **Salvar**.
2. Clique em **Avançar**.
3. Na etapa **Especificar Rotas**, copie o JSON abaixo na caixa de texto. Os módulos publicam todas as mensagens no tempo de execução do Edge. As regras declarativas no tempo de execução definem o local para onde as mensagens fluem. Neste tutorial, você precisa de duas rotas. A primeira rota transporta mensagens de sensor de temperatura para o módulo de filtro pelo ponto de extremidade "input1", que é o ponto de extremidade configurado com o manipulador **FilterMessages**. A segunda rota transporta as mensagens do módulo de filtro para o Hub IoT. Nessa rota, `upstream` é um destino especial que manda o Hub do Edge enviar mensagens para o Hub IoT. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
    }
    ```

4. Clique em **Avançar**.
5. Na etapa **Examinar Modelo**, clique em **Enviar**. 
6. Volte para a página de detalhes do dispositivo IoT Edge e clique em **Atualizar**. Você deverá ver o novo módulo **filterfunction** em execução junto com o módulo **tempSensor** e o **tempo de execução do IoT Edge**. 

## <a name="view-generated-data"></a>Exibir dados gerados

Para monitorar mensagens de dispositivo para nuvem enviadas do seu dispositivo IoT Edge para o seu hub IoT:
1. Configure a extensão Kit de Ferramentas do Azure IoT com a cadeia de conexão para o hub IoT: 
    1. Use o comando do menu **Exibição | Explorer** para abrir o gerenciador do VS Code. 
    3. No gerenciador, clique em **DISPOSITIVOS HUB IOT** e clique em **...**. Clique em **Definir Cadeia de Conexão do Hub IoT** e insira a cadeia de conexão para o hub IoT ao qual seu dispositivo IoT Edge se conecta na janela pop-up. 

        Para localizar a cadeia de conexão, clique no bloco para o hub IoT no portal do Azure e clique em **Políticas de acesso compartilhado**. Em **Políticas de acesso compartilhado**, clique na política **iothubowner** e copie a cadeia de conexão do Hub IoT na janela **iothubowner**.   

1. Para monitorar os dados recebidos no hub IoT, use o comando de menu **Exibir | Paleta de Comandos... | IoT: Iniciar o monitoramento de mensagem D2C**. 
2. Para interromper o monitoramento de dados, use o comando de menu **Exibição | Paleta de Comandos... | IoT: Interromper o monitoramento de mensagem D2C**. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma função do Azure que contém código para filtrar os dados brutos gerados pelo seu dispositivo IoT Edge. Para continuar explorando o Azure IoT Edge, aprenda a usar um dispositivo IoT Edge como gateway. 

> [!div class="nextstepaction"]
> [Criar um dispositivo de gateway IoT Edge](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
