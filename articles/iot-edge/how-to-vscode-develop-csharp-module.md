---
title: "Usar o Visual Studio Code para desenvolver um módulo em C# com o Azure IoT Edge | Microsoft Docs"
description: "Desenvolver e implantar um módulo C# com Azure IoT Edge no Visual Studio Code sem comutação de contexto."
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4cf07d5c4a21fa989e7de6e996cc62424099e3e5
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="use-visual-studio-code-to-develop-a-c-module-with-azure-iot-edge"></a>Usar o Visual Studio Code para desenvolver um módulo C# com o Azure IoT Edge
Este artigo fornece instruções detalhadas para usar o [Visual Studio Code](https://code.visualstudio.com/) como a ferramenta de desenvolvimento principal para desenvolver e implantar os módulos do Azure IoT Edge. 

## <a name="prerequisites"></a>pré-requisitos
Este tutorial assume que você está usando um computador ou uma máquina virtual que executa Windows ou Linux como seu computador de desenvolvimento. O dispositivo do IoT Edge pode ser outro dispositivo físico ou você pode simular dispositivo do IoT Edge no computador de desenvolvimento.

Antes de iniciar essas diretrizes, conclua os tutoriais a seguir:
- Implantar o Azure IoT Edge em um dispositivo simulado no [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) ou no [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [Desenvolver e implantar um módulo do IoT Edge em C# em seu dispositivo simulado](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module)

Aqui está uma lista de verificação com os itens que você deverá ter após concluir os tutoriais anteriores:

- [Visual Studio Code](https://code.visualstudio.com/) 
- [Extensão do Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
- [C# para extensão do Visual Studio Code (fornecido por OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
- [Docker](https://docs.docker.com/engine/installation/)
- [SDK 2.0 do .NET Core](https://www.microsoft.com/net/core#windowscmd) 
- [Python 2.7](https://www.python.org/downloads/)
- [Script de controle do IoT Edge](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- Modelo AzureIoTEdgeModule (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- Um Hub IoT ativo com pelo menos um dispositivo do IoT Edge

Também é útil instalar o [Suporte do Docker para VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) para gerenciar melhor suas imagens e contêineres do módulo.

## <a name="deploy-an-azure-iot-edge-module-in-vs-code"></a>Implantar um módulo do Azure IoT Edge no VS Code

### <a name="list-your-iot-hub-devices"></a>Listar os dispositivos de Hub IoT
Há duas maneiras de listar os dispositivos de Hub IoT no VS Code. Você pode escolher qualquer uma para continuar.

#### <a name="sign-in-to-your-azure-account-in-vs-code-and-choose-your-iot-hub"></a>Entre na sua conta do Azure no VSCode e escolha o Hub IoT
1. Na paleta de comandos (F1 ou Ctrl+Shift+P), digite e selecione **Azure: Sign in** (Azure: Entrar). Em seguida, selecione **Copiar e Abrir**. Cole (Ctrl + V) o código no seu navegador e selecione **Continuar**. Em seguida, entre usando a sua conta do Azure. Você pode ver as informações da sua conta na barra de status do VS Code.
2. Na paleta de comando, digite e selecione **IoT: Selecionar Hub IoT**. Primeiro, selecione a assinatura onde você criou o Hub IoT no tutorial anterior. Em seguida, escolha o Hub IoT que contém o dispositivo do IoT Edge.

    ![Captura de tela da lista de dispositivos](./media/how-to-vscode-develop-csharp-module/device-list.png)

#### <a name="set-the-iot-hub-connection-string"></a>Defina a cadeia de caracteres de conexão do Hub IoT
Na paleta de comandos, digite e selecione **IoT: Definir a cadeia de caracteres de conexão do Hub IoT**. Certifique-se de colar a cadeia de caracteres de conexão na política **iothubowner**. (É possível encontrá-la nas políticas de acesso compartilhado do seu Hub IoT no Portal do Azure.)
 
Você pode consultar a lista de dispositivos no Devices Explorer do Hub IoT, na barra lateral à esquerda.

### <a name="start-your-iot-edge-runtime-and-deploy-a-module"></a>Iniciar o tempo de execução do IoT Edge e implantar um módulo
Instale e inicie o tempo de execução do Azure IoT Edge no dispositivo. Implante um módulo de sensor simulado que envie dados telemétricos para o Hub IoT do Azure.
1. Na Paleta de Comandos, selecione **Edge: Setup Edge** (Edge: configurar Edge) e escolha sua ID do dispositivo do IoT Edge. Alternativamente, clique com o botão direito do mouse na ID do dispositivo do IoT Edge na **Lista de Dispositivos** e selecione **Setup Edge**.

    ![Captura de tela do tempo de execução de configuração do Edge](./media/how-to-vscode-develop-csharp-module/setup-edge.png)

2. Na paleta de comandos, selecione **Edge: Start Edge** para iniciar o tempo de execução do Edge. É possível consultar as saídas correspondentes no terminal integrado.

    ![Captura de tela do tempo de execução de início do Edge](./media/how-to-vscode-develop-csharp-module/start-edge.png)

3. Verifique o status de tempo de execução do IoT Edge no Docker Explorer. Verde significa que ele está sendo executado e o tempo de execução do IoT Edge foi iniciado com êxito. O computador agora simula um dispositivo do IoT Edge.

    ![Captura de tela do status de tempo de execução do Edge](./media/how-to-vscode-develop-csharp-module/edge-runtime.png)

4. Simule um sensor que continue enviando mensagens para o dispositivo do IoT Edge. Na paleta de comandos, digite e selecione **Edge: Generate Edge configuration file** (Edge: Gerar arquivo de configuração do Edge). Selecione uma pasta para criar esse arquivo. No arquivo deployment.json que é gerado, substitua o conteúdo `<registry>/<image>:<tag>` por `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview` e salve o arquivo.

    ![Captura de tela do módulo de sensor](./media/how-to-vscode-develop-csharp-module/sensor-module.png)

5. Selecione **Edge: Create deployment for Edge device** e escolha a ID do dispositivo do IoT Edge para criar uma nova implantação. Alternativamente, você pode clicar com o botão direito do mouse na ID do dispositivo do IoT Edge na lista de dispositivos e selecionar **Criar implantação para dispositivo do Edge**. 

6. Você deverá ver o IoT Edge iniciar a execução no Docker Explorer com o sensor simulado. Clique com o botão direito do mouse no contêiner no Docker Explorer. Você pode inspecionar os logs do Docker para cada módulo. Além disso, você pode exibir a lista de módulo na lista de dispositivos.

    ![Captura de tela da lista de módulos](./media/how-to-vscode-develop-csharp-module/module-list.png)

7. Clique com o botão direito do mouse na ID do dispositivo do IoT Edge e você poderá monitorar mensagens D2C no VS Code.
8. Para parar o tempo de execução do IoT Edge e o módulo do sensor, digite e selecione **Edge: Stop Edge** na paleta de comandos.

## <a name="develop-and-deploy-a-c-module-in-vs-code"></a>Desenvolver e implantar o módulo em C# no VS Code
No tutorial [Desenvolver um módulo C#](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module), você atualiza, compila e publica sua imagem do módulo no VS Code. Em seguida, você acessa o Portal do Azure para implantar o módulo C#. Esta seção apresenta como usar o VS Code para implantar e monitorar o módulo em C#.

### <a name="start-a-local-docker-registry"></a>Inicie um registro de Docker local
Você pode usar qualquer registro compatível com o Docker neste tutorial. Dois serviços de registro do Docker populares disponíveis na nuvem são o [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) e o [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Esta seção usa um [registro de Docker local](https://docs.docker.com/registry/deploying/), que é mais fácil de testar durante o desenvolvimento inicial.
No **terminal integrado** (Ctrl + `) do VS Code, execute o comando a seguir para iniciar um registro local:  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> Esse exemplo mostra as configurações de registro que são apropriadas apenas para testes. Um registro pronto para produção deve ser protegido pelo TLS e o ideal é usar um mecanismo de controle de acesso. Recomendamos que você use o [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) ou o [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) para implantar módulos do IoT Edge prontos para produção.

### <a name="create-an-iot-edge-module-project"></a>Criar um projeto de módulo do IoT Edge
As etapas a seguir mostram como criar um módulo do IoT Edge baseado no .NET Core 2.0, usando o Visual Studio Code e a extensão do Azure IoT Edge. Se você concluiu essa seção no tutorial anterior, pode ignorá-la com segurança.
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
4. Navegue para a pasta **FilterModule** e clique em **Selecionar Pasta** para abrir o projeto no Visual Studio Code.
5. No VS Code Explorer, clique em **Program.cs** para abri-lo. Na parte superior do **program.cs**, inclua os namespaces a seguir:
   ```csharp
   using Microsoft.Azure.Devices.Shared;
   using System.Collections.Generic;  
   using Newtonsoft.Json;
   ```

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

8. No método **Init**, o código cria e configura um objeto **DeviceClient**. Esse objeto permite que o módulo conecte-se ao tempo de execução do IoT Edge local para enviar e receber mensagens. O tempo de execução do IoT Edge fornece ao módulo a cadeia de caracteres de conexão usada no método **Init**. Após criar o objeto **DeviceClient**, o código registra um retorno de chamada para receber mensagens do hub do IoT Edge por meio do ponto de extremidade **Entrada1**. Substitua o método `SetInputMessageHandlerAsync` por um novo e adicione um método `SetDesiredPropertyUpdateCallbackAsync` para as atualizações de propriedades desejadas. Para fazer essa alteração, substitua a última linha do método **Init** pelo seguinte código:

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

10. Substitua o método `PipeMessage` pelo método `FilterMessages`. Esse método é chamado sempre que o módulo receber uma mensagem do hub do IoT Edge. Ele filtra as mensagens que reportam temperaturas abaixo do limite de temperatura definido através do módulo gêmeo. Ele também adiciona a propriedade **MessageType** à mensagem com o valor definido para **Alerta**. 

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

11. Para compilar o projeto, clique com o botão direito do mouse no arquivo **FilterModule.csproj** no Explorer e selecione **Compilar módulo IoT Edge**. Esse processo compila o módulo e exporta o binário e suas dependências para uma pasta que é usada para criar uma imagem do Docker. 

    ![Captura de tela do VS Code Explorer](./media/how-to-vscode-develop-csharp-module/build-module.png)

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Criar uma imagem do Docker e publicá-la no registro

1. No VS Code Explorer, expanda a pasta **Docker**. Em seguida, expanda a pasta para sua plataforma de contêiner **linux-x64** ou **windows-nano**.
2. Clique com o botão direito do mouse no arquivo **Dockerfile** e selecione**Compilar imagem do Docker do módulo do IoT Edge**. 

    ![Captura de tela do VS Code Explorer](./media/how-to-vscode-develop-csharp-module/build-docker-image.png)

3. Na janela **Selecionar Pasta** procure por ou insira `./bin/Debug/netcoreapp2.0/publish`. Selecione **Selecionar pasta como EXE_DIR**.
4. Na caixa de texto pop-up na parte superior da janela do VS Code, insira o nome da imagem. Por exemplo: `<your container registry address>/filtermodule:latest`. Se você estiver implantando o registro local, deverá ser `localhost:5000/filtermodule:latest`.
5. Envie a imagem por push para o repositório do Docker. Use o comando **Edge: Push IoT Edge module Docker image** e insira a URL da imagem na caixa de texto pop-up na parte superior da janela do VS Code. Use a mesma URL de imagem usada na etapa anterior. Verifique o log do console para certificar-se de que a imagem foi enviada por push com êxito.

    ![Captura de tela da imagem do Docker enviada por push](./media/how-to-vscode-develop-csharp-module/push-image.png) ![Captura de tela do log do console](./media/how-to-vscode-develop-csharp-module/pushed-image.png)

### <a name="deploy-your-iot-edge-modules"></a>Implantar seus módulos do IoT Edge

1. Abra o arquivo `deployment.json` e substitua a seção **módulos** pelo seguinte:
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

2. Substitua a seção **rotas** pelo seguinte:
    ```json
    "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
    "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
    ```
   > [!NOTE]
   > As regras declarativas no tempo de execução definem o local para onde as mensagens fluem. Neste tutorial, você precisa de duas rotas. A primeira rota transporta mensagens do sensor de temperatura para o módulo de filtro por meio do ponto de extremidade "input1". Esse é o ponto de extremidade configurado com o manipulador FilterMessages. A segunda rota transporta as mensagens do módulo de filtro para o Hub IoT. Nessa rota, upstream é um destino especial que informa o Hub do IoT Edge para enviar mensagens ao Hub IoT.

3. Salve o arquivo.
4. Na paleta de comandos, selecione **Edge: Criar implantação para dispositivo do Edge**. Em seguida, selecione a ID do seu dispositivo do IoT Edge para criar uma implantação. Ou clique com o botão direito do mouse na ID do dispositivo na lista de dispositivos e selecione **Criar implantação para dispositivo do Edge**.

    ![Captura de tela da opção Criar implantação](./media/how-to-vscode-develop-csharp-module/create-deployment.png)

5. Selecione o `deployment.json` que você atualizou. Você poderá ver as saídas correspondentes da sua implantação na janela de saída.

    ![Captura de tela da janela de saída](./media/how-to-vscode-develop-csharp-module/deployment-succeeded.png)

6. Inicie o tempo de execução do IoT Edge na paleta de comandos (selecione **Edge: Start Edge**).
7. Você poderá ver o tempo de execução do IoT Edge em execução no Docker Explorer, com o sensor simulado e o módulo de filtro.

    ![Captura de tela do Docker Explorer](./media/how-to-vscode-develop-csharp-module/solution-running.png)

8. Clique com o botão direito do mouse na ID do dispositivo do IoT Edge e você poderá monitorar mensagens D2C no VS Code.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um módulo no IoT Edge e o implantou no dispositivo do IoT Edge no VS Code. Para saber mais sobre outros cenários quando você estiver desenvolvendo o Azure IoT Edge no VS Code, consulte o seguinte tutorial:

> [!div class="nextstepaction"]
> [Depurar módulo em C# no VS Code](how-to-vscode-debug-csharp-module.md)
