<properties linkid="dev-java-how-to-service-bus-queues" urlDisplayName="Filas do Service Bus" pageTitle="Como usar filas do Service Bus (Java) - Azure" metaKeywords="Filas do Service Bus do Azure, filas do Azure, mensagens do Azure, Java de filas do Azure" description="Saiba como usar as filas do Service Bus no Azure. Amostras de código escritas Java" metaCanonical="" services="service-bus" documentationCenter="Java" title="Como usar filas do Service Bus" authors="waltpo" solutions="" manager="" editor="mollybos" />








# Como usar as filas do Service Bus

Este guia mostrará como usar as filas do Service Bus. As amostras são escritas em Java e usam o [SDK do Azure para Java][]. Os cenários abrangidos incluem **criar filas**, **enviar e receber mensagens** e **excluir filas**.

## Sumário

-   [O que são as filas do Service Bus?][]
-   [Criar um namespace de serviço][]
-   [Obter as credenciais de gerenciamento padrão do namespace][]
-   [Configurar seu aplicativo para usar o Service Bus][]
-   [Como criar um Provedor de token de segurança][]
-   [Como criar uma fila][How to: Create a Security Token Provider]
-   [Como enviar mensagens a uma fila][]
-   [Como receber mensagens de uma fila][]
-   [Como tratar falhas do aplicativo e mensagens ilegíveis][]
-   [Próximas etapas][]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <a name="bkmk_ConfigApp"> </a>Configurar seu aplicativo para usar o Service Bus

Adicione as seguintes instruções de importação à parte superior do arquivo Java:

	// Include the following imports to use service bus APIs
	import com.microsoft.windowsazure.services.serviceBus.*;
	import com.microsoft.windowsazure.services.serviceBus.models.*; 
	import com.microsoft.windowsazure.services.core.*; 
	import javax.xml.datatype.*;
	
## <a name="bkmk_HowToCreateQueue"> </a>Como criar uma fila

Você pode executar operações de gerenciamento de filas do Service Bus por meio da
classe **ServiceBusContract**. O objeto **ServiceBusContract** é criado com uma configuração adequada que encapsula as permissões de token para gerenciá-la, e a classe **ServiceBusContract** é o único ponto de comunicação com o Azure.

A classe **ServiceBusService** fornece métodos para criar, enumerar e excluir filas. O exemplo abaixo mostra como um objeto **ServiceBusService** pode ser usado para criar uma fila denominada "TestQueue" com um namespace chamado "HowToSample":

    Configuration config = 
    	ServiceBusConfiguration.configureWithWrapAuthentication(
          "HowToSample",
          "your_service_bus_owner",
          "your_service_bus_key",
          ".servicebus.windows.net",
          "-sb.accesscontrol.windows.net/WRAPv0.9");

    ServiceBusContract service = ServiceBusService.create(config);
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    try
    {     
		CreateQueueResult result = service.createQueue(queueInfo);
    }
	catch (ServiceException e)
	{
	    System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Existem métodos em QueueInfo que permitem que as propriedades
da fila sejam ajustadas (por exemplo, para definir o valor da "vida útil" padrão a ser aplicado às mensagens enviadas para a fila). O exemplo a seguir mostra como criar uma fila denominada "TestQueue" com um tamanho máximo de 5 GB:

    long maxSizeInMegabytes = 5120;
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateQueueResult result = service.createQueue(queueInfo);

Observe que você pode usar o método **listQueues** em **ServiceBusContract**
para verificar se já existe uma fila com um nome especificado dentro de um
namespace de serviço.

## <a name="bkmk_HowToSendMsgs"> </a>Como enviar mensagens a uma fila

Para enviar uma mensagem a uma Fila do Service Bus, seu aplicativo obterá um
objeto **ServiceBusContract**. O código abaixo demonstra como enviar uma mensagem para a fila "TestQueue" que criamos acima dentro do nosso namespace de serviço "HowToSample":

    try
    {
        BrokeredMessage message = new BrokeredMessage("MyMessage");
        service.sendQueueMessage("TestQueue", message);
    }
    catch (ServiceException e) 
    {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

As mensagens enviadas para (e recebidas das) filas do Service Bus são instâncias da classe **BrokeredMessage**. Os objetos **BrokeredMessage** têm um conjunto de métodos padrão (como **getLabel**, **getTimeToLive**
**setLabel** e **setTimeToLive**), um dicionário usado para manter propriedades personalizadas específicas do aplicativo e um corpo de dados arbitrários do aplicativo. Um aplicativo pode definir o corpo da mensagem transmitindo qualquer objeto serializável para o construtor de
**BrokeredMessage** e o serializador adequado será usado para serializar o objeto. Como alternativa, um **java.E/S.InputStream** pode ser fornecido.

O exemplo a seguir demonstra como enviar cinco mensagens de teste para **MessageSender** de "TestQueue" que obtivemos no trecho de código acima:

    for (int i=0; i<5; i++)
    {
         // Create message, passing a string message for the body.
         BrokeredMessage message = new BrokeredMessage("Test message " + i);
         // Set an additional app-specific property.
         message.setProperty("MyProperty", i); 
         // Send message to the queue
         service.sendQueueMessage("TestQueue", message);
    }

As filas do Service Bus suportam um tamanho máximo de mensagem de 256 KB (o cabeçalho,
que inclui as propriedades padrão e personalizadas do aplicativo, pode ter um
tamanho máximo de 64 KB). Não há nenhum limite no número de mensagens
mantidas em uma fila mas há uma capacidade do tamanho total das mensagens
mantidas por uma fila. O tamanho da fila é definido no momento da criação, com um limite superior de 5 GB.

## <a name="bkmk_HowToReceiveMsgs"> </a>Como receber mensagens de uma fila

A principal maneira para receber mensagens de uma fila é usar um
objeto **ServiceBusContract**. As mensagen recebidas podem funcionar de dois modos diferentes: **ReceiveAndDelete** e **PeekLock**.

Ao usar o modo **ReceiveAndDelete**, o recebimento é uma operação única, isto é, quando o Service Bus recebe uma solicitação de leitura de uma mensagem em uma fila, ele marca a mensagem como sendo consumida e a retorna para o aplicativo. O modo **ReceiveAndDelete** (que é o modelo padrão) é o modelo mais simples e funciona melhor em cenários nos quais o aplicativo possa tolerar o não processamento de uma mensagem em caso de falha. Para entender como isso funciona, considere um cenário no qual o consumidor emite a solicitação de recebimento e ocorre uma falha antes de processá-la.
Como o Service Bus terá marcado a mensagem
como consumida, quando o aplicativo for reiniciado e começar a consumir
as mensagens novamente, ele terá perdido a mensagem que
foi consumida antes da falha.

No modo **PeekLock**, o recebimento se torna uma operação de dois estágios, o que torna possível o suporte a aplicativos que não podem tolerar mensagens ausentes. Quando o Service Bus recebe uma solicitação, ele localiza a próxima mensagem a ser consumida, ele a bloqueia para evitar que outros consumidores a recebam e depois a retorna para o aplicativo. Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento chamando **Delete** na mensagem recebida. Quando o Service
Bus vê a chamada **Delete**, ele marca a mensagem como sendo
consumida e remove-a da fila.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e processadas usando o modo **PeekLock** (não o modo padrão). O exemplo abaixo executa um loop infinito e processa mensagens assim que elas chegam em "TestQueue":

    	try
	{
		ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
		opts.setReceiveMode(ReceiveMode.PEEK_LOCK);
	
		while(true)  { 
	         ReceiveQueueMessageResult resultQM = 
	     			service.receiveQueueMessage("TestQueue", opts);
		    BrokeredMessage message = resultQM.getValue();
		    if (message != null && message.getMessageId() != null)
		    {
			    System.out.println("MessageID: " + message.getMessageId());    
			    // Display the queue message.
			    System.out.print("From queue: ");
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
			        message.getProperty("MyProperty"));
			    // Remove message from queue.
			    System.out.println("Deleting this message.");
			    //service.deleteMessage(message);
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

## <a name="bkmk_HowToHandleAppCrashes"> </a>Como tratar falhas do aplicativo e mensagens ilegíveis

O Service Bus proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros em seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um
aplicativo receptor não for capaz de processar a mensagem por algum motivo,
ele chamará o método **unlockMessage** na mensagem recebida (em vez do
método **deleteMessage**). Isso fará com que o Service Bus desbloqueie a
mensagem na fila e disponibilize-a para que ela possa ser recebida novamente,
pelo mesmo aplicativo de consumo ou por algum outro
aplicativo de consumo.

Também existe um tempo limite associado a uma mensagem bloqueada na fila, e se o aplicativo falhar em processar a mensagem antes da expiração do tempo limite do bloqueio (por exemplo, se o aplicativo falhar), o Service Bus desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Se houver falha no aplicativo após o processamento da mensagem, mas antes que o método **deleteMessage** seja emitido, a mensagem será fornecida novamente ao aplicativo quando ele for reiniciado. Isso é frequentemente chamado de **Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez, mas, em algumas situações, a mesma mensagem pode ser fornecida novamente. Se o cenário não tolerar processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega da mensagem duplicada. Isso geralmente é obtido com a propriedade **getMessageId** da mensagem, que permanecerá constante nas tentativas da entrega.

## <a name="bkmk_NextSteps"> </a>Próximas etapas

Agora que você aprendeu as noções básicas sobre Filas do Service Bus, consulte o tópico do MSDN [Filas, tópicos e assinaturas][] para obter mais informações.

  [SDK do Azure para Java]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh690953(v=vs.103).aspx
  [O que são as filas do Service Bus?]: #what-are-service-bus-queues
  [Criar um namespace de serviço]: #create-a-service-namespace
  [Obter as credenciais de gerenciamento padrão do namespace]: #obtain-default-credentials
  [Configurar seu aplicativo para usar o Service Bus]: #bkmk_ConfigApp
  [Como criar um Provedor de token de segurança]: #bkmk_HowToCreateQueue
  [Como enviar mensagens a uma fila]: #bkmk_HowToSendMsgs
  [Como receber mensagens de uma fila]: #bkmk_HowToReceiveMsgs
  [Como tratar falhas do aplicativo e mensagens ilegíveis]: #bkmk_HowToHandleAppCrashes
  [Próximas etapas]: #bkmk_NextSteps
  [Diagrama da Fila do Service Bus]: ../../../DevCenter/Java/Media/SvcBusQueues_01_FlowDiagram.jpg
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com/
  [Captura de tela do nó do Service Bus]: ../../../DevCenter/Java/Media/SvcBusQueues_02_SvcBusNode.jpg
  [Criar uma captura de tela do novo namespace]: ../../../DevCenter/Java/Media/SvcBusQueues_03_CreateNewSvcNamespace.jpg
  [Captura de tela de namepaces disponíveis]: ../../../DevCenter/Java/Media/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
  [Captura de tela da lista de namespaces]: ../../../DevCenter/Java/Media/SvcBusQueues_05_NamespaceList.jpg
  [Captura de tela do Painel Propriedades]: ../../../DevCenter/Java/Media/SvcBusQueues_06_PropertiesPane.jpg
  [Captura de tela da Chave padrão]: ../../../DevCenter/Java/Media/SvcBusQueues_07_DefaultKey.jpg
  [Filas, tópicos e assinaturas]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh367516.aspx

