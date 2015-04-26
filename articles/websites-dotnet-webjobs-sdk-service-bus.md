<properties 
	pageTitle="Como usar o barramento de serviço do Azure com o SDK WebJobs" 
	description="Saiba como usar tópicos e filas do barramento de serviço do Azure com o SDK WebJobs." 
	services="web-sites, service-bus" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/15/2014" 
	ms.author="tdykstra"/>

# Como usar o barramento de serviço do Azure com o SDK WebJobs

Este guia fornece exemplos de código C# que mostram como disparar um processo quando um blob do Azure é criado ou atualizado. Os exemplos de código usam [SDK WebJobs](websites-dotnet-webjobs-sdk.md) versão 1. x.

O guia pressupõe que você saiba [como criar um projeto WebJob no Visual Studio com conexão de cadeia de caracteres que apontam para sua conta de armazenamento](websites-dotnet-webjobs-sdk-get-started.md).

Os trechos de código mostram apenas funções, não o código que cria o objeto `JobHost` como neste exemplo:

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## Sumário

-   [Pré-requisitos](#prerequisites)
-   [Como disparar uma função quando uma mensagem da fila é recebida ](#trigger)
-   [Como criar as mensagens em fila](#create)
-   [Como trabalhar com tópicos do barramento de serviço](#topics)
-   [Tópicos relacionados abordados no artigo de filas de armazenamento](#queues)
-   [Próximas etapas](#nextsteps)

## <a id="prerequisites"></a>Pré-requisitos

Para trabalhar com o barramento de serviço, você precisa instalar o pacote do NuGet [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) além dos pacotes SDK WebJobs. 

Você também deve definir a cadeia de conexão AzureWebJobsServiceBus, além de cadeias de conexão de armazenamento.

## <a id="trigger"></a>Como disparar uma função quando for recebida uma mensagem de fila do barramento de serviço

Para gravar uma função que o SDK WebJobs chama quando é recebida uma mensagem da fila, use o atributo `ServiceBusTrigger`. O construtor de atributo tem um parâmetro que especifica o nome da fila para sondagem.

### Como funciona o ServicebusTrigger

O SDK recebe uma mensagem em `PeekLock` modo e chamadas `Complete` na mensagem se a função é concluído com êxito, ou chamadas `Abandon` se a função falhar. Se a função for executada além do tempo limite `PeekLock`, o bloqueio é renovado automaticamente.

O barramento de serviço faz seu próprio tratamento de fila suspeita, para que nenhum deles seja controlado, nem configurável no SDK WebJobs. 

### Mensagens da fila da cadeia de caracteres

O exemplo de código a seguir lê uma mensagem da fila que contém uma cadeia de caracteres e grava a cadeia de caracteres para o painel SDK WebJobs.

		public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
		    TextWriter logger)
		{
		    logger.WriteLine(message);
		}

**Observação:** se você estiver criando uma mensagem da fila em um aplicativo que não usa o SDK WebJobs, certifique-se de definir [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) para "text/plain".

### Mensagem da fila POCO

O SDK desserializará automaticamente uma mensagem da fila que contém o JSON para um POCO do tipo [(objeto Plain Old CLR](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)). O exemplo de código a seguir lê uma mensagem da fila que contém um objeto `BlobInformation` que tem uma propriedade `BlobName`:

		public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
		    TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

Para obter exemplos de código mostrando como usar propriedades da POCO para trabalhar com blobs e tabelas na mesma função, consulte a versão das filas de armazenamento [deste artigo](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#pocoblobs).

### Os tipos ServiceBusTrigger funcionam com

Além dos tipos `string` e POCO, você pode usar o atributo `ServiceBusTrigger` com uma matriz de bytes ou um objeto `BrokeredMessage`.

## <a id="create"></a>Como criar a fila de mensagens do barramento de serviço

Para escrever uma função que cria uma nova mensagem de fila, use o atributo `ServiceBus` e passe o nome da fila para o construtor de atributo. 


### Crie uma mensagem da fila única em uma função não assíncrona

O exemplo de código a seguir usa um parâmetro de saída para criar uma nova mensagem na fila denominada "outputqueue" com o mesmo conteúdo que a mensagem recebida na fila denominada "inputqueue".

		public static void CreateQueueMessage(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] out string outputQueueMessage)
		{
		    outputQueueMessage = queueMessage;
		}

O parâmetro de saída para a criação de uma mensagem da fila única pode ser qualquer um dos seguintes tipos:

* `string`
* `byte[]`
* `BrokeredMessage`
* Um tipo POCO serializável que você define. Automaticamente serializado como JSON.

Para parâmetros de tipo POCO, uma mensagem da fila sempre é criada quando a função termina; se o parâmetro for null, o SDK cria uma mensagem da fila que devolverá null quando a mensagem é recebida e desserializada. Para outros tipos, se o parâmetro for null, nenhuma mensagem da fila é criada.

### Crie várias mensagens de fila ou em funções assíncronas

Para criar várias mensagens, use o atributo `ServiceBus` com `ICollector<T>` ou `IAsyncCollector<T>`, conforme mostrado no exemplo de código a seguir:

		public static void CreateQueueMessages(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Cada mensagem da fila é criada imediatamente quando o método `Add` é chamado.

## <a id="topics"></a>Como trabalhar com tópicos do barramento de serviço

Para gravar uma função que o SDK chama quando uma mensagem é recebida em um tópico do barramento de serviço, use o atributo `ServiceBusTrigger` com o construtor que usa o nome do tópico e de assinatura, conforme mostrado no exemplo de código a seguir:

		public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
		    TextWriter logger)
		{
		    logger.WriteLine("Topic message: " + message);
		}

Para criar uma mensagem sobre um tópico, use o atributo `ServiceBus` com um nome de tópico da mesma forma que você pode usá-lo com um nome de fila.

## <a id="queues"></a>Tópicos relacionados abordados no artigo de instruções de filas de armazenamento

Para obter informações sobre cenários de SDK WebJobs não específicos para o barramento de serviço, consulte [Como usar armazenamento de fila do Azure com o SDK WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Tópicos abordados nesse artigo incluem o seguinte:

* Funções assíncronas
* Várias instâncias
* Desligamento normal
* Use atributos de SDK WebJobs no corpo de uma função
* Defina as cadeias de conexão do SDK no código
* Defina valores para parâmetros do construtor do SDK WebJobs no código
* Dispare uma função manualmente
* Grave logs

## <a id="nextsteps"></a>Próximas etapas

Este guia fornece exemplos de código que mostram como lidar com cenários comuns para trabalhar com o barramento de serviço do Azure. Para obter mais informações sobre como usar o WebJobs do Azure e o SDK WebJobs, consulte [Recursos recomendados do WebJobs do Azure](http://go.microsoft.com/fwlink/?linkid=390226).



<!--HONumber=42-->
