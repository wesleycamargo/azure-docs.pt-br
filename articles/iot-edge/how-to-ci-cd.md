---
title: Integração contínua e implantação contínua do Azure IoT Edge | Microsoft Docs
description: Visão geral sobre a integração contínua e a implantação contínua do Azure IoT Edge
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5099ca70503ba2ed4ae8f4969a9199816c4986fb
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302564"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Integração contínua e implantação contínua no Azure IoT Edge

Este artigo descreve como é possível usar os recursos de integração contínua e de implantação contínua do Azure DevOps Services e do TFS (Microsoft Team Foundation Server) para compilar, testar e implantar aplicativos de forma rápida e eficiente no Azure IoT Edge. 

Neste artigo, você aprenderá a:
* Criar e fazer check-in de um exemplo de solução do Azure IoT Edge contendo testes de unidade.
* Instale a extensão do Azure IoT Edge para o Azure DevOps.
* Configure a CI (integração contínua) para criar a solução e executar os testes de unidade.
* Configure a CD (implantação contínua) para implantar a solução e exibir as respostas.

O tempo para concluir as etapas deste artigo é de 30 minutos.

![CI e CD](./media/how-to-ci-cd/cd.png)

## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Criar um exemplo de solução do Azure IoT Edge usando o Visual Studio Code

Nesta seção, você criará uma solução de exemplo do IoT Edge contendo testes de unidade que podem ser executados como parte do processo de build. Antes de seguir as diretrizes nesta seção, conclua as etapas em [Desenvolver uma solução do IoT Edge com vários módulos no Visual Studio Code](tutorial-multiple-modules-in-vscode.md).

1. Na paleta de comandos do VS Code, digite e execute o comando **Edge: Nova solução IoT Edge**. Em seguida, selecione a pasta do espaço de trabalho, forneça um nome para a solução (o nome padrão é **EdgeSolution**) e crie um módulo C# (**FilterModule**) como o primeiro módulo de usuário nesta solução. Você também precisa especificar o repositório de imagens do Docker para o seu primeiro módulo. O repositório de imagens padrão baseia-se em um registro de Docker local (`localhost:5000/filtermodule`). Você precisa alterá-lo para o Registro de Contêiner do Azure (`<your container registry address>/filtermodule`) ou o Docker Hub para obter mais integração contínua.

    ![Configurar o ACR](./media/how-to-ci-cd/acr.png)

2. A janela de código VS carregará seu espaço de trabalho da solução do Azure IoT Edge. Opcionalmente, você pode digitar e executar **Edge: adicionar módulo do IoT Edge** para adicionar mais módulos. Há uma pasta `modules`, uma pasta `.vscode` e um arquivo de modelo de manifesto de implantação na pasta raiz. Todos os códigos de usuário do módulo serão subpastas na pasta `modules`. O `deployment.template.json` é o modelo de manifesto de implantação. Alguns dos parâmetros nesse arquivo serão analisados a partir de `module.json`, que existe em cada pasta de módulo.

3. Agora, sua solução do IoT Edge está pronta. O módulo C# padrão atua como um módulo de mensagem do pipe. No `deployment.template.json`, você verá que esta solução contém dois módulos. A mensagem será gerada pelo módulo `tempSensor`, será enviada diretamente pelo pipe por meio de `FilterModule` e, em seguida, será enviada para o Hub IoT. Substitua todo o arquivo **Program.cs** pelo conteúdo abaixo. Para obter mais informações sobre este trecho de código, veja [Criar um projeto de módulo C# do IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module#create-an-iot-edge-module-project).

    ```csharp
    namespace FilterModule
    {
        using System;
        using System.IO;
        using System.Runtime.InteropServices;
        using System.Runtime.Loader;
        using System.Security.Cryptography.X509Certificates;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using System.Collections.Generic;     // for KeyValuePair<>
        using Microsoft.Azure.Devices.Shared; // for TwinCollection
        using Newtonsoft.Json;                // for JsonConvert

        public class MessageBody
        {
            public Machine machine { get; set; }
            public Ambient ambient { get; set; }
            public string timeCreated { get; set; }
        }
        public class Machine
        {
            public double temperature { get; set; }
            public double pressure { get; set; }
        }
        public class Ambient
        {
            public double temperature { get; set; }
            public int humidity { get; set; }
        }

        public class Program
        {
            static int counter;
            static int temperatureThreshold { get; set; } = 25;

            static void Main(string[] args)
            {
                Init().Wait();

                // Wait until the app unloads or is cancelled
                var cts = new CancellationTokenSource();
                AssemblyLoadContext.Default.Unloading += (ctx) => cts.Cancel();
                Console.CancelKeyPress += (sender, cpe) => cts.Cancel();
                WhenCancelled(cts.Token).Wait();
            }

            /// <summary>
            /// Handles cleanup operations when app is cancelled or unloads
            /// </summary>
            public static Task WhenCancelled(CancellationToken cancellationToken)
            {
                var tcs = new TaskCompletionSource<bool>();
                cancellationToken.Register(s => ((TaskCompletionSource<bool>)s).SetResult(true), tcs);
                return tcs.Task;
            }

            /// <summary>
            /// Initializes the ModuleClient and sets up the callback to receive
            /// messages containing temperature information
            /// </summary>
            static async Task Init()
            {
                MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
                ITransportSettings[] settings = { mqttSetting };

                // Open a connection to the Edge runtime
                ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
                await ioTHubModuleClient.OpenAsync();
                Console.WriteLine("IoT Hub module client initialized.");

                // Register callback to be called when a message is received by the module
                await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessage, ioTHubModuleClient);
            }

            /// <summary>
            /// This method is called whenever the module is sent a message from the EdgeHub. 
            /// It just pipe the messages without any change.
            /// It prints all the incoming messages.
            /// </summary>
            static async Task<MessageResponse> FilterMessage(Message message, object userContext)
            {
                int counterValue = Interlocked.Increment(ref counter);

                var moduleClient = userContext as ModuleClient;
                if (moduleClient == null)
                {
                    throw new InvalidOperationException("UserContext doesn't contain " + "expected values");
                }

                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                var filteredMessage = filter(message);

                if (filteredMessage != null && !string.IsNullOrEmpty(messageString))
                {
                    var pipeMessage = new Message(messageBytes);
                    foreach (var prop in message.Properties)
                    {
                        pipeMessage.Properties.Add(prop.Key, prop.Value);
                    }
                    await moduleClient.SendEventAsync("output1", pipeMessage);
                    Console.WriteLine("Received message sent");
                }
                return MessageResponse.Completed;
            }

            public static Message filter(Message message)
            {
                var counterValue = Interlocked.Increment(ref counter);

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
                    return filteredMessage;
                }
                return null;
            }
        }
    }
    ```

4. Crie um projeto de teste de unidade do .Net Core. No Explorador de Arquivos do VS Code, crie uma nova pasta **tests\FilterModuleTest** no espaço de trabalho. Em seguida, no terminal integrado do VS Code (**Ctrl + '**), execute os seguintes comandos para criar um projeto de teste xunit e adicionar a referência ao projeto **FilterModule**.

    ```cmd
    cd tests\FilterModuleTest
    dotnet new xunit
    dotnet add reference ../../modules/FilterModule/FilterModule.csproj
    ```

    ![Estrutura de pastas](./media/how-to-ci-cd/add-test-project.png)

5. Na pasta **FilterModuleTest**, atualize o nome do arquivo de **UnitTest1.cs** para **FilterModuleTest.cs**. Selecione e abra **FilterModuleTest.cs**, substitua todo o código pelo trecho de código abaixo, que contém os testes de unidade em relação ao projeto FilterModule.

    ```csharp
    using Xunit;
    using FilterModule;
    using Newtonsoft.Json;
    using System;
    using System.IO;
    using System.Runtime.InteropServices;
    using System.Runtime.Loader;
    using System.Security.Cryptography.X509Certificates;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Client.Transport.Mqtt;

    namespace FilterModuleTest
    {
        public class FilterModuleTest
        {
            [Fact]
            public void filterLessThanThresholdTest()
            {
                var source = createMessage(25 - 1);
                var result = Program.filter(source);
                Assert.True(result == null);
            }

            [Fact]
            public void filterMoreThanThresholdAlertPropertyTest()
            {
                var source = createMessage(25 + 1);
                var result = Program.filter(source);
                Assert.True(result.Properties["MessageType"] == "Alert");
            }

            [Fact]
            public void filterMoreThanThresholdCopyPropertyTest()
            {
                var source = createMessage(25 + 1);
                source.Properties.Add("customTestKey", "customTestValue");
                var result = Program.filter(source);
                Assert.True(result.Properties["customTestKey"] == "customTestValue");
            }

            private Message createMessage(int temperature)
            {
                var messageBody = createMessageBody(temperature);
                var messageString = JsonConvert.SerializeObject(messageBody);
                var messageBytes = Encoding.UTF8.GetBytes(messageString);
                return new Message(messageBytes);
            }

            private MessageBody createMessageBody(int temperature)
            {
                var messageBody = new MessageBody
                {
                    machine = new Machine
                    {
                        temperature = temperature,
                        pressure = 0
                    },
                    ambient = new Ambient
                    {
                        temperature = 0,
                        humidity = 0
                    },
                    timeCreated = string.Format("{0:O}", DateTime.Now)
                };

                return messageBody;
            }
        }
    }
    ```

6. No terminal integrado, você pode inserir os comandos a seguir para executar os testes de unidade localmente. 
    ```cmd
    dotnet test
    ```

    ![Teste de unidade](./media/how-to-ci-cd/unit-test.png)

7. Salve esses projetos e faça o check-in deles no repositório do Azure DevOps ou do TFS.
    

> [!NOTE]
> Para obter mais informações de como usar o Azure Repos, confira [Compartilhar seu código com o GIT do Visual Studio e do Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts).


## <a name="configure-continuous-integration"></a>Configurar a integração contínua
Nesta seção, você criará um pipeline de build configurado para ser executado automaticamente quando você fizer check-in das alterações na solução do IoT Edge de exemplo e executar automaticamente os testes de unidade que contiver.

1. Entre em sua organização do Azure DevOps (**https://**_sua-conta_**.visualstudio.com**) e abra o projeto no qual você fez check-in do aplicativo de exemplo.

    ![Fazer check-in do código](./media/how-to-ci-cd/init-project.png)

1. Visite o [Azure IoT Edge para Azure DevOps](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy) no Azure DevOps Marketplace. Clique em **Obter gratuitamente** e siga o assistente para instalar esta extensão em sua organização do Azure DevOps ou baixe para o TFS.

    ![Instalar a extensão](./media/how-to-ci-cd/install-extension.png)

1. No Azure DevOps, abra o hub **Build &amp; Versão** e, na guia **Builds**, escolha **+ Novo pipeline**. Ou, se você já tiver pipelines de build, escolha o botão **+ Novo**. 

    ![Novo build](./media/how-to-ci-cd/add-new-build.png)

1. Se solicitado, selecione o tipo de origem **GIT do Azure DevOps** e, em seguida, selecione o projeto, o repositório e o branch no qual o código está localizado. Escolha **Continuar**.

    ![Selecionar git do Azure DevOps](./media/how-to-ci-cd/select-vsts-git.png)

1. Na janela **Selecionar um modelo**, escolha **começar com um Processo vazio**.

    ![Iniciar vazio](./media/how-to-ci-cd/start-with-empty.png)

1. Clique em **+** no lado direito da **Fase 1** para adicionar uma tarefa à fase. Em seguida, pesquise e selecione **.Net Core** e clique em **Adicionar** para adicionar essa tarefa à fase.

    ![Teste do dotnet](./media/how-to-ci-cd/add-dot-net-core.png)

1. Atualize o **Nome de exibição** para **teste do dotnet** e, na lista suspensa **Comando**, selecione **testar**. Adicione o caminho abaixo ao **Caminho para projetos**.

    ```
    tests/FilterModuleTest/*.csproj
    ```

    ![Configurar o teste do dotnet](./media/how-to-ci-cd/dotnet-test.png)

1. Clique em **+** no lado direito da **Fase 1** para adicionar uma tarefa à fase. Em seguida, pesquise e selecione **Azure IoT Edge** e clique no botão **Adicionar** **duas vezes** para adicionar essas tarefas à fase.

    ![IoT Edge](./media/how-to-ci-cd/add-azure-iot-edge.png)

1. Na primeira tarefa do Azure IoT Edge, atualize o **Nome de exibição** para **Build e push do módulo** e na lista suspensa **Ação**, selecione **Compilar e enviar por push**. Na caixa de texto **Arquivo Module.json**, adicione o caminho abaixo. Em seguida, escolha **Tipo de Registro de Contêiner**. Configure e selecione o mesmo registro em seu código. Essa tarefa compilará e enviará por push todos os módulos da solução e publicará o registro de contêiner especificado. Se efetuar push de módulos para diferentes registros, você poderá ter várias tarefas de **Compilar módulo e efetuar push**.

    ```
    **/module.json
    ```

    ![Build e push do módulo](./media/how-to-ci-cd/module-build-push.png)

1. Na segunda tarefa do Azure IoT Edge, atualize o **Nome de exibição** para **Implantar no dispositivo do IoT Edge** e na lista suspensa **Ação**, selecione **Implantar no dispositivo do IoT Edge**. Selecione sua assinatura do Azure e insira seu nome do Hub IoT. Você pode especificar uma ID de implantação do IoT Edge e a prioridade de implantação. Você também pode escolher implantar em um único dispositivo ou em vários. Se você estiver implantando em vários dispositivos, será necessário especificar a condição de destino do dispositivo. Por exemplo, se você quiser usar Marcas de dispositivo como a condição, será necessário atualizar as Marcas de dispositivos correspondentes antes da implantação. 

    ![Implantar no Edge](./media/how-to-ci-cd/deploy-to-edge.png)

1. Clique no **Processo** e verifique se a **Fila de agentes** é uma **Versão prévia do Linux hospedada**.

    ![Configurar](./media/how-to-ci-cd/configure-env.png)

1. Abra a guia **Gatilhos** e habilite o gatilho **Integração contínua**. Verifique se o branch que contém seu código está incluído.

    ![Gatilho](./media/how-to-ci-cd/configure-trigger.png)

1. Salve o novo pipeline de build e coloque um novo build na fila. Clique no botão **Salvar e colocar na fila**.

1. Na barra de mensagem que aparece, escolha o link para o build. Ou acesse o pipeline de build para ver o trabalho de build mais recente na fila.

    ![Compilação](./media/how-to-ci-cd/build-def.png)

1. Após a conclusão do build, você poderá ver o resumo de cada tarefa e os resultados no arquivo de log em tempo real. 
    
    ![Concluído](./media/how-to-ci-cd/complete.png)

1. Você pode voltar ao VS Code e verificar o Device Explorer do Hub IoT. A execução do dispositivo do Edge com o módulo deverá ser iniciada (verifique se você adicionou as credenciais do registro no tempo de execução do Edge).

    ![Edge em execução](./media/how-to-ci-cd/edge-running.png)

## <a name="continuous-deployment-to-iot-edge-devices"></a>Implantação contínua para dispositivos do IoT Edge

Para habilitar a implantação contínua, basicamente você precisa configurar os trabalhos de CI com dispositivos adequados do IoT Edge, habilitando os **Gatilhos** para os branches do projeto. Em uma prática de DevOps clássica, um projeto contém dois branchs principais. O branch mestre deve ser a versão estável do código e o branch de desenvolvimento deve conter as últimas alterações no código. Cada desenvolvedor da equipe deve criar um fork do branch de desenvolvimento para seu próprio branch do recurso ao iniciar a atualização do código, o que significa que todas as confirmações acontecem nos branchs do recurso fora do branch de desenvolvimento. E cada confirmação enviada por push deve ser testada pelo sistema de CI. Depois que o código for completamente testado localmente, o branch do recurso deverá ser mesclado ao branch de desenvolvimento por meio de uma solicitação de pull. Quando o código no branch do desenvolvedor for testado pelo sistema de CI, ele poderá ser mesclado com o branch mestre por meio de uma solicitação de pull.

Portanto, ao implantar em dispositivos do IoT Edge, há três ambientes principais.
- No branch de recurso, você pode usar o dispositivo do IoT Edge simulado no computador de desenvolvimento ou implantar em um dispositivo físico do IoT Edge.
- Nos branch do desenvolvedor, você deve implantar em um dispositivo físico do IoT Edge.
- No branch mestre, os dispositivos do IoT Edge de destino devem ser os dispositivos de produção.

## <a name="next-steps"></a>Próximas etapas

* Entenda a implantação do IoT Edge em [Noções básicas sobre implantações do IoT Edge para dispositivos únicos ou em escala](module-deployment-monitoring.md)
* Siga as etapas para criar, atualizar ou excluir uma implantação em [Implantar e monitorar os módulos do IoT Edge em larga escala](how-to-deploy-monitor.md).
