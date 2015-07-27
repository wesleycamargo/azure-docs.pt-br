<properties
	pageTitle="Como usar Hubs de notificação com Node. js"
	description="Saiba como usar os Hubs de notificação para enviar notificações por push de um aplicativo do Node. js."
	services="notification-hubs"
	documentationCenter="nodejs"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="06/02/2015"
	ms.author="wesmc"/>

# Como usar os Hubs de notificação do Node. js
<div class="dev-center-tutorial-selector sublanding">
    	<a href="/documentation/articles/notification-hubs-java-backend-how-to/" title="Java">Java</a><a href="/documentation/articles/notification-hubs-php-backend-how-to/" title="PHP">PHP</a><a href="/documentation/articles/notification-hubs-python-backend-how-to/" title="Python">Python</a><a href="/documentation/articles/notification-hubs-nodejs-how-to-use-notification-hubs/" title="Node.js" class="current">Node.js</a>
</div>

##Visão geral

Este guia mostrará como usar os Hubs de Notificação de aplicativos Node.js. Os cenários abordados incluem **o envio de notificações para aplicativos Android, iOS, Windows Phone e Windows Store**. Para obter mais informações sobre Hubs de Notificação, consulte a seção [Próximas etapas](#next).

##O que são Hubs de Notificação?

Os Hubs de Notificação do Azure fornecem uma infraestrutura de várias plataformas escalonáveis e fáceis de usar para o envio de notificações por push para dispositivos móveis. Para obter mais informações, consulte [Hubs de Notificação do Azure](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx).

##Criar um aplicativo Node.js

Criar um aplicativo Node.js em branco. Para obter instruções sobre como criar um aplicativo Node.js, consulte [Criar e implantar um aplicativo Node.js para um Site do Azure][nodejswebsite], [Serviço de Nuvem do Node.js][Node.js Cloud Service] (Usando o PowerShell do Windows) ou [Site com o WebMatrix].

##Configurar seu aplicativo para usar o Hub de Notificação

Para usar o Hub de notificação do Azure, você precisa baixar e usar o pacote Node.js do Azure. Isso inclui um conjunto de bibliotecas de conveniência que se comunicam com os serviços REST.

### Usar o NPM (gerenciador de pacotes de nós) para obter o pacote

1.  Use uma interface de linha de comando, como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix), e navegue até a pasta onde você criou o aplicativo de exemplo.

2.  Digite **npm install azure** na janela de comando, que deve resultar na seguinte saída:

        azure@0.7.0 node_modules\azure
		|-- dateformat@1.0.2-1.2.3
		|-- xmlbuilder@0.4.2
		|-- node-uuid@1.2.0
		|-- mime@1.2.9
		|-- underscore@1.4.4
		|-- validator@0.4.28
		|-- tunnel@0.0.2
		|-- wns@0.5.3
		|-- xml2js@0.2.6 (sax@0.4.2)
		|-- request@2.16.6 (forever-agent@0.2.0, aws-sign@0.2.0, tunnel-agent@0.2.0, oauth-sign@0.2.0, json-stringify-safe@3.0.0, cookie-jar@0.2.0, node-uuid@1.4.0, qs@0.5.5, hawk@0.10.2, form-data@0.0.7)

3.  Você pode executar o comando **ls** ou **dir** manualmente para verificar se uma pasta **node_modules** foi criada. Dentro dessa pasta, você encontrará o pacote **Azure**, que contém as bibliotecas necessárias para acessar o Hub de Notificação.

### Importar o módulo

Usando um editor de texto, adicione o seguinte à parte superior do arquivo **server.js** do aplicativo:

    var azure = require('azure');

### Instalar uma conexão do hub de notificação do Azure

O objeto **NotificationHubService** permite trabalhar com Hubs de Notificação. O código a seguir cria um objeto **NotificationHubService** para o Hub de Notificação chamado **hubname**. Adicione-o próximo ao início do arquivo **server.js**, após a instrução de importação do módulo Azure:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

O valor da conexão **connectionstring** pode ser obtido no Portal de Gerenciamento do Azure executando as seguintes etapas:

1. No Portal de Gerenciamento do Azure, selecione **Barramento de Serviço** e, em seguida, selecione o namespace que contém o Hub de Notificação.

2. Selecione **HUBS DE NOTIFICAÇÃO**e, em seguida, selecione o hub que você deseja usar.

3. Selecione **Exibir Cadeia de Conexão** na seção **visão rápida** e copie o valor da cadeia de conexão.

> [AZURE.NOTE]Você também pode recuperar a cadeia de conexão usando o cmdlet **Get-AzureSbNamespace** fornecido pelo PowerShell do Azure ou o comando **azure sb namespace show** com as Ferramentas de Linha de Comando do Azure (CLI do Azure).

</div>

##Como enviar notificações

O objeto **NotificationHubService** expõe as seguintes instâncias de objeto para o envio de notificações a dispositivos e aplicativos específicos:

* **Android** - use o objeto **GcmService**, que está disponível em **notificationHubService.gcm**
* **iOS** - use o objeto **ApnsService**, que está disponível em **notificationHubService.apns**
* **Windows Phone** - use o objeto **MpnsService**, que está disponível em **notificationHubService.mpns**
* **Aplicativos da Windows Store** - use o objeto **WnsService**, que está disponível em **notificationHubService.wns**

### Como enviar notificações de aplicativos Android

O objeto **GcmService** fornece um método **send** que pode ser usado para enviar notificações aos aplicativos Android. O método **send** aceita os seguintes parâmetros:

* Tags - o identificador da marca. Se nenhuma marca for fornecida, a notificação será enviada a todos os clientes
* Payload - o JSON da mensagem ou a carga de cadeia de caracteres
* Callback - a função de retorno de chamada

Para obter mais informações sobre o formato de payload, consulte a seção Payload de [Implementando o servidor GCM (a página pode estar em inglês)](http://developer.android.com/google/gcm/server.html#payload).

O código a seguir usa a instância **GcmService** exposta pelo **NotificationHubService** para enviar uma mensagem a todos os clientes.

	var payload = {
	  data: {
	    msg: 'Hello!'
	  }
	};
	notificationHubService.gcm.send(null, payload, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### Como enviar notificações de aplicativos iOS

O objeto **ApnsService** fornece um método **send** que pode ser usado para enviar notificações aos aplicativos iOS. O método **send** aceita os seguintes parâmetros:

* Tags - o identificador da marca. Se nenhuma marca for fornecida, a notificação será enviada a todos os clientes
* Payload - o JSON da mensagem ou a carga de cadeia de caracteres
* Callback - a função de retorno de chamada

Para obter mais informações sobre o formato de payload, consulte a seção Carga de notificação do [Guia de Programação de Notificações Locais e por Push (a página pode estar em inglês)](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html).

O código a seguir usa a instância **ApnsService** exposta por **NotificationHubService** para enviar uma mensagem de alerta a todos os clientes:

	var payload={
	    alert: 'Hello!'
	  };
	notificationHubService.apns.send(null, payload, function(error){
	  if(!error){
 	    // notification sent
      }
	});

### Como enviar notificações do Windows Phone

O objeto **MpnsService** fornece um método **send** que pode ser usado para enviar notificações aos aplicativos do Windows Phone. O método **send** aceita os seguintes parâmetros:

* Tags - o identificador da marca. Se nenhuma marca for fornecida, a notificação será enviada a todos os clientes
* Payload - a carga XML da mensagem
* TargetName - ‘toast’ para notificações do sistema. ‘token’ para notificações lado a lado.
* NotificationClass - a prioridade da notificação. Consulte a seção Elementos de cabeçalhos HTTP de [Notificações por push de um servidor (a página pode estar em inglês)](http://msdn.microsoft.com/library/hh221551.aspx) para obter os valores válidos.
* Options - cabeçalhos de solicitação opcional
* Callback - a função de retorno de chamada

Para obter uma lista de TargetName válido, NotificationClass e opções de cabeçalho, consulte [Notificações por push de um servidor (a página pode estar em inglês)](http://msdn.microsoft.com/library/hh221551.aspx).

O código a seguir usa a instância **MpnsService** exposta pelo **NotificationHubService** para enviar um alerta de notificação do sistema:

	var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
	notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### Como enviar notificações de aplicativos da Windows Store

O objeto **WnsService** fornece um método **send** que pode ser usado para enviar notificações aos aplicativos da Windows Store. O método **send** aceita os seguintes parâmetros:

* Tags - o identificador da marca. Se nenhuma marca for fornecida, a notificação será enviada a todos os clientes
* Payload - a carga da mensagem XML
* Type - o tipo de notificação
* Options - cabeçalhos de solicitação opcional
* Callback - a função de retorno de chamada

Para obter uma lista de tipos e cabeçalhos de solicitação válidos, consulte [Solicitação de serviço e cabeçalhos de resposta do serviço de notificação por push (a página pode estar em inglês)](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

O código a seguir usa a instância **WnsService** exposta pelo **NotificationHubService** para enviar um alerta de notificação do sistema:

	var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
	notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
	  if(!error){
 	    // notification sent
	  }
	});

## Próximas etapas

Agora que você já sabe as noções básicas do uso de hubs de notificação, siga estes links para saber mais.

-   Consulte a Referência do MSDN: [Hubs de Notificação do Azure][].
-   Visite o repositório [SDK do Azure para o nó] no GitHub.

  [SDK do Azure para o nó]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Next Steps]: #nextsteps
  [What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
  [Create a Service Namespace]: #create-a-service-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
  [Create a Node.js Application]: #Create_a_Nodejs_Application
  [Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [How to: Create a Topic]: #How_to_Create_a_Topic
  [How to: Create Subscriptions]: #How_to_Create_Subscriptions
  [How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
  [How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
  [How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Azure Management Portal]: http://manage.windowsazure.com
  [image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Site com o WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
  [Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
 

<!---HONumber=July15_HO3-->