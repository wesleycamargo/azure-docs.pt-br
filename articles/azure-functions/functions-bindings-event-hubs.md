<properties
	pageTitle="Associações do Hub de Eventos do Azure Functions | Microsoft Azure"
	description="Entenda como usar associações do Hub de Eventos do Azure no Azure Functions."
	services="functions"
	documentationCenter="na"
	authors="wesmc7777"
	manager="erikre"
	editor=""
	tags=""
	keywords="azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="05/27/2016"
	ms.author="wesmc"/>

# Associações do Hub de Eventos do Azure Functions

Este artigo explica como configurar e codificar associações do [Hub de Eventos do Azure](../event-hubs/event-hubs-overview.md) no Azure Functions. O Azure Functions dá suporte a associações de gatilho e de saída para os Hubs de Eventos do Azure.

[AZURE.INCLUDE [introdução](../../includes/functions-bindings-intro.md)]


## Associações de gatilho do Hub de Eventos do Azure

Um gatilho do Hub de Eventos do Azure pode ser usado para responder a um evento enviado para uma transmissão de evento do hub de eventos. É necessário ter acesso de leitura ao hub de eventos para configurar uma associação de gatilho.

#### function.json para a associação de gatilho do Hub de Eventos

O arquivo *function.json* para um gatilho do Hub de Eventos do Azure especifica as seguintes propriedades:

- `type`: deve ser definido como *eventHubTrigger*.
- `name`: nome da variável usada no código de função referente à mensagem do hub de eventos.
- `direction`: deve ser definido como *in*.
- `path`: nome do hub de eventos.
- `connection`: nome de uma configuração de aplicativo que contém a cadeia de conexão para o namespace no qual o hub de eventos reside. Copie essa cadeia de conexão clicando no botão **Informações de Conexão** do namespace, não no próprio hub de eventos. Essa cadeia de conexão deve ter, pelo menos, permissões de leitura para ativar o gatilho.

		{
		  "bindings": [
		    {
		      "type": "eventHubTrigger",
		      "name": "myEventHubMessage",
		      "direction": "in",
		      "path": "MyEventHub",
		      "connection": "myEventHubReadConnectionString"
		    }
		  ],
		  "disabled": false
		}

#### Exemplo do C# de gatilho do Hub de Eventos do Azure
 
Usando o function.json de exemplo acima, o corpo da mensagem de evento será registrado com o código de função do C# abaixo:
 
	using System;
	
	public static void Run(string myEventHubMessage, TraceWriter log)
	{
	    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
	}

#### Exemplo do Node.js de gatilho do Hub de Eventos do Azure
 
Usando o function.json de exemplo acima, o corpo da mensagem de evento será registrado com o código de função do Node.js abaixo:
 
	module.exports = function (context, myEventHubMessage) {
	    context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);	
	    context.done();
	};


## Associação de saída do Hub de Eventos do Azure

Uma associação de saída do Hub de Eventos do Azure é usada para gravar eventos em uma transmissão de evento do hub de eventos. É necessário ter permissão de envio para um hub de eventos a fim de gravar eventos nele.

#### function.json para a associação de saída do Hub de Eventos

O arquivo *function.json* para uma associação de saída do Hub de Eventos do Azure especifica as seguintes propriedades:

- `type`: deve ser definido como *eventHub*.
- `name`: nome da variável usada no código de função referente à mensagem do hub de eventos.
- `path`: nome do hub de eventos.
- `connection`: nome de uma configuração de aplicativo que contém a cadeia de conexão para o namespace no qual o hub de eventos reside. Copie essa cadeia de conexão clicando no botão **Informações de Conexão** do namespace, não no próprio hub de eventos. Essa cadeia de conexão deve ter permissões de envio para enviar a mensagem à transmissão do Hub de Eventos.
- `direction`: deve ser definido como *out*.

	    {
	      "type": "eventHub",
	      "name": "outputEventHubMessage",
	      "path": "myeventhub",
	      "connection": "MyEventHubSend",
	      "direction": "out"
	    }


#### Exemplo de código do C# de associação de saída do Hub de Eventos do Azure
 
O seguinte código de função de exemplo do C# demonstra a gravação de um evento em uma transmissão de evento do Hub de Eventos. Este exemplo representa a associação de saída do Hub de Eventos mostrada acima como aplicada a um gatilho de temporizador do C#.
 
	using System;
	
	public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
	{
	    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
	
	    log.Verbose(msg);   
	    
	    outputEventHubMessage = msg;
	}

#### Exemplo de código do Node.js de associação de saída do Hub de Eventos do Azure
 
O seguinte código de função de exemplo do Node.js demonstra a gravação de um evento em uma transmissão de evento do Hub de Eventos. Este exemplo representa a associação de saída do Hub de Eventos mostrada acima como aplicada a um gatilho de temporizador do Node.js.
 
	module.exports = function (context, myTimer) {
	    var timeStamp = new Date().toISOString();
	    
	    if(myTimer.isPastDue)
	    {
	        context.log('TimerTriggerNodeJS1 is running late!');
	    }

	    context.log('TimerTriggerNodeJS1 function ran!', timeStamp);   
	    
	    context.bindings.outputEventHubMessage = "TimerTriggerNodeJS1 ran at : " + timeStamp;
	
	    context.done();
	};

## Próximas etapas

[AZURE.INCLUDE [próximas etapas](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0713_2016-->