<properties urlDisplayName="Service Bus Queues" pageTitle="Como usar filas do barramento de serviço (Ruby) - Azure" metaKeywords="filas do barramento de serviço do Azure, filas do Azure, sistema de mensagens do Azure, filas do Azure em Ruby" description="Learn how to use Service Bus queues in Azure. Code samples written in Ruby." metaCanonical="" services="service-bus" documentationCenter="Ruby" title="How to Use Service Bus Queues" authors="guayan" solutions="" manager="wpickett" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="guayan" />




# Como usar as filas do Service Bus

Este guia mostra como usar as filas do Barramento de Serviço. Os exemplos são gravados no Ruby e usam a gema do Azure. Os cenários abrangidos incluem **criar filas, enviar e receber mensagens** e **excluir filas**. Para obter mais informações sobre filas, consulte a seção [Próximas etapas](#next-steps).

## Sumário

* [O que são as filas do Service Bus?](#what-are-service-bus-queues)
* [Criar um namespace de serviço](#create-a-service-namespace)
* [Obter as credenciais de gerenciamento padrão do namespace](#obtain-default-credentials)
* [Criar um aplicativo Ruby](#create-a-ruby-application)
* [Configurar seu aplicativo para usar o Service Bus](#configure-your-application-to-use-service-bus)
* [Configurar uma conexão do Service Bus do Azure](#setup-a-windows-azure-service-bus-connection)
* [Como criar uma fila](#how-to-create-a-queue)
* [Como enviar mensagens para uma fila](#how-to-send-messages-to-a-queue)
* [Como receber mensagens de uma fila](#how-to-receive-messages-from-a-queue)
* [Como tratar falhas do aplicativo e mensagens ilegíveis](#how-to-handle-application-crashes-and-unreadable-messages)
* [Próximas etapas](#next-steps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <a id="create-a-ruby-application"></a>Criar um aplicativo Ruby

Crie um aplicativo Ruby. Para obter instruções, consulte [Criar um aplicativo Ruby no Azure (a página pode estar em inglês)](/pt-br/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a id="configure-your-application-to-use-service-bus"></a>Configurar seu aplicativo para usar o Service Bus

Para usar o Service Bus do Azure, você precisará baixar e usar o pacote do Azure do Ruby, que inclui um conjunto de bibliotecas convenientes que se comunicam com os serviços REST de armazenamento.

### Usar RubyGems para obter o pacote

1. Use uma interface da linha de comando como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).

2. Digite "gem install azure" na janela de comandos para instalar a gema e as dependências.

### Importar o pacote

Use seu editor de texto favorito e adicione o seguinte na parte superior do arquivo do Ruby no qual você pretende usar o armazenamento:

    require "azure"

## <a id="setup-a-windows-azure-service-bus-connection"></a>Configurar uma conexão do Service Bus do Azure

O módulo do azure lerá as variáveis de ambiente e **AZURE\_SERVICEBUS\_NAMESPACE** e **AZURE\_SERVICEBUS\_ACCESS_KEY** para obter as informações necessárias para se conectar ao namespace Barramento de Serviço do Azure. Se essas variáveis de ambiente não forem definidas, você deverá especificar as informações do namespace antes de usar **Azure::ServiceBusService** com o seguinte código:

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

## <a id="how-to-create-a-queue"></a>Como criar uma fila

O objeto **Azure::ServiceBusService** permite que você trabalhe com filas. Para criar uma fila, use o método **create_queue()**. O seguinte exemplo cria uma fila ou imprime o erro, se houver algum.

    azure_service_bus_service = Azure::ServiceBusService.new
    begin
      queue = azure_service_bus_service.create_queue("test-queue")
    rescue
      puts $!
    end

Você também pode passar em um objeto **Azure::ServiceBus::Queue** com opções adicionais, que permite que a substituição de configurações padrão da fila como a vida útil da mensagem ou o tamanho máximo da fila. O seguinte exemplo mostra a definição do tamanho máximo da fila para 5 GB e a vida útil para 1 minuto:

    queue = Azure::ServiceBus::Queue.new("test-queue")
    queue.max_size_in_megabytes = 5120
    queue.default_message_time_to_live = "PT1M"

    queue = azure_service_bus_service.create_queue(queue)

## <a id="how-to-send-messages-to-a-queue"></a>Como enviar mensagens para uma fila

Para enviar uma mensagem a uma fila do Barramento de Serviço, o aplicativo chama o método **send\_queue\_message()** no objeto **Azure::ServiceBusService**. Mensagens enviadas para (e recebidas das) as filas do Barramento de Serviço são objetos **Azure::ServiceBus::BrokeredMessage** e têm um conjunto de propriedades padrão (como **rótulo** e **time\_to\_live**), um dicionário que é usado para manter propriedades personalizadas específicas ao aplicativo e um corpo de dados arbitrários do aplicativo. Um aplicativo pode definir o corpo da mensagem transmitindo um valor da cadeia como a mensagem e quaisquer propriedades padrão solicitadas serão preenchidas com valores padrão.

O exemplo a seguir demonstra como enviar uma mensagem de teste à fila chamada "test-queue" que use **send\_queue\_message()**:

    message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
    message.correlation_id = "test-correlation-id"
    azure_service_bus_service.send_queue_message("test-queue", message)

As filas do Service Bus suportam um tamanho máximo de mensagem de 256 KB (o cabeçalho, incluindo as propriedades padrão e personalizadas do aplicativo podem ter um tamanho máximo de 64 KB). Não há nenhum limite no número de mensagens mantidas em uma fila mas há uma capacidade do tamanho total das mensagens mantidas por uma fila. O tamanho da fila é definido no momento da criação, com um limite superior de 5 GB.

## <a id="how-to-receive-messages-from-a-queue"></a>Como receber mensagens de uma fila

As mensagens são recebidas de uma fila com o método de **receive\_queue\_message()** no objeto **Azure::ServiceBusService**. Por padrão, as mensagens são lidas e bloqueadas sem excluí-las da fila. Entretanto, você pode excluir mensagens da fila à medida que são lidas, definindo a opção **:peek_lock** para **false**.

O comportamento padrão faz com que a leitura e a exclusão se dividam em uma operação de dois estágios, o que torna possível o suporte a aplicativos que não podem tolerar mensagens ausentes. Quando o Service Bus recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, ela a bloqueia para evitar que outros consumidores a recebam e a retorna para o aplicativo. Depois que o aplicativo termina de processar a mensagem (ou a armazena de forma segura para um processamento futuro), ele conclui o segundo estágio processo de recebimento chamando o método **delete\_queue\_message()** e fornecendo a mensagem a ser excluída como um parâmetro. O método **delete\_queue\_message()** marcará a mensagem como consumida e a removerá da fila.

Se o parâmetro **:peek\_lock** estiver definido como **false**, a leitura e a exclusão da mensagem se tornam o modelo mais simples e funcionam melhor em cenários nos quais o aplicativo pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Service Bus marcou a mensagem como consumida e o aplicativo reiniciou e começou a consumir mensagens novamente, ela perdeu a mensagem que foi consumida antes da falha.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e processadas usando **receive\_queue\_message()**. O exemplo primeiro recebe e exclui uma mensagem usando **:peek\_lock** definido para **false**, em seguida, ele recebe outra mensagem e exclui a mensagem usando **delete\_queue\_message()**:

    message = azure_service_bus_service.receive_queue_message("test-queue", 
	  { :peek_lock => false })
    message = azure_service_bus_service.receive_queue_message("test-queue")
    azure_service_bus_service.delete_queue_message(message)

## <a id="how-to-handle-application-crashes-and-unreadable-messages"></a>Como tratar falhas do aplicativo e mensagens ilegíveis

O Service Bus proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros em seu aplicativo ou das dificuldades do processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele chamará o método **unlock\_queue\_message()** no objeto **Azure::ServiceBusService**. Isso fará com que o service bus desbloqueie a mensagem na fila e disponibilize-a para que possa ser recebida novamente, tanto pelo mesmo aplicativo de consumo quanto por algum outro.

Também há um tempo limite associado a uma mensagem bloqueada na fila e, se o aplicativo não conseguir processar a mensagem antes da expiração do tempo limite do bloqueio (por exemplo, em caso de falha do aplicativo), o Service Bus desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que o método **delete\_queue\_message()** seja chamado, a mensagem será fornecida novamente ao aplicativo quando reiniciar. Isso é frequentemente chamado de **Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez mas, em algumas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega de mensagem duplicada. Isso geralmente é obtido com a propriedade **message\_id** da mensagem, que permanecerá constante nas tentativas da entrega.

## <a id="next-steps"></a>Próximas etapas

Agora que você já sabe as noções básicas das filas de Service Bus, siga estes links para saber mais.

-   Consultar a referência de MSDN: [Filas, tópicos e assinaturas](http://msdn.microsoft.com/pt-br/library/windowsazure/hh367516.aspx)
-   Visite o repositório [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) no GitHub

Para fazer uma comparação entre as filas de Barramento de Serviço do Azure discutidas nesse artigo e as filas do Azure discutidas no artigo [Como usar o serviço Fila do Azure](/pt-br/develop/ruby/how-to-guides/queue-service/), consulte [Filas do Azure e filas de Service Bus do Azure - semelhanças e diferenças](http://msdn.microsoft.com/pt-br/library/windowsazure/hh767287.aspx)
