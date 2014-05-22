<properties linkid="develop-php-how-to-guides-service-bus-topics" urlDisplayName="Tópicos de Service Bus" pageTitle="Como usar os tópicos de Service Bus (PHP) - Azure" metaKeywords="" description="Saiba como usar os tópicos de Service Bus com o PHP no Azure." metaCanonical="" services="service-bus" documentationCenter="PHP" title="Como usar os tópicos/assinaturas do Service Bus" authors="" solutions="" manager="" editor="" />


# Como usar os tópicos/assinaturas do Service Bus

Este guia mostra como usar os tópicos e as assinaturas do Barramento de Serviço. As amostras são escritas em PHP e usam o [SDK do Azure para PHP][download-sdk]. Os cenários abordados incluem a **criação de tópicos e assinaturas**, a **criação de filtros de assinatura**, o **envio de mensagens para um tópico**, o **recebimento de mensagens de uma assinatura** e a **exclusão de tópicos e assinaturas**.

## Sumário

-   [O que são os tópicos e as assinaturas do Service Bus?](#what-are-service-bus-topics)
-   [Criar um namespace de serviço](#create-a-service-namespace)
-   [Obter as credenciais de gerenciamento padrão do namespace](#obtain-default-credentials)
- 	[Criar um aplicativo PHP](#CreateApplication)
-	[Obter as bibliotecas de cliente do Azure](#GetClientLibrary)
-   [Configurar seu aplicativo para usar o Service Bus](#ConfigureApp)
-   [Como criar um tópico](#CreateTopic)
-   [Como: criar uma assinatura](#CreateSubscription)
-   [Como enviar mensagens a um tópico](#SendMessage)
-   [Como receber mensagens de uma assinatura](#ReceiveMessages)
-   [Como tratar falhas do aplicativo e mensagens ilegíveis](#HandleCrashes)
-   [Como excluir tópicos e assinaturas](#DeleteTopicsAndSubscriptions)
-   [Próximas etapas](#NextSteps)

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

##<a id="CreateApplication"></a>Criar um aplicativo PHP

O único requisito para a criação de um aplicativo PHP que acessa o serviço de Blob do Azure é a referência de classes no [SDK do Azure para PHP][download-sdk] em seu código. Você pode usar as ferramentas de desenvolvimento para criar seu aplicativo, incluindo o bloco de notas.

> [WACOM.NOTE]
> A instalação do PHP também deve ter a <a href="http://php.net/openssl">extensão OpenSSL</a> instalada e habilitada.

Neste guia, você usará os recursos de serviços que podem ser chamados em um aplicativo PHP localmente ou no código em execução dentro de uma função web do Azure, uma função de trabalho ou um site.

##<a id="GetClientLibrary"></a>Obter as bibliotecas de cliente do Azure

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

##<a id="ConfigureApp"></a>Configurar seu aplicativo para usar o Service Bus

Para usar as APIs do tópico de Service Bus do Azure, você precisará:

1. Fazer referência ao arquivo de carregador automático usando a instrução [require_once][require-once] e
2. Fazer referência a qualquer classe que você possa usar.

O exemplo a seguir mostra como incluir o arquivo de carregador automático e fazer referência à classe **ServicesBuilder**.

	> [WACOM.NOTE]
	> Esse exemplo (e outros exemplos deste artigo) pressupõe que você tenha instalado as Bibliotecas de Cliente do PHP para o Azure por meio do Compositor. Se você instalou as bibliotecas manualmente ou como um pacote PEAR, você precisará fazer referência ao arquivo de carregador automático <code>WindowsAzure.php</code>.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


Nos exemplos abaixo, a instrução `exigir_uma vez` será mostrada sempre, mas somente as classes necessárias para executar o exemplo serão referenciadas.

##<a id="ConnectionString"></a>Configurar uma conexão do Service Bus do Azure

Para criar uma instância de cliente do Barramento de Serviço do Azure, você deve primeiramente ter uma cadeia de conexão válida de acordo com este formato:

	Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]

Em que o ponto de extremidade normalmente está no formato `https://[yourNamespace].servicebus.windows.net`.

Para criar qualquer cliente de serviço do Azure é necessário usar a classe **ServicesBuilder**. Você pode:

* passar a cadeia de conexão diretamente para ele ou
* usar o **CloudConfigurationManager (CCM)** para verificar várias origens externas para a cadeia de conexão:
	* por padrão, ele é fornecido com suporte para uma origem externa - variáveis de ambiente
	* você pode adicionar novas origens ao estender a classe **ConnectionStringSource**

Para os exemplos descritos aqui, a cadeia de conexão será passada diretamente.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	
	$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

##<a id="CreateTopic"></a>Como: criar um tópico

As operações de gerenciamento dos tópicos do Barramento de Serviço podem ser executadas por meio da
Classe **ServiceBusRestProxy**. Um objeto **ServiceBusRestProxy** é construído por meio do método de fábrica **ServicesBuilder::createServiceBusService** com uma cadeia de conexão apropriada que encapsula as permissões de token para gerenciá-lo.

O exemplo a seguir mostra como criar uma instância de um **ServiceBusRestProxy** e chamar **ServiceBusRestProxy->createTopic** para criar um tópico denominado `mytopic` em um namespace de serviço `MySBNamespace`:

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\TopicInfo;
	
	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
	try	{		
		// Create topic.
		$topicInfo = new TopicInfo("mytopic");
		$serviceBusRestProxy->createTopic($topicInfo);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/dd179357
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

	> [WACOM.NOTE]
	> Você pode usar o método <b>listTopics</b> nos objetos <b>ServiceBusRestProxy</b> para verificar se já existe um tópico com um nome especificado em um namespace de serviço.

##<a id="CreateSubscription"></a>Como: criar uma assinatura

As assinaturas do tópico também são criadas com o método **ServiceBusRestProxy->createSubscription**. As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens passadas para a fila virtual da assinatura.

### Criar uma assinatura com o filtro padrão (MatchAll)

**MatchAll** será o filtro padrão usado se nenhum filtro for especificado quando uma nova assinatura for criada. Quando o filtro **MatchAll** é usado, todas as mensagens publicadas no tópico são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma assinatura denominada 'mysubscription' e usa o filtro padrão **MatchAll**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
	try	{
		// Create subscription.
		$subscriptionInfo = new SubscriptionInfo("mysubscription");
		$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/dd179357
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

### Criar assinaturas com filtros

Você também pode configurar filtros que permitem atribuir um escopo a quais mensagens enviadas a um tópico devem aparecer dentro de uma assinatura específica do tópico. O tipo de filtro mais flexível compatível com as assinaturas é o **SqlFilter**, que implementa um subconjunto do SQL92. Os filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para obter mais informações sobre SqlFilters, consulte a [propriedade SqlFilter.SqlExpression][sqlfilter].

	> [WACOM.NOTE]
	> Cada regra uma assinatura processa as mensagens de entrada independentemente, adicionando suas mensagens de resultado à assinatura. Além disso, cada nova assinatura tem uma <b>Regra</b> padrão com um filtro que adiciona todas as mensagens do tópico à assinatura. Para receber apenas as mensagens correspondentes ao filtro, você deve remover a regra padrão. Você pode remover a regra padrão usando o método <b>ServiceBusRestProxy->deleteRule</b>.

O exemplo a seguir cria uma assinatura denominada "HighMessages" com um **SqlFilter** que seleciona apenas as mensagens que têm uma propriedade **MessageNumber** personalizada maior que 3 (consulte [Como: enviar mensagens para um tópico](#SendMessage) para obter informações sobre como adicionar propriedades personalizadas a mensagens):

	$subscriptionInfo = new SubscriptionInfo("HighMessages");
   	$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

	$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

  	$ruleInfo = new RuleInfo("HighMessagesRule");
   	$ruleInfo->withSqlFilter("MessageNumber > 3");
   	$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);

Observe que o código acima requer o uso de um namespace adicional: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

De maneira semelhante, o exemplo a seguir cria uma assinatura denominada "LowMessages" com um SqlFilter que seleciona apenas as mensagens que tenham uma propriedade
MessageNumber menor ou igual a 3:

	$subscriptionInfo = new SubscriptionInfo("LowMessages");
   	$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

	$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

  	$ruleInfo = new RuleInfo("LowMessagesRule");
   	$ruleInfo->withSqlFilter("MessageNumber <= 3");
   	$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);

Agora, quando uma mensagem for enviada para o tópico `mytopic`, ela será sempre entregue aos destinatários inscritos na assinatura `mysubscription` e será entregue de forma seletiva para os destinatários inscritos nas assinaturas "HighMessages" e "LowMessages" (dependendo do conteúdo da mensagem).

##<a id="SendMessage"></a>Como: enviar mensagens a um tópico

Para enviar uma mensagem a um tópico de Service Bus, seu aplicativo chamará o método **ServiceBusRestProxy->sendTopicMessage**. O código abaixo demonstra como enviar uma mensagem ao tópico `mytopic` que criamos acima no
namespace de serviço `MySBNamespace`.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\BrokeredMessage;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Create message.
		$message = new BrokeredMessage();
		$message->setBody("my message");
	
		// Send message.
		$serviceBusRestProxy->sendTopicMessage("mytopic", $message);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/hh780775
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

As mensagens enviadas aos tópicos de Service Bus são instâncias da classe **BrokeredMessage**. Os objetos **BrokeredMessage** têm um conjunto de propriedades e métodos padrão (como **getLabel**, **getTimeToLive**, **setLabel** e **setTimeToLive**), bem como propriedades que podem ser usadas para manter as propriedades personalizadas específicas do aplicativo. O exemplo a seguir demonstra como enviar cinco mensagens de teste para o tópico `mytopic` criado anteriormente. O método **setProperty** é usado para adicionar uma propriedade personalizada (`MessageNumber`) a cada mensagem. Observe como o valor da propriedade `MessageNumber` varia em cada mensagem (ele pode ser usado para determinar quais assinaturas o receberão, conforme mostrado na seção [Como: criar uma assinatura](#CreateSubscription) acima):

	for($i = 0; $i < 5; $i++){
		// Create message.
		$message = new BrokeredMessage();
		$message->setBody("my message ".$i);
			
		// Set custom property.
		$message->setProperty("MessageNumber", $i);
			
		// Send message.
		$serviceBusRestProxy->sendTopicMessage("mytopic", $message);
	}

As filas de Service Bus oferecem suporte a um tamanho máximo de mensagem de 256 KB (o cabeçalho, que inclui as propriedades do aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 KB). Não há nenhum limite no número de mensagens mantidas em uma fila mas há uma capacidade do tamanho total das mensagens mantidas por uma fila. Este limite superior do tamanho da fila é 5 GB.

##<a id="ReceiveMessages"></a>Como: receber mensagens de uma assinatura

A principal maneira de receber mensagens de uma assinatura é usando um método **ServiceBusRestProxy ->receiveSubscriptionMessage**. As mensagens recebidas podem funcionar de dois modos diferentes: **ReceiveAndDelete** (o padrão) e **PeekLock**.

Quando o modo **ReceiveAndDelete** for usado, o recebimento será uma operação única, isto é, quando o Service Bus receber uma solicitação de leitura de uma mensagem em uma assinatura, ele marcará a mensagem como sendo consumida e a retornará ao aplicativo.-  O modo **ReceiveAndDelete** é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Barramento de Serviço terá marcado a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

No modo **PeekLock**, o recebimento de uma mensagem se torna uma operação de dois estágios, o possibilita o suporte a aplicativos que não podem tolerar mensagens ausentes. Quando o Service Bus recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, ela a bloqueia para evitar que outros consumidores a recebam e a retorna para o aplicativo. Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento passando a mensagem recebida para **ServiceBusRestProxy->deleteMessage**. Quando o Barramento de Serviço vê a chamada **deleteMessage**, ele marca a mensagem como sendo consumida e remove-a da fila.

O exemplo a seguir demonstra como uma mensagem pode ser recebida e processada usando o modo **PeekLock** (não o modo padrão). 

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Set receive mode to PeekLock (default is ReceiveAndDelete)
		$options = new ReceiveMessageOptions();
		$options->setPeekLock();
	
		// Get message.
		$message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", 
																	"mysubscription", 
																	$options);
		echo "Body: ".$message->getBody()."<br />";
		echo "MessageID: ".$message->getMessageId()."<br />";
		
		/*---------------------------
			Process message here.
		----------------------------*/
		
		// Delete message. Not necessary if peek lock is not set.
		echo "Deleting message...<br />";
		$serviceBusRestProxy->deleteMessage($message);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/pt-br/library/windowsazure/hh780735
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

##<a id="HandleCrashes"></a>Como tratar falhas do aplicativo e mensagens ilegíveis

O Service Bus proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não for capaz de processar a mensagem por algum motivo, ele chamará o método **unlockMessage** na mensagem recebida (em vez do método **deleteMessage**). Isso fará com que o Service Bus desbloqueie a mensagem na fila e disponibilize-a para que ela possa ser recebida novamente pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na fila e, se o aplicativo não conseguir processar a mensagem antes da expiração do tempo limite do bloqueio (por exemplo, em caso de falha do aplicativo), o Service Bus desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Se houver falha do aplicativo após o processamento da mensagem, mas antes da solicitação **deleteMessage** ser emitida, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Isso é frequentemente chamado de **Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez mas, em algumas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega de mensagem duplicada. Isso geralmente é feito com o método **getMessageId** da mensagem, que permanecerá constante nas tentativas da entrega.

##<a id="DeleteTopicsAndSubscriptions"></a>Como excluir tópicos e assinaturas

Para excluir um tópico ou uma assinatura, use o **ServiceBusRestProxy -> deleteTopic** ou **ServiceBusRestProxy -> deleteSubscripton**, respectivamente. Observe que a exclusão de um tópico também excluirá todas as assinaturas registradas com o tópico.

O exemplo a seguir mostra como excluir um tópico (`mytopic`) e suas assinaturas registradas.

    require_once 'vendor\autoload.php';

	use WindowsAzure\ServiceBus\ServiceBusService;
	use WindowsAzure\ServiceBus\ServiceBusSettings;
	use WindowsAzure\Common\ServiceException;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
	try	{		
		// Delete topic.
		$serviceBusRestProxy->deleteTopic("mytopic");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/dd179357
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Ao usar o método **deleteSubscription**, você poderá excluir uma assinatura de forma independente:

	$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");

##<a id="NextSteps"></a>Próximas etapas

Agora que você aprendeu as noções básicas sobre Filas do Service Bus, consulte o tópico do MSDN [Filas, tópicos e assinaturas][] para obter mais informações.

[download-sdk]: http://go.microsoft.com/fwlink/?LinkId=252473
[O que são os tópicos e as assinaturas do Service Bus?]: #bkmk_WhatAreSvcBusTopics
[Criar um namespace de serviço]: #bkmk_CreateSvcNamespace
[Obter as credenciais de gerenciamento padrão do namespace]: #bkmk_ObtainDefaultMngmntCredentials
[Configurar seu aplicativo para usar o Service Bus]: #bkmk_ConfigYourApp
[Como criar um tópico]: #bkmk_HowToCreateTopic
[Como criar assinaturas]: #bkmk_HowToCreateSubscrip
[Como enviar mensagens a um tópico]: #bkmk_HowToSendMsgs
[Como receber mensagens de uma assinatura]: #bkmk_HowToReceiveMsgs
[Como tratar falhas do aplicativo e mensagens ilegíveis]: #bkmk_HowToHandleAppCrash
[Como excluir tópicos e assinaturas]: #bkmk_HowToDeleteTopics
[Próximas etapas]: #bkmk_NextSteps
[Diagrama dos tópicos de Service Bus]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
[Portal de Gerenciamento do Azure]: http://manage.windowsazure.com/
[Service Bus Topics diagram]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
[Azure Management Portal]: http://manage.windowsazure.com/
[Service Bus Node screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-03.png
[Create a New Namespace screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
[Namespace List screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
[Properties Pane screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
[Default Key screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
[Queues, Topics, and Subscriptions]: http://msdn.microsoft.com/en-us/library/windowsazure/hh367516.aspx
[Available Namespaces screenshot]: ../../../DevCenter/Java/Media/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[sqlfilter]: http://msdn.microsoft.com/pt-br/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx

[require-once]: http://php.net/require_once
