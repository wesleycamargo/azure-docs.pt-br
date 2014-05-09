<properties linkid="develop-python-service-bus-queues" urlDisplayName="Filas do Barramento de Serviço" pageTitle="Como usar filas do Barramento de Serviço (Python) - Azure" metaKeywords="Filas do Barramento de Serviço do Azure, filas do Azure, mensagens do Azure, Python de filas do Azure" description="Saiba como usar as filas do Barramento de Serviço do Azure. Exemplos de código escritos em Python" metaCanonical="" services="service-bus" documentationCenter="Python" title="Como usar filas do Barramento de Serviço" authors="" solutions="" manager="" editor="" />




# Como usar filas do Barramento de Serviço
Este guia mostra como usar as filas do Barramento de Serviço. Os exemplos são escritos em Python e usam o módulo Python Azure. Os cenários
abrangidos incluem **criar filas, enviar e receber mensagens** e
**excluir filas**. Para obter mais informações sobre filas, consulte a seção [Próximas etapas][].

## Sumário

-   [O que são as filas do Service Bus?][]
-   [Criar um namespace de serviço][]
-   [Obter as credenciais de gerenciamento padrão do namespace][]
-   [Tutorial: Criar uma fila][]
-   [Tutoria: Enviar mensagens a uma fila][]
-   [Tutorial: Receber mensagens de uma fila][]
-   [Como tratar falhas do aplicativo e mensagens ilegíveis][]
-   [Próximas etapas][]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

**Observação:** se você precisar instalar o Python ou as bibliotecas de cliente, consulte o [Guia de Instalação do Python (a página pode estar em inglês)](../python-how-to-install/).


## <a name="create-queue"> </a>Como criar uma fila

O objeto **ServiceBusService** permite que você trabalhe com filas. Adicione o seguinte próximo à parte superior de qualquer arquivo Python no qual você deseja acessar o Barramento de Serviço do Azure programaticamente:

	from azure.servicebus import *

O código a seguir cria um objeto **ServiceBusService**. Substitua 'mynamespace', 'mykey' e 'myissuer' pelo namespace, chave e emissor reais.

	bus_service = ServiceBusService(service_namespace='mynamespace', account_key='mykey', issuer='myissuer')
	
	bus_service.create_queue('taskqueue')

**create_queue** também oferece suporte a opções adicionais, que permitem que você substitua as configurações padrão de fila, como a vida útil de mensagens ou o tamanho máximo de filas. O seguinte exemplo mostra a definição do tamanho máximo da fila para 5 GB e a vida útil para 1 minuto:

	queue_options = Queue()
	queue_options.max_size_in_megabytes = '5120'
	queue_options.default_message_time_to_live = 'PT1M'

	bus_service.create_queue('taskqueue', queue_options)

## <a name="send-messages"> </a>Como enviar mensagens a uma fila

Para enviar uma mensagem a uma fila do Service Bus, seu aplicativo chamará o
O método **send\_queue\_message** no objeto **ServiceBusService**.

O exemplo a seguir demonstra como enviar uma mensagem de teste à fila chamada *taskqueue usando* **send\_queue\_message**:

	msg = Message('Test Message')
	bus_service.send_queue_message('taskqueue', msg)

As filas do Service Bus suportam um tamanho máximo de mensagem de 256 KB (o cabeçalho,
que inclui as propriedades padrão e personalizadas do aplicativo, podem ter um
tamanho máximo de 64 KB). Não há nenhum limite no número de mensagens
mantidas em uma fila mas há uma capacidade do tamanho total das mensagens
mantidas por uma fila. O tamanho da fila é definido no momento da criação, com um limite superior de 5 GB.

## <a name="receive-messages"> </a>Como receber mensagens de uma fila

As mensagens são recebidas de uma fila usando o método de **receive\_queue\_message** no objeto **ServiceBusService**.

	msg = bus_service.receive_queue_message('taskqueue')
	print(msg.body)

Por padrão, as mensagens são excluídas da fila conforme são lidas. No entanto, você pode ler (espiar) e bloquear a mensagem sem excluí-la da fila definindo o parâmetro opcional **peek\_lock** como **true**.

O comportamento padrão da leitura e da exclusão da mensagem como parte da operação de recebimento é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo possa tolerar o não processamento de uma mensagem em caso de falha. Para entender como isso funciona, considere um cenário no qual o consumidor emite a solicitação de recebimento e ocorre uma falha antes de processá-la. Como o Service Bus terá marcado a mensagem
como consumida, quando o aplicativo for reiniciado e começar a consumir
as mensagens novamente, ele terá perdido a mensagem que
foi consumida antes da falha.


Se o parâmetro **peek\_lock** for definido como **True**, o processo de recebimento se tornará uma operação de duas etapas, o que torna possível o suporte a aplicativos que não podem tolerar mensagens ausentes. Quando o Service Bus recebe uma solicitação, ele localiza a próxima mensagem a ser consumida, ele a bloqueia para evitar que outros consumidores a recebam e depois a retorna para o aplicativo.
Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento chamando o método **delete** no objeto **Message**. O método **delete** marcará a mensagem como sendo consumida e a removerá da fila.

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
	print(msg.body)

	msg.delete()

## <a name="handle-crashes"> </a>Como tratar falhas do aplicativo e mensagens ilegíveis

O Service Bus proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros em seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele chamará o método **unlock** no
objeto **Message**. Isso fará com que o Barramento de Serviço desbloqueie a
mensagem na fila e a disponibilize para que possa ser recebida novamente
tanto pelo mesmo aplicativo de consumo ou por outro.

Também existe um tempo limite associado a uma mensagem bloqueada na fila e, se houver falha no aplicativo ao processar a mensagem antes da expiração do tempo limite do bloqueio (por exemplo, se houver uma falha no aplicativo), o Barramento de Serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Se houver uma falha no aplicativo depois do processamento da mensagem, mas antes do método **delete** ser chamado, a mensagem será fornecida ao aplicativo novamente quando ele for reiniciado. Isso normalmente é chamado de
**Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez, mas, em determinadas situações, a mesma mensagem pode ser fornecida novamente. Se o cenário não tolerar processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega da mensagem duplicada. Isso geralmente é feito usando a
propriedade **MessageId** da mensagem, que permanecerá constante entre as tentativas de entrega.

## <a name="next-steps"> </a>Próximas etapas

Agora que você aprendeu os princípios básicos das filas do Barramento de Serviço, siga estes links para obter mais informações.

-   Consulte a Referência do MSDN: [Filas, tópicos e assinaturas.][]

  [Próximas etapas]: #next-steps
  [O que são as filas do Service Bus?]: #what-are-service-bus-queues
  [Criar um namespace de serviço]: #create-a-service-namespace
  [Obter as credenciais de gerenciamento padrão do namespace]: #obtain-default-credentials
  [Tutorial: Criar uma fila]: #create-queue
  [Tutoria: Enviar mensagens a uma fila]: #send-messages
  [Tutorial: Receber mensagens de uma fila]: #receive-messages
  [Como tratar falhas do aplicativo e mensagens ilegíveis]: #handle-crashes
  [Conceitos de filas]: ../../../DevCenter/dotNet/Media/sb-queues-08.png
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  
  
  
  
  
  [Filas, tópicos e assinaturas.]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh367516.aspx

