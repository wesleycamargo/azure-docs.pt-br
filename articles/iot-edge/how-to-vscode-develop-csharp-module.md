---
title: "Usar o Visual Studio Code para desenvolver um módulo em C# com o Azure IoT Edge | Microsoft Docs"
description: "Desenvolver e implantar um módulo em C# com Azure IoT Edge no VS Code sem troca de contexto"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 269f77e5015175e45e0078926ef06699811889a4
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2017
---
# <a name="use-visual-studio-code-to-develop-c-module-with-azure-iot-edge"></a>Usar o Visual Studio Code para desenvolver um módulo em C# com o Azure IoT Edge
Este artigo fornece instruções detalhadas de como usar o [Visual Studio Code](https://code.visualstudio.com/) como a ferramenta de desenvolvimento principal para desenvolver seus módulos do IoT Edge. 

## <a name="prerequisites"></a>pré-requisitos
Este tutorial assume que você está usando um computador ou uma máquina virtual que executa Windows ou Linux como seu computador de desenvolvimento. O dispositivo do IoT Edge pode ser outro dispositivo físico ou você pode simular o dispositivo do IoT Edge em seu computador de desenvolvimento.

Conclua os tutoriais a seguir antes de iniciar estas diretrizes.
- Implantar o Azure IoT Edge em um dispositivo simulado no [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) ou no [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [Desenvolver e implantar um módulo do IoT Edge em C# em seu dispositivo simulado](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module)

Aqui está uma lista de verificação com os itens que você terá ao concluir os tutoriais anteriores.

- [Visual Studio Code](https://code.visualstudio.com/). 
- [Extensão do Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
- [C# para extensão do Visual Studio Code (com OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
- [Docker](https://docs.docker.com/engine/installation/)
- [SDK 2.0 do .NET Core](https://www.microsoft.com/net/core#windowscmd). 
- [Python 2.7](https://www.python.org/downloads/)
- [Script de controle do IoT Edge](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- Modelo AzureIoTEdgeModule (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- Um hub IoT ativo com pelo menos um dispositivo do IoT Edge.

Também é recomendável instalar o [Suporte do Docker para VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) a fim de gerenciar melhor suas imagens e contêineres do módulo.

## <a name="deploy-an-azure-iot-edge-module-in-vs-code"></a>Implantar um módulo do Azure IoT Edge no VS Code

### <a name="list-your-iot-hub-devices"></a>Listar os dispositivos de Hub IoT
Há duas maneiras de listar os dispositivos de Hub IoT em seu VS Code. Você pode escolher qualquer uma para continuar.

#### <a name="sign-in-your-azure-account-in-vscode-and-choose-your-iot-hub"></a>Entre na sua conta do Azure no VSCode e escolha o Hub IoT
1. Na Paleta de Comandos (F1 ou Ctrl+Shift+P), digite e selecione **Azure: Sign in** (Azure: Entrar). Em seguida, clique em  **Copy* & Open** (Copiar e Abrir) no pop-up. Cole (Ctrl+V) o código no seu navegador e clique no botão Continue (Continuar). Em seguida, faça logon com sua conta do Azure. Você pode ver suas informações de conta na barra de status do VS Code.
2. Na Paleta de Comandos (F1 ou Ctrl+Shift+P), digite e selecione **IoT: Select IoT Hub** (IoT: Selecionar Hub IoT). Primeiro selecione a assinatura em que criou seu Hub IoT no tutorial anterior. Escolha o Hub IoT que contém o dispositivo do IoT Edge.


#### <a name="set-iot-hub-connection-string"></a>Definir cadeia de conexão do Hub IoT
1. Na Paleta de Comandos (F1 ou Ctrl+Shift+P), digite e selecione **IoT: Set IoT Hub Connection String** (IoT: Definir Cadeia de Conexão do Hub IoT). Cole a cadeia de conexão na política **iothubowner** (você pode encontrá-la nas políticas de acesso compartilhado do seu Hub IoT no portal do Azure).
 

Você pode ver a lista de dispositivos no Device Explorer do Hub IoT na barra do lado esquerdo.

### <a name="start-your-iot-edge-runtime-and-deploy-a-module"></a>Iniciar o tempo de execução do IoT Edge e implantar um módulo
Instale e inicie o tempo de execução do Azure IoT Edge no dispositivo. E implante um módulo de sensor simulada que enviará dados de telemetria para o Hub IoT.
1. Na Paleta de Comandos, selecione **Edge: Setup Edge** (Edge: configurar Edge) e escolha sua ID do dispositivo do IoT Edge. Ou clique com o botão direito do mouse na ID do dispositivo do IoT Edge na lista de dispositivos e selecione **Setup Edge** (Configurar Edge).
2. Na Paleta de Comandos, selecione **Edge: Start Edge** (Edge: Start Edge) para iniciar o tempo de execução do Edge. Você pode ver as saídas correspondentes no terminal integrado.
3. Verifique o status do tempo de execução do Edge no Explorador do Docker. Verde significa que ele está em execução. O tempo de execução do IoT Edge foi iniciado com êxito.
4. Agora seu tempo de execução do Edge está em execução, o que significa que o computador agora simula um dispositivo do Edge. A próxima etapa é simular um sensor que fica enviando mensagens para seu dispositivo do Edge. Na Paleta de Comandos, digite e selecione **Edge: Generate Edge configuration file** (Edge: gerar arquivo de configuração do Edge). E selecione uma pasta para criar esse arquivo. No arquivo deployment.json gerado, substitua a linha "<registry>/<image>:<tag>" por `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
5. Selecione **Edge: Create deployment for Edge device** (Edge: criar implantação para dispositivo do Edge) e escolha a ID do dispositivo do Edge para criar uma nova implantação. Ou clique com o botão direito do mouse na ID do dispositivo Edge na lista de dispositivos e selecione **Criar implantação para dispositivo do Edge**. 
6. Você verá seu IoT Edge iniciar a execução no Explorador do Docker com o sensor simulado. Clique com botão direito do mouse no contêiner no Explorador do Docker. Você pode assistir aos logs de docker para cada módulo.
7. Clique com o botão direito do mouse na ID do dispositivo do Edge para poder monitorar mensagens D2C no VS Code.
8. Para interromper o tempo de execução do IoT Edge e o módulo de sensor, você pode digitar e selecionar **Edge: Stop Edge** na Paleta de Comandos.

## <a name="develop-and-deploy-a-c-module-in-vs-code"></a>Desenvolver e implantar o módulo em C# no VS Code
No tutorial [Desenvolver um módulo em C#](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module), você atualiza, cria e publica sua imagem de módulo no VS Code e visita o portal do Azure para implantar o módulo em C#. Esta seção apresenta como usar o VS Code para implantar e monitorar o módulo em C#.

### <a name="start-a-local-docker-registry"></a>Iniciar um registro local do Docker
Você pode usar qualquer registro compatível com o Docker neste tutorial. Dois serviços de registro do Docker populares disponíveis na nuvem são o [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) e o [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Esta seção usa um [registro local do Docker](https://docs.docker.com/registry/deploying/), que é mais fácil para fins de teste durante o desenvolvimento inicial.
No **terminal integrado** (Ctrl + ') do VS Code, execute os seguintes comandos para iniciar um registro local.  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> O exemplo acima mostra as configurações do registro que são adequadas somente para teste. Um registro pronto para produção deve ser protegido por TLS e deve, idealmente, usar um mecanismo de controle de acesso. Recomendamos que você use o [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) ou o [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) para implantar módulos do IoT Edge prontos para produção.

### <a name="create-an-iot-edge-module-project"></a>Criar um projeto de módulo do IoT Edge
As etapas a seguir mostram como criar um módulo do IoT Edge baseado no .NET Core 2.0 usando o Visual Studio Code e a extensão do Azure IoT Edge. Se você concluiu esta seção no tutorial anterior, pode ignorá-la com segurança.
1. No Visual Studio Code, selecione **Exibir** > **Terminal Integrado** para abrir o terminal integrado do Visual Studio Code.
3. No terminal integrado, digite o seguinte comando para instalar (ou atualizar) o modelo **AzureIoTEdgeModule** no dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. Criar um projeto para o novo módulo. O comando a seguir cria a pasta do projeto, **FilterModule**, na pasta de trabalho atual:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```
 
3. Selecione **Arquivo** > **Abrir Pasta**.
4. Navegue para a pasta **FilterModule**  e clique em **Selecionar Pasta** para abrir o projeto no Visual Studio Code.
5. No Visual Studio Code explorer, clique em **Program.cs** para abri-lo.
6. Adicione a `temperatureThreshold` variável para a classe do **Programa**. Esta variável define o valor que a temperatura medida deve exceder para que os dados sejam enviados para o Hub IoT. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

7. Adicione as classes `MessageBody`, `Machine` e `Ambient` à classe **Programa**. Essas classes definem o esquema esperado para o corpo de mensagens de entrada.

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

8. No método **Init**, o código cria e configura um objeto **DeviceClient**. Esse objeto permite que o módulo se conecte ao tempo de execução do Azure IoT Edge local para enviar e receber mensagens. A cadeia de caracteres de conexão utilizada no método **Init** é fornecida ao módulo pelo tempo de execução do IoT Edge. Depois de criar o **DeviceClient**, o código registra um retorno de chamada para receber mensagens do hub IoT Edge por meio do ponto de extremidade **Entrada1**. Substitua o método `SetInputMessageHandlerAsync` por um novo e adicione um método `SetDesiredPropertyUpdateCallbackAsync` para as atualizações de propriedades desejadas. Para fazer essa alteração, substitua a última linha do método **Init** pelo seguinte código:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

9. Adicione o método `onDesiredPropertiesUpdate` à classe **Programa**. Este método recebe atualizações sobre as propriedades desejadas do módulo gêmeo e atualiza a variável **temperatureThreshold** para corresponder. Todos os módulos possuem seu próprio módulo gêmeo, o que permite configurar o código em execução dentro de um módulo diretamente da nuvem.

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

10. Substitua o método `PipeMessage` pelo método `FilterMessages`. Esse método é chamado sempre que o módulo recebe uma mensagem do hub do IoT Edge. Ele filtra as mensagens que reportam temperaturas abaixo do limite de temperatura definido através do módulo gêmeo. Ele também adiciona a propriedade **MessageType** à mensagem com o valor definido para **Alerta**. 

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

11. Para compilar o projeto, clique com o botão direito do mouse no arquivo **FilterModule.csproj** no Explorer e clique em **Compilar módulo IoT Edge**. Esse processo compila o módulo e exporta o binário e suas dependências para uma pasta que é usada para criar uma imagem do Docker.


### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Criar uma imagem do Docker e publicá-la no registro

1. No gerenciador do Visual Studio Code, expanda a pasta **Docker**. Em seguida, expanda a pasta para sua plataforma de contêiner **linux-x64** ou **windows-nano**.
2. Clique com botão direito do mouse no arquivo **Dockerfile** e clique em **Criar imagem do Docker para o modulo IoT Edge**. 
3. Na janela **Selecionar Pasta** procure por ou insira `./bin/Debug/netcoreapp2.0/publish`. Clique em **Selecionar pasta como EXE_DIR**.
4. Na caixa de texto pop-up na parte superior da janela do VS Code, insira o nome da imagem. Por exemplo: `<your container registry address>/filtermodule:latest`. Se você estiver implantando o registro local, deverá ser `localhost:5000/filtermodule:latest`.
5. Envie a imagem por push para o repositório do Docker. Use o comando **Edge: Push IoT Edge module Docker image** e insira a URL da imagem na caixa de texto pop-up na parte superior da janela do VS Code. Use a mesma URL de imagem usada na etapa acima.

### <a name="deploy-your-iot-edge-modules"></a>Implantar seus módulos do IoT Edge

1. Abra o arquivo `deployment.json`, substitua a seção **módulos** pelo conteúdo abaixo:
    ```json
    "tempSensor": {
        "version": "1.0",
        "type": "docker",
        "status": "running",
        "restartPolicy": "always",
        "settings": {
            "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
            "createOptions": ""
        }
    },
    "filtermodule": {
        "version": "1.0",
        "type": "docker",
        "status": "running",
        "restartPolicy": "always",
        "settings": {
            "image": "localhost:5000/filtermodule:latest",
            "createOptions": ""
        }
    }
    ```

2. Substitua a seção **rotas** pelo conteúdo abaixo:
    ```json
    {
        "routes": {
            "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
            "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        }
    }
    ```
   > [!NOTE]
   > As regras declarativas no tempo de execução definem o local para onde as mensagens fluem. Neste tutorial, você precisa de duas rotas. A primeira rota transporta mensagens de sensor de temperatura para o módulo de filtro pelo ponto de extremidade "input1", que é o ponto de extremidade configurado com o manipulador FilterMessages. A segunda rota transporta as mensagens do módulo de filtro para o Hub IoT. Nessa rota, upstream é um destino especial que manda o Hub do Edge enviar mensagens para o Hub IoT.

3. Salve o arquivo.
4. Na Paleta de Comandoss, selecione **Edge: Create deployment for Edge device**. Em seguida, selecione a ID do seu dispositivo do IoT Edge para criar uma implantação. Ou clique com o botão direito do mouse na ID do dispositivo na lista de dispositivos e selecione **Criar implantação para dispositivo do Edge**.
5. Selecione o `deployment.json` que você atualizou. Você poderá ver as saídas correspondentes da sua implantação na janela de saída.
6. Inicie o tempo de execução do Edge na Paleta de Comandoss. **Edge: iniciar o Edge**
7. Você poderá ver o tempo de execução do IoT Edge iniciar a execução no gerenciador do Docker, com o sensor simulado e o módulo de filtro.
8. Clique com o botão direito do mouse na ID do dispositivo do Edge para poder monitorar mensagens D2C no VS Code.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um módulo no IoT Edge e o implantou no dispositivo do IoT Edge no VS Code. Você pode prosseguir para um dos tutoriais a seguir para saber mais sobre outros cenários de desenvolvimento do Azure IoT Edge no VS Code.

> [!div class="nextstepaction"]
> [Depurar módulo em C# no VS Code](how-to-vscode-debug-csharp-module.md)
