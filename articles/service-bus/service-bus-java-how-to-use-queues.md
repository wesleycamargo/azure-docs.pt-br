<properties 
	pageTitle="Como usar filas do barramento de serviço (Java) - Azure" 
	description="Aprenda a usar as filas do barramento de serviço no Azure. Exemplos de códigos escritos em Java." 
	services="service-bus" 
	documentationCenter="java" 
	authors="sethmanheim" 
	manager="timlt" 
	/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="sethm"/>

# Como usar filas do Barramento de Serviço

Este guia descreve como usar as filas do barramento de serviço. Os exemplos são escritos em Java e usam o [SDK do Azure para Java][]. Os cenários abrangidos incluem **criar filas****, enviar e receber mensagens**e **excluir filas**.

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## Configurar seu aplicativo para usar o Barramento de serviço

Adicione as seguintes instruções de importação à parte superior do arquivo Java:

	// Include the following imports to use service bus APIs
	import com.microsoft.windowsazure.services.serviceBus.*;
	import com.microsoft.windowsazure.services.serviceBus.models.*; 
	import com.microsoft.windowsazure.services.core.*; 
	import javax.xml.datatype.*;
	
## Como criar uma fila

Operações de gerenciamento para as filas do Barramento de Serviço podem ser realizadas pela classe**ServiceBusContract**. Um objeto **ServiceBusContract** é construído com uma configuração adequada que encapsula as permissões de token para gerenciá-lo, e a classe **ServiceBusContract** é o único ponto de comunicação com o Azure.

A classe **ServiceBusService** fornece métodos para criar, enumerar e excluir filas. O exemplo abaixo mostra como um objeto **ServiceBusService** pode ser usado para criar uma fila chamada "TestQueue" com um namespace chamado "HowToSample":

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

Existem métodos em QueueInfo que permitem que as propriedades da fila sejam ajustadas (por exemplo, para definir o valor da "vida útil" padrão a ser aplicado às mensagens enviadas para a fila). O exemplo a seguir mostra como criar uma fila denominada "TestQueue" com um tamanho máximo de 5 GB:

    long maxSizeInMegabytes = 5120;
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateQueueResult result = service.createQueue(queueInfo);

Observe que você pode usar o método **listQueues** em objetos **ServiceBusContract** para verificar se já existe uma fila com um nome especificado dentro de um namespace de serviço.

## Como enviar mensagens para uma fila

Para enviar uma mensagem a uma fila do Barramento de Serviço, seu aplicativo obterá um objeto**ServiceBusContract**. O código abaixo demonstra como enviar uma mensagem para a fila "TestQueue" que criamos acima dentro do nosso
namespace de serviço "HowToSample":

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

As mensagens enviadas para (e recebidas das) filas de Barramento de Serviço são instâncias da classe **BrokeredMessage**. **Objeto BrokeredMessage** possuem um conjunto de métodos padrão (como **getLabel**, **getTimeToLive**, **setLabel** e **setTimeToLive**), um dicionário usado para manter propriedades específicas do aplicativo personalizado e um corpo de dados arbitrários do aplicativo. Um aplicativo pode definir o corpo da mensagem passando qualquer objeto serializável para o construtor do **BrokeredMessage** e o serializador adequado então será usado para serializar o objeto. Como alternativa, um **java.IO.InputStream** pode ser fornecido.

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

As filas do Barramento de Serviço dão suporte a um tamanho máximo de mensagem de 256 KB (o cabeçalho, que inclui as propriedades padrão e personalizadas do aplicativo podem ter um tamanho máximo de 64 KB). Não há nenhum limite no número de mensagens mantidas em uma fila mas há uma capacidade do tamanho total das mensagens mantidas por uma fila. O tamanho da fila é definido no momento da criação, com um limite superior de 5 GB.

## Como receber mensagens de uma fila

A maneira mais fácil de receber mensagens de uma fila é usar um objeto **ServiceBusContract**. As mensagens recebidas podem funcionar de dois modos diferentes: **ReceiveAndDelete** e **PeekLock**.

Quando o modo **ReceiveAndDelete** for usado, o recebimento será uma operação única, isto é, quando o Barramento de Serviço receber uma solicitação de leitura de uma mensagem em uma fila, ele marcará a mensagem como sendo consumida e a retornará ao aplicativo. **O modo ReceiveAndDelete** (que é o modo padrão) é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo possa tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la.
Como o Barramento de Serviço terá marcado a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

No modo **PeekLock**, o recebimento de uma mensagem se torna uma operação de dois estágios, o que possibilita o suporte aos aplicativos que não podem tolerar mensagens ausentes. Quando o Barramento de Serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, a bloqueia para evitar que outros clientes a recebam e a retorna para o aplicativo. Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento chamando **Delete** na mensagem recebida. Quando o Barramento de Serviço vê a chamada **Delete**, ele marca a mensagem como tendo sido consumida e remove-a da fila.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e processadas usando o modo **PeekLock** (não o modo padrão). O exemplo abaixo executa um loop infinito e processa mensagens assim que elas chegam em nossa "TestQueue":

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

## Como tratar falhas do aplicativo e mensagens ilegíveis

O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não for capaz de processar a mensagem por algum motivo, ele chamará o método **unlockMessage** na mensagem recebida (em vez do método **deleteMessage**). Isso fará com que o Barramento de Serviço desbloqueie a mensagem na fila e disponibilize-a para ser recebida novamente, pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na fila e, se o aplicativo não conseguir processar a mensagem antes da expiração do tempo limite do bloqueio (por exemplo, em caso de falha do aplicativo), o Barramento de
Serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Se houver falha do aplicativo após o processamento da mensagem, mas antes da solicitação **deleteMessage** ser emitida, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Isso é frequentemente chamado de **Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez mas, em algumas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega de mensagem duplicada. Isso geralmente é feito com o método **getMessageId** da mensagem, que permanecerá constante nas tentativas da entrega.

## Próximas etapas

Agora que você aprendeu as noções básicas sobre Filas do Barramento de Serviço, consulte o tópico do MSDN [Filas, tópicos e assinaturas][] para obter mais informações.

  [SDK do Azure para Java]: http://azure.microsoft.com/develop/java/
  [O que são as filas do Barramento de Serviço?]: #what-are-service-bus-queues
  [Criar um namespace de serviço]: #create-a-service-namespace
  [Obter as Credenciais de Gerenciamento Padrão para o Namespace]: #obtain-default-credentials
  [Configurar seu aplicativo para usar o Barramento de serviço]: #bkmk_ConfigApp
  [Como: Criar um provedor de token de segurança]: #bkmk_HowToCreateQueue
  [Como: Enviar mensagens para uma fila]: #bkmk_HowToSendMsgs
  [Como: Receber mensagens de uma fila]: #bkmk_HowToReceiveMsgs
  [Como: Tratar falhas do aplicativo e mensagens ilegíveis]: #bkmk_HowToHandleAppCrashes
  [Próximas etapas]: #bkmk_NextSteps
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com/
  [Filas, tópicos e assinaturas]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx

<!--HONumber=47-->
 