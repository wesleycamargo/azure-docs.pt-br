---
title: Tutorial de C# do Azure IoT Edge | Microsoft Docs
description: Este tutorial mostra como criar um módulo do IoT Edge com código em C# e implantá-lo em um dispositivo de borda.
services: iot-edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 61bcb01f549b6a47f3c4899975d4b1b23fbd9e3b
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957013"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Tutorial: Desenvolver e implantar um módulo do IoT Edge em C# em seu dispositivo simulado

Use os módulos do Azure IoT Edge para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial o orienta através da criação e implantação de um módulo IoT Edge que filtra os dados do sensor. Você utilizará o dispositivo IoT Edge simulado que foi criado em Implantar Azure IoT Edge em um dispositivo simulado nos inícios rápidos de [Windows][lnk-tutorial1-win] ou [Linux][lnk-tutorial1-lin]. Neste tutorial, você aprenderá como:    

> [!div class="checklist"]
> * Use o Visual Studio Code para criar um módulo do IoT Edge baseado no SDK .NET Core 2.0.
> * Usar o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no registro.
> * Implantar o módulo no dispositivo IoT Edge.
> * Exibir os dados gerados.


O módulo IoT Edge que criado neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Ele somente envia mensagens upstream se a temperatura estiver acima de um limite especificado. Esse tipo de análise na borda é útil para reduzir a quantidade de dados que é comunicada e armazenada na nuvem. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo do Azure IoT Edge:

* Você pode usar seu computador de desenvolvimento ou uma máquina virtual como um dispositivo do Edge seguindo as etapas no início rápido para os [dispositivos Linux](quickstart-linux.md) ou [Windows](quickstart.md).

Recursos de nuvem:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão ou gratuito no Azure. 

Recursos de desenvolvimento:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# para extensão do Visual Studio Code (com OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [Extensão do Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) para Visual Studio Code. 
* [SDK do .NET Core 2.1](https://www.microsoft.com/net/download).
* [CE do Docker](https://docs.docker.com/install/)


## <a name="create-a-container-registry"></a>Criar um registro de contêiner
Neste tutorial, você utiliza a extensão do Azure IoT Edge do Visual Studio Code para compilar um módulo e criar uma **imagem de contêiner** dos arquivos. Em seguida, você efetua push dessa imagem para um **registro** que armazena e gerencia suas imagens. Finalmente, você implanta a imagem do seu registro para executar no dispositivo IoT Edge.  

Você pode usar qualquer registro compatível com o Docker neste tutorial. Dois serviços de registro do Docker populares disponíveis na nuvem são o [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) e o [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Este tutorial utiliza o Registro de Contêiner do Azure. 

1. No [Portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Contêineres** > **Registro de Contêiner do Azure** .
2. Nomeie seu registro, escolha uma assinatura, selecione um grupo de recursos e defina a SKU para **Básica**. 
3. Selecione **Criar**.
4. Depois que o registro de contêiner for criado, navegue até ele e selecione **Chaves de acesso**. 
5. Alterne **Usuário administrador** para **Ativar**.
6. Copie os valores para **Servidor de logon**, **Nome de usuário** e **Senha**. Você usará esses valores mais tarde no tutorial para publicar a imagem do Docker no registro e adicionar as credenciais de registro ao tempo de execução do Azure IoT Edge. 

## <a name="create-an-iot-edge-module-project"></a>Criar um projeto de módulo do IoT Edge
As etapas a seguir criam um projeto de módulo do IoT Edge baseado no SDK do .NET Core 2.0 usando o Visual Studio Code e a extensão do Azure IoT Edge.

### <a name="create-a-new-solution"></a>Criar uma nova solução

Crie um modelo de solução de C# que possa ser personalizado com seu próprio código. 

1. No Visual Studio Code, selecione **Exibir** > **Paleta de comandos** para abrir a paleta de comandos do VS Code. 

2. Na paleta de comandos, insira e execute o comando **Azure: Entrar** e siga as instruções para entrar em sua conta do Azure. Se já tiver entrado, pode ignorar esta etapa.

3. Na paleta de comandos, insira e execute o comando **Azure IoT Edge: nova solução IoT Edge**. Na paleta de comandos, forneça as seguintes informações para criar sua solução: 

   1. Selecione a pasta na qual deseja criar a solução. 
   2. Forneça um nome para a solução ou aceite o padrão **EdgeSolution**.
   3. Escolha **Módulo C#** como o modelo de módulo. 
   4. Substitua o nome do módulo padrão **CSharpModule**. 
   5. Especifique o registro de contêiner do Azure que você criou na seção anterior como o repositório de imagens do primeiro módulo. Substitua **localhost:5000** pelo valor de servidor de logon que você copiou. A cadeia de caracteres final se parece com \<nome do registro\>.azurecr.io/csharpmodule.

   ![Fornecer o repositório de imagem do Docker](./media/tutorial-csharp-module/repository.png)

A janela do VS Code carregará seu workspace da solução IoT Edge. O workspace da solução contém cinco componentes de nível superior. Você não editará a pasta **\.vscode** ou o arquivo **\.gitignore** neste tutorial. A pasta **módulos** contém o código C# para o seu módulo, bem como Dockerfiles para a compilação de seu módulo como uma imagem de contêiner. O arquivo **\.env** armazena suas credenciais de registro de contêiner. O arquivo **deployment.template.json** contém as informações que o tempo de execução do IoT Edge usa para implantar módulos em um dispositivo. 

Se você não especifica um registro de contêiner durante a criação de sua solução, mas aceita o valor de localhost:5000, você não terá um arquivo \.env. 

   ![Workspace da solução C#](./media/tutorial-csharp-module/workspace.png)

### <a name="add-your-registry-credentials"></a>Adicionar suas credenciais de registro

O arquivo do ambiente armazena as credenciais para o registro de contêiner e as compartilha com o tempo de execução do IoT Edge. O tempo de execução precisa dessas credenciais para efetuar pull de imagens privadas para o dispositivo IoT Edge. 

1. No explorador do VS Code, abra o arquivo .env. 
2. Atualize os campos com os valores de **nome de usuário** e **senha** que você copiou do registro de contêiner do Azure. 
3. Salve o arquivo. 

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com código personalizado

1. No explorador do VS Code, abra **módulos** > **CSharpModule** > **Program.cs**.

5. Na parte superior do namespace **CSharpModule**, adicione três instruções **using** aos tipos usados posteriormente:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

6. Adicione a variável **temperatureThreshold** à classe **Program**. Essa variável define o valor que a temperatura medida deve exceder para que os dados sejam enviados ao hub IoT. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

7. Adicione as classes **MessageBody**, **Machine** e **Ambient** à classe **Program**. Essas classes definem o esquema esperado para o corpo de mensagens de entrada.

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

8. No método **Init**, o código cria e configura um objeto **ModuleClient**. Esse objeto permite que o módulo se conecte ao tempo de execução do Azure IoT Edge local para enviar e receber mensagens. A cadeia de caracteres de conexão utilizada no método **Init** é fornecida ao módulo pelo tempo de execução do IoT Edge. Depois de criar o **ModuleClient**, o código lê o valor **temperatureThreshold** das propriedades desejadas do módulo gêmeo. O código registra um retorno de chamada para receber mensagens de um hub IoT Edge por meio do ponto de extremidade **input1**. Substitua o método **SetInputMessageHandlerAsync** por um novo e adicione um método **SetDesiredPropertyUpdateCallbackAsync** para atualizações das propriedades desejadas. Para fazer essa alteração, substitua a última linha do método **Init** pelo seguinte código:

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    var moduleTwinCollection = moduleTwin.Properties.Desired;
    try {
        temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
    } catch(ArgumentOutOfRangeException e) {
        Console.WriteLine($"Property TemperatureThreshold not exist: {e.Message}"); 
    }

    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

9. Adicione o método **onDesiredPropertiesUpdate** à classe **Program**. Este método recebe atualizações sobre as propriedades desejadas do módulo gêmeo e atualiza a variável **temperatureThreshold** para corresponder. Todos os módulos possuem seu próprio módulo gêmeo, o que permite configurar o código em execução dentro de um módulo diretamente da nuvem.

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
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

10. Substitua o método **PipeMessage** pelo método **FilterMessages**. Esse método é chamado sempre que o módulo recebe uma mensagem do hub do IoT Edge. Ele filtra as mensagens que reportam temperaturas abaixo do limite de temperatura definido através do módulo gêmeo. Ele também adiciona a propriedade **MessageType** à mensagem com o valor definido para **Alerta**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
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
                await moduleClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

11. Salve o arquivo.

## <a name="build-your-iot-edge-solution"></a>Criar solução IoT Edge

Na seção anterior, você criou uma solução IoT Edge e adicionou um código a **CSharpModule** para filtrar mensagens em que a temperatura relatada da máquina estiver abaixo do limite aceitável. Agora você precisa compilar a solução como uma imagem de contêiner e enviá-la por push para seu registro de contêiner. 

1. Entre no Docker inserindo o comando a seguir no terminal integrado do Visual Studio Code. Em seguida, envie sua imagem de módulo por push para o registro de contêiner do Azure.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Use o nome de usuário, a senha e o servidor de logon que você copiou de seu registro de contêiner do Azure na primeira seção. Você também pode recuperar esses valores da seção **Chaves de acesso** do registro no portal do Azure.

2. No explorador do VS Code, abra o arquivo deployment.template.json no workspace da solução IoT Edge. Esse arquivo manda o **$edgeAgent** implantar dois módulos: **tempSensor** e **CSharpModule**. O valor **CSharpModule.image** é definido como uma versão Linux amd64 da imagem. 

   Verifique se o modelo tem o nome do módulo correto, não o nome padrão **SampleModule** alterado quando você criou a solução de IoT Edge.

   Para saber mais sobre manifestos de implantação, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).

3. No arquivo deployment.template.json, a seção **registryCredentials** armazena suas credenciais de registro do Docker. Os pares de nome de usuário e senha reais são armazenados no arquivo .env que é ignorado pelo git.  

4. Adicione o módulo gêmeo **CSharpModule** no manifesto de implantação. Insira o seguinte conteúdo JSON na parte inferior da seção **modulesContent**, após o módulo gêmeo do **$edgeHub**: 
    ```json
        "CSharpModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. Salve o arquivo.

5. No explorador do VS Code, clique com o botão direito do mouse no arquivo deployment.template.json e selecione **Compilar e enviar por push solução IoT Edge**. 

Quando você solicitar ao Visual Studio Code para compilar sua solução, primeiro ele usará as informações no modelo de implantação e gerará um arquivo deployment.json em uma nova pasta chamada **config**. Em seguida, ele executará dois comandos no terminal integrado: `docker build` e `docker push`. Esses dois comandos compilam seu código, conteinerizam o CSharpModule.dll e enviam o código por push para o registro de contêiner especificado ao inicializar a solução. 

Você pode conferir o endereço de imagem de contêiner completo com marca no terminal integrado do VS Code. O endereço da imagem é criado a partir de informações que estão no arquivo module.json com o formato \<repositório\>:\<versão\>-\<plataforma\>. Para este tutorial,ele deve ser parecido com registryname.azurecr.io/csharpmodule:0.0.1-amd64.

## <a name="deploy-and-run-the-solution"></a>Implantar e executar a solução

O artigo de início rápido que você usou para configurar seu dispositivo do IoT Edge, você implantou um módulo usando o portal do Azure. Também é possível implantar módulos usando a extensão Kit de ferramentas do Azure IoT para Visual Studio Code. Você já tem um manifesto de implantação preparado para o seu cenário, o arquivo **deployment.json**. Agora, tudo o que você precisa fazer é selecionar um dispositivo para receber a implantação.

1. Na paleta de comandos do VS Code, execute **Hub IoT do Azure: selecionar Hub IoT**. 

2. Escolha a assinatura e o hub IoT que contêm o dispositivo do IoT Edge que você deseja configurar. 

3. No explorador do VS Code, expanda a seção **Dispositivos do Hub IoT do Azure**. 

4. Clique com o botão direito no nome do seu dispositivo IoT Edge e selecione **Criar Implantação para Dispositivo Único**. 

   ![Criar implantação para dispositivo único](./media/tutorial-csharp-module/create-deployment.png)

5. Selecione o arquivo **deployment.json** na pasta **config** e clique em **Selecionar Manifesto de Implantação do Edge**. Não use o arquivo deployment.template.json. 

6. Clique no botão Atualizar. Você deve ver o novo **CSharpModule** sendo executado com o módulo **TempSensor** em **$edgeAgent** e **$edgeHub**.  

## <a name="view-generated-data"></a>Exibir os dados gerados

Depois que você aplica o manifesto de implantação no seu dispositivo IoT Edge, o tempo de execução do IoT Edge no dispositivo coleta as novas informações de implantação e inicia a execução nele. Todos os módulos em execução no dispositivo que não estão incluídos no manifesto de implantação são interrompidos. Todos os módulos ausentes do dispositivo são iniciados. 

Você pode ver o status do seu dispositivo IoT Edge usando a seção **Dispositivos de Hub IoT do Azure** do explorer do Visual Studio Code. Expanda os detalhes do seu dispositivo para ver uma lista de módulos implantados e em execução. 

No próprio dispositivo IoT Edge você pode ver o status dos seus módulos de implantação usando o comando `iotedge list`. Você deverá ver quatro módulos: os dois módulos de tempo de execução do IoT Edge, tempSensor e o módulo personalizado que você criou neste tutorial. Pode levar alguns minutos para que todos os módulos iniciem, portanto, se você não vir todos inicialmente, execute novamente o comando. 

Para exibir as mensagens que estão sendo geradas por qualquer módulo, use o comando `iotedge logs <module name>`. 

Você pode exibir as mensagens que chegam ao Hub IoT usando o Visual Studio Code. 

1. Para monitorar os dados que chegam ao hub IoT, selecione as reticências (**...** ) e selecione **Iniciar Monitoramento de Mensagens D2C**.
2. Para monitorar a mensagem D2C para um dispositivo específico, clique com o botão direito do mouse no dispositivo na lista e selecione **Iniciar Monitoramento de Mensagens D2C**.
3. Para interromper o monitoramento de dados, execute o comando **Hub IoT do Azure: Parar o monitoramento de mensagens D2C** na paleta de comandos. 
4. Para exibir ou atualizar o módulos gêmeo, clique com o botão direito do mouse no módulo na lista e selecione **Editar módulo gêmeo**. Para atualizar o módulo gêmeo, salve o arquivo JSON gêmeo, clique com o botão direito do mouse na área do editor e selecione **Atualizar Módulo Gêmeo**.
5. Para exibir os logs do Docker, instale o [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) para VS Code. Você pode encontrar os módulos em execução localmente no Docker Explorer. No menu de contexto, selecione **Mostrar Logs** para exibi-los no terminal integrado.
 
## <a name="clean-up-resources"></a>Limpar recursos 

Se você pretende continuar no próximo artigo recomendado, pode manter os recursos e as configurações já criados e reutilizá-los. Você também pode continuar usando o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, você pode excluir as configurações locais e os recursos do Azure criados neste artigo para evitar encargos. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um módulo do IoT Edge com código para filtrar os dados brutos gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para criar seus próprios módulos, você poderá saber mais sobre como [desenvolver um módulo C# com o Azure IoT Edge para Visual Studio Code](how-to-develop-csharp-module.md). Você pode seguir para os próximos tutoriais a fim de descobrir outras formas pelas quais o Azure IoT Edge pode ajudá-lo a transformar dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Armazenar dados na borda com os bancos de dados do SQL Server](tutorial-store-data-sql-server.md)

<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
