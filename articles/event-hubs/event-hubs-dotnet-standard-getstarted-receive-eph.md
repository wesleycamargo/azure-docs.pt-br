---
title: Receber eventos dos Hubs de Eventos do Azure usando a biblioteca do .NET Standard | Microsoft Docs
description: Introdução à recepção de mensagens com o EventProcessorHost no .NET Standard
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 9adbd8b9e7934ebe454d14ac6e47fe96898c9184
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234384"
---
# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-standard"></a>Introdução à recepção de mensagens com o Host do Processador de Eventos no .NET Standard
Os Hubs de Eventos são um serviço que processa grandes quantidades de dados de eventos (telemetria) a partir de aplicativos e dispositivos conectados. Depois de coletar dados para Hubs de Eventos, você pode armazenar os dados usando um cluster de armazenamento ou transformá-los usando um provedor de análise em tempo real. Essa funcionalidade de coleta e processamento de eventos em grande escala é um componente fundamental de arquiteturas de aplicativos modernas, incluindo a IoT (Internet das Coisas). Para obter uma visão detalhada dos Hubs de Eventos, confira [Visão geral de Hubs de Eventos](event-hubs-about.md) e [Recursos de Hubs de Eventos](event-hubs-features.md).

Este tutorial mostra como gravar um aplicativo de console .NET Core que recebe mensagens de um Hub de Eventos usando o [Host do Processador de Eventos](event-hubs-event-processor-host.md). O [Host do Processador de Eventos](event-hubs-event-processor-host.md) é uma classe do .NET que simplifica o recebimento de eventos de hubs de eventos ao gerenciar pontos de verificação persistentes e recebimentos paralelos desses hubs de eventos. Ao usar o Host do Processador de Eventos, você pode dividir eventos através de vários receptores, mesmo quando hospedados em nós diferentes. Este exemplo mostra como usar o Host do Processador de Eventos para um único destinatário. O exemplo de [Processamento de eventos de escala horizontal][Processamento de eventos de escala horizontal com Hubs de Eventos] mostra como usar o Host do Processador de Eventos com vários destinatários.

> [!NOTE]
> Você pode baixar este início rápido como uma amostra com o [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver), substituir as cadeias de caracteres `EventHubConnectionString` e `EventHubName`, `StorageAccountName`, `StorageAccountKey` e `StorageContainerName` com seus valores de hub de eventos, e executá-los. Como alternativa, é possível seguir as etapas deste tutorial para criar sua própria solução.

## <a name="prerequisites"></a>Pré-requisitos
* [Microsoft Visual Studio 2015 ou 2017](https://www.visualstudio.com). Os exemplos neste tutorial usam o Visual Studio 2017, mas também há suporte para o Visual Studio 2015.
* [Ferramentas do .NET Core do Visual Studio 2015 ou 2017](https://www.microsoft.com/net/core).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Criar um namespace de Hubs de Eventos e um hub de eventos
A primeira etapa é usar o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo Hubs de eventos e obter as credenciais de gerenciamento das quais que seu aplicativo precisa para se comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento [neste artigo](event-hubs-create.md) e então continue com as etapas a seguir neste tutorial.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

## <a name="create-a-console-application"></a>Criar um aplicativo de console

Inicie o Visual Studio. No menu **Arquivo**, clique em **Novo** e em **Projeto**. Crie um aplicativo de console do .NET Core.

![Novo Projeto][2]

## <a name="add-the-event-hubs-nuget-package"></a>Adicione o pacote NuGet de Hubs de Eventos

Adicione os pacotes NuGet da biblioteca .NET Standard [**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) e [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) a seu projeto seguindo estas etapas: 

1. Clique com o botão direito do mouse no projeto recém-criado e selecione **Gerenciar Pacotes NuGet**.
2. Clique na guia **Procurar**, pesquise por **Microsoft.Azure.EventHubs** e selecione o pacote **Microsoft.Azure.EventHubs**. Clique em **Instalar** para concluir a instalação e feche essa caixa de diálogo.
3. Repita as etapas 1 e 2 e instalar o pacote **Microsoft.Azure.EventHubs.Processor**.

## <a name="implement-the-ieventprocessor-interface"></a>Implementar a interface IEventProcessor

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto, clique em **Adicionar** e em **Classe**. Nomeie a nova classe **SimpleEventProcessor**.

2. Abra o arquivo SimpleEventProcessor.cs e adicione as seguintes instruções `using` à parte superior do arquivo.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. Implementar o `IEventProcessor` interface. Substitua todo o conteúdo da classe `SimpleEventProcessor` pelo código a seguir:

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

## <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Atualizar o método Main para usar o SimpleEventProcessor

1. Adicione as instruções `using` a seguir à parte superior do arquivo Program.cs.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. Adicione constantes à classe `Program` da cadeia de conexão do hub de eventos, do nome do hub de eventos, do nome do contêiner da conta de armazenamento, do nome da conta de armazenamento e da chave da conta de armazenamento. Adicione o código a seguir, substituindo os espaços reservados pelos valores correspondentes:

    ```csharp
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. Adicione um novo método chamado `MainAsync` à classe `Program`, conforme demonstrado a seguir:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EventHubName,
            PartitionReceiver.DefaultConsumerGroupName,
            EventHubConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. Adicione a linha de código a seguir ao método `Main`:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    O arquivo Program.cs deve ficar assim:

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EventHubName,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EventHubConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. Execute o programa e certifique-se de que não existem erros.

Parabéns! Agora você recebeu mensagens de um hub de eventos usando o Host do Processador de Eventos.

> [!NOTE]
> Este tutorial usa uma única instância do [EventProcessorHost](event-hubs-event-processor-host.md). Para aumentar o rendimento, recomendamos que você execute várias instâncias de [EventProcessorHost](event-hubs-event-processor-host.md), conforme mostrado na amostra de [Processamento de evento com escala](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3). Nesses casos, as múltiplas instâncias são coordenadas automaticamente umas com as outras para balancear a carga de eventos recebidos. 

## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você criou um aplicativo .NET Standard que recebeu mensagens de um hub de eventos. Para saber como enviar eventos para um hub de eventos usando o .NET Standard, consulte [Enviar eventos de hub de eventos – .NET Standard](event-hubs-dotnet-standard-getstarted-send.md).

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png
