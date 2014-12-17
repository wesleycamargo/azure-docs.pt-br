<properties urlDisplayName="Service Bus Topics" pageTitle="Como usar tópicos de Barramento de Serviço (Java) - Azure" metaKeywords="Introdução a tópicos de barramento de serviço do Azure, Introdução a tópicos de Barramento de Serviço, publicar mensagens de assinar do Azure, tópicos de mensagens e assinatura do Azure, tópico de Barramento de Serviço Java" description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for Java applications." metaCanonical="" services="service-bus" documentationCenter="Java" title="How to Use Service Bus Topics/Subscriptions" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# Como usar os tópicos e as assinaturas do Service Bus

Este guia mostra como usar os tópicos e as assinaturas do Barramento de Serviço
. Os exemplos são escritos em Java e usam o [SDK do Azure para Java][]. Os cenários abordados incluem os **tópicos de criação
e assinaturas**, **filtros de assinatura de criação**, **envio de
mensagens para um tópico**, **recebimento de mensagens de uma assinatura** e
**excluir tópicos e assinaturas**.

## Sumário

-   [O que são os tópicos e as assinaturas do Service Bus?][]
-   [Criar um namespace de serviço][]
-   [Obter as credenciais de gerenciamento padrão do namespace][]
-   [Configurar seu aplicativo para usar o Service Bus][]
-   [Como: Criar um tópico][]
-   [Como: criar assinaturas][]
-   [Como: enviar mensagens para um tópico][]
-   [Como: Receber mensagens de uma assinatura][]
-   [Como: Tratar falhas do aplicativo e mensagens ilegíveis][]
-   [Como: excluir tópicos e assinaturas][]
-   [Próximas etapas][]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## <a name="bkmk_ConfigYourApp"> </a>Configurar seu aplicativo para usar o Service Bus

Adicione as seguintes instruções de importação à parte superior do arquivo Java:

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;
    import javax.xml.datatype.*;

Adicione as Bibliotecas do Azure para Java a seu caminho de compilação e inclua-o em seu assembly de implantação do projeto.

## <a name="bkmk_HowToCreateTopic"> </a>Como criar um tópico

As operações de gerenciamento dos tópicos do Barramento de Serviço podem ser executadas por meio da
classe **ServiceBusContract**. Um objeto **ServiceBusContract** é
construído com uma configuração adequada que encapsula as
permissões de token para gerenciá-las e a classe **ServiceBusContract** é
o único ponto de comunicação com o Azure.

A classe **ServiceBusService** fornece métodos para os tópicos de criar, enumerar
e excluir. O exemplo a seguir mostra como um objeto **ServiceBusService**
pode ser usado para criar um tópico chamado "TestTopic" com um namespace chamado "HowToSample":

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

Há métodos em **TopicInfo** que permitem que as propriedades do tópico
sejam ajustadas (por exemplo: para definir o valor de "time-to-live" padrão a ser
aplicado às mensagens enviadas ao tópico). A exemplo a seguir mostra como
criar um tópico chamado "TestTopic" com um tamanho máximo de 5 GB:

    long maxSizeInMegabytes = 5120;  
	TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateTopicResult result = service.createTopic(topicInfo);

Observe que é possível usar o método **listTopics** em objetos
**ServiceBusContract** para verificar se um tópico com um nome especificado
já existe no namespace de serviço.

## <a name="bkmk_HowToCreateSubscrip"> </a>Como criar assinaturas

As assinaturas do tópico também são criadas com a classe **ServiceBusService**
. As assinaturas são nomeadas e podem ter um filtro opcional que
restringe o conjunto de mensagens passadas para a fila virtual
de assinaturas.

### Criar uma assinatura com o filtro padrão (MatchAll)

O filtro **MatchAll** é o filtro padrão usado se nenhum filtro
for especificado quando uma nova assinatura é criada. Quando o filtro **MatchAll**
é usado, todas as mensagens publicadas no tópico são colocadas na
fila virtual da assinatura. O código a seguir cria uma
assinatura chamada "AllMessages" e usa o filtro padrão **MatchAll**
.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result = 
        service.createSubscription("TestTopic", subInfo);

### Criar assinaturas com os filtros

Você também pode configurar filtros que permitam definir o escopo de quais mensagens enviadas
para um tópico devem aparecer dentro de uma assinatura de tópico específica.

O tipo mais flexível de filtro suportado por assinaturas é o
**SqlFilter** que implementa um subconjunto do SQL92. Filtros SQL operam
nas propriedades das mensagens publicadas para o tópico. Para
obter mais detalhes sobre as expressões que podem ser usados com um filtro SQL,
examine a sintaxe SqlFilter.SqlExpression.

O exemplo a seguir cria uma assinatura chamada "HighMessages" com um
**SqlFilter** que seleciona somente mensagens com uma propriedade
**Propriedade MessageNumber** maior que 3:

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

Da mesma forma, o exemplo a seguir cria uma assinatura denominada
"LowMessages" com   
 um SqlFilter que seleciona somente mensagens com uma propriedade MessageNumber
menor   
 ou igual a 3:

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


Quando uma mensagem agora é enviada a "TestTopic", ela sempre será
entregue aos destinatários inscritos para a assinatura de tópico "AllMessages"
e entregues de forma seletiva aos destinatários inscritos para as
assinaturas de tópico "HighMessages" e "LowMessages" (dependendo do
conteúdo da mensagem).

## <a name="bkmk_HowToSendMsgs"> </a>Como enviar mensagens a um tópico

Para enviar uma mensagem a um Tópico do Barramento de Serviço, seu aplicativo obterá um
objeto **ServiceBusContract**. O código abaixo demonstra como enviar uma
mensagem para o tópico "TestTopic" criado anteriormente no
namespace de serviço "HowToSample":

    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendTopicMessage("TestTopic", message);

As mensagens enviadas aos Tópicos do Barramento de Serviço são instâncias da
classe **BrokeredMessage**. **Objetos BrokeredMessage** têm um conjunto de
métodos padrão (como **setLabel** e **TimeToLive**), um dicionário
que é usado para armazenar propriedades específicas de aplicativo personalizado e um corpo
de dados arbitrários do aplicativo. Um aplicativo pode definir o corpo da
mensagem passando qualquer objeto serializável para o construtor de
**BrokeredMessage**, e o **DataContractSerializer** adequado então será
usado para serializar o objeto. Como alternativa, um
**java.io.InputStream** pode ser fornecido.

O exemplo a seguir demonstra como enviar cinco mensagens de teste para
"TestTopic" **MessageSender** obtido no trecho de código acima.
Observe como o valor da propriedade **MessageNumber** de cada mensagem varia na
iteração do loop (isso determinará quais assinaturas
o recebem):

    for (int i=0; i<5; i++)  {
       	// Create message, passing a string message for the body
		BrokeredMessage message = new BrokeredMessage("Test message " + i);
		// Set some additional custom app-specific property
		message.setProperty("MessageNumber", i);
		// Send message to the topic
		service.sendTopicMessage("TestTopic", message);
	}

Tópicos do barramento de serviço têm suporte para um tamanho máximo da mensagem de 256 MB (o cabeçalho,
que inclui as propriedades padrão e personalizadas do aplicativo, pode ter
um tamanho máximo de 64 MB). Não há nenhum limite ao número de mensagens
mantidas em um tópico, mas há um limite ao tamanho total das mensagens
mantidas por um tópico. O tamanho do tópico é definido no momento da criação, com um
limite superior de 5 GB.

## <a name="bkmk_HowToReceiveMsgs"> </a>Como receber mensagens de uma assinatura

A principal maneira de receber mensagens de uma assinatura é usar um
objeto **ServiceBusContract**. Mensagens recebidas podem funcionar em dois
modos diferentes: **ReceiveAndDelete** e **PeekLock**.

Ao usar o modo **ReceiveAndDelete**, receber é uma operação de disparo único
- ou seja, quando o barramento de serviço recebe uma solicitação de leitura de uma
mensagem, ele marca a mensagem como sendo consumida e a retorna para o
aplicativo. **O modo ReceiveAndDelete** é o modelo mais simples e funciona
melhor para cenários em que um aplicativo pode tolerar o não processamento uma
mensagem em caso de falha. Para entender isso, considere um
cenário em que o consumidor emita a solicitação de recebimento e, em seguida,
falhe antes de processá-la. Como o barramento de serviço terá marcado a
mensagem como tendo sido consumida, quando o aplicativo for reiniciado e começar
consumir a mensagem novamente, ele terá perdido a mensagem que foi
consumida antes da falha.

No modo **PeekLock**, o recebimento se torna uma operação de dois estágios, o que torna
possível o suporte a aplicativos que não podem tolerar mensagens
faltando. Quando o barramento de serviço recebe uma solicitação, ele localiza a próxima mensagem
a ser consumida, bloqueia-a para evitar que outros consumidores as recebam e
retorna para o aplicativo. Depois de o aplicativo terminar de
processar a mensagem (ou armazená-la de forma confiável para processamento futuro), ele
conclui a segunda etapa do processo de recebimento chamando **Excluir**
na mensagem recebida. Quando o barramento de serviço vê a chamada **Excluir**, ele
marca a mensagem tendo sido sendo consumida e remove-a do tópico.

O exemplo a seguir demonstra como as mensagens podem ser recebidas
e processadas usando o modo **PeekLock** (não o modo padrão). O exemplo
a seguir executa um loop e processa mensagens na assinatura "HighMessages" e, em seguida, sai quando não há mais mensagens (como alternativa, pode ser configurado para aguardar novas mensagens).

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

O barramento de serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos
erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um
aplicativo de receptor não puder processar a mensagem por algum motivo,
ele pode chamar o método **unlockMessage** na mensagem recebida
(em vez do método **deleteMessage**). Isso fará com que o barramento de serviço
desbloqueie a mensagem dentro do tópico e a disponibilize para ser
recebida novamente, seja pelo mesmo aplicativo de consumo ou por outro
aplicativo de consumo.

Também há um tempo limite associado a uma mensagem bloqueada no
tópico e, se o aplicativo falhar em processar a mensagem antes de
o tempo limite de bloqueio expirar (por exemplo, se o aplicativo falhar), o barramento de serviço
desbloqueará a mensagem automaticamente e a disponibilizará para ser
recebida novamente.

No caso de o aplicativo falhar após processar a mensagem
, mas antes de a solicitação **deleteMessage** ser emitida, a mensagem
será entregue novamente ao aplicativo quando ele for reiniciado. Isso geralmente é
chamado**Processamento de pelo menos uma vez**, ou seja, cada mensagem será
processada pelo menos uma vez, mas em determinadas situações, a mesma mensagem pode
ser entregue novamente. Se o cenário não tolerar processamento duplicado,
os desenvolvedores de aplicativos deverão adicionar lógica extra a seus
aplicativos para tratar a entrega de mensagens duplicadas. Isso geralmente é obtido
usando o método **getMessageId** da mensagem, que permanecerá
constante nas tentativas da entrega.

## <a name="bkmk_HowToDeleteTopics"> </a>Como excluir tópicos e assinaturas

A principal maneira de excluir tópicos e assinaturas é usar um
objeto **ServiceBusContract**. Excluir um tópico também exclui todas as assinaturas registradas
com o tópico. As assinaturas também podem ser excluídas de forma independente.

    // Delete subscriptions
    service.deleteSubscription("TestTopic", "AllMessages");
    service.deleteSubscription("TestTopic", "HighMessages");
    service.deleteSubscription("TestTopic", "LowMessages");

    // Delete a topic
	service.deleteTopic("TestTopic");

# <a name="bkmk_NextSteps"> </a>Próximas etapas

Agora que você aprendeu os conceitos básicos das filas do barramento de serviço, consulte o tópico MSDN
de [Filas, tópicos e assinaturas de barramento de serviço][] para obter mais informações.

  [SDK do Azure para Java]: http://www.windowsazure.com/pt-br/develop/java/
  [O que são os tópicos e as assinaturas do Service Bus?]: #what-are-service-bus-topics
  [Criar um namespace de serviço]: #create-a-service-namespace
  [Obter as credenciais de gerenciamento padrão do namespace]: #obtain-default-credentials
  [Configurar seu aplicativo para usar o Service Bus]: #bkmk_ConfigYourApp
  [Como: Criar um tópico]: #bkmk_HowToCreateTopic
  [Como: criar assinaturas]: #bkmk_HowToCreateSubscrip
  [Como: enviar mensagens para um tópico]: #bkmk_HowToSendMsgs
  [Como: Receber mensagens de uma assinatura]: #bkmk_HowToReceiveMsgs
  [Como: Tratar falhas do aplicativo e mensagens ilegíveis]: #bkmk_HowToHandleAppCrash
  [Como: excluir tópicos e assinaturas]: #bkmk_HowToDeleteTopics
  [Próximas etapas]: #bkmk_NextSteps
  [Diagrama dos tópicos de Service Bus]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com/
  [Captura de tela do nó do Service Bus]: ../../../DevCenter/dotNet/Media/sb-queues-03.png
  [Criar um novo namespace ]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
  [Captura de tela da lista de namespaces]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
  [Captura de tela do painel Propriedades]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
  [Captura de tela da chave padrão]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
  [Filas, tópicos e assinaturas de barramento de serviço]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx
