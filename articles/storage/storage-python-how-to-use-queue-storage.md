<properties
	pageTitle="Como usar o Armazenamento de fila do Python | Microsoft Azure"
	description="Saiba como usar o serviço Fila do Azure do Python para criar e excluir filas, bem como para inserir, obter e excluir mensagens."
	services="storage"
	documentationCenter="python"
	authors="emgerner-msft"
	manager="wpickett"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="07/26/2016"
	ms.author="emgerner"/>

# Como usar o Armazenamento de fila do Python

[AZURE.INCLUDE [armazenamento-seletor-fila-include](../../includes/storage-selector-queue-include.md)] <br/> [AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## Visão geral

Este guia mostra como executar cenários comuns usando o serviço de armazenamento de Fila do Azure. Os exemplos são escritos em Python e usam o [Microsoft Azure Storage SDK for Python] (SDK do Armazenamento do Microsoft Azure para Python). Os cenários abrangidos incluem **inserir**, **exibir**, **obter** e **excluir** mensagens da fila, bem como **criar e excluir filas**. Para obter mais informações sobre filas, consulte a seção [Próximas etapas].

[AZURE.INCLUDE [armazenamento-fila-conceitos-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Como criar uma fila

O objeto **QueueService** permite que você trabalhe com filas. O código a seguir cria um objeto **QueueService**. Adicione o seguinte próximo à parte superior de qualquer arquivo Python no qual você deseja acessar o Armazenamento do Azure programaticamente:

	from azure.storage.queue import QueueService

O código a seguir cria um objeto **QueueService** usando o nome da conta de armazenamento e a chave da conta. Substitua “myaccount” e “mykey” pelo nome da sua conta e sua chave.

	queue_service = QueueService(account_name='myaccount', account_key='mykey')

	queue_service.create_queue('taskqueue')


## Como inserir uma mensagem em uma fila

Para inserir uma mensagem em uma fila, use o método **put\_message** para criar uma nova mensagem e adicione-a à fila.

	queue_service.put_message('taskqueue', u'Hello World')


## Como: espiar a próxima mensagem

Você pode inspecionar a mensagem na frente de uma fila sem removê-la da fila chamando o método **peek\_messages**. Por padrão, **peek\_messages** inspeciona uma única mensagem.

	messages = queue_service.peek_messages('taskqueue')
	for message in messages:
		print(message.content)


## Como: remover mensagens da fila

Seu código remove uma mensagem de uma fila em duas etapas. Quando você chamar **get\_messages**, receberá a próxima mensagem em uma fila por padrão. As mensagens retornadas de **get\_messages** tornam-se invisíveis para todas as outras mensagens de leitura de código da fila. Por padrão, essa mensagem permanece invisível por 30 segundos. Para concluir a remoção da mensagem da fila, chame também **delete\_message**. Este processo de duas etapas de remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. O código chama **delete\_message** logo depois que a mensagem é processada.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		print(message.content)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)

Há duas maneiras de personalizar a recuperação da mensagem de uma fila. Primeiro, você pode obter um lote de mensagens (até 32). Segundo, você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo mais ou menos tempo para seu código processar totalmente cada mensagem. O exemplo de código a seguir usa o método **get\_messages** para receber 16 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop for. Ele também define o tempo limite de invisibilidade de cinco minutos para cada mensagem.

	messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
	for message in messages:
		print(message.content)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)		


## Como: alterar o conteúdo de uma mensagem em fila

Você pode alterar o conteúdo de uma mensagem in-loco na fila. Se a mensagem representar uma tarefa de trabalho, você poderá usar esse recurso para atualizar o status da tarefa de trabalho. O código a seguir usa o método **update\_message** para atualizar uma mensagem. O tempo limite de visibilidade está definido como 0, indicando que a mensagem será exibida imediatamente e o conteúdo será atualizado.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		queue_service.update_message('taskqueue', message.message_id, message.pop_receipt, 0, u'Hello World Again')

## Como obter o comprimento da fila

Você pode obter uma estimativa do número de mensagens em uma fila. O método **get\_queue\_metadata** solicita que o serviço Fila retorne metadados sobre a fila e a **approximate\_message\_count**. O resultado é aproximado apenas porque as mensagens podem ser adicionadas ou removidas depois que o serviço de fila responde à sua solicitação.

	metadata = queue_service.get_queue_metadata('taskqueue')
	count = metadata.approximate_message_count

## Como excluir uma fila

Para excluir uma fila e todas as mensagens contidas nela, chame o método **delete\_queue**.

	queue_service.delete_queue('taskqueue')

## Próximas etapas

Agora que você aprendeu os conceitos básicos do Armazenamento de Filas, siga estes links para saber mais.

- [Centro de desenvolvedores do Python](/develop/python/)
- [API REST de serviços de armazenamento do Azure](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog da equipe de Armazenamento do Azure]
- [SDK do Armazenamento do Microsoft Azure para Python]

[Blog da equipe de Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK for Python]: https://github.com/Azure/azure-storage-python
[SDK do Armazenamento do Microsoft Azure para Python]: https://github.com/Azure/azure-storage-python

<!---HONumber=AcomDC_0727_2016-->