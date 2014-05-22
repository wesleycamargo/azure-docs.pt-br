<properties linkid="dev-ruby-how-to-service-bus-topics" urlDisplayName="Tópicos de Service Bus" pageTitle="Como usar os tópicos de Service Bus (Ruby) - Azure" metaKeywords="Introdução aos tópicos de Service Bus do Azure, Introdução aos tópicos de Service Bus, publicar mensagens de assinatura do Azure, tópicos e assinaturas de mensagens do Azure, tópico de Service Bus ruby" description="Saiba como usar tópicos e assinaturas do Service Bus no Azure. Os exemplos de código são escritos para os aplicativos Ruby." metaCanonical="" services="service-bus" documentationCenter="Ruby" title="Como usar tópicos/assinaturas do Service Bus" authors="guayan" solutions="" manager="" editor="" />





# Como usar os tópicos/assinaturas do Service Bus

Este guia mostrará como usar os tópicos e as assinaturas do Service Bus dos aplicativos Ruby. Os cenários abordados incluem a **criação de tópicos e assinaturas, a criação de filtros de assinatura, o envio de mensagens** para um tópico, o **recebimento de mensagens de uma assinatura** e a **exclusão de tópicos e assinaturas**. Para obter mais informações sobre tópicos e assinaturas, consulte a seção [Próximas etapas](#NextSteps).

## Sumário
* [O que são os tópicos e as assinaturas do Service Bus](#what-are-service-bus-topics)
* [Criar um namespace de serviço](#create-a-service-namespace)
* [Obter as credenciais de gerenciamento padrão do namespace](#obtain-default-credentials)
* [Criar um aplicativo Ruby](#create-a-ruby-application)
* [Configurar seu aplicativo para usar o Service Bus](#configure-your-application-to-use-service-bus)
* [Configurar uma conexão do Service Bus do Azure](#setup-a-windows-azure-service-bus-connection)
* [Como criar um tópico](#how-to-create-a-topic)
* [Como criar assinaturas](#how-to-create-subscriptions)
* [Como enviar mensagens a um tópico](#how-to-send-messages-to-a-topic)
* [Como receber mensagens de uma assinatura](#how-to-receive-messages-from-a-subscription)
* [Como tratar falhas do aplicativo e mensagens ilegíveis](#how-to-handle-application-crashes-and-unreadable-messages)
* [Como excluir tópicos e assinaturas](#how-to-delete-topics-and-subscriptions)
* [Próximas etapas](#NextSteps)

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## <a id="create-a-ruby-application"></a>Criar um aplicativo Ruby

Crie um aplicativo Ruby. Para obter instruções, consulte [Criar um aplicativo Ruby no Azure (a página pode estar em inglês)](/pt-br/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a id="configure-your-application-to-use-service-bus"></a>Configurar seu aplicativo para usar o Service Bus

Para usar o Service Bus do Azure, você precisará baixar e usar o pacote do Azure do Ruby, que inclui um conjunto de bibliotecas convenientes que se comunicam com os serviços REST de armazenamento.

### Usar RubyGems para obter o pacote

1. Use uma interface de linha de comando, como o **PowerShell** (Windows), o **Terminal** (Mac) ou o **Bash** (Unix).

2. Digite "gem install azure" na janela de comando para instalar o gem e as dependências.

### Importar o pacote

Use seu editor de texto favorito e adicione o seguinte na parte superior do arquivo do Ruby no qual você pretende usar o armazenamento:

    require "azure"

## <a id="setup-a-windows-azure-service-bus-connection"></a>Configurar uma conexão do Service Bus do Azure

O módulo do azure lerá as variáveis de ambiente **AZURE\_SERVICEBUS\_NAMESPACE** e **AZURE\_SERVICEBUS\_ACCESS\_KEY**
para obter as informações necessárias para se conectar ao namespace do Service Bus do Azure. Se essas variáveis de ambiente não estiverem definidas, você deverá especificar as informações do namespace antes de usar **Azure::ServiceBusService** com o seguinte código:

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

## <a id="how-to-create-a-topic"></a>Como criar um tópico

O objeto **Azure::ServiceBusService** permite que você trabalhe com tópicos. O código a seguir cria um objeto **Azure::ServiceBusService**. Para criar um tópico, use o método **create\_topic()**. O exemplo a seguir cria um tópico ou imprime o erro, se houver algum.

	azure_service_bus_service = Azure::ServiceBusService.new
	begin
      topic = azure_service_bus_service.create_queue("test-topic")
    rescue
      puts $!
    end

Você também pode passar um objeto **Azure::ServiceBus::Topic** com opções adicionais, que permite a substituição das configurações de tópico padrão, como a vida útil da mensagem ou o tamanho máximo da fila. O exemplo a seguir mostra a configuração do tamanho máximo da fila para 5 GB e a vida útil para 1 minuto:

	topic = Azure::ServiceBus::Topic.new("test-topic")
    topic.max_size_in_megabytes = 5120
    topic.default_message_time_to_live = "PT1M"

    topic = azure_service_bus_service.create_topic(topic)

## <a id="how-to-create-subscriptions"></a>Como criar assinaturas

As assinaturas do tópico também são criadas com o objeto **Azure::ServiceBusService**. As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens entregues à fila virtual da assinatura.

**Observação**
As assinaturas são persistentes e continuarão existindo até que elas, ou o tópico ao qual estão associadas, sejam excluídas. Se o seu aplicativo contiver a lógica para criar uma assinatura, ele deverá primeiro verificar se a assinatura já existe usando o método getSubscription.

### Criar uma assinatura com o filtro padrão (MatchAll)

**MatchAll** será o filtro padrão usado se nenhum filtro for especificado quando uma nova assinatura for criada. Quando o filtro **MatchAll** é usado, todas as mensagens publicadas no tópico são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma assinatura denominada "all-messages" e usa o filtro padrão **MatchAll**.

	subscription = azure_service_bus_service.create_subscription("test-topic", 
	  "all-messages")

### <a id="how-to-create-subscriptions"></a>Criar assinaturas com filtros

Você também pode configurar filtros que permitem atribuir um escopo a quais mensagens enviadas a um tópico devem aparecer dentro de uma assinatura específica do tópico.

O tipo de filtro mais flexível com suporte das assinaturas é o **Azure::ServiceBus::SqlFilter**, que implementa um subconjunto do SQL92. Os filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para obter mais detalhes sobre as expressões que podem ser usadas com um filtro SQL, examine a sintaxe [SqlFilter.SqlExpression](http://msdn.microsoft.com/pt-br/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx).

Os filtros podem ser adicionados a uma assinatura usando o método **create\_rule()** do objeto
**Azure::ServiceBusService**. Este método permite que você adicione novos filtros a uma assinatura existente.

**Observação**
Como o filtro padrão é aplicado automaticamente a todas as assinaturas novas, você deve primeiro remover o filtro padrão ou **MatchAll** substituirá todos os outros filtros que possam ser especificados. Você pode remover a regra padrão usando o método **delete\_rule()** do objeto **Azure::ServiceBusService**.

Os exemplos a seguir criam uma assinatura denominada "high-messages" com um **Azure::ServiceBus::SqlFilter** que seleciona apenas mensagens com uma propriedade **message\_number** personalizada maior que 3:

	subscription = azure_service_bus_service.create_subscription("test-topic", 
	  "high-messages")
	azure_service_bus_service.delete_rule("test-topic", "high-messages", 
	  "$Default")
	
	rule = Azure::ServiceBus::Rule.new("high-messages-rule")
	rule.topic = "test-topic"
	rule.subscription = "high-messages"
	rule.filter = Azure::ServiceBus::SqlFilter.new({ 
	  :sql_expression => "message_number > 3" })
	rule = azure_service_bus_service.create_rule(rule)

Da mesma forma, o exemplo a seguir cria uma assinatura denominada "low-messages" com um **Azure::ServiceBus::SqlFilter** que seleciona apenas as mensagens com uma propriedade **message_number** menor ou igual a 3:

	subscription = azure_service_bus_service.create_subscription("test-topic", 
	  "low-messages")
	azure_service_bus_service.delete_rule("test-topic", "low-messages", 
	  "$Default")
	
	rule = Azure::ServiceBus::Rule.new("low-messages-rule")
	rule.topic = "test-topic"
	rule.subscription = "low-messages"
	rule.filter = Azure::ServiceBus::SqlFilter.new({ 
	  :sql_expression => "message_number <= 3" })
	rule = azure_service_bus_service.create_rule(rule)

Quando uma mensagem for enviada para "test-topic", ela sempre será fornecida aos destinatários inscritos na assinatura do tópico "all-messages" e será fornecida de forma seletiva para os destinatários inscritos nas assinaturas dos tópicos "high-messages" e "low-messages" (dependendo do conteúdo de mensagem).

## <a id="how-to-send-messages-to-a-topic"></a>Como enviar mensagens a um tópico

Para enviar uma mensagem para um tópico de Service Bus, seu aplicativo deverá usar o método **send\_topic\_message()** do objeto **Azure::ServiceBusService**. As mensagens enviadas para os tópicos de Service Bus são objetos **Azure::ServiceBus::BrokeredMessage**. Os objetos **Azure::ServiceBus::BrokeredMessage** têm um conjunto de propriedades padrão (como **label** e **time\_to\_live**), um dicionário usado para armazenar propriedades personalizadas específicas do aplicativo e um corpo de dados da cadeia de caracteres. Um aplicativo pode definir o corpo da mensagem, passando um valor da cadeia de caracteres para um método **send\_topic\_message()** e qualquer propriedade padrão obrigatória será preenchida com valores padrão.

O exemplo a seguir demonstra como enviar cinco mensagens de teste para "test-topic". Observe que o valor da propriedade personalizada **message_number** de cada mensagem varia de acordo com a iteração do loop (isso determinará qual assinatura o receberá):

	5.times do |i|
	  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i, 
	    { :message_number => i })
	  azure_service_bus_service.send_topic_message("test-topic", message)
	end

Os tópicos de Service Bus oferecem suporte a um tamanho máximo de mensagem de 256 MB (o cabeçalho, que inclui as propriedades do aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 MB). Não há nenhum limite no número de mensagens mantidas em um tópico mas há uma capacidade do tamanho total das mensagens mantidas por um tópico. O tamanho do tópico é definido no momento da criação, com um limite máximo de 5 GB.

## <a id="how-to-receive-messages-from-a-subscription"></a>Como receber mensagens de uma assinatura

As mensagens são recebidas de uma assinatura usando o método **receive\_subscription\_message()** no objeto **Azure::ServiceBusService**. Por padrão, as mensagens são lidas (pico) e bloqueadas sem excluí-las da assinatura. Você pode ler e excluir a mensagem da assinatura, definindo a opção **peek\_lock** para **false**.

O comportamento padrão faz com que a leitura e a exclusão se dividam em uma operação de dois estágios, o que torna possível o suporte a aplicativos que não podem tolerar mensagens ausentes. Quando o Service Bus recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, ela a bloqueia para evitar que outros consumidores a recebam e a retorna para o aplicativo. Depois que o aplicativo termina de processar a mensagem (ou a armazena de forma segura para um processamento futuro), ele conclui o segundo estágio do processo de recebimento, chamando o método **delete\_subscription\_message()** e fornecendo a mensagem a ser excluída como um parâmetro. O método **delete\_subscription\_message()** marcará a mensagem como sendo consumida e a removerá da assinatura.

Se o parâmetro **:peek\_lock** estiver definido como **false**, a leitura e a exclusão da mensagem se tornarão o modelo mais simples e funcionarão melhor em cenários nos quais o aplicativo pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Barramento de Serviço terá marcado a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e processadas usando **receive\_subscription\_message()**. O exemplo primeiro recebe e exclui uma mensagem da assinatura "low-messages" usando **:peek\_lock** definido para **false** e, em seguida, recebe outra mensagem de "high-messages" e exclui a mensagem usando **delete\_queue\_message()**:

    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "low-messages", { :peek_lock => false })
    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "high-messages")
    azure_service_bus_service.delete_subscription_message("test-topic", 
	  "high-messages", message.sequence_number, message.lock_token)

## <a id="how-to-handle-application-crashes-and-unreadable-messages"></a>Como tratar falhas do aplicativo e mensagens ilegíveis

O Service Bus proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele poderá chamar o método **unlock\_subscription\_message()** no objeto **Azure::ServiceBusService**. Isso fará com que o Service Bus desbloqueie a mensagem na assinatura e disponibilize-a para ser recebida novamente, pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na assinatura, e se o aplicativo falhar em processar a mensagem antes da expiração do tempo limite do bloqueio (por exemplo, se o aplicativo falhar), o service bus desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Se houver uma falha no aplicativo após o processamento da mensagem, mas antes de o método **delete\_subscription\_message()** ser chamado, a mensagem será novamente entregue ao aplicativo quando ele for reiniciado. Isso é frequentemente chamado de **Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez mas, em algumas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega de mensagem duplicada. Isso geralmente é realizado com a propriedade **message\_id** da mensagem, que permanecerá constante nas tentativas da entrega.

## <a id="how-to-delete-topics-and-subscriptions"></a>Como excluir tópicos e assinaturas

Os tópicos e as assinaturas são persistentes e devem ser explicitamente excluídos por meio do [Portal de Gerenciamento do Azure](https://manage.windowsazure.com) ou de forma programática. O exemplo a seguir demonstra como excluir o tópico denominado "test-topic".

	azure_service_bus_service.delete_topic("test-topic")

A exclusão de um tópico também excluirá todas as assinaturas registradas com o tópico. As assinaturas também podem ser excluídas de forma independente. O código a seguir demonstra como excluir a assinatura denominada "high-messages" do tópico "test-topic":

	azure_service_bus_service.delete_subscription("test-topic", "high-messages")

## <a id="NextSteps"></a>Próximas etapas

Agora que você já sabe os princípios dos tópicos do Service Bus, acesse estes links para saber mais.

-   Consulte a Referência do MSDN: [Filas, tópicos e assinaturas](http://msdn.microsoft.com/pt-br/library/windowsazure/hh367516.aspx)
-   Referência de API para [SqlFilter](http://msdn.microsoft.com/pt-br/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx)
-	Visite o repositório [SDK do Azure para Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) no GitHub

