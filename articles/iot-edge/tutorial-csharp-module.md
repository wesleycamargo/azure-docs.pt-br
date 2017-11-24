---
title: "Módulo do Azure IoT Edge em C# | Microsoft Docs"
description: "Criar um módulo do IoT Edge com código em C# e implantá-lo em um dispositivo de borda"
services: iot-edge
keywords: 
author: JimacoMS2
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: fb7674d8c292e7d571a94ac4625b0858a90704b3
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>Desenvolver e implantar um módulo do IoT Edge em C# em seu dispositivo simulado - versão prévia

Use os módulos do IoT Edge para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial orienta você pela criação e implantação de um módulo do IoT Edge que filtra dados de sensor no dispositivo IoT Edge simulado criado nos tutoriais Implantar o Azure IoT Edge em um dispositivo simulado no [Windows][lnk-tutorial1-win] ou no [Linux][lnk-tutorial1-lin]. Neste tutorial, você aprenderá como:    

> [!div class="checklist"]
> * Use o Visual Studio Code para criar um módulo do IoT Edge baseado .NET Core 2.0
> * Usar o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no registro 
> * Implantar o módulo no dispositivo IoT Edge
> * Exibir dados gerados


O módulo do IoT Edge criado neste tutorial filtra os dados de temperatura gerados pelo dispositivo e envia apenas mensagens upstream quando a temperatura está acima do limite especificado. 

## <a name="prerequisites"></a>Pré-requisitos

* O dispositivo do Azure IoT Edge criado no guia de início rápido ou no tutorial anterior.
* A cadeia de conexão do Hub IoT para o hub IoT ao qual o dispositivo IoT Edge se conecta.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extensão do Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). (Você pode instalar a extensão do painel de extensões no Visual Studio Code.)
* [C# para extensão do Visual Studio Code (com OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). (Você pode instalar a extensão do painel de extensões no Visual Studio Code.)
* Extensão do Azure IoT Edge para Visual Studio Code
* [Docker](https://docs.docker.com/engine/installation/). A CE (Community Edition) para sua plataforma é suficiente para este tutorial. Instale-a no computador em que você executa o VS Code.
* [SDK 2.0 do .NET Core](https://www.microsoft.com/net/core#windowscmd). 

## <a name="choose-or-sign-up-for-a-docker-registry"></a>Escolha ou se inscreva para obter um registro Docker
Neste tutorial, você usa a extensão do Azure IoT Edge para o VS Code para criar um módulo e uma [imagem do Docker](https://docs.docker.com/glossary/?term=image). Em seguida, aplique essa imagem do Docker a um [repositório Docker](https://docs.docker.com/glossary/?term=repository) hospedado em um [registro Docker](https://docs.docker.com/glossary/?term=registry). Por fim, você implanta a imagem do Docker empacotada como um [contêiner Docker](https://docs.docker.com/glossary/?term=container) do registro para o dispositivo IoT Edge.  

Você pode usar qualquer registro compatível com o Docker neste tutorial. Dois serviços de registro do Docker populares disponíveis na nuvem são o **Registro de Contêiner do Azure** e o **Hub do Docker**:

- O [Registro de Contêiner do Azure](https://docs.microsoft.com/en-us/azure/container-registry/) está disponível nas [assinaturas pagas](https://azure.microsoft.com/en-us/pricing/details/container-registry/). Para este tutorial, a assinatura **Básica** é suficiente. 

- O [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) oferece um repositório privado gratuito se você se inscrever e obter uma ID de Docker (gratuita). 
    1. Para se inscrever e obter uma ID do Docker, siga as instruções em [Registrar-se para uma ID do Docker](https://docs.docker.com/docker-id/#register-for-a-docker-id) no site do Docker. 

    2. Para criar um repositório particular do Docker, siga as instruções em [Criando um novo repositório no Hub do Docker](https://docs.docker.com/docker-hub/repos/#creating-a-new-repository-on-docker-hub) no site do Docker.

Ao longo deste tutorial, quando for o caso, serão fornecidos comandos para o Registro de Contêiner do Azure e o Hub do Docker.

## <a name="create-an-iot-edge-module-project"></a>Criar um projeto de módulo do IoT Edge
As etapas a seguir mostram como criar um módulo do IoT Edge baseado no .NET Core 2.0 usando o Visual Studio Code e a extensão do Azure IoT Edge.
1. Abra o VS Code.
2. Use o comando de menu **Exibição | Terminal Integrado** para abrir o terminal integrado do VS Code.
3. No terminal integrado, digite o seguinte comando para instalar (ou atualizar) o modelo **AzureIoTEdgeModule** no dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. No terminal integrado, digite o seguinte comando para criar um projeto para o novo módulo:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```

    >[!NOTE]
    > O comando cria a pasta do projeto, **FilterModule**, na pasta de trabalho atual. Se você deseja criá-lo em outro local, altere os diretórios antes de executar o comando.
 
3. Use o comando de menu **Arquivo | Abrir Pasta**, navegue até a pasta **FilterModule** e clique em **Selecionar Pasta** para abrir o projeto no Visual Studio Code.
4. No Visual Studio Code explorer, clique em **Program.cs** para abri-lo.
5. Adicione o seguinte campo à classe **Program**.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

1. Adicione as seguintes classes à classe **Program**. Essas classes definem o esquema esperado para o corpo de mensagens de entrada.

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

1. No método **Init**, o código cria e configura um objeto **DeviceClient**. Esse objeto permite que o módulo se conecte ao tempo de execução do Azure IoT Edge local para enviar e receber mensagens. O parâmetro de cadeia de conexão usado no método **Init** é fornecido ao módulo pelo tempo de execução do IoT Edge na variável de ambiente **EdgeHubConnectionString**. Depois de criar o **DeviceClient**, o código registra um retorno de chamada para receber mensagens do hub IoT Edge por meio do ponto de extremidade **Entrada1**. Substitua o método usado como retorno de chamada para tratar mensagens com um novo e anexe um retorno de chamada para atualizações de propriedades desejadas no gêmeo do modo. Para fazer essa alteração, substitua a última linha do método **Init** pelo seguinte código:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

1. Adicione o método a seguir à classe**Program** para atualizar o campo **temperatureThreshold** com base nas propriedades desejadas enviadas pelo serviço de back-end por meio do gêmeo do módulo. Todos os módulos têm seus próprios gêmeos de módulo. Um gêmeo de módulo permite que um serviço de back-end configure o código em execução dentro de um módulo.

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"].exists())
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

1. Substitua o método **PipeMessage** pelo método a seguir. Este método é chamado sempre que o módulo recebe uma mensagem do Hub do Edge. Ele filtra as mensagens com base no valor de temperatura no corpo da mensagem e no valor de limite de temperatura definido pelo gêmeo do módulo.

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        int counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            byte[] messageBytes = message.GetBytes();
            string messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get message body
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await deviceClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

11. Compile o projeto. Use o comando do menu **Exibição | Explorer** para abrir o gerenciador do VS Code. No gerenciador, clique com botão direito do mouse no arquivo **FilterModule.csproj** e clique em **Criar módulo do IoT Edge** para compilar o módulo e exportar o binário e suas dependências para uma pasta de onde a imagem do Docker será criada na próxima etapa.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Criar uma imagem do Docker e publicá-la no registro

1. Cria a imagem do Docker.
    1. No gerenciador do VS Code, clique na pasta **Docker** para abri-la. Selecione a pasta para a sua plataforma de contêiner: **linux-x64** ou **windows-nano**. 
    2. Clique com botão direito do mouse no arquivo **Dockerfile** e clique em **Criar imagem do Docker para o modulo IoT Edge**. 
    3. Na caixa **Selecionar Pasta**, procure ou insira `./bin/Debug/netcoreapp2.0/publish`. Clique em **Selecionar pasta como EXE_DIR**.
    4. Na caixa de texto pop-up na parte superior da janela do VS Code, insira o nome da imagem. Por exemplo, `<docker registry address>/filtermodule:latest`, em que o *endereço de registro do docker* é sua ID do Docker se você está usando o Hub do Docker, ou é semelhante a `<your registry name>.azurecr.io` se você está usando o Registro de Contêiner do Azure.
 
4. Entre com o Docker. No terminal integrado, digite o seguinte comando: 

    - Hub do Docker (insira suas credenciais quando solicitado):
        
        ```csh/sh
        docker login
        ```

    - Para o Registro de Contêiner do Azure:
        
        ```csh/sh
        docker login -u <username> -p <password> <Login server>
        ```
        
        Para localizar o nome de usuário, a senha e o servidor de logon, vá para o [portal do Azure] (https://portal.azure.com). Em **Todos os recursos**, clique no bloco do seu registro de contêiner do Azure para abrir suas propriedades; em seguida, clique em **Chaves de acesso**. Copie os valores nos campos **Nome de usuário**, **Senha** e **Servidor de logon**. O servidor de logon deve estar no formato: `<your registry name>.azurecr.io`.

3. Envie a imagem por push para o repositório do Docker. Use o comando de menu **Exibição | Paleta de comandos... | Edge: Enviar imagem do Docker para o módulo do IoT Edge por push** e digite o nome da imagem na caixa de texto pop-up na parte superior da janela do VS Code. Use o mesmo nome de imagem usado na etapa 1.c.

## <a name="add-registry-credentials-to-edge-runtime-on-your-edge-device"></a>Adicionar credenciais de registro ao tempo de execução do Edge em seu dispositivo Edge
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

1. No [portal do Azure](https://portal.azure.com), navegue para o hub IoT.
2. Vá para **IoT Edge (versão prévia)** e selecione o dispositivo IoT Edge.
3. Selecione **Definir Módulos**. 
2. Adicione o módulo **tempSensor**. A etapa só é necessária se você ainda não tiver implantado o módulo **tempSensor** para o dispositivo IoT Edge.
    1. Selecione **Adicionar Módulo do IoT Edge**.
    2. No campo **Nome**, insira `tempSensor`.
    3. No campo **URI da Imagem**, insira `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Deixe as outras configurações inalteradas e clique em **Salvar**.
9. Adicione o **filtermodule**
    1. Selecione **Adicionar Módulo do IoT Edge** novamente.
    2. No campo **Nome**, insira `filtermodule`.
    3. No campo **imagem**, insira seu endereço de imagem, por exemplo, `<docker registry address>/filtermodule:latest`.
    4. Marque a caixa **Editar gêmeo do módulo**.
    5. Substitua o JSON na caixa de texto para o gêmeo do módulo com o seguinte JSON: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Clique em **Salvar**.
12. Clique em **Avançar**.
13. Na etapa **Especificar Rotas**, copie o JSON abaixo na caixa de texto. Os módulos publicam todas as mensagens no tempo de execução do Edge. As regras declarativas no tempo de execução definem o local para onde as mensagens fluem. Neste tutorial, você precisa de duas rotas. A primeira rota transporta mensagens de sensor de temperatura para o módulo de filtro pelo ponto de extremidade "input1", que é o ponto de extremidade configurado com o manipulador **FilterMessages**. A segunda rota transporta as mensagens do módulo de filtro para o Hub IoT. Nessa rota, `upstream` é um destino especial que manda o Hub do Edge enviar mensagens para o Hub IoT. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. Clique em **Avançar**.
5. Na etapa **Examinar Modelo**, clique em **Enviar**. 
6. Volte para a página de detalhes do dispositivo IoT Edge e clique em **Atualizar**. Você deverá ver o novo módulo **filtermodule** em execução junto com o módulo **tempSensor** e o **tempo de execução do IoT Edge**. 

## <a name="view-generated-data"></a>Exibir dados gerados

Para monitorar mensagens de dispositivo para nuvem enviadas do seu dispositivo IoT Edge para o seu hub IoT:
1. Configure a extensão Kit de Ferramentas do Azure IoT com a cadeia de conexão para o hub IoT: 
    1. Use o comando do menu **Exibição | Explorer** para abrir o gerenciador do VS Code. 
    3. No gerenciador, clique em **DISPOSITIVOS HUB IOT** e clique em **...**. Clique em **Definir Cadeia de Conexão do Hub IoT** e insira a cadeia de conexão para o hub IoT ao qual seu dispositivo IoT Edge se conecta na janela pop-up. 

        Para localizar a cadeia de conexão, clique no bloco para o hub IoT no portal do Azure e clique em **Políticas de acesso compartilhado**. Em **Políticas de acesso compartilhado**, clique na política **iothubowner** e copie a cadeia de conexão do Hub IoT na janela **iothubowner**.   

1. Para monitorar os dados recebidos no hub IoT, use o comando de menu **Exibir | Paleta de Comandos... | IoT: Iniciar o monitoramento de mensagem D2C**. 
2. Para interromper o monitoramento de dados, use o comando de menu **Exibição | Paleta de Comandos... | IoT: Interromper o monitoramento de mensagem D2C**. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um módulo do IoT Edge que contém código para filtrar os dados brutos gerados pelo seu dispositivo IoT Edge. Continue com um dos tutoriais a seguir para saber mais sobre outras formas pelas quais o Azure IoT Edge pode ajudá-lo a transformar dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Implantar a função do Azure como um módulo](tutorial-deploy-function.md)
> [Implantar o Azure Stream Analytics como um módulo](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
