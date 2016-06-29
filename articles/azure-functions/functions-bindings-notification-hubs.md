<properties
	pageTitle="Associação do Hub de Notificação do Azure Functions | Microsoft Azure"
	description="Entenda como usar a associação de Hub de Notificação do Azure no Azure Functions."
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
	ms.date="05/16/2016"
	ms.author="wesmc"/>

# Associação de saída do Hub de Notificação do Azure Functions

Este artigo explica como configurar e codificar associações do Hub de Notificação no Azure Functions.

[AZURE.INCLUDE [introdução](../../includes/functions-bindings-intro.md)]

Suas funções podem enviar notificações por push usando um Hub de Notificação do Azure configurado com muito poucas linhas de código. No entanto, o hub de notificação deve estar configurado para os PNS (Serviços de Notificações de Plataforma) que você deseja usar. Para saber mais sobre a configuração de um Hub de Notificação do Azure e sobre o desenvolvimento de aplicativos cliente que se registram para receber notificações, consulte [Introdução aos Hubs de Notificações](../notification-hubs/notification-hubs-windows-store-dotnet-get-started.md) e clique em sua plataforma de cliente de destino na parte superior.

## function.json para associação de saída do Hub de Notificação do Azure

O arquivo function.json fornece as seguintes propriedades:

- `name`: nome da variável usada no código de função para a mensagem do hub de notificação.
- `type`: deve ser definido como *"notificationHub"*.
- `tagExpression`: as expressões de marca permitem que você especifique que as notificações sejam entregues a um conjunto de dispositivos que se registraram para receber notificações que correspondem à expressão de marca. Para saber mais, veja [Expressões de marca e de roteamento](../notification-hubs/notification-hubs-routing-tag-expressions.md).
- `hubName`: nome do recurso de hub de notificação no portal do Azure.
- `connection`: essa cadeia de conexão deve ser uma cadeia de conexão de **Configuração de Aplicativo** definida com o valor *DefaultFullSharedAccessSignature* para seu hub de notificação.
- `direction`: deve ser definido como *”out”*. 
 
function.json de exemplo:

	{
	  "bindings": [
	    {
	      "name": "notification",
	      "type": "notificationHub",
	      "tagExpression": "",
	      "hubName": "my-notification-hub",
	      "connection": "MyHubConnectionString",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

## Configuração da cadeia de conexão do Hub de Notificação do Azure

Para usar uma associação de saída de um hub de notificação, você deve configurar a cadeia de conexão para o hub. Você pode fazer isso na guia *Integrar* simplesmente selecionando seu hub de notificação ou criando um novo.

Você também pode adicionar manualmente uma cadeia de conexão a um hub existente adicionando uma cadeia de conexão à *DefaultFullSharedAccessSignature* para seu hub de notificação. Essa cadeia de conexão fornece sua permissão de acesso à função para enviar mensagens de notificação. O valor da cadeia de conexão *DefaultFullSharedAccessSignature* pode ser acessado do botão **chaves** na folha principal do seu recurso de hub de notificação no portal do Azure. Para adicionar manualmente uma cadeia de conexão ao hub, use estas etapas:

1. Na folha **Aplicativo de funções** do portal do Azure, clique em **Configurações do Aplicativo de funções > Vá para as configurações do Serviço de Aplicativo**.

2. Na folha **Configurações**, clique em **Configurações do Aplicativo**.

3. Role para baixo até a seção **Cadeias de conexão** e adicione uma entrada nomeada para o valor *DefaultFullSharedAccessSignature* para o hub de notificação. Altere o tipo para **Personalizado**.
4. Faça referência ao nome da sua cadeia de conexão nas associações de saída. Semelhante à **MyHubConnectionString** usada no exemplo acima.

## Exemplo de código de Hub de Notificação do Azure para um gatilho de temporizador do Node.js 

Este exemplo envia uma notificação para um [registro de modelo](../notification-hubs/notification-hubs-templates.md) que contém `location` e `message`.

	module.exports = function (context, myTimer) {
	    var timeStamp = new Date().toISOString();
	   
	    if(myTimer.isPastDue)
	    {
	        context.log('Node.js is running late!');
	    }
	    context.log('Node.js timer trigger function ran!', timeStamp);  
	    context.bindings.notification = {
	        location: "Redmond",
	        message: "Hello from Node!"
	    };
	    context.done();
	};

## Exemplo de código de Hub de Notificação do Azure para um gatilho de fila do C#

Este exemplo envia uma notificação para um [registro de modelo](../notification-hubs/notification-hubs-templates.md) que contém `message`.


	using System;
	using System.Threading.Tasks;
	using System.Collections.Generic;
	 
	public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = GetTemplateProperties(myQueueItem);
	}
	 
	private static IDictionary<string, string> GetTemplateProperties(string message)
	{
	    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
	    templateProperties["message"] = message;
	    return templateProperties;
	}

Este exemplo envia uma notificação para um [registro de modelo](../notification-hubs/notification-hubs-templates.md) que contém `message` usando uma cadeia de caracteres JSON válida.

	using System;
	 
	public static void Run(string myQueueItem,  out string notification, TraceWriter log)
	{
		log.Info($"C# Queue trigger function processed: {myQueueItem}");
		notification = "{"message":"Hello from C#. Processed a queue item!"}";
	}

## Exemplo de código C# de gatilho de fila do Hub de Notificação do Azure usando o tipo Notificação

Este exemplo mostra como usar o tipo `Notification` definido na [Biblioteca de Hubs de Notificações do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). Para usar esse tipo e a biblioteca, você deve carregar um arquivo *project.json* para seu aplicativo de funções. O arquivo project.json é um arquivo de texto JSON que terá esta aparência:

	{
	  "frameworks": {
	    ".NETFramework,Version=v4.6": {
	      "dependencies": {
	        "Microsoft.Azure.NotificationHubs": "1.0.4"
	      }
	    }
	  }
	}

Para obter mais informações sobre o carregamento do seu arquivo project.json, confira [uploading a project.json file](functions-reference.md#fileupdate) (carregando um arquivo project.json).

Código de exemplo:

	using System;
	using System.Threading.Tasks;
	using Microsoft.Azure.NotificationHubs;
	 
	public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
	{
	   log.Info($"C# Queue trigger function processed: {myQueueItem}");
	   notification = GetTemplateNotification(myQueueItem);
	}
	private static TemplateNotification GetTemplateNotification(string message)
	{
	    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
	    templateProperties["message"] = message;
	    return new TemplateNotification(templateProperties);
	}

## Próximas etapas

[AZURE.INCLUDE [próximas etapas](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0615_2016-->