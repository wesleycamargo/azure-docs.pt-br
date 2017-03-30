---
title: Receber eventos dos Hubs de Eventos do Azure usando o .NET Standard | Microsoft Docs
description: "Introdução ao recebimento de mensagens com o EventProcessorHost no .NET Standard"
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 65ed5164b8d010453ed34e8b8cdf68915e136007
ms.lasthandoff: 03/22/2017

---

# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-standard"></a>Introdução à recepção de mensagens com o Host do Processador de Eventos no .NET Standard

> [!NOTE]
> Este exemplo está disponível em [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleEphReceiver).

Este tutorial mostra como escrever um aplicativo de console do .NET Core que recebe mensagens de um Hub de Eventos usando o **EventProcessorHost**. É possível executar a solução do [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleEphReceiver) no estado em que se encontra, substituindo as cadeias de caracteres pelos valores do Hub de Eventos e da conta de armazenamento. Se preferir, é possível seguir as etapas deste tutorial para criar sua própria solução.

## <a name="prerequisites"></a>Pré-requisitos

* [Microsoft Visual Studio 2015 ou 2017](http://www.visualstudio.com). Os exemplos neste tutorial usam o Visual Studio 2015, mas também há suporte para o Visual Studio 2017.
* [Ferramentas do .NET Core do Visual Studio 2015 ou 2017](http://www.microsoft.com/net/core).
* Uma assinatura do Azure.
* Um namespace dos Hubs de Eventos do Azure.
* Uma conta de armazenamento do Azure.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Criar um namespace dp Hubs de Eventos e um Hub de Eventos  

A primeira etapa é usar o [portal do Azure](https://portal.azure.com) para criar um namespace para o tipo Hubs de Eventos e obter as credenciais de gerenciamento das quais o aplicativo precisa para se comunicar com o Hub de Eventos. Para criar um namespace e um Hub de Eventos, siga o procedimento descrito [neste artigo](event-hubs-create.md) e continue com as próximas etapas.  

## <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure  

1. Entre no [Portal do Azure](https://portal.azure.com).  
2. No painel de navegação esquerdo do portal, clique em **Novo**, em **Armazenamento** e em **Conta de Armazenamento**.  
3. Preencha os campos na folha da conta de armazenamento e clique em **Criar**.

    ![Criar Conta de Armazenamento][1]

4. Depois de ver a mensagem **Implantações Bem-Sucedidas**, clique no nome da nova conta de armazenamento. Na folha **Dados Básicos**, clique em **Blobs**. Quando a folha **Serviço Blob** abrir, clique em **+ Contêiner** na parte superior. Forneça um nome para o contêiner e feche a folha **Serviço Blob**.  
5. Clique em **Chaves de acesso** na folha esquerda e copie o nome do contêiner de armazenamento, a conta de armazenamento e o valor de **key1**. Salve esses valores no Bloco de notas ou em outro local temporário.  

## <a name="create-a-console-application"></a>Criar um aplicativo de console

1. Inicie o Visual Studio. No menu **Arquivo**, clique em **Novo** e em **Projeto**. Crie um aplicativo de console do .NET Core.

    ![Novo Projeto][2]

2. No Gerenciador de Soluções, clique duas vezes no arquivo **project.json** para abri-lo no editor do Visual Studio.
3. Adicione a cadeia de caracteres `"portable-net45+win8"` à declaração `"imports"`, na seção `"frameworks"`. Essa seção agora deve aparecer conforme demonstrado a seguir. Essa cadeia de caracteres é necessária devido à dependência do armazenamento do Azure OData:

    ```json
    "frameworks": {
      "netcoreapp1.0": {
        "imports": [
          "dnxcore50",
          "portable-net45+win8"
        ]
      }
    }
    ```

4. No menu **Arquivo**, clique em **Salvar Tudo**.

Observe que este tutorial mostra como escrever um aplicativo .NET Core. Se desejar direcionar todo o .NET Framework, adicione a seguinte linha de código ao arquivo project.json, na seção `"frameworks"`:

```json
"net451": {
},
```

## <a name="add-the-event-hubs-nuget-package"></a>Adicione o pacote NuGet de Hubs de Evento

Adicione os seguintes pacotes NuGet ao projeto:
* [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)
* [`Microsoft.Azure.EventHubs.Processor`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/)

## <a name="implement-the-ieventprocessor-interface"></a>Implementar a interface IEventProcessor

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto, clique em **Adicionar** e em **Classe**. Nomeie a nova classe **SimpleEventProcessor**.

2. Abra o arquivo SimpleEventProcessor.cs e adicione as seguintes instruções `using` à parte superior do arquivo.

    ```csharp
    using System.Text;
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
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

## <a name="write-a-main-console-method-that-uses-the-simpleeventprocessor-class-to-receive-messages"></a>Escrever um método de console principal que usa a classe SimpleEventProcessor para receber mensagens de um Hub de Eventos

1. Adicione as instruções `using` a seguir à parte superior do arquivo Program.cs.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    ```

2. Adicione constantes à classe `Program` da cadeia de conexão dos Hubs de Eventos, do nome do Hub de Eventos, do nome do contêiner da conta de armazenamento, do nome da conta de armazenamento e da chave da conta de armazenamento. Adicione o código a seguir, substituindo os espaços reservados pelos valores correspondentes.

    ```csharp
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
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
            EhEntityPath,
            PartitionReceiver.DefaultConsumerGroupName,
            EhConnectionString,
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
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
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
                    EhEntityPath,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EhConnectionString,
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

Parabéns! Agora você recebeu mensagens de um Hub de Eventos usando o Host do Processador de Eventos.

## <a name="next-steps"></a>Próximas etapas
Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Visão Geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcore.png

