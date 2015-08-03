<properties 
	pageTitle="Como usar filas do barramento de serviço (Python) - Azure" 
	description="Saiba como usar as filas do barramento de serviço do Azure do Python." 
	services="service-bus" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="07/06/2015" 
	ms.author="huvalo"/>


# Como usar filas do Barramento de Serviço

Este guia descreve como usar as filas do barramento de serviço. Os exemplos são escritos em Python e usam o [pacote do Python Azure][]. Os cenários abrangidos incluem **criar filas, enviar e receber mensagens** e **excluir filas**.

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

**Observação:** para instalar o Python ou o [pacote do Python Azure][], confira o [Guia de instalação do Python](../python-how-to-install.md).

## Como criar uma fila

O objeto **ServiceBusService** permite que você trabalhe com filas. Adicione o seguinte código próximo à parte superior de qualquer arquivo Python no qual você deseja acessar o Barramento de Serviço do Azure programaticamente:

	from azure.servicebus import ServiceBusService, Message, Queue

O código a seguir cria um objeto **ServiceBusService**. Substitua 'mynamespace', 'sharedaccesskeyname' e 'sharedaccesskey' pelo namespace, nome chave e valor de assinatura de acesso compartilhado (SAS).

	bus_service = ServiceBusService(
		service_namespace='mynamespace',
		shared_access_key_name='sharedaccesskeyname',
		shared_access_key_value='sharedaccesskey')

Os valores para o nome chave e valor da SAS podem ser encontrados na informação de conexão do Portal do Azure, ou na janela das **Propriedades** do Visual Studio ao selecionar o namespace do Barramento de Serviço no Gerenciador de Servidores (conforme mostrado na seção anterior).

	bus_service.create_queue('taskqueue')

**create_queue** também oferece suporte para opções adicionais, que permitem a substituição de configurações padrão da fila, como a vida útil da mensagem ou o tamanho máximo da fila. O exemplo a seguir define o tamanho máximo da fila como 5 GB e a vida útil como um minuto:

	queue_options = Queue()
	queue_options.max_size_in_megabytes = '5120'
	queue_options.default_message_time_to_live = 'PT1M'

	bus_service.create_queue('taskqueue', queue_options)

## Como enviar mensagens para uma fila

Para enviar uma mensagem para uma fila de Barramento de Serviço, seu aplicativo chama o método **send\_queue\_message** no objeto **ServiceBusService**.

O exemplo a seguir demonstra como enviar uma mensagem de teste à fila chamada *taskqueue* usando **send_queue_message**:

	msg = Message(b'Test Message')
	bus_service.send_queue_message('taskqueue', msg)

As filas do Barramento de Serviço dão suporte a um tamanho máximo de mensagem de 256 KB (o cabeçalho, que inclui as propriedades padrão e personalizadas do aplicativo podem ter um tamanho máximo de 64 KB). Não há nenhum limite no número de mensagens mantidas em uma fila mas há uma capacidade do tamanho total das mensagens mantidas por uma fila. O tamanho da fila é definido no momento da criação, com um limite superior de 5 GB.

## Como receber mensagens de uma fila

As mensagens são recebidas de uma fila usando o método **receive\_queue\_message** no objeto **ServiceBusService**:

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
	print(msg.body)

As mensagens são excluídas da fila conforme elas são lidas quando o parâmetro **peek\_lock** é definido como **False**. Você pode ler (peek) e bloquear a mensagem sem excluí-la da fila definindo o parâmetro **peek\_lock** como **True**.

O comportamento da leitura e da exclusão da mensagem como parte da operação de recebimento é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo possa tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Barramento de Serviço terá marcado a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

Se o parâmetro **peek_lock** estiver definido como **True**, o processo de recebimento se torna uma operação de duas etapas, o que torna possível o suporte a aplicativos que não toleram mensagens ausentes. Quando o Barramento de Serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, a bloqueia para evitar que outros clientes a recebam e a retorna para o aplicativo. Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento chamando o método **delete** no objeto **Message**. O método **delete** marcará a mensagem como tendo sido consumida e a removerá da fila.

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
	print(msg.body)

	msg.delete()

## Como tratar falhas do aplicativo e mensagens ilegíveis

O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele chamará o método **unlock** no objeto **Message**. Isso fará com que o Barramento de Serviço desbloqueie a mensagem na fila e disponibilize-a para ser recebida novamente, pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na fila e, se o aplicativo não conseguir processar a mensagem antes da expiração do tempo limite do bloqueio (por exemplo, em caso de falha do aplicativo), o Service Bus desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que o método **delete** seja chamado, a mensagem será fornecida novamente ao aplicativo quando ele reiniciar. Isso é frequentemente chamado de **Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez mas, em algumas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega de mensagem duplicada. Isso geralmente é obtido com a propriedade **Message_id** da mensagem, que permanecerá constante nas tentativas da entrega.

## Próximas etapas

Agora que você já sabe as noções básicas das filas de Barramento de Serviço, siga estes links para saber mais.

-   Confira a Referência do MSDN: [Filas, tópicos e assinaturas][].

[Azure Management Portal]: http://manage.windowsazure.com
[pacote do Python Azure]: https://pypi.python.org/pypi/azure
[Filas, tópicos e assinaturas]: http://msdn.microsoft.com/library/azure/hh367516.aspx
 

<!---HONumber=July15_HO4-->