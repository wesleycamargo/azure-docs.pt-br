<properties urlDisplayName="Queue Service" pageTitle="Como usar o serviço Fila (Python) | Microsoft Azure" metaKeywords="Azure Queue Service messaging Python" description="Saiba como usar o serviço Fila do Azure para criar e excluir filas, bem como para inserir, obter e excluir mensagens. Amostras escritas em Python." metaCanonical="" services="storage" documentationCenter="Python" title="How to Use the Queue Storage Service from Python" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/19/2014" ms.author="robmcm" />



# Como usar o serviço de armazenamento Fila com Python. Este guia mostra como executar cenários comuns usando o serviço de armazenamento Fila do Microsoft Azure. Os exemplos foram escritos usando a API do Python. Os cenários abordados incluem **inserir**, **espiar**, **obter** e **excluir** mensagens de fila, bem como **criar e excluir** filas. Para obter mais informações sobre filas, consulte a seção [Próximas etapas][].

## Sumário

[O que é Armazenamento de Filas?][]   
 [Conceitos][]   
 [Criar uma conta de armazenamento do Azure][]   
 [Como: Criar uma fila][]   
 [Como: Inserir uma mensagem em uma fila][]   
 [Como: Espiar a próxima mensagem][]   
 [Como: Remover a próxima mensagem da fila][]   
 [Como: Alterar o conteúdo de uma mensagem na fila][]   
 [Como: Opções adicionais para remover mensagens da fila][]   
 [Como: Obter o tamanho da fila][]   
 [Como: Excluir uma fila][]   
 [Próximas etapas][]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <a name="create-account"> </a>Criar uma conta de armazenamento do Azure
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]


**Observação**:  Se você precisar instalar o Python ou as bibliotecas de cliente, consulte o [Guia de instalação do Python (a página pode estar em inglês)](../python-how-to-install/).

## <a name="create-queue"> </a>Como: Criar uma fila

O objeto **QueueService** permite que você trabalhe com filas. O código a seguir cria um objeto **QueueService**.Adicione o seguinte próximo à parte superior de qualquer arquivo Python no qual você deseja acessar o Armazenamento do Azure programaticamente:

	from azure.storage import QueueService

O código a seguir cria um objeto **QueueService** usando o nome da conta de armazenamento e a chave da conta.Substitua 'Minha conta' e 'mykey' com a conta real e a chave.

	queue_service = QueueService(account_name='myaccount', account_key='mykey')

	queue_service.create_queue('taskqueue')


## <a name="insert-message"> </a>Como: Inserir uma mensagem em uma fila

Para inserir uma mensagem em uma fila, use o método **put\_message** para criar uma nova mensagem e adicione-a à fila.

	queue_service.put_message('taskqueue', 'Hello World')


## <a name="peek-message"> </a>Como: Espiar a próxima mensagem

Você pode inspecionar a mensagem na frente de uma fila sem removê-la da fila chamando o método **peek\_messages**. Por padrão, **peek\_messages** espia uma única mensagem.

	messages = queue_service.peek_messages('taskqueue')
	for message in messages:
		print(message.message_text)


## <a name="get-message"> </a>Como: Remover a próxima mensagem da fila

Seu código remove uma mensagem de uma fila em duas etapas.   Quando você chamar **get\_messages**, receberá a próxima mensagem em uma fila por padrão. As mensagens retornadas de **get\_messages** tornam-se invisíveis para todas as outras mensagens de leitura de código da fila.Por padrão, essa mensagem permanece invisível por 30 segundos.Para concluir a remoção da mensagem da fila, chame também **delete\_message**.Este processo de duas etapas de remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente.O código chama **delete\_message** logo depois que a mensagem é processada.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)


## <a name="change-contents"> </a>Como: Alterar o conteúdo de uma mensagem na fila

Você pode alterar o conteúdo de uma mensagem in-loco na fila. Se a mensagem representar uma tarefa de trabalho, você poderá usar esse recurso para atualizar o status da tarefa de trabalho. O código a seguir usa o método **update\_message** para atualizar uma mensagem.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		queue_service.update_message('taskqueue', message.message_id, 'Hello World Again', message.pop_receipt, 0)

## <a name="advanced-get"> </a>Como: Opções adicionais para remover mensagens da fila

Há duas maneiras de personalizar a recuperação da mensagem de uma fila.  Primeiro, você pode obter um lote de mensagens (até 32). Segundo, você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo mais ou menos tempo para seu código processar totalmente cada mensagem. O exemplo de código a seguir usa o método **get\_messages** para receber 16 mensagens em uma chamada.Em seguida, ele processa cada a mensagem usando um loop for.Ele também define o tempo limite de invisibilidade de cinco minutos para cada mensagem.

	messages = queue_service.get_messages('taskqueue', numofmessages=16, visibilitytimeout=5*60)
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)

## <a name="get-queue-length"> </a>Como: Obter o tamanho da fila

Você pode obter uma estimativa do número de mensagens em uma fila. Método **get\_queue\_metadata** solicita que o serviço Fila retorne metadados sobre a fila, e **x-ms-approximate-messages-count** deve ser usado como índice no dicionário retornado para localizar a contagem.O resultado é aproximado apenas porque as mensagens podem ser adicionadas ou removidas depois que o serviço de fila responde à sua solicitação.

	queue_metadata = queue_service.get_queue_metadata('taskqueue')
	count = queue_metadata['x-ms-approximate-messages-count']

## <a name="delete-queue"> </a>Como: Excluir uma fila

Para excluir uma fila e todas as mensagens contidas nela, chame o método**delete\_queue**.

	queue_service.delete_queue('taskqueue')

## <a name="next-steps"> </a>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de fila, siga estes links para aprender como executar tarefas de armazenamento mais complexas.

-   Consulte a referência de MSDN: [Armazenando e acessando dados no Azure][]
-   Visite o[ Blog da Equipe de Armazenamento do Azure][]

  [Próximas etapas]: #next-steps
  [O que é Armazenamento de Filas?]: #what-is
  [Conceitos]: #concepts
  [Criar uma conta de armazenamento do Azure]: #create-account
  [Como: Criar uma fila]: #create-queue
  [Como: Inserir uma mensagem em uma fila]: #insert-message
  [Como: Espiar a próxima mensagem]: #peek-message
  [Como: Remover a próxima mensagem da fila]: #get-message
  [Como: Alterar o conteúdo de uma mensagem na fila]: #change-contents
  [Como: Opções adicionais para remover mensagens da fila]: #advanced-get
  [Como: Obter o tamanho da fila]: #get-queue-length
  [Como: Excluir uma fila]: #delete-queue
  [Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx
  [Blog da equipe do Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/

<!--HONumber=35.1-->
