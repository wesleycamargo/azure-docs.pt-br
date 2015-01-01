<properties urlDisplayName="Queue Service" pageTitle="Como usar o serviço Fila (Ruby) | Microsoft Azure" metaKeywords="Azure Queue Service get messages Ruby" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Ruby." metaCanonical="" services="storage" documentationCenter="Ruby" title="How to Use the Queue Storage Service from Ruby" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="11/24/2014" ms.author="tomfitz" />





# Como usar o serviço de armazenamento de filas do Ruby

Este guia mostra como executar cenários comuns usando o serviço de armazenamento de filas do Microsoft Azure. Os exemplos são gravados usando a API do Ruby Azure.
Os cenários abordados incluem **inserir, ****espiar, ****obter**e **excluir** mensagens da fila, bem como **criar e excluir filas**. Para obter mais informações sobre filas, consulte a seção [Próximas
Etapas](#next-steps) .

## Sumário

* [O que é Armazenamento de Filas?](#what-is)
* [Conceitos](#concepts)
* [Criar uma conta de armazenamento do Azure](#CreateAccount)
* [Criar um aplicativo Ruby](#create-a-ruby-application)
* [Configurar seu aplicativo para acessar o armazenamento](#configure-your-application-to-access-storage)
* [Configurar uma conexão de armazenamento do Azure](#setup-a-windows-azure-storage-connection)
* [Como: Criar uma fila](#how-to-create-a-queue)
* [Como: Inserir uma mensagem em uma fila](#how-to-insert-a-message-into-a-queue)
* [Como: Espiar a próxima mensagem](#how-to-peek-at-the-next-message)
* [Como: Remover a próxima mensagem da fila](#how-to-dequeue-the-next-message)
* [Como: Alterar o conteúdo de uma mensagem na fila](#how-to-change-the-contents-of-a-queued-message)
* [Como: Opções adicionais para remover mensagens da fila](#how-to-additional-options-for-dequeuing-messages)
* [Como: Obter o tamanho da fila](#how-to-get-the-queue-length)
* [Como: Excluir uma fila](#how-to-delete-a-queue)
* [Próximas etapas](#next-steps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <a id="CreateAccount"></a>Criar uma conta de armazenamento do Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a id="create-a-ruby-application"></a>Criar um aplicativo Ruby

Crie um aplicativo Ruby. Para obter instruções, consulte [Criar um aplicativo Ruby no Azure](/pt-br/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a id="configure-your-application-to-access-storage"></a>Configurar seu aplicativo para acessar o armazenamento

Para usar o armazenamento do Azure, você deverá baixar e usar o pacote do Azure do Ruby, que inclui um conjunto de bibliotecas convenientes que se comunicam com os serviços REST do armazenamento.

### Usar RubyGems para obter o pacote

1. Use uma interface de linha de comando, como o **PowerShell** (Windows), o **Terminal** (Mac) ou o **Bash** (Unix).

2. Digite "gem install azure" na janela de comando para instalar a gema e as dependências.

### Importar o pacote

Use seu editor de texto favorito e adicione o seguinte na parte superior do arquivo do Ruby no qual você pretende usar o armazenamento:

	exigir "azure"

## <a id="setup-a-windows-azure-storage-connection"></a>Configurar uma conexão de armazenamento do Azure

O módulo azure lerá as variáveis de ambiente **AZURE\_STORAGE\_ACCOUNT** e **AZURE\_STORAGE\_ACCESS_KEY** 
para obter informações necessárias para se conectar à sua conta de armazenamento do Azure. Se essas variáveis de ambiente não estiverem definidas, 
você deve especificar as informações da conta antes de usar **Azure:: QueueService** com o seguinte código:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your Azure storage access key>"

Para obter esses valores:

1. Faça logon no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com/).
2. Navegue até a conta de armazenamento que você deseja usar
3. Clique em **GERENCIAR CHAVES** na parte inferior do painel de navegação.
4. Na caixa de diálogo pop-up, você verá o nome da conta de armazenamento, a chave de acesso primária e a chave de acesso secundária. Para obter a chave de acesso, você pode selecionar tanto a chave primária quanto a chave secundária.

## <a id="how-to-create-a-queue"></a>Como: Criar uma fila

O código a seguir cria o objeto **Azure::QueueService**, permitindo que você trabalhe com filas.

	azure_queue_service = Azure::QueueService.new

Use o método **create_queue()** para criar uma fila com o nome especificado.

	begin
	  azure_queue_service.create_queue("test-queue")
	rescue
	  puts $!
	end

## <a id="how-to-insert-a-message-into-a-queue"></a>Como: Inserir uma mensagem em uma fila

Para inserir uma mensagem em uma fila, use o método **create_message()** para criar uma nova mensagem e adicione-a à fila.

	azure_queue_service.create_message("test-queue", "test message")

## <a id="how-to-peek-at-the-next-message"></a>Como: Espiar a próxima mensagem

Você pode inspecionar a mensagem na frente de uma fila sem removê-la da fila chamando o método **peek\_messages()**. Por padrão, **peek\_messages()**inspeciona uma única mensagem.Você também pode especificar quantas mensagens deseja inspecionar.

	result = azure_queue_service.peek_messages("test-queue",
	  {:number_of_messages => 10})

## <a id="how-to-dequeue-the-next-message"></a>Como: Remover a próxima mensagem da fila

É possível remover uma mensagem da fila em duas etapas.

1. Quando você chamar **list\_messages()**, receberá a próxima mensagem em uma fila por padrão. Você também pode especificar quantas mensagens deseja obter.As mensagens retornadas de **list\_messages()** se tornam invisíveis para todas as outras mensagens de leitura de código da fila.Passe o tempo limite de visibilidade em segundos como um parâmetro.

2. Para concluir a remoção da mensagem da fila, chame também **delete_message()**.

Este processo de duas etapas de remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. O código chama **delete\_message** logo depois que a mensagem é processada.

	messages = azure_queue_service.list_messages("test-queue", 30)
	azure_queue_service.delete_message("test-queue", 
	  messages[0].id, messages[0].pop_receipt)

## <a id="how-to-change-the-contents-of-a-queued-message"></a>Como: Alterar o conteúdo de uma mensagem na fila

Você pode alterar o conteúdo de uma mensagem in-loco na fila. O código a seguir usa o método **update_message()** para atualizar uma mensagem.O método retornará uma tupla que contém o recebimento pop da mensagem da fila e um valor de data/hora UTC que representa quando a mensagem estará visível na fila.

	message = azure_queue_service.list_messages("test-queue", 30)
	pop_receipt, time_next_visible = azure_queue_service.update_message(
	  "test-queue", message.id, message.pop_receipt, "updated test message", 
	  30)

## <a id="how-to-additional-options-for-dequeuing-messages"></a>Como: Opções adicionais para remover mensagens da fila

Há duas maneiras de personalizar a recuperação da mensagem de uma fila.

1. Você pode obter um lote de mensagens.

2. Você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo mais ou menos tempo para seu código processar totalmente cada mensagem.

O exemplo de código a seguir usa o método **list\_messages()** para receber 15 mensagens em uma chamada. Em seguida, ele lê e exclui cada mensagem.Ele também define o tempo limite de invisibilidade de cinco minutos para cada mensagem.

	azure_queue_service.list_messages("test-queue", 300
	  {:number_of_messages => 15}).each do |m|
	  puts m.message_text
	  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
	end

## <a id="how-to-get-the-queue-length"></a>Como: Obter o tamanho da fila

Você pode obter uma estimativa do número de mensagens na fila. O método **get\_queue\_metadata()** solicita que o serviço Fila retorne os metadados e a contagem de mensagens aproximados sobre a fila.

	message_count, metadata = azure_queue_service.get_queue_metadata(
	  "test-queue")

## <a id="how-to-delete-a-queue"></a>Como: Excluir uma fila

Para excluir uma fila e todas as mensagens contidas nela, chame o método **delete\_queue()** no objeto de fila.

	azure_queue_service.delete_queue("test-queue")

## <a id="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de filas, siga estes links para saber como fazer tarefas mais complexas de armazenamento.

- Consulte a referência de MSDN: [Armazenando e acessando dados no Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx)
- Visite o[ Blog da Equipe de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Visite o repositório[SDK do Azure para Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) no GitHub.

Para fazer uma comparação entre o serviço Fila do Azure discutido neste artigo e filas do barramento de serviço do Azure discutidas no artigo [Como usar filas do Barramento de Serviço](/pt-br/develop/ruby/how-to-guides/service-bus-queues/) , consulte [Filas do Azure e Filas do Barramento de Serviço do Azure - semelhanças e diferenças](http://msdn.microsoft.com/pt-br/library/windowsazure/hh767287.aspx)

<!--HONumber=35_1-->
