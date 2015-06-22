<properties 
	pageTitle="Como usar tópicos do barramento de serviço (Java) - Azure" 
	description="Aprenda a usar assinaturas e tópicos do barramento de serviço no Azure. Exemplos de código são escritos para aplicativos Java." 
	services="service-bus" 
	documentationCenter="java" 
	authors="sethmanheim" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="sethm"/>

# Como usar os tópicos/assinaturas do Barramento de Serviço

Este guia descreve como usar assinaturas e tópicos do barramento de serviço. Os exemplos são escritos em Java e usam o [SDK do Azure para Java][]. Os cenários abordados incluem **criar de tópicos e assinaturas**, ** criar de filtros de assinatura**, **enviar mensagens para um tópico**, **receber mensagens de uma assinatura**, e
**excluir tópicos e assinaturas**.

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## Configurar seu aplicativo para usar o Barramento de serviço

Adicione as seguintes instruções de importação à parte superior do arquivo Java:

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;
    import javax.xml.datatype.*;

Adicione as Bibliotecas do Azure para Java a seu caminho de compilação e inclua-o em seu assembly de implantação do projeto.

## Como criar um tópico

As operações de gerenciamento dos tópicos do Barramento de Serviço podem ser executadas pela classe**ServiceBusContract**. Um objeto **ServiceBusContract** é construído com uma configuração adequada que encapsula as permissões de token para gerenciá-lo, e a classe **ServiceBusContract** é o único ponto de comunicação com o Azure.

A classe **ServiceBusService** oferece métodos para criar, enumerar e excluir tópicos. O exemplo abaixo mostra como um objeto **ServiceBusService**pode ser usado para criar um tópico chamado "TestTopic" com um namespace chamado "HowToSample":

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

Existem métodos em **TopicInfo** que permitem propriedades do tópico a ser ajustado (por exemplo: para definir o valor padrão de "vida útil" a ser aplicado às mensagens enviadas para o tópico). O exemplo a seguir mostra como criar um tópico chamado "TestTopic" com um tamanho máximo de 5 GB:

    long maxSizeInMegabytes = 5120;  
	TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateTopicResult result = service.createTopic(topicInfo);

Observe que você pode usar o método **listTopics** em
objetos **ServiceBusRestProxy** para verificar se já existe um tópico com um nome especificado em um namespace de serviço.

## Como criar assinaturas

As assinaturas de tópicos também são criadas com a classe **ServiceBusService**. As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens passadas para a fila virtual da assinatura.

### Criar uma assinatura com o filtro padrão (MatchAll)

O filtro **MatchAll** é o filtro padrão usado se nenhum filtro for especificado quando uma nova assinatura for criada. Quando o filtro **MatchAll** é usado, todas as mensagens publicadas no tópico são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma assinatura denominada "AllMessages" e usa o filtro padrão **MatchAll**.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result = 
        service.createSubscription("TestTopic", subInfo);

### Criar assinaturas com os filtros

Você também pode configurar filtros que permitem atribuir um escopo a quais mensagens enviadas a um tópico devem aparecer dentro de uma assinatura específica do tópico.

O tipo de filtro mais flexível compatível com as assinaturas é o **SqlFilter**, que implementa um subconjunto do SQL92. Os filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para obter mais detalhes sobre as expressões que podem ser usadas com um filtro SQL, examine a sintaxe SqlFilter.SqlExpression.

O exemplo abaixo cria uma assinatura denominada "HighMessages" com um **SqlFilter** que seleciona apenas as mensagens que tenham uma propriedade **MessageNumber** personalizada maior que 3:

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

De maneira semelhante, o exemplo a seguir cria uma assinatura denominada "LowMessages" com um SqlFilter que seleciona apenas as mensagens que tenham uma propriedade MessageNumber menor ou igual a 3:

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


Quando uma mensagem for enviada para "TestTopic", ela sempre será fornecida aos destinatários inscritos na assinatura do tópico "AllMessages" e será fornecida de forma seletiva para os destinatários inscritos nas assinaturas dos tópicos "HighMessages" e "LowMessages" (dependendo do conteúdo da mensagem).

## Como enviar mensagens a um tópico

Para enviar uma mensagem a um Tópico do Barramento de Serviço, seu aplicativo obterá um
objeto **ServiceBusContract**. O código abaixo demonstra como enviar uma mensagem ao tópico "TestTopic" que criamos acima em nosso
namespace de serviço "HowToSample":

    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendTopicMessage("TestTopic", message);

As mensagens enviadas aos Tópicos do Barramento de Serviço são instâncias da
classe **BrokeredMessage**. Objetos **BrokeredMessage** possuem um conjunto de métodos padrão (como **setLabel** e **TimeToLive**), um dicionário usado para manter propriedades específicas do aplicativo personalizado e um corpo de dados arbitrários do aplicativo. Um aplicativo pode definir o corpo da mensagem, passando qualquer objeto serializável para o construtor da**BrokeredMessage**e o**DataContractSerializer** adequado então será usado para serializar o objeto. Como alternativa, um
**java.io.InputStream** pode ser fornecido.

O exemplo a seguir demonstra como enviar cinco mensagens de teste para **MessageSender** de "TestTopic" que obtivemos no trecho de código acima.
Observe como o valor da propriedade **MessageNumber** de cada mensagem varia de acordo com a iteração do loop (isso determinará qual assinatura o receberá):

    for (int i=0; i<5; i++)  {
       	// Create message, passing a string message for the body
		BrokeredMessage message = new BrokeredMessage("Test message " + i);
		// Set some additional custom app-specific property
		message.setProperty("MessageNumber", i);
		// Send message to the topic
		service.sendTopicMessage("TestTopic", message);
	}

Os tópicos de Barramento de Serviço oferecem suporte a um tamanho máximo de mensagem de 256 MB (o cabeçalho, que inclui as propriedades do aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 MB). Não há nenhum limite no número de mensagens mantidas em um tópico mas há uma capacidade do tamanho total das mensagens mantidas por um tópico. O tamanho do tópico é definido no momento da criação, com um limite máximo de 5 GB.

## Como receber mensagens de uma assinatura

A principal maneira de receber mensagens de uma assinatura é usar um objeto **ServiceBusContract**. As mensagens recebidas podem funcionar de dois modos diferentes: **ReceiveAndDelete** e **PeekLock**.

Ao usar o modo **ReceiveAndDelete**, o recebimento é uma operação única, isto é, quando o Barramento de Serviço recebe uma solicitação de leitura de uma mensagem, ele marca a mensagem como sendo consumida e a retorna para o aplicativo. **O modo ReceiveAndDelete** é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Barramento de Serviço terá marcado a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

No modo **PeekLock**, o recebimento de uma mensagem se torna uma operação de dois estágios, o que possibilita o suporte aos aplicativos que não podem tolerar mensagens ausentes. Quando o Barramento de Serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, a bloqueia para evitar que outros clientes a recebam e a retorna para o aplicativo. Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento chamando **Delete** na mensagem recebida. Quando o Barramento de Serviço vê a chamada **Delete**, ele marca a mensagem como sendo consumida e a remove do tópico.

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

## Como tratar falhas do aplicativo e mensagens ilegíveis

O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não for capaz de processar a mensagem por algum motivo, ele chamará o método **unlockMessage** na mensagem recebida (em vez do método **deleteMessage**). Isso fará com que o Barramento de Serviço desbloqueie a mensagem no tópico e a disponibilize para que seja recebida novamente pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada no tópico e, se o aplicativo não conseguir processar a mensagem antes do tempo limite do bloqueio expirar (por exemplo, em caso de falha do aplicativo), o Barramento de Serviço desbloqueará a mensagem automaticamente, disponibilizando-a para ser recebida novamente.

Se houver falha do aplicativo após o processamento da mensagem, mas antes da solicitação **deleteMessage** ser emitida, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Isso é frequentemente chamado de **Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez mas, em algumas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega de mensagem duplicada. Isso geralmente é feito com o método **getMessageId** da mensagem, que permanecerá constante nas tentativas da entrega.

## Como excluir tópicos e assinaturas

A principal maneira de excluir tópicos e assinaturas é usar um objeto**ServiceBusContract**. A exclusão de um tópico também excluirá todas as assinaturas registradas com o tópico. As assinaturas também podem ser excluídas de forma independente.

    // Delete subscriptions
    service.deleteSubscription("TestTopic", "AllMessages");
    service.deleteSubscription("TestTopic", "HighMessages");
    service.deleteSubscription("TestTopic", "LowMessages");

    // Delete a topic
	service.deleteTopic("TestTopic");

## Próximas etapas

Agora que você aprendeu as noções básicas sobre filas do Barramento de Serviço, consulte o tópico do MSDN [Filas, tópicos e assinaturas do Barramento de Serviço][] para obter mais informações.

  [SDK do Azure para Java]: http://azure.microsoft.com/develop/java/
  [O que são os tópicos e as assinaturas do Barramento de Serviço?]: #what-are-service-bus-topics
  [Criar um namespace de serviço]: #create-a-service-namespace
  [Obter as Credenciais de Gerenciamento Padrão para o Namespace]: #obtain-default-credentials
  [Configurar seu aplicativo para usar o Barramento de serviço]: #bkmk_ConfigYourApp
  [Como: Criar um tópico]: #bkmk_HowToCreateTopic
  [Como: Criar assinaturas]: #bkmk_HowToCreateSubscrip
  [Como: Enviar mensagens para um tópico]: #bkmk_HowToSendMsgs
  [Como: Receber mensagens de uma assinatura]: #bkmk_HowToReceiveMsgs
  [Como: Tratar falhas do aplicativo e mensagens ilegíveis]: #bkmk_HowToHandleAppCrash
  [Como: Excluir tópicos e assinaturas]: #bkmk_HowToDeleteTopics
  [Próximas etapas]: #bkmk_NextSteps
  [Diagramas dos tópicos do Barramento de Serviço]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com/
  [Captura de tela do nó do Barramento de Serviço]: ../../../DevCenter/dotNet/Media/sb-queues-03.png
  [Criar um novo namespace ]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
  [Captura de tela da lista de namespaces]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
  [Captura de tela do painel Propriedades]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
  [Captura de tela da chave padrão]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
  [Filas, tópicos e assinaturas do Barramento de Serviço]: http://msdn.microsoft.com/library/hh367516.aspx

<!--HONumber=47-->
 