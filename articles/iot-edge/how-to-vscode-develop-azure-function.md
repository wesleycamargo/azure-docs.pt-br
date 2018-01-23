---
title: Usar o Visual Studio Code para desenvolver e implantar Azure Functions no Azure IoT Edge | Microsoft Docs
description: Desenvolver e implantar um Azure Functions em C# com Azure IoT Edge no VS Code sem troca de contexto
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 9637986d10a0e89568b2f79ede3d7b7468bb99a7
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2017
---
# <a name="use-visual-studio-code-to-develop-and-deploy-azure-functions-to-azure-iot-edge"></a>Usar o Visual Studio Code para desenvolver e implantar Azure Functions no Azure IoT Edge

Este artigo fornece instruções detalhadas de como usar o [Visual Studio Code](https://code.visualstudio.com/) como a ferramenta de desenvolvimento principal para desenvolver e implantar o Azure Functions no IoT Edge. 

## <a name="prerequisites"></a>pré-requisitos
Este tutorial assume que você esteja usando um computador ou uma máquina virtual que executa Windows ou Linux como seu computador de desenvolvimento. O dispositivo do IoT Edge pode ser outro dispositivo físico ou você pode simular o dispositivo do IoT Edge no computador de desenvolvimento.

Conclua os tutoriais a seguir antes de iniciar estas diretrizes.
- Implantar o Azure IoT Edge em um dispositivo simulado no [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) ou no [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [Implantar o Azure Functions](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function)

Aqui está uma lista de verificação com os itens que você terá ao concluir os tutoriais anteriores.

- [Visual Studio Code](https://code.visualstudio.com/). 
- [Extensão do Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
- [C# para extensão do Visual Studio Code (com OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
- [Docker](https://docs.docker.com/engine/installation/)
- [SDK 2.0 do .NET Core](https://www.microsoft.com/net/core#windowscmd). 
- [Python 2.7](https://www.python.org/downloads/)
- [Script de controle do IoT Edge](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- Modelo AzureIoTEdgeFunction (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
- Um hub IoT ativo com pelo menos um dispositivo do IoT Edge.

Também é recomendável instalar o [Suporte do Docker para VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) para gerenciar melhor suas imagens e contêineres do módulo.

> [!NOTE]
> Atualmente, o Azure Functions no IoT Edge só é compatível com C#.

## <a name="deploy-azure-iot-functions-in-vs-code"></a>Implantar funções do Azure IoT no VS Code
No tutorial [Implantar o Azure Functions](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function), você atualiza, cria e publica suas imagens de módulo de função no VS Code e, em seguida, acessa o Portal do Azure para implantar o Azure Functions. Esta seção apresenta como usar o VS Code para implantar e monitorar o Azure Functions.

### <a name="start-a-local-docker-registry"></a>Iniciar um registro local do Docker
Você pode usar qualquer registro compatível com o Docker neste tutorial. Dois serviços de registro do Docker populares disponíveis na nuvem são o [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) e o [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Esta seção usa um [registro local do Docker](https://docs.docker.com/registry/deploying/), que é mais fácil para fins de teste durante o desenvolvimento inicial.
No **terminal integrado** (Ctrl + ') do VS Code, execute os seguintes comandos para iniciar um registro local.  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> O exemplo acima mostra as configurações do registro que são adequadas somente para teste. Um registro pronto para produção deve ser protegido por TLS e deve, idealmente, usar um mecanismo de controle de acesso. Recomendamos que você use [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) ou o [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) para implantar módulos do IoT Edge prontos para produção.

### <a name="create-a-function-project"></a>Criar um projeto de função
As etapas a seguir mostram como criar um módulo do IoT Edge baseado no .NET Core 2.0 usando o Visual Studio Code e a extensão do Azure IoT Edge. Se você concluiu esta seção no tutorial anterior, você pode ignorá-la com segurança.

1. No Visual Studio Code, selecione **Exibir** > **Terminal Integrado** para abrir o terminal integrado do Visual Studio Code.
2. Para instalar ou atualizar o modelo **AzureIoTEdgeFunction** em dotnet, execute o seguinte comando no terminal integrado:

   ```cmd/sh
   dotnet new -i Microsoft.Azure.IoT.Edge.Function
   ```
3. Criar um projeto para o novo módulo. O comando a seguir cria a pasta do projeto, **FilterFunction**, na pasta de trabalho atual:

   ```cmd/sh
   dotnet new aziotedgefunction -n FilterFunction
   ```
 
4. Selecione **Arquivo > Abrir Pasta**, procure a pasta **FilterFunction** e abra o projeto no VS Code.
5. Navegue até a pasta **FilterFunction** e clique em **Selecionar Pasta** para abrir o projeto no VS Code.
6. No gerenciador do VS Code, expanda a pasta **EdgeHubTrigger-Csharp** e, em seguida, abra o arquivo **run.csx**.
7. Substitua o conteúdo do arquivo pelo código a seguir:

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

8. Salve o arquivo.

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Criar uma imagem do Docker e publicá-la no registro

1. No gerenciador do Visual Studio Code, expanda a pasta **Docker**. Em seguida, expanda a pasta para sua plataforma de contêiner **linux-x64** ou **windows-nano**.
2. Clique com botão direito do mouse no arquivo **Dockerfile** e clique em **Criar imagem do Docker para o modulo IoT Edge**. 
3. Navegue até a pasta do projeto **FilterFunction** e clique em **Selecionar Pasta como EXE_DIR**. 
4. Na caixa de texto pop-up na parte superior da janela do VS Code, insira o nome da imagem. Por exemplo: `<your container registry address>/filterfunction:latest`. Se você estiver implantando no registro local, deverá ser `localhost:5000/filterfunction:latest`.
5. Envie a imagem por push para o repositório do Docker. Use o comando **Edge: Push IoT Edge module Docker image** e insira a URL da imagem na caixa de texto pop-up na parte superior da janela do VS Code. Use a mesma URL de imagem usada na etapa acima.

### <a name="deploy-your-function-to-iot-edge"></a>Implantar a função no IoT Edge

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
   "filterfunction": {
      "version": "1.0",
      "type": "docker",
      "status": "running",
      "restartPolicy": "always",
      "settings": {
         "image": "localhost:5000/filterfunction:latest",
         "createOptions": ""
      }
   }
   ```

2. Substitua a seção **rotas** pelo conteúdo abaixo:
   ```json
   {
       "routes":{
           "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
           "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
   }
   ```
   > [!NOTE]
   > As regras declarativas no tempo de execução definem o local para onde as mensagens fluem. Neste tutorial, você precisa de duas rotas. A primeira rota transporta mensagens do sensor de temperatura para a função de filtro pelo ponto de extremidade "input1", que é o ponto de extremidade configurado com o manipulador FilterMessages. A segunda rota transporta as mensagens da função de filtro para o Hub IoT. Nessa rota, upstream é um destino especial que manda o Hub do Edge enviar mensagens para o Hub IoT.

3. Salve esse arquivo.
4. Na Paleta de comandos, selecione **Edge: criar implantação para dispositivo do Edge**. Em seguida, selecione a ID do seu dispositivo do IoT Edge para criar uma implantação. Ou clique com o botão direito do mouse na ID do dispositivo na lista de dispositivos e selecione **Criar implantação para dispositivo do Edge**.
5. Selecione o `deployment.json` que você atualizou. Você poderá ver as saídas correspondentes da sua implantação na janela de saída.
6. Inicie o tempo de execução do Edge na Paleta de comandos. **Edge: iniciar Edge**
7. Você poderá ver o tempo de execução do IoT Edge iniciar a execução no gerenciador do Docker, com o sensor simulado e a função de filtro.
8. Clique com o botão direito do mouse na ID do dispositivo do Edge, e você poderá monitorar mensagens D2C no VS Code.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma função do Azure no IoT Edge e a implantou no dispositivo do IoT Edge no VS Code. Você pode prosseguir para um dos tutoriais a seguir para saber mais sobre outros cenários de desenvolvimento do Azure IoT Edge no VS Code.

> [!div class="nextstepaction"]
> [Depurar Azure Functions no VS Code](how-to-vscode-debug-azure-function.md)
