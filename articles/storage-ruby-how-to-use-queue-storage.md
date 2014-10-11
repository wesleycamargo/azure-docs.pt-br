<properties linkid="dev-ruby-how-to-service-bus-queues" urlDisplayName="Queue Service" pageTitle="How to use the queue service (Ruby) | Microsoft Azure" metaKeywords="Azure Queue Service get messages Ruby" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Ruby." metaCanonical="" services="storage" documentationCenter="Ruby" title="How to Use the Queue Storage Service from Ruby" authors="guayan" solutions="" manager="" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="guayan"></tags>

# Como usar o serviço de armazenamento de filas do Ruby

Este guia mostra como executar cenários comuns usando o serviço de armazenamento de Fila do Windows
Azure. Os exemplos são escritos usando a API Ruby Azure.
Os cenários abordados incluem **inserir**, **espiar**, **obter**
e **excluir** mensagens da fila, além de **criar e
excluir filas**. Para obter mais informações sobre filas, consulte a seção [Próximas
etapas][].

## Sumário

-   [O que é armazenamento de fila?][]
-   [Conceitos][]
-   [Criar uma conta de armazenamento do Azure][]
-   [Criar um aplicativo Ruby][]
-   [Configurar seu aplicativo para acessar o armazenamento][]
-   [Configurar uma conexão de armazenamento do Azure][]
-   [Como: Criar uma fila][]
-   [Como: Inserir uma mensagem em uma fila][]
-   [Como: Exibir na próxima mensagem][]
-   [Como: Remover a próxima mensagem da fila][]
-   [Como: Mudar o conteúdo de uma mensagem enfileirada][]
-   [Como: Opções adicionais para remover mensagens da fila][]
-   [Como: Obter o tamanho da fila][]
-   [Como: Excluir uma fila][]
-   [Próximas etapas][Próximas
    etapas]

[WACOM.INCLUDE [howto-queue-storage][]]

## <span id="CreateAccount"></span></a>Criar uma conta de armazenamento do Azure

[WACOM.INCLUDE [create-storage-account][]]

## <span id="create-a-ruby-application"></span></a>Criar um aplicativo Ruby

Crie um aplicativo Ruby. Para obter instruções,
consulte [Criar um aplicativo Ruby no Azure][].

## <span id="configure-your-application-to-access-storage"></span></a>Configurar seu aplicativo para acessar o armazenamento

Para usar o armazenamento do Azure, você deverá baixar e usar o pacote do Azure do Ruby, que inclui um conjunto de bibliotecas convenientes que se comunicam com os serviços REST do armazenamento.

### Usar RubyGems para obter o pacote

1.  Use uma interface de linha de comando como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).

2.  Digite "gem install azure" na janela de comandos para instalar a gema e as dependências.

### Importar o pacote

Use seu editor de texto favorito e adicione o seguinte na parte superior do arquivo do Ruby no qual você pretende usar o armazenamento:

    require "azure"

## <span id="setup-a-windows-azure-storage-connection"></span></a>Configurar uma conexão de armazenamento do Azure

O módulo do Azure lerá as variáveis de ambiente **AZURE\_STORAGE\_ACCOUNT** e **AZURE\_STORAGE\_ACCESS\_KEY**
para obter as informações necessárias para se conectar à sua conta de armazenamento do Azure. Se essas variáveis de ambiente não forem definidas,
especifique as informações da conta antes de usar **Azure::QueueService** com o seguinte código:

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your Azure storage access key>"

Para obter esses valores:

1.  Faça logon no [Portal de Gerenciamento do Azure][].
2.  Navegue até a conta de armazenamento que você deseja usar
3.  Clique em **GERENCIAR CHAVES** na parte inferior do painel de navegação.
4.  Na caixa de diálogo pop-up, você verá o nome da conta de armazenamento, a chave de acesso primária e a chave de acesso secundária. Para obter a chave de acesso, você pode selecionar tanto a chave primária quanto a chave secundária.

## <span id="how-to-create-a-queue"></span></a>Como: Criar uma fila

O código a seguir cria o objeto **Azure::QueueService**, permitindo que você trabalhe com filas.

    azure_queue_service = Azure::QueueService.new

Use o método **create\_queue()** para criar uma fila com o nome especificado.

    begin
      azure_queue_service.create_queue("test-queue")
    rescue
      puts $!
    end

## <span id="how-to-insert-a-message-into-a-queue"></span></a>Como: Inserir uma mensagem em uma fila

Para inserir uma mensagem em uma fila, use o método **create\_message()** para criar uma nova mensagem e adicione-a à fila.

    azure_queue_service.create_message("test-queue", "test message")

## <span id="how-to-peek-at-the-next-message"></span></a>Como: Espiar a próxima mensagem

Você pode espiar a mensagem na frente de uma fila sem removê-la da fila chamando o método **peek\_messages()**. Por padrão, **peek\_messages()** exibe uma única mensagem. Você também pode especificar quantas mensagens deseja espiar.

    result = azure_queue_service.peek_messages("test-queue",
      {:number_of_messages => 10})

## <span id="how-to-dequeue-the-next-message"></span></a>Como: Remover a próxima mensagem da fila

É possível remover uma mensagem da fila em duas etapas.

1.  Quando você chamar **list\_messages()**, receberá a próxima mensagem em uma fila por padrão. Você também pode especificar quantas mensagens deseja obter. As mensagens retornadas de **list\_messages()** se tornam invisíveis para todas as outras mensagens de leitura de código da fila. Passe o tempo limite de visibilidade em segundos como um parâmetro.

2.  Para concluir a remoção da mensagem da fila, chame também **delete\_message()**.

Este processo de duas etapas de remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. O código chama **delete\_message()** logo depois que a mensagem é processada.

    messages = azure_queue_service.list_messages("test-queue", 30)
    azure_queue_service.delete_message("test-queue", 
      messages[0].id, messages[0].pop_receipt)

## <span id="how-to-change-the-contents-of-a-queued-message"></span></a>Como: Alterar o conteúdo de uma mensagem na fila

Você pode alterar o conteúdo de uma mensagem in-loco na fila. O código a seguir usa o método **update\_message()** para atualizar uma mensagem. O método retornará uma tupla que contém o recebimento pop da mensagem da fila e um valor de data/hora UTC que representa quando a mensagem estará visível na fila.

    message = azure_queue_service.list_messages("test-queue", 30)
    pop_receipt, time_next_visible = azure_queue_service.update_message(
      "test-queue", message.id, message.pop_receipt, "updated test message", 
      30)

## <span id="how-to-additional-options-for-dequeuing-messages"></span></a>Como: Opções adicionais para remover mensagens da fila

Há duas maneiras de personalizar a recuperação da mensagem de uma fila.

1.  Você pode obter um lote de mensagens.

2.  Você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo mais ou menos tempo para seu código processar totalmente cada mensagem.

O exemplo de código a seguir usa o método **list\_messages()** para receber 15 mensagens em uma chamada. Em seguida, ele lê e exclui cada mensagem. Ele também define o tempo limite de invisibilidade de cinco minutos para cada mensagem.

    azure_queue_service.list_messages("test-queue", 300
      {:number_of_messages => 15}).each do |m|
      puts m.message_text
      azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
    end

## <span id="how-to-get-the-queue-length"></span></a>Como: Obter o tamanho da fila

Você pode obter uma estimativa do número de mensagens na fila. O método **get\_queue\_metadata()** solicita que o serviço Fila retorne os metadados e a contagem de mensagens aproximados sobre a fila.

    message_count, metadata = azure_queue_service.get_queue_metadata(
      "test-queue")

## <span id="how-to-delete-a-queue"></span></a>Como: Excluir uma fila

Para excluir uma fila e todas as mensagens contidas nela, chame o método **delete\_queue()** no objeto de fila.

    azure_queue_service.delete_queue("test-queue")

## <span id="next-steps"></span></a> Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de filas, siga estes links para saber como fazer tarefas mais complexas de armazenamento.

-   Consulte a referência de MSDN: [Armazenando e acessando dados no Azure][]
-   Visite o [Blog da Equipe de Armazenamento do Azure][]
-   Visite o repositório [SDK do Azure para Ruby][] no GitHub

Para fazer uma comparação entre o serviço Fila do Azure discutido neste artigo e as filas de Service Bus do Azure discutidas no artigo [Como usar as filas de Service Bus][], consulte [Filas do Azure e filas de Service Bus do Azure - semelhanças e diferenças][]

  [Próximas etapas]: #next-steps
  [O que é armazenamento de fila?]: #what-is
  [Conceitos]: #concepts
  [Criar uma conta de armazenamento do Azure]: #CreateAccount
  [Criar um aplicativo Ruby]: #create-a-ruby-application
  [Configurar seu aplicativo para acessar o armazenamento]: #configure-your-application-to-access-storage
  [Configurar uma conexão de armazenamento do Azure]: #setup-a-windows-azure-storage-connection
  [Como: Criar uma fila]: #how-to-create-a-queue
  [Como: Inserir uma mensagem em uma fila]: #how-to-insert-a-message-into-a-queue
  [Como: Exibir na próxima mensagem]: #how-to-peek-at-the-next-message
  [Como: Remover a próxima mensagem da fila]: #how-to-dequeue-the-next-message
  [Como: Mudar o conteúdo de uma mensagem enfileirada]: #how-to-change-the-contents-of-a-queued-message
  [Como: Opções adicionais para remover mensagens da fila]: #how-to-additional-options-for-dequeuing-messages
  [Como: Obter o tamanho da fila]: #how-to-get-the-queue-length
  [Como: Excluir uma fila]: #how-to-delete-a-queue
  [howto-queue-storage]: ../includes/howto-queue-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [Criar um aplicativo Ruby no Azure]: /en-us/develop/ruby/tutorials/web-app-with-linux-vm/
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [Blog da Equipe de Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [SDK do Azure para Ruby]: https://github.com/WindowsAzure/azure-sdk-for-ruby
  [Como usar as filas de Service Bus]: /en-us/develop/ruby/how-to-guides/service-bus-queues/
  [Filas do Azure e filas de Service Bus do Azure - semelhanças e diferenças]: http://msdn.microsoft.com/en-us/library/windowsazure/hh767287.aspx
