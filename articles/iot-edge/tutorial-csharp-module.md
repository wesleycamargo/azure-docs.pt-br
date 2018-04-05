---
title: Módulo do Azure IoT Edge em C# | Microsoft Docs
description: Criar um módulo do IoT Edge com código em C# e implantá-lo em um dispositivo de borda
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/14/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 95ca66f34548f86e25c1e7af331fa88797847906
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>Desenvolver e implantar um módulo do IoT Edge em C# em seu dispositivo simulado - versão prévia

Use os módulos do IoT Edge para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial o orienta através da criação e implantação de um módulo IoT Edge que filtra os dados do sensor. Você utilizará o dispositivo IoT Edge simulado que foi criado em Implantar Azure IoT Edge em um dispositivo simulado nos tutoriais do [Windows][lnk-tutorial1-win] ou [Linux][lnk-tutorial1-lin]. Neste tutorial, você aprenderá como:    

> [!div class="checklist"]
> * Use o Visual Studio Code para criar um módulo do IoT Edge baseado .NET Core 2.0
> * Usar o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no registro 
> * Implantar o módulo no dispositivo IoT Edge
> * Exibir os dados gerados


O módulo IoT Edge que criado neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Ele somente envia mensagens upstream se a temperatura estiver acima de um limite especificado. Este tipo de análise na borda é útil para reduzir a quantidade de dados comunicados e armazenados na nuvem. 

## <a name="prerequisites"></a>pré-requisitos

* O dispositivo do Azure IoT Edge criado no guia de início rápido ou no primeiro tutorial.
* A cadeia de caracteres de conexão de chave primária para o dispositivo IoT Edge.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extensão do Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [C# para extensão do Visual Studio Code (com OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Docker](https://docs.docker.com/engine/installation/) no mesmo computador com o Visual Studio Code. A CE (Community Edition) é suficiente para este tutorial. 
* [SDK 2.0 do .NET Core](https://www.microsoft.com/net/core#windowscmd). 

## <a name="create-a-container-registry"></a>Criar um registro de contêiner
Neste tutorial, você utiliza a extensão do Azure IoT Edge do Visual Studio Code para compilar um módulo e criar uma **imagem de contêiner** dos arquivos. Em seguida, você efetua push dessa imagem para um **registro** que armazena e gerencia suas imagens. Finalmente, você implanta a imagem do seu registro para executar no dispositivo IoT Edge.  

Você pode usar qualquer registro compatível com o Docker neste tutorial. Dois serviços de registro do Docker populares disponíveis na nuvem são o [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) e o [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Este tutorial utiliza o Registro de Contêiner do Azure. 

1. No [Portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Contêineres** > **Registro de Contêiner do Azure** .
2. Nomeie seu registro, escolha uma assinatura, selecione um grupo de recursos e defina a SKU para **Básica**. 
3. Selecione **Criar**.
4. Depois que o registro de contêiner for criado, navegue até ele e selecione **Chaves de acesso**. 
5. Alterne **Usuário administrador** para **Ativar**.
6. Copie os valores para **Servidor de logon**, **Nome de usuário** e **Senha**. Você usará esses valores no tutorial posteriormente ao publicar a imagem do Docker no registro e ao adicionar as credenciais de registro para o tempo de execução do Edge. 

## <a name="create-an-iot-edge-module-project"></a>Criar um projeto de módulo do IoT Edge
As etapas a seguir mostram como criar um módulo do IoT Edge baseado no .NET Core 2.0 usando o Visual Studio Code e a extensão do Azure IoT Edge.
1. No Visual Studio Code, selecione **Exibir** > **Terminal Integrado** para abrir o terminal integrado do Visual Studio Code.
2. No terminal integrado, digite o seguinte comando para instalar (ou atualizar) o modelo **AzureIoTEdgeModule** no dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

3. Criar um projeto para o novo módulo. O comando a seguir cria a pasta do projeto, **FilterModule**, com o seu repositório de contêiner. O segundo parâmetro deve estar na forma de `<your container registry name>.azurecr.io` se você estiver usando o registro de contêiner do Azure. Digite o seguinte comando na pasta de trabalho atual:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule -r <your container registry address>/filtermodule
    ```
 
4. Selecione **Arquivo** > **Abrir Pasta**.
5. Navegue para a pasta **FilterModule**  e clique em **Selecionar Pasta** para abrir o projeto no Visual Studio Code.
6. No Visual Studio Code explorer, clique em **Program.cs** para abri-lo.

   ![Abra Program.cs][1]

7. Na parte superior do namespace **FilterModule**, adicione três instruções `using` aos tipos usados posteriormente:

    ```csharp
    using System.Collections.Generic;     // for KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // for TwinCollection
    using Newtonsoft.Json;                // for JsonConvert
    ```

8. Adicione a `temperatureThreshold` variável para a classe do **Programa**. Esta variável define o valor que a temperatura medida deve exceder para que os dados sejam enviados para o Hub IoT. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

9. Adicione as classes `MessageBody`, `Machine` e `Ambient` à classe **Programa**. Essas classes definem o esquema esperado para o corpo de mensagens de entrada.

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

10. No método **Init**, o código cria e configura um objeto **DeviceClient**. Esse objeto permite que o módulo se conecte ao tempo de execução do Azure IoT Edge local para enviar e receber mensagens. A cadeia de caracteres de conexão utilizada no método **Init** é fornecida ao módulo pelo tempo de execução do IoT Edge. Depois de criar o **DeviceClient**, o código lê o TemperatureThreshold das propriedades desejadas no Módulo Gêmeo e registra um retorno de chamada para receber mensagens do Hub IoT Edge por meio do ponto de extremidade **Entrada1**. Substitua o método `SetInputMessageHandlerAsync` por um novo e adicione um método `SetDesiredPropertyUpdateCallbackAsync` para as atualizações de propriedades desejadas. Para fazer essa alteração, substitua a última linha do método **Init** pelo seguinte código:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read TemperatureThreshold from Module Twin Desired Properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    var moduleTwinCollection = moduleTwin.Properties.Desired;
    if (moduleTwinCollection["TemperatureThreshold"] != null)
    {
        temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
    }

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

11. Adicione o método `onDesiredPropertiesUpdate` à classe **Programa**. Este método recebe atualizações sobre as propriedades desejadas do módulo gêmeo e atualiza a variável **temperatureThreshold** para corresponder. Todos os módulos possuem seu próprio módulo gêmeo, o que permite configurar o código em execução dentro de um módulo diretamente da nuvem.

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
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

12. Substitua o método `PipeMessage` pelo método `FilterMessages`. Esse método é chamado sempre que o módulo recebe uma mensagem do hub do IoT Edge. Ele filtra as mensagens que reportam temperaturas abaixo do limite de temperatura definido através do módulo gêmeo. Ele também adiciona a propriedade **MessageType** à mensagem com o valor definido para **Alerta**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
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
            var deviceClient = (DeviceClient)userContext;
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

13. Salve o arquivo.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Criar uma imagem do Docker e publicá-la no registro

1. Entre no Docker, inserindo o seguinte comando no terminal integrado do Visual Studio Code: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

2. No código explorador do VS Code clique com o botão direito no arquivo **module.json** e clique no **módulo de Docker do Azure IoT Edge de Push**. Na caixa suspensa pop-up na parte superior da janela de Código do VS, selecione sua plataforma de contêiner, ou **amd64** para contêiner Linux ou **windows-amd64** para o contêiner do Windows. O Código do VS compila seu código, coloca em contêiner o `FilterModule.dll` e enviar por push para o registro de contêiner especificado.


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
4. Verifique se o módulo **tempSensor** é preenchido automaticamente. Se não, utilize as seguintes etapas para adicioná-lo:
    1. Selecione **Adicionar Módulo do IoT Edge**.
    2. No campo **Nome**, insira `tempSensor`.
    3. No campo **URI da Imagem**, insira `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Deixe as outras configurações inalteradas e clique em **Salvar**.
5. Adicionar o **filterModule** que você criou nas seções anteriores. 
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
6. Clique em **Próximo**.
7. Na etapa **Especificar Rotas**, copie o JSON abaixo na caixa de texto. Os módulos publicam todas as mensagens no tempo de execução do Edge. As regras declarativas no tempo de execução definem o local do fluxo de mensagens. Neste tutorial, você precisa de duas rotas. A primeira rota transporta mensagens de sensor de temperatura para o módulo de filtro pelo ponto de extremidade "input1", que é o ponto de extremidade configurado com o manipulador **FilterMessages**. A segunda rota transporta as mensagens do módulo de filtro para o Hub IoT. Nessa rota, `upstream` é um destino especial que manda o Hub do Edge enviar mensagens para o Hub IoT. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

8. Clique em **Próximo**.
9. Na etapa **Examinar Modelo**, clique em **Enviar**. 
10. Volte para a página de detalhes do dispositivo IoT Edge e clique em **Atualizar**. Você deverá ver o novo módulo **filtermodule** em execução junto com o módulo **tempSensor** e o **tempo de execução do IoT Edge**. 

## <a name="view-generated-data"></a>Exibir dados gerados

Para monitorar mensagens de dispositivo para nuvem enviadas do seu dispositivo IoT Edge para o seu hub IoT:
1. Configure a extensão Kit de Ferramentas do Azure IoT com a cadeia de conexão para o hub IoT: 
    1. Abra o gerenciador do Visual Studio Code, selecionando **Exibir** > **Explorer**. 
    2. No gerenciador, clique em **DISPOSITIVOS HUB IOT** e clique em **...**. Clique em **Definir Cadeia de Conexão do Hub IoT** e insira a cadeia de conexão para o hub IoT ao qual seu dispositivo IoT Edge se conecta na janela pop-up. 

        Para localizar a cadeia de conexão, clique no bloco para o hub IoT no portal do Azure e clique em **Políticas de acesso compartilhado**. Em **Políticas de acesso compartilhado**, clique na política **iothubowner** e copie a cadeia de conexão do Hub IoT na janela **iothubowner**.   

2. Para monitorar os dados recebidos do Hub IoT, selecione **Exibir** > **Paleta de Comandos** e procure o comando de menu **IoT: Iniciar o monitoramento de mensagem D2C**. 
3. Para parar de monitorar os dados, use o comando de menu **IoT: Parar o monitoramento de mensagem D2C**. 

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
