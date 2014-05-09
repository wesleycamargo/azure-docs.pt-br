<properties linkid="dev-java-how-to-service-bus-topics" urlDisplayName="Tópicos do Barramento de Serviço" pageTitle="Como usar os tópicos do Barramento de Serviço (Java) - Azure" metaKeywords="Introdução aos tópicos do Barramento de Serviço do Azure, Introdução aos tópicos do Barramento de Serviço do Azure, Azure publicar assinar mensagens, tópicos e assinaturas de mensagens do Azure, tópico do Barramento de Serviço Java" description="Saiba como usar os tópicos e assinaturas do Barramento de Serviço do Azure. Os exemplos de código são escritos para aplicativos Java." metaCanonical="" services="service-bus" documentationCenter="Java" title="Como usar tópicos/assinaturas do Barramento de Serviço" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" />







# Como usar os tópicos/assinaturas do Service Bus

Este guia mostra como usar os tópicos e as assinaturas do Barramento de Serviço. Os exemplos são escritos em Java e usam o [SDK do Azure para Java][]. Os cenários abrangidos incluem **criar tópicos e assinaturas**, **criar filtros de assinatura**, **enviar mensagens a um tópico**, **receber mensagens de uma assinatura** e
**excluir tópicos e assinaturas**.

## Sumário

-   [O que são os tópicos e as assinaturas do Service Bus?][]
-   [Criar um namespace de serviço][]
-   [Obter as credenciais de gerenciamento padrão do namespace][]
-   [Configurar seu aplicativo para usar o Service Bus][]
-   [Como criar um tópico][]
-   [Como criar assinaturas][]
-   [Como enviar mensagens a um tópico][]
-   [Como receber mensagens de uma assinatura][]
-   [Como tratar falhas do aplicativo e mensagens ilegíveis][]
-   [Como excluir tópicos e assinaturas][]
-   [Próximas etapas][]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## <a name="bkmk_ConfigYourApp"> </a>Configurar seu aplicativo para usar o Barramento de Serviço

Adicione as seguintes instruções de importação à parte superior do arquivo Java:

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;
    import javax.xml.datatype.*;

Adicione as Bibliotecas do Azure para Java a seu caminho de compilação e inclua-o em seu assembly de implantação do projeto.

## <a name="bkmk_HowToCreateTopic"> </a>Como criar um tópico

As operações de gerenciamento dos tópicos do Barramento de Serviço podem ser executadas por meio da
classe **ServiceBusContract**. O objeto **ServiceBusContract** é criado com uma configuração adequada que encapsula as permissões de token para gerenciá-lo e a classe **ServiceBusContract** é o único ponto de comunicação com o Azure.

A classe **ServiceBusService** fornece métodos para criar, enumerar e excluir tópicos. O exemplo abaixo mostra como um objeto **ServiceBusService** pode ser usado para criar um tópico chamado "TestTopic" com um namespace chamado "HowToSample":

    Configuration config = 
    	ServiceBusConfiguration.configureWithWrapAuthentication(
          "HowToSample",
          "your_service_bus_owner",
          "your_service_bus_key",
          ".servicebus.windows.net",
          "-sb.accesscontrol.windows.net/WRAPv0.9");

	ServiceBusContract service = ServiceBusService.create(config);
    TopicInfo topicInfo = new TopicInfo("TestTopic");
	try  
	{
    	CreateTopicResult result = service.createTopic(topicInfo);
	}
	catch (ServiceException e) {
		System.out.print("ServiceException encountered: ");
	    System.out.println(e.getMessage());
		System.exit(-1);
	}

Existem métodos em **TopicInfo** que permitem que as propriedades do tópico fila sejam ajustadas (por exemplo, para definir o valor da "vida útil" padrão a ser aplicado às mensagens enviadas ao tópico). O exemplo a seguir mostra como criar um tópico chamado "TestTopic" com um tamanho máximo de 5 GB:

    long maxSizeInMegabytes = 5120;  
	TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateTopicResult result = service.createTopic(topicInfo);

Observe que você pode usar o método **listTopics** em
objetos **ServiceBusContract:** para verificar se já existe um tópico com um nome especificado em um namespace de serviço.

## <a name="bkmk_HowToCreateSubscrip"> </a>Como criar assinaturas

As assinaturas de tópicos também são criadas com a classe **ServiceBusService**. As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens passadas para a fila virtual de assinaturas.

### Criar uma assinatura com o filtro padrão (MatchAll)

O filtro **MatchAll** é o filtro padrão usado se nenhum filtro for especificado quando uma nova assinatura for criada. Quando o filtro **MatchAll** é usado, todas as mensagens publicadas no tópico são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma assinatura chamada "AllMessages"' e usa o filtro padrão **MatchAll**.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result = 
        service.createSubscription("TestTopic", subInfo);

### Criar assinaturas com filtros

Também é possível configurar filtros que permitam atribuir um escopo a quais mensagens enviadas a um tópico devem ser exibidas dentro de uma assinatura específica do tópico.

O tipo mais flexível de filtro suportado por assinaturas é o
**SqlFilter**, que implementa um subconjunto de SQL92. Os filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para obter mais detalhes sobre as expressões que podem ser usadas com um filtro SQL, examine a sintaxe de SqlFilter.SqlExpression.

O exemplo a seguir cria uma assinatura chamada "HighMessages" com um
**SqlFilter** que seleciona somente mensagens com uma propriedade
**MessageNumber** personalizada maior que três:

    // Create a "HighMessages" filtered subscription  
	SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
    CreateSubscriptionResult result = 
		service.createSubscription("TestTopic", subInfo);
	RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
	ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
	CreateRuleResult ruleResult = 
		service.createRule("TestTopic", "HighMessages", ruleInfo);
    // Delete the default rule, otherwise the new rule won't be invoked.
    service.deleteRule("TestTopic", "HighMessages", "$Default");

De maneira semelhante, o exemplo a seguir cria uma assinatura chamada
"LowMessages" com   
 um SqlFilter que seleciona somente mensagens que tenham uma propriedade
MessageNumber menor   
 ou igual a três:

    // Create a "LowMessages" filtered subscription
	SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
	CreateSubscriptionResult result = 
		service.createSubscription("TestTopic", subInfo);
    RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
	ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
	CreateRuleResult ruleResult = 
		service.createRule("TestTopic", "LowMessages", ruleInfo);
    // Delete the default rule, otherwise the new rule won't be invoked.
    service.deleteRule("TestTopic", "LowMessages", "$Default");


Agora, quando uma mensagem for enviada a "TestTopic", ela sempre será entregue aos destinatários inscritos na assinatura do tópico "AllMessages" e será entregue de forma seletiva aos destinatários inscritos nas assinaturas dos tópicos "HighMessages" e "LowMessages" (dependendo do conteúdo da mensagem).

## <a name="bkmk_HowToSendMsgs"> </a>Como enviar mensagens a um tópico

Para enviar uma mensagem a um Tópico do Barramento de Serviço, seu aplicativo obterá um
objeto **ServiceBusContract**. O código abaixo demonstra como enviar uma mensagem ao tópico "TestTopic" que criamos acima em nosso namespace de serviço "HowToSample":

    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendTopicMessage("TestTopic", message);

As mensagens enviadas aos Tópicos do Barramento de Serviço são instâncias da
classe **BrokeredMessage**. Os objetos **BrokeredMessage** têm um conjunto de métodos padrão (como **setLabel** e **TimeToLive**), um dicionário usado para manter propriedades personalizadas do aplicativo personalizado e um corpo de dados de aplicativos arbitrários. Um aplicativo pode definir o corpo da mensagem passando qualquer objeto serializável para o construtor de
**BrokeredMessage**, e o **DataContractSerializer** adequado será usado para serializar o objeto. Como alternativa, um
O **java.io.InputStream** pode ser fornecido.

O exemplo a seguir demonstra como enviar cinco mensagens de teste para **MessageSender** de "TestTopic" que obtivemos no trecho de código acima.
Observe como o valor da propriedade **MessageNumber** de cada mensagem varia de acordo com a iteração do loop (isso determinará quais assinaturas a recebem):

    for (int i=0; i<5; i++)  {
       	// Create message, passing a string message for the body
		BrokeredMessage message = new BrokeredMessage("Test message " + i);
		// Set some additional custom app-specific property
		message.setProperty("MessageNumber", i);
		// Send message to the topic
		service.sendTopicMessage("TestTopic", message);
	}

Os tópicos do Service Bus suportam um tamanho máximo de 256 MB de mensagens (o cabeçalho, que inclui as propriedades padrão e personalizadas do aplicativo, pode ter um tamanho máximo de 64 MB). Não há nenhum limite sobre o número de mensagens mantidas em um tópico, mas há um limite de utilização para o tamanho total das mensagens mantidas por um tópico. O tamanho do tópico é definido no momento da criação, com um limite máximo de 5 GB.

## <a name="bkmk_HowToReceiveMsgs"> </a>Como receber mensagens de uma assinatura

A principal maneira de receber mensagens de uma assinatura é usar um
objeto **ServiceBusContract**. As mensagen srecebidas podem funcionar em dois modos diferentes: **ReceiveAndDelete** e **PeekLock**.

Ao usar o modo **ReceiveAndDelete**, o recebimento é uma operação única, - isto é, quando o Barramento de Serviço recebe uma solicitação de leitura de uma mensagem, ele marca a mensagem como sendo consumida e a retorna para o aplicativo. O modo **ReceiveAndDelete** é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo pode tolerar o não processamento de uma mensagem em caso de falha. Para entender como isso funciona, considere um cenário no qual o consumidor emite a solicitação de recebimento e uma falha ocorre antes do processamento da solicitação. Como o Barramento de Serviço terá marcado a mensagem
como consumida, quando o aplicativo for reiniciado e começar a consumir
as mensagens novamente, ele terá perdido a mensagem que
foi consumida antes da falha.

No modo **PeekLock**, o recebimento se torna uma operação de dois estágios, o que torna possível o suporte a aplicativos que não podem tolerar mensagens ausentes. Quando o Service Bus recebe uma solicitação, ele localiza a próxima mensagem a ser consumida, ele a bloqueia para evitar que outros consumidores a recebam e depois a retorna para o aplicativo. Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento chamando **Delete** na mensagem recebida. Quando o Barramento de
Serviço vê a chamada **Delete**, ele marca a mensagem como sendo
consumida e a remove do tópico.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e processadas usando o modo **PeekLock** (não o modo padrão). O exemplo a seguir executa um loop e processa mensagens na assinatura "HighMessages" e, em seguida, sai quando não há mais mensagens (como alternativa, pode ser configurado para aguardar novas mensagens).

	try
	{
		ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
		opts.setReceiveMode(ReceiveMode.PEEK_LOCK);
	
		while(true)  { 
		    ReceiveSubscriptionMessageResult  resultSubMsg = 
		        service.receiveSubscriptionMessage("TestTopic", "HighMessages", opts);
		    BrokeredMessage message = resultSubMsg.getValue();
		    if (message != null && message.getMessageId() != null)
		    {
			    System.out.println("MessageID: " + message.getMessageId());    
			    // Display the topic message.
			    System.out.print("From topic: ");
			    byte[] b = new byte[200];
			    String s = null;
			    int numRead = message.getBody().read(b);
			    while (-1 != numRead)
	            {
	                s = new String(b);
	                s = s.trim();
	                System.out.print(s);
	                numRead = message.getBody().read(b);
			    }
	            System.out.println();
			    System.out.println("Custom Property: " + 
			        message.getProperty("MessageNumber"));
			    // Delete message.
			    System.out.println("Deleting this message.");
			    service.deleteMessage(message);
		    }  
		    else  
		    {        
		        System.out.println("Finishing up - no more messages.");        
		        break; 
		        // Added to handle no more messages.
		        // Could instead wait for more messages to be added.
		    }
	    }
	}
	catch (ServiceException e) {
	    System.out.print("ServiceException encountered: ");
	    System.out.println(e.getMessage());
	    System.exit(-1);
	}
	catch (Exception e) {
	    System.out.print("Generic exception encountered: ");
	    System.out.println(e.getMessage());
	    System.exit(-1);
	} 

## <a name="bkmk_HowToHandleAppCrash"> </a>Como tratar falhas do aplicativo e mensagens ilegíveis

O Service Bus proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros em seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um
aplicativo receptor não for capaz de processar a mensagem por algum motivo,
ele chamará o método **unlockMessage** na mensagem recebida (em vez do
método **deleteMessage**). Isso fará com que o Barramento de Serviço desbloqueie a mensagem no tópico e a disponibilize para que possa ser recebida novamente, tanto pelo mesmo aplicativo de consumo quanto por outro.

Também existe um tempo limite associado a uma mensagem bloqueada no tópico, e se o aplicativo não puder processar a mensagem antes da expiração do tempo limite do bloqueio (por exemplo, se houver falha no aplicativo), o Barramento de Serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Se houver falha no aplicativo após o processamento da mensagem, mas antes da solicitação **deleteMessage** ser enviada, a mensagem será entregue novamente ao aplicativo quando ele for reiniciado. Isso é frequentemente chamado de **Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez, mas, em algumas situações, a mesma mensagem pode ser entregue novamente. Se o cenário não tolerar processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega da mensagem duplicada. Isso geralmente é obtido com o método **getMessageId** da mensagem, que permanecerá constante nas tentativas da entrega.

## <a name="bkmk_HowToDeleteTopics"> </a>Como excluir tópicos e assinaturas

A principal maneira de excluir tópicos e assinaturas é usar um
objeto **ServiceBusContract**. Excluir um tópico também excluirá todas as assinaturas que estiverem registradas com o tópico. As assinaturas também podem ser excluídas de forma independente.

    // Delete subscriptions
    service.deleteSubscription("TestTopic", "AllMessages");
    service.deleteSubscription("TestTopic", "HighMessages");
    service.deleteSubscription("TestTopic", "LowMessages");

    // Delete a topic
	service.deleteTopic("TestTopic");

# <a name="bkmk_NextSteps"> </a>Próximas etapas

Agora que você aprendeu as noções básicas sobre filas do Barramento de Serviço, consulte o tópico do MSDN [Filas, tópicos e assinaturas do Barramento de Serviço][] para obter mais informações.

  [SDK do Azure para Java]: http://www.windowsazure.com/pt-br/develop/java/
  [O que são os tópicos e as assinaturas do Service Bus?]: #what-are-service-bus-topics
  [Criar um namespace de serviço]: #create-a-service-namespace
  [Obter as credenciais de gerenciamento padrão do namespace]: #obtain-default-credentials
  [Configurar seu aplicativo para usar o Service Bus]: #bkmk_ConfigYourApp
  [Como criar um tópico]: #bkmk_HowToCreateTopic
  [Como criar assinaturas]: #bkmk_HowToCreateSubscrip
  [Como enviar mensagens a um tópico]: #bkmk_HowToSendMsgs
  [Como receber mensagens de uma assinatura]: #bkmk_HowToReceiveMsgs
  [Como tratar falhas do aplicativo e mensagens ilegíveis]: #bkmk_HowToHandleAppCrash
  [Como excluir tópicos e assinaturas]: #bkmk_HowToDeleteTopics
  [Próximas etapas]: #bkmk_NextSteps
  [Diagrama de tópicos do Barramento de Serviço]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com/
  [Captura de tela de nó do Barramento de Serviço]: ../../../DevCenter/dotNet/Media/sb-queues-03.png
  [Criar um novo namespace ]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
  [Captura de tela da lista de namespaces]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
  [Captura de tela do painel Propriedades]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
  [Captura de tela de chave padrão]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
  [Filas, tópicos e assinaturas do Barramento de Serviço]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx

