<properties 
	pageTitle="Como usar o Armazenamento de Fila do Ruby | Microsoft Azure" 
	description="Saiba como usar o serviço Fila do Azure para criar e excluir filas, bem como para inserir, obter e excluir mensagens. Exemplos gravados no Ruby." 
	services="storage" 
	documentationCenter="ruby" 
	authors="tfitzmac,tamram" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="tomfitz"/>


# Como usar o Armazenamento de fila do Ruby

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## Visão geral

Este guia mostra como executar cenários comuns usando o serviço
Serviço de Armazenamento de Fila do Azure. Os exemplos são gravados usando a API do Ruby Azure.
Os cenários abordados incluem **inserir**, **espiar**, **obter** e **excluir** mensagens de fila, bem como **criar e excluir filas**.

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Criar um aplicativo Ruby

Crie um aplicativo Ruby. Para obter instruções, consulte [Criar um aplicativo Ruby no Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## Configurar seu aplicativo para acessar o armazenamento

Para usar o armazenamento do Azure, você deverá baixar e usar o pacote do Azure do Ruby, que inclui um conjunto de bibliotecas convenientes que se comunicam com os serviços REST do armazenamento.

### Use o RubyGems para obter o pacote

1. Use uma interface de linha de comando, como o **PowerShell** (Windows), o **Terminal** (Mac) ou o **Bash** (Unix).

2. Digite "gem install azure" na janela de comando para instalar a gema e as dependências.

### Importar o pacote

Use seu editor de texto favorito e adicione o seguinte na parte superior do arquivo do Ruby no qual você pretende usar o armazenamento:

	require "azure"

## Configurar uma conexão de armazenamento do Azure

O módulo do Azure lerá as variáveis de ambiente **AZURE_STORAGE_ACCOUNT** e **AZURE_STORAGE_ACCESS_KEY** para obter as informações necessárias para se conectar à sua conta de armazenamento do Azure. Se essas variáveis de ambiente não forem definidas, você deverá especificar as informações da conta antes de usar **Azure::QueueService** com o seguinte código:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your Azure storage access key>"

Para obter esses valores:

1. Faça logon no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com/).
2. Navegue até a conta de armazenamento que você deseja usar
3. Clique em **GERENCIAR CHAVES** na parte inferior do painel de navegação.
4. Na caixa de diálogo pop-up, você verá o nome da conta de armazenamento, a chave de acesso primária e a chave de acesso secundária. Para obter a chave de acesso, você pode selecionar tanto a chave primária quanto a chave secundária.

## Como: Criar uma fila

O código a seguir cria um objeto **Azure::QueueService**, permitindo que você trabalhe com filas.

	azure_queue_service = Azure::QueueService.new

Use o método **create_queue()** para criar uma fila com o nome especificado.

	begin
	  azure_queue_service.create_queue("test-queue")
	rescue
	  puts $!
	end

## Como: Inserir uma mensagem em uma fila

Para inserir uma mensagem em uma fila, use o método **create_message()** para criar uma nova mensagem e adicione-a à fila.

	azure_queue_service.create_message("test-queue", "test message")

## Como: Espiar a próxima mensagem

Você pode inspecionar a mensagem na frente de uma fila sem removê-la da fila chamando o método **peek_messages()**. Por padrão, **peek_messages()** inspeciona uma única mensagem. Você também pode especificar quantas mensagens deseja inspecionar.

	result = azure_queue_service.peek_messages("test-queue",
	  {:number_of_messages => 10})

## Como: Remover a próxima mensagem da fila

É possível remover uma mensagem da fila em duas etapas.

1. Quando você chamar **list_messages()**, receberá a próxima mensagem em uma fila por padrão. Você também pode especificar quantas mensagens deseja obter. As mensagens retornadas de **list_messages()** se tornam invisíveis para todas as outras mensagens de leitura de código da fila. Passe o tempo limite de visibilidade em segundos como um parâmetro.

2. Para concluir a remoção da mensagem da fila, chame também **delete_message()**.

Este processo de duas etapas de remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. O código chama **delete_message** logo depois que a mensagem é processada.

	messages = azure_queue_service.list_messages("test-queue", 30)
	azure_queue_service.delete_message("test-queue", 
	  messages[0].id, messages[0].pop_receipt)

## Como: Alterar o conteúdo de uma mensagem na fila

Você pode alterar o conteúdo de uma mensagem in-loco na fila. O código a seguir usa o método **update_message()** para atualizar uma mensagem. O método retornará uma tupla que contém o recebimento pop da mensagem da fila e um valor de data/hora UTC que representa quando a mensagem estará visível na fila.

	message = azure_queue_service.list_messages("test-queue", 30)
	pop_receipt, time_next_visible = azure_queue_service.update_message(
	  "test-queue", message.id, message.pop_receipt, "updated test message", 
	  30)

## Como: Opções adicionais para remover mensagens da fila

Há duas maneiras de personalizar a recuperação da mensagem de uma fila.

1. Você pode obter um lote de mensagens.

2. Você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo mais ou menos tempo para seu código processar totalmente cada mensagem.

O exemplo de código a seguir usa o método **list_messages()** para receber 15 mensagens em uma chamada. Em seguida, ele lê e exclui cada mensagem. Ele também define o tempo limite de invisibilidade de cinco minutos para cada mensagem.

	azure_queue_service.list_messages("test-queue", 300
	  {:number_of_messages => 15}).each do |m|
	  puts m.message_text
	  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
	end

## Como: Obter o tamanho da fila

Você pode obter uma estimativa do número de mensagens na fila. O método **get_queue_metadata()** solicita que o serviço Fila retorne os metadados e a contagem de mensagens aproximados sobre a fila.

	message_count, metadata = azure_queue_service.get_queue_metadata(
	  "test-queue")

## Como: Excluir uma fila

Para excluir uma fila e todas as mensagens contidas nela, chame o método **delete_queue()** no objeto de fila.

	azure_queue_service.delete_queue("test-queue")

## Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de fila, siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

- Consulte a referência de MSDN: [Armazenamento do Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- Visite o Blog da equipe do [Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Visite o repositório [SDK do Azure para Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) no GitHub

Para fazer uma comparação entre o serviço Fila do Azure discutido neste artigo e filas do barramento de serviço do Azure discutidas no artigo [Como usar filas do Barramento de Serviço](/develop/ruby/how-to-guides/service-bus-queues/), consulte [Filas do Azure e Filas do Barramento de Serviço do Azure - semelhanças e diferenças](http://msdn.microsoft.com/library/azure/hh767287.aspx)

<!--HONumber=49--> 