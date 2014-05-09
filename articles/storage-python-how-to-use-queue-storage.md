<properties linkid="develop-python-queue-service" urlDisplayName="Serviço Fila" pageTitle="Como usar o serviço Fila (Python) | Microsoft Azure" metaKeywords="Serviço Fila do Azure enviando mensagens para o Python" description="Aprenda a usar o serviço Fila do Azure para criar e excluir filas e inserir, obter e excluir mensagens. Exemplos escritos em Python." metaCanonical="" services="storage" documentationCenter="Python" title="Como usar o serviço de armazenamento Fila com Python" authors="" solutions="" manager="" editor="" />



# Como usar o serviço de armazenamento Fila com Python
Este guia mostra como executar cenários comuns usando o
serviço de armazenamento Fila do Azure. Os exemplos foram escritos usando a API do Python. Os cenários abordados incluem mensagens das filas de **inserção**, **inspeção**,
**obtenção** e **exclusão**, bem como a **criação e exclusão de filas**. Para obter mais informações sobre filas, consulte a seção [Próximas etapas][].

## Sumário

[O que é armazenamento de fila?][]   
 [Conceitos][]   
 [Criar uma conta de Armazenamento do Azure][]   
 [Como criar uma fila][]   
 [Como inserir uma mensagem em uma fila][]   
 [Como inspecionar a próxima mensagem][]   
 [Como remover a próxima mensagem da fila][]   
 [Como alterar o conteúdo de uma mensagem em fila][]   
 [Como utilizar opções adicionais para remover mensagens da fila][]   
 [Como obter o comprimento da fila][]   
 [Como excluir uma fila][]   
 [Próximas etapas][]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <a name="create-account"> </a>Criar uma conta de armazenamento do Azure
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]


**Observação:** se você precisar instalar o Python ou as bibliotecas de cliente, consulte o [Guia de Instalação do Python (a página pode estar em inglês)](../python-how-to-install/).

## <a name="create-queue"> </a>Como criar uma fila

O objeto **QueueService** permite que você trabalhe com filas. O código a seguir cria um objeto **QueueService**. Adicione o seguinte próximo à parte superior de qualquer arquivo Python no qual você deseja acessar o Armazenamento do Azure programaticamente:

	from azure.storage import *

O código a seguir cria um objeto **QueueService** usando o nome da conta de armazenamento e a chave da conta. Substitua 'Minha conta' e 'mykey' com a conta real e a chave.

	queue_service = QueueService(account_name='myaccount', account_key='mykey')

	queue_service.create_queue('taskqueue')


## <a name="insert-message"> </a>Como inserir uma mensagem em uma fila

Para inserir uma mensagem em uma fila, use o método **put\_message** para criar uma nova mensagem e adicioná-la à fila.

	queue_service.put_message('taskqueue', 'Hello World')


## <a name="peek-message"> </a>Como inspecionar a próxima mensagem

Você pode inspecionar a mensagem na frente de uma fila sem removê-la da fila chamando o método **peek\_messages**. Por padrão,
**peek\_messages** inspeciona uma única mensagem.

	messages = queue_service.peek_messages('taskqueue')
	for message in messages:
		print(message.message_text)


## <a name="get-message"> </a>Como remover a próxima mensagem da fila

Seu código remove uma mensagem de uma fila em duas etapas. Quando você chamar
**get\_messages**, receberá a próxima mensagem em uma fila por padrão. As mensagens retornadas de **get\_messages** se tornam invisíveis para todas as outras mensagens de leitura de código desta fila. Por padrão, essa mensagem permanece invisível por 30 segundos. Para concluir a remoção da mensagem da fila, você também deve chamar **delete\_message**. Este processo de duas etapas de remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. O código chama **delete\_message** logo após a mensagem ser processada.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)


## <a name="change-contents"> </a>Como alterar o conteúdo de uma mensagem em fila

Você pode alterar o conteúdo de uma mensagem in-loco na fila. Se a mensagem representar uma tarefa de trabalho, você poderá usar esse recurso para atualizar o status da tarefa de trabalho. O código a seguir usa o método **update\_message** para atualizar uma mensagem.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		queue_service.update_message('taskqueue', message.message_id, 'Hello World Again', message.pop_receipt, 0)

## <a name="advanced-get"> </a>Como utilizar opções adicionais para remover mensagens da fila

Há duas maneiras de personalizar a recuperação da mensagem de uma fila.
Primeiro, você pode obter um lote de mensagens (até 32). Em segundo lugar, você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo mais ou menos tempo para o código processar completamente cada mensagem. O seguinte exemplo de código usa o
método **get\_messages** para obter 16 mensagens em uma chamada. Em seguida, processa cada mensagem usando um loop. Ele também define o tempo limite de invisibilidade para cinco minutos para cada mensagem.

	messages = queue_service.get_messages('taskqueue', numofmessages=16, visibilitytimeout=5*60)
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)

## <a name="get-queue-length"> </a>Como obter o comprimento da fila

Você pode obter uma estimativa do número de mensagens em uma fila. O
método **get\_queue\_metadata** solicita que o serviço de fila retorne metadados sobre a fila, e **x-ms-approximate-messages-count** deve ser usado como índice no dicionário retornado para localizar a contagem.
O resultado é aproximado porque as mensagens podem ser adicionadas ou removidas depois que o serviço de fila responde à sua solicitação.

	queue_metadata = queue_service.get_queue_metadata('taskqueue')
	count = queue_metadata['x-ms-approximate-messages-count']

## <a name="delete-queue"> </a>Como excluir uma fila

Para excluir uma fila e todas as mensagens contidas nela, chame o
método **delete\_queue**.

	queue_service.delete_queue('taskqueue')

## <a name="next-steps"> </a>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de fila, siga estes links para aprender como fazer tarefas de armazenamento mais complexas.

-  Consulte a referência do MSDN: [Armazenando e acessando dados no Azure][]
-   Acesse o [Blog da Equipe de Armazenamento do Azure][]

  [Próximas etapas]: #next-steps
  [O que é armazenamento de fila?]: #what-is
  [Conceitos]: #concepts
  [Criar uma conta de armazenamento do Azure]: #create-account
  [Como criar uma fila]: #create-queue
  [Como inserir uma mensagem em uma fila]: #insert-message
  [Como inspecionar a próxima mensagem]: #peek-message
  [Como remover a próxima mensagem da fila]: #get-message
  [Como alterar o conteúdo de uma mensagem em fila]: #change-contents
  [Como utilizar opções adicionais para remover mensagens da fila]: #advanced-get
  [Como obter o comprimento da fila]: #get-queue-length
  [Como excluir uma fila]: #delete-queue
  [Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx
  [Blog da Equipe de Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/

