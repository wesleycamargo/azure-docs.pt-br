---
title: Receber eventos dos Hubs de Eventos do Azure usando o .NET Standard | Microsoft Docs
description: "Introdução à recepção de mensagens com o EventProcessorHost no .NET Standard"
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
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 7506430f4ec5522165274f05a2fd5b77e3d6252d
ms.lasthandoff: 03/06/2017

---

# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-standard"></a>Introdução à recepção de mensagens com o Host do Processador de Eventos no .NET Standard

> [!NOTE]
> Este exemplo está disponível em [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleEphReceiver).

Este tutorial mostra como escrever um aplicativo de console .NET Core que recebe mensagens de um Hub de Eventos usando o **EventProcessorHost**. Você pode executar a solução [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleEphReceiver) como está, substituindo as cadeias de caracteres pelos valores do Hub de Eventos e da conta de armazenamento ou então seguir as etapas neste tutorial para criar as suas próprias. 

## <a name="prerequisites"></a>Pré-requisitos

1. [Microsoft Visual Studio 2015 ou 2017](http://www.visualstudio.com). Os exemplos neste tutorial usam o Visual Studio 2015, mas também há suporte para o Visual Studio 2017.
2. [Ferramentas do .NET Core do Visual Studio 2015 ou 2017](http://www.microsoft.com/net/core).
3. Uma assinatura do Azure.
4. Um namespace dos Hubs de Eventos.
5. Uma conta de armazenamento do Azure.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Criar um namespace de Hubs de Eventos e um Hub de Eventos  
  
A primeira etapa é usar o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo Hubs de eventos e obter as credenciais de gerenciamento das quais que seu aplicativo precisa para se comunicar com o Hub de Eventos. Para criar um namespace e um Hub de eventos, execute o procedimento [neste artigo](event-hubs-create.md) e então continue com as etapas a seguir.  

## <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure  

1. Faça logon no [portal do Azure](https://portal.azure.com).  
2. No painel de navegação esquerdo do portal, clique em **Novo**, em **Armazenamento** e em **Conta de Armazenamento**.  
3. Preencha os campos na folha da conta de armazenamento e clique em **Criar**.
  
    ![][1]

4. Depois de ver a mensagem **Implantações Bem-sucedidas**, clique no nome da nova conta de armazenamento e, na folha **Fundamentos**, clique em **Blobs**. Quando a folha **Serviço Blob** abrir, clique em **+ Contêiner** na parte superior. Nomeie o contêiner e feche a folha **Serviço Blob**.  
5. Clique em **Chaves de acesso** na folha esquerda e copie o nome do contêiner de armazenamento, a conta de armazenamento e o valor de **key1**. Salve esses valores no Bloco de notas ou em outro local temporário.  
    
## <a name="create-a-console-application"></a>Criar um aplicativo de console

1. Inicie o Visual Studio. No menu Arquivo, clique em **Novo** e em **Projeto**. Crie um aplicativo de console do .NET Core.

    ![][2]

2. No Gerenciador de Soluções, clique duas vezes no arquivo **project.json** para abri-lo no editor do Visual Studio.
3. Adicione a cadeia de caracteres `"portable-net45+win8"` à declaração `"imports"` dentro da seção `"frameworks`". Essa seção agora deve aparecer conforme demonstrado a seguir. Essa cadeia de caracteres é necessária devido à dependência do armazenamento do Azure OData:

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

4. No menu Arquivo, clique em **Salvar Tudo**.

Observe que este tutorial mostra como escrever um aplicativo .NET Core, mas se desejar direcionar o .NET Framework completo, adicione a seguinte linha de código ao arquivo project.json, na seção `"frameworks"`:

```json
"net451": {
},
``` 

## <a name="add-the-event-hubs-nuget-package"></a>Adicione o pacote NuGet de Hubs de Evento

* Adicione os seguintes pacotes NuGet ao projeto:
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

2. Adicione constantes à classe `Program` para a cadeia de conexão de Hubs de Eventos, nome do Hub de Eventos, nome do contêiner de armazenamento, nome da conta de armazenamento e chave de conta de armazenamento. Adicione o código a seguir, substituindo os espaços reservados pelos valores correspondentes.

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
  
Parabéns! Você recebeu agora mensagens de um Hub de Eventos usando o Host do Processador de Eventos.

## <a name="next-steps"></a>Próximas etapas
Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Visão Geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcore.png
