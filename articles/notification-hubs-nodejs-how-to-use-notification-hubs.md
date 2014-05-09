<properties linkid="develop-nodejs-how-to-guides-service-bus-notification-hubs" urlDisplayName="Hubs de Notificação" pageTitle="Hubs de Notificação de Barramento de Serviço - Centro de desenvolvimento do Node.js" metaKeywords="" description="Saiba como usar Hubs de Notificação de Barramento de Serviço para enviar notificações por push. Os exemplos de código são escritos para aplicativos Node.js." metaCanonical="" services="service-bus" documentationCenter="Node.js" title="Como usar Hubs de Notificação de Barramento de Serviço" authors="larryfr" solutions="" manager="" editor="" />





# Como usar os Hubs de Notificação de Barramento de Serviços

Este guia mostrará como usar os Hubs de Notificação de Barramento de Serviço em aplicativos Node.js. Os cenários abordados incluem **o envio de notificações para aplicativos Android, iOS, Windows Phone e Windows Store**. Para obter mais informações sobre Hubs de Notificação, consulte a seção [Próximas etapas](#next).

## Sumário

-   [O que são Hubs de Notificação de Barramento de Serviço?](#hub)
-   [Criar um aplicativo Node.js](#create)
-   [Configurar o aplicativo para usar o Barramento de Serviço](#config)
-   [Como enviar notificações](#send)
-   [Próximas etapas](#next)

##<a id="hub"></a> O que são Hubs de Notificação de Barramento de Serviço? 

Os Hubs de Notificação de Barramento de Serviço do Azure fornecem uma infraestrutura de várias plataformas escalonável para o envio de notificações a dispositivos móveis. Para obter mais informações, consulte [Hubs de Notificação de Barramento de Serviço do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj927170.aspx).

##<a id="create"></a> Criar um aplicativo Node.js

Criar um aplicativo Node.js em branco. Para obter instruções sobre como criar um aplicativo Node.js, consulte [Criar e implantar um aplicativo Node.js para um Site do Azure], [Serviço de Nuvem do Node.js] (Usando o PowerShell do Windows) ou [Site com o WebMatrix].

##<a id="config"></a> Configurar seu aplicativo para usar o Barramento de Serviço

Para usar o Service Bus do Azure, você precisa baixar e usar o pacote Azure do Node.js. Isso inclui um conjunto de bibliotecas de conveniência que se comunicam com os serviços REST do Service Bus.

### Usar o Gerenciador de Pacotes de Nós (NPM) para obter o pacote

1.  Use uma interface de linha de comando, como **PowerShell** (Windows,) **Terminal** (Mac) ou **Bash** (Unix), e vá até a pasta onde você criou a amostra do aplicativo.

2.  Digite **npm install azure** na janela de comando, que deve
    resultar na seguinte saída:

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

3.  Você pode executar o comando **ls** ou **dir** manualmente para verificar se uma
    **pasta node\_modules** foi criada. Dentro dessa pasta, você encontrará o
    pacote **azure**, que contém as bibliotecas necessárias para acessar
    os Hubs de Notificação de Barramento de Serviço.

### Importar o módulo

Usando um editor de texto, adicione o seguinte à parte superior
do arquivo **server.js** do aplicativo:

    var azure = require('azure');

### Configurar uma conexão de Barramento de Serviço do Azure

O objeto **NotificationHubService** permite trabalhar com Hubs de Notificação. O código a seguir cria um objeto **NotificationHubService** para o Hub de Notificação chamado **hubname**. Adicione-o próximo à parte superior do arquivo **server.js**, após a instrução de importação do módulo azure:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

O valor da conexão **connectionstring** pode ser obtido no Portal de Gerenciamento do Azure executando as seguintes etapas:

1. No Portal de Gerenciamento do Azure, selecione **Barramento de Serviço** e, em seguida, selecione o namespace que contém o Hub de Notificação.

2. Selecione **HUBS DE NOTIFICAÇÃO**e, em seguida, selecione o hub que você deseja usar.

3. Selecione **Exibir Cadeia de Conexão** na seção **visão rápida** e copie o valor da cadeia de conexão.

<div class="dev-callout">
<strong>Observação</strong>
<p>Você também pode recuperar a cadeia de conexão usando o cmdlet <b>Get-AzureSbNamespace</b> fornecido pelo PowerShell do Azure ou o comando <b>azure sb namespace show</b> com as Ferramentas de Linha de Comando do Azure.</p>

</div>

##<a id="send"></a> Como enviar notificações

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
* Payload - carga XML da mensagem
* TargetName - 'toast' para notificações do sistema. 'token' para notificações de bloco.
* NotificationClass - a prioridade da notificação. Consulte a seção Elementos de cabeçalhos HTTP de [Notificações por push de um servidor (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/hh221551.aspx) para obter os valores válidos.
* Options - cabeçalhos de solicitação opcional
* Callback - a função de retorno de chamada

Para obter uma lista de TargetName válido, NotificationClass e opções de cabeçalho, consulte [Notificações por push de um servidor (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/hh221551.aspx).

O código a seguir usa a instância **MpnsService** exposta pelo **NotificationHubService** para enviar um alerta de notificação do sistema:

	var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
	notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### Como enviar notificações de aplicativos da Windows Store

O objeto **WnsService** fornece um método **send** que pode ser usado para enviar notificações aos aplicativos da Windows Store.  O método **send** aceita os seguintes parâmetros:

* Tags - o identificador da marca. Se nenhuma marca for fornecida, a notificação será enviada a todos os clientes
* Payload - a carga da mensagem XML
* Type - o tipo de notificação
* Options - cabeçalhos de solicitação opcional
* Callback - a função de retorno de chamada

Para obter uma lista de tipos e cabeçalhos de solicitação válidos, consulte [Solicitação de serviço e cabeçalhos de resposta do serviço de notificação por push (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windows/apps/hh465435.aspx).

O código a seguir usa a instância **WnsService** exposta pelo **NotificationHubService** para enviar um alerta de notificação do sistema:

	var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
	notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
	  if(!error){
 	    // notification sent
	  }
	});

##<a id="next"></a> Próximas etapas

Agora que você já sabe os princípios dos tópicos do Service Bus, acesse estes links para saber mais.

-   Consulte a Referência do MSDN: [Hubs de Notificação de Barramento de Serviço do Azure][]
-   Acesse o repositório [SDK do Azure para Nós] no GitHub.

  [SDK do Azure para Nós]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Próximas etapas]: #nextsteps
  [O que são os tópicos e as assinaturas do Service Bus?]: #what-are-service-bus-topics
  [Criar um namespace de serviço]: #create-a-service-namespace
  [Obter as credenciais de gerenciamento padrão do namespace]: #obtain-default-credentials
  [Criar um aplicativo Node.js]: #Create_a_Nodejs_Application
  [Configurar seu aplicativo para usar o Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [Como criar um tópico]: #How_to_Create_a_Topic
  [Como criar assinaturas]: #How_to_Create_Subscriptions
  [Como enviar mensagens a um tópico]: #How_to_Send_Messages_to_a_Topic
  [Como receber mensagens de uma assinatura]: #How_to_Receive_Messages_from_a_Subscription
  [Como tratar falhas do aplicativo e mensagens ilegíveis]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [Como excluir tópicos e assinaturas]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Conceitos do tópico]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [imagem]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/pt-br/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Hubs de Notificação de Barramento de Serviço do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/pt-br/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Site com o WebMatrix]: /pt-br/develop/nodejs/tutorials/web-site-with-webmatrix/
  [Serviço de Nuvem do Node.js]: /pt-br/documentation/articles/cloud-services-nodejs-develop-deploy-app/
[Portal de Gerenciamento anterior]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [Criar e implantar um aplicativo Node.js para um Site do Azure]: /pt-br/develop/nodejs/tutorials/create-a-website-(mac)/
  [Serviço de nuvem Node.js com armazenamento]: /pt-br/develop/nodejs/tutorials/web-app-with-storage/
  [Aplicativo Web Node.js Web com armazenamento]: /pt-br/develop/nodejs/tutorials/web-site-with-storage/

