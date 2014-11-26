<properties linkid="dev-ruby-how-to-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (Ruby) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Ruby" description="Learn how to use Service Bus queues in Azure. Code samples written in Ruby." metaCanonical="" services="service-bus" documentationCenter="Ruby" title="How to Use Service Bus Queues" authors="guayan" solutions="" manager="" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="guayan" />

# Como usar as filas do Service Bus

Este guia mostrará como usar as filas do Service Bus. Os exemplos são
gravados no Ruby e usam a gema do Azure. Os cenários
abrangidos incluem **criar filas, enviar e receber mensagens** e
**excluir filas**. Para obter mais informações sobre filas, consulte a seção [Próximas etapas][Próximas etapas].

## Sumário

-   [O que são as filas do Service Bus?][O que são as filas do Service Bus?]
-   [Criar um namespace de serviço][Criar um namespace de serviço]
-   [Obter as credenciais de gerenciamento padrão do namespace][Obter as credenciais de gerenciamento padrão do namespace]
-   [Criar um aplicativo Ruby][Criar um aplicativo Ruby]
-   [Configurar seu aplicativo para usar o Service Bus][Configurar seu aplicativo para usar o Service Bus]
-   [Configurar uma conexão do Service Bus do Azure][Configurar uma conexão do Service Bus do Azure]
-   [Como criar uma fila][Como criar uma fila]
-   [Como enviar mensagens para uma fila][Como enviar mensagens para uma fila]
-   [Como receber mensagens de uma fila][Como receber mensagens de uma fila]
-   [Como tratar falhas do aplicativo e mensagens ilegíveis][Como tratar falhas do aplicativo e mensagens ilegíveis]
-   [Próximas etapas][Próximas etapas]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <span id="create-a-ruby-application"></span></a>Criar um aplicativo Ruby

Crie um aplicativo Ruby. Para obter instruções, consulte [Criar um aplicativo Ruby no Azure (a página pode estar em inglês)][Criar um aplicativo Ruby no Azure (a página pode estar em inglês)].

## <span id="configure-your-application-to-use-service-bus"></span></a>Configurar seu aplicativo para usar o Service Bus

Para usar o Service Bus do Azure, você precisará baixar e usar o pacote do Azure do Ruby, que inclui um conjunto de bibliotecas convenientes que se comunicam com os serviços REST de armazenamento.

### Usar RubyGems para obter o pacote

1.  Use uma interface da linha de comando como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).

2.  Digite "gem install azure" na janela de comandos para instalar a gema e as dependências.

### Importar o pacote

Use seu editor de texto favorito e adicione o seguinte na parte superior do arquivo do Ruby no qual você pretende usar o armazenamento:

    require "azure"

## <span id="setup-a-windows-azure-service-bus-connection"></span></a>Configurar uma conexão do Service Bus do Azure

O módulo do azure lerá as variáveis de ambiente **AZURE\_SERVICEBUS\_NAMESPACE** e **AZURE\_SERVICEBUS\_ACCESS\_KEY**
 para obter as informações necessárias para se conectar ao seu namespace do Service Bus do Azure. Se essas variáveis de ambiente não forem definidas, você deverá especificar as informações do namespace antes de usar **Azure::ServiceBusService** com o seguinte código:

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

## <span id="how-to-create-a-queue"></span></a>Como criar uma fila

O objeto **Azure::ServiceBusService** permite que você trabalhe com filas. Para criar uma fila, use o método **create\_queue()**. O exemplo a seguir cria uma fila ou imprime o erro, se houver.

    azure_service_bus_service = Azure::ServiceBusService.new
    begin
      queue = azure_service_bus_service.create_queue("test-queue")
    rescue
      puts $!
    end

Você também pode passar em um objeto **Azure::ServiceBus::Queue** com opções adicionais, que permite que a substituição de configurações padrão da fila como a vida útil da mensagem ou o tamanho máximo da fila. O exemplo a seguir mostra a configuração do tamanho máximo da fila para 5 GB e a vida útil para 1 minuto:

    queue = Azure::ServiceBus::Queue.new("test-queue")
    queue.max_size_in_megabytes = 5120
    queue.default_message_time_to_live = "PT1M"

    queue = azure_service_bus_service.create_queue(queue)

## <span id="how-to-send-messages-to-a-queue"></span></a>Como enviar mensagens para uma fila

Para enviar uma mensagem a uma fila do Service Bus, o aplicativo chama o método **send\_queue\_message()** no objeto **Azure::ServiceBusService**. Mensagens enviadas para (e recebidas das) as filas do service bus são objetos **Azure::ServiceBus::BrokeredMessage** e têm um conjunto de propriedades padrão (como **rótulo** e **time\_to\_live**), um dicionário que é usado para manter propriedades personalizadas específicas ao aplicativo e um corpo de dados arbitrários do aplicativo. Um aplicativo pode definir o corpo da mensagem, passando um valor da cadeia de caracteres como a mensagem, e qualquer propriedade padrão necessária será preenchida com valores padrão.

O exemplo a seguir demonstra como enviar uma mensagem de teste à fila chamada "test-queue" que use **send\_queue\_message()**:

    message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
    message.correlation_id = "test-correlation-id"
    azure_service_bus_service.send_queue_message("test-queue", message)

As filas de Service Bus oferecem suporte a um tamanho máximo de mensagem de 256 KB (o cabeçalho, que inclui as propriedades do aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 KB). Não há nenhum limite no número de mensagens mantidas em uma fila mas há uma capacidade do tamanho total das mensagens mantidas por uma fila. O tamanho da fila é definido no momento da criação, com um limite superior de 5 GB.

## <span id="how-to-receive-messages-from-a-queue"></span></a>Como receber mensagens de uma fila

As mensagens são recebidas de uma fila com o método de **receive\_queue\_message()** no objeto **Azure::ServiceBusService**. Por padrão, as mensagens são lidas e bloqueadas sem excluí-las da fila. Entretanto, você pode excluir mensagens da fila à medida que são lidas, definindo a opção **:peek\_lock** para **false**.

O comportamento padrão faz com que a leitura e a exclusão se dividam em uma operação de dois estágios, o que torna possível o suporte a aplicativos que não podem tolerar mensagens ausentes. Quando o Service Bus recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, ela a bloqueia para evitar que outros consumidores a recebam e a retorna para o aplicativo. Depois que o aplicativo termina de processar a mensagem (ou a armazena de forma segura para um processamento futuro), ele conclui o segundo estágio processo de recebimento chamando o método **delete\_queue\_message()** e fornecendo a mensagem a ser excluída como um parâmetro. O método **delete\_queue\_message()** marcará a mensagem como consumida e a removerá da fila.

Se o parâmetro **:peek\_lock** estiver definido como **false**, a leitura e a exclusão da mensagem se tornam o modelo mais simples e funcionam melhor em cenários nos quais o aplicativo pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Barramento de Serviço terá marcado a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e processadas usando **receive\_queue\_message()**. O exemplo primeiro recebe e exclui uma mensagem usando **: peek\_lock** definido para **false**, em seguida, ele recebe outra mensagem e exclui a mensagem usando **delete\_queue\_message()**:

    message = azure_service_bus_service.receive_queue_message("test-queue", 
      { :peek_lock => false })
    message = azure_service_bus_service.receive_queue_message("test-queue")
    azure_service_bus_service.delete_queue_message(message)

## <span id="how-to-handle-application-crashes-and-unreadable-messages"></span></a>Como tratar falhas do aplicativo e mensagens ilegíveis

O Service Bus proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele chamará o método **unlock\_queue\_message()** no objeto **Azure::ServiceBusService**. Isso fará com que o Service Bus desbloqueie a mensagem na fila e disponibilize-a para que ela possa ser recebida novamente pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na fila e, se o aplicativo não conseguir processar a mensagem antes da expiração do tempo limite do bloqueio (por exemplo, em caso de falha do aplicativo), o Service Bus desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que o método **delete\_queue\_message()** seja chamado, a mensagem será fornecida novamente ao aplicativo quando reiniciar. Isso é frequentemente chamado de **Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez mas, em algumas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega de mensagem duplicada. Isso geralmente é obtido com a propriedade **message\_id** da mensagem, que permanecerá constante nas tentativas da entrega.

## <span id="next-steps"></span></a>Próximas etapas

Agora que você já sabe as noções básicas das filas de Service Bus, siga estes links para saber mais.

-   Consulte a referência de MSDN: [Filas, tópicos e assinaturas][Filas, tópicos e assinaturas]
-   Visite o repositório [SDK do Azure para o nó][SDK do Azure para o nó] no GitHub.

Para fazer uma comparação entre as filas de Service Bus do Azure discutidas nesse artigo e as filas do Azure discutidas no artigo [Como usar o serviço Fila do Azure][Como usar o serviço Fila do Azure], consulte [Filas do Azure e filas de Service Bus do Azure - semelhanças e diferenças][Filas do Azure e filas de Service Bus do Azure - semelhanças e diferenças]

  [Próximas etapas]: #next-steps
  [Criar um namespace de serviço]: #create-a-service-namespace
  [Obter as credenciais de gerenciamento padrão do namespace]: #obtain-default-credentials
  [Criar um aplicativo Ruby]: #create-a-ruby-application
  [Configurar seu aplicativo para usar o Service Bus]: #configure-your-application-to-use-service-bus
  [Configurar uma conexão do Service Bus do Azure]: #setup-a-windows-azure-service-bus-connection
  [Como criar uma fila]: #how-to-create-a-queue
  [Como enviar mensagens para uma fila]: #how-to-send-messages-to-a-queue
  [Como receber mensagens de uma fila]: #how-to-receive-messages-from-a-queue
  [Como tratar falhas do aplicativo e mensagens ilegíveis]: #how-to-handle-application-crashes-and-unreadable-messages
  [Filas, tópicos e assinaturas]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh367516.aspx
  [SDK do Azure para o nó]: https://github.com/WindowsAzure/azure-sdk-for-ruby
  [Como usar o serviço Fila do Azure]: /pt-br/develop/ruby/how-to-guides/queue-service/
  [Filas do Azure e filas de Service Bus do Azure - semelhanças e diferenças]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh767287.aspx
