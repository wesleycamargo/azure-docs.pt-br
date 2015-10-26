<properties
	pageTitle="Como usar os tópicos de Barramento de Serviço (Ruby) | Microsoft Azure"
	description="Aprenda a usar assinaturas e tópicos do barramento de serviço no Azure. Exemplos de código são escritos para aplicativos Ruby."
	services="service-bus"
	documentationCenter="ruby"
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="08/31/2015"
	ms.author="tomfitz"/>





# Como usar os tópicos/assinaturas do Barramento de Serviço

Este guia mostrará como usar os tópicos e as assinaturas do Service Bus dos aplicativos Ruby. Os cenários abordados incluem a **criação de tópicos e assinaturas, a criação de filtros de assinatura, o envio de mensagens** para um tópico, o **recebimento de mensagens de uma assinatura** e a **exclusão de tópicos e assinaturas**. Para obter mais informações sobre tópicos e assinaturas, consulte a seção [Próximas etapas](#next-steps).

## O que são tópicos e assinaturas do Barramento de Serviço

Os tópicos e assinaturas do Barramento de serviço oferecem suporte a um modelo de **comunicação de mensagens de publicação/assinatura**. Durante o uso de tópicos e assinaturas, os componentes de um aplicativo distribuído não se comunicam diretamente uns com os outros, eles trocam mensagens por meio de um tópico, que atua como um intermediário.

![Conceitos de tópico](./media/service-bus-ruby-how-to-use-topics-subscriptions/sb-topics-01.png)

Em contraste com filas do Barramento de serviço, em que cada mensagem é processada por um único consumidor, tópicos e assinaturas fornecem uma forma de comunicação de **um para muitos** usando um padrão de publicação/assinatura. É possível registrar várias assinaturas para um tópico. Quando uma mensagem é enviada a um tópico, é disponibilizada para cada assinatura para ser manipulada/processada de forma independente.

Uma assinatura de tópico é semelhante a uma fila virtual que recebe cópias das mensagens enviadas para o tópico. Outra opção é registrar regras de filtro para um tópico por assinatura, o que permite que você filtre/restrinja quais mensagens para um tópico são recebidas por quais assinaturas de tópico.

As assinaturas e os tópicos do Barramento de Serviço permitem o dimensionamento para processar um grande número de mensagens em muitos usuários e aplicativos.

## Criar um namespace de serviço

Para começar a usar filas do Barramento de Serviço no Azure, primeiro crie um namespace de serviço. Um namespace de serviço fornece um contêiner de controle para endereçamento dos recursos do Barramento de Serviço em seu aplicativo. Você deve criar o namespace através da interface de linha de comando porque o Portal não cria o barramento de serviço com uma conexão do ACS.

Para criar um namespace:

1. Abra uma janela de console do PowerShell do Azure.

2. Digite o comando para criar um namespace de barramento de serviço do Azure, conforme mostrado abaixo. Forneça seu próprio valor de namespace e especifique a mesma região que a do seu aplicativo.

      New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true

      ![Criar um Namespace](./media/service-bus-ruby-how-to-use-topics-subscriptions/showcmdcreate.png)

## Obter credenciais de gerenciamento padrão do namespace

A fim de executar operações de gerenciamento, como criar uma fila no novo namespace, obtenha as credenciais de gerenciamento para o namespace.

O cmdlet do PowerShell que você executou para criar o namespace do barramento de serviço do Azure exibe a chave que você pode usar para gerenciar o namespace. Copie o valor de **DefaultKey**. Você usará esse valor em seu código posteriormente neste tutorial.

      ![Copy key](./media/service-bus-ruby-how-to-use-topics-subscriptions/defaultkey.png)

> [AZURE.NOTE]Você também poderá encontrar essa chave se efetuar logon no [Portal do Azure](http://manage.windowsazure.com/) e navegar até as informações de conexão para seu namespace do barramento de serviço.

## Criar um aplicativo Ruby

Crie um aplicativo Ruby. Para obter instruções, consulte [Criar um aplicativo Ruby no Azure (a página pode estar em inglês)](/develop/ruby/tutorials/web-app-with-linux-vm/).

## Configurar seu aplicativo para usar o Barramento de Serviço

Para usar o Service Bus do Azure, você precisará baixar e usar o pacote do Azure do Ruby, que inclui um conjunto de bibliotecas convenientes que se comunicam com os serviços REST de armazenamento.

### Usar RubyGems para obter o pacote

1. Use uma interface da linha de comando como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).

2. Digite "gem install azure" na janela de comandos para instalar a gema e as dependências.

### Importar o pacote

Use seu editor de texto favorito e adicione o seguinte na parte superior do arquivo do Ruby no qual você pretende usar o armazenamento:

    require "azure"

## Configurar uma conexão do Barramento de Serviço do Azure

O módulo do Azure irá ler as variáveis de ambiente **AZURE\_SERVICEBUS\_NAMESPACE** e **AZURE\_SERVICEBUS\_ACCESS\_KEY** para obter as informações necessárias para se conectar ao seu namespace do barramento de serviço do Azure. Se essas variáveis de ambiente não forem definidas, você deverá especificar as informações do namespace antes de usar **Azure::ServiceBusService** com o seguinte código:

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

Defina o valor de namespace do barramento de serviço para o valor que você criou, em vez de toda a URL. Por exemplo, use **"yourexamplenamespace"**, não "yourexamplenamespace.servicebus.windows.net".

## Como criar um tópico

O objeto **Azure::ServiceBusService** permite que você trabalhe com tópicos. O código a seguir cria um objeto **Azure::ServiceBusService**. Para criar um tópico, use o método **create\_topic()**. O exemplo a seguir cria um tópico ou imprime o erro, se houver algum.

	azure_service_bus_service = Azure::ServiceBusService.new
	begin
      topic = azure_service_bus_service.create_queue("test-topic")
    rescue
      puts $!
    end

Você também pode passar em um objeto **Azure::ServiceBus::Topic** com opções adicionais, que permite que a substituição de configurações padrão do tópico como a vida útil da mensagem ou o tamanho máximo da fila. O exemplo a seguir mostra a configuração do tamanho máximo da fila para 5 GB e a vida útil para 1 minuto:

	topic = Azure::ServiceBus::Topic.new("test-topic")
    topic.max_size_in_megabytes = 5120
    topic.default_message_time_to_live = "PT1M"

    topic = azure_service_bus_service.create_topic(topic)

## Como criar assinaturas

As assinaturas do tópico também são criadas com o objeto **Azure::ServiceBusService**. As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens entregues à fila virtual da assinatura.

As assinaturas são persistentes e continuarão existindo até que elas ou o tópico ao qual estão associadas sejam excluídos. Se seu aplicativo contiver a lógica para criar uma assinatura, ele deve primeiro verificar se a assinatura já existe usando o método getSubscription.

### Criar uma assinatura com o filtro padrão (MatchAll)

**MatchAll** será o filtro padrão usado se nenhum filtro for especificado quando uma nova assinatura for criada. Quando o filtro **MatchAll** é usado, todas as mensagens publicadas no tópico são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma assinatura denominada "all-messages" e usa o filtro padrão **MatchAll**.

	subscription = azure_service_bus_service.create_subscription("test-topic",
	  "all-messages")

### <a id="how-to-create-subscriptions"></a>Criar Assinaturas com os Filtros

Você também pode configurar filtros que permitem atribuir um escopo a quais mensagens enviadas a um tópico devem aparecer dentro de uma assinatura específica do tópico.

O tipo o mais flexível de filtro compatível com assinaturas é o **Azure::ServiceBus::SqlFilter**, que implementa um subconjunto de SQL92. Os filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para obter mais detalhes sobre as expressões que podem ser usadas com um filtro SQL, examine a sintaxe [SqlFilter.SqlExpression](http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx).

Os filtros podem ser adicionados a uma assinatura usando o método **create\_rule ()** do objeto **Azure::ServiceBusService**. Este método permite que você adicione novos filtros a uma assinatura existente.

Como o filtro padrão é aplicado automaticamente em todas as assinaturas novas, você deve primeiro remover o filtro padrão, ou o filtro **MatchAll** substituirá todos os outros filtros que você possa especificar. Você pode remover a regra padrão usando o método **delete\_rule ()** do objeto **Azure::ServiceBusService**.

Os exemplos a seguir criam uma assinatura chamada "high-messages" com um **Azure::ServiceBus::SqlFilter** que seleciona apenas mensagens que têm uma propriedade **message\_number** personalizada maior que 3:

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

Da mesma forma, o seguinte exemplo cria uma assinatura chamada "low-messages" com um **Azure::ServiceBus::SqlFilter** que seleciona apenas mensagens que têm uma propriedade **message\_number** menor que ou igual a 3:

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

Quando uma mensagem é enviada para "test-topic", ela sempre será fornecida aos destinatários inscritos na assinatura do tópico "all-messages" e será fornecida de forma seletiva para os destinatários inscritos nas assinaturas dos tópicos "high-messages" e "low-messages" (dependendo do conteúdo de mensagem).

## Como enviar mensagens a um tópico

Para enviar uma mensagem a um tópico do Service Bus, o aplicativo deve usar o método **send\_topic\_message()** do objeto **Azure::ServiceBusService**. As mensagens enviadas aos tópicos do Service Bus são objetos **Azure::ServiceBus::BrokeredMessage**. Os objetos **Azure::ServiceBus::BrokeredMessage** têm um conjunto de propriedades padrão (como **rótulo** e **time\_to\_live**), um dicionário que é usado para manter propriedades personalizadas específicas ao aplicativo e um corpo de dados da cadeia. Um aplicativo pode definir o corpo da mensagem transmitindo um valor da cadeia ao método **send\_topic\_message()** e todas as propriedades padrão exigidas serão preenchidas por valores padrão.

O exemplo a seguir demonstra como enviar cinco mensagens de teste para "test-topic". Observe que o valor de propriedade personalizado de **message\_number** de cada mensagem varia de acordo com a iteração do loop (isso determinará qual assinatura recebe isso):

	5.times do |i|
	  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
	    { :message_number => i })
	  azure_service_bus_service.send_topic_message("test-topic", message)
	end

Os tópicos de Service Bus oferecem suporte a um tamanho máximo de mensagem de 256 MB (o cabeçalho, que inclui as propriedades do aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 MB). Não há nenhum limite no número de mensagens mantidas em um tópico mas há uma capacidade do tamanho total das mensagens mantidas por um tópico. O tamanho do tópico é definido no momento da criação, com um limite máximo de 5 GB.

## Como receber mensagens de uma assinatura

As mensagens são recebidas de uma assinatura com o método de **receive\_subscription\_message()** no objeto **Azure::ServiceBusService**. Por padrão, as mensagens são lidas (pico) e bloqueadas sem excluí-las da assinatura. Você pode ler e excluir a mensagem da assinatura definindo a opção **peek\_lock** para **false**.

O comportamento padrão faz com que a leitura e a exclusão se dividam em uma operação de dois estágios, o que torna possível o suporte a aplicativos que não podem tolerar mensagens ausentes. Quando o Service Bus recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, ela a bloqueia para evitar que outros consumidores a recebam e a retorna para o aplicativo. Depois que o aplicativo termina de processar a mensagem (ou a armazena de forma segura para um processamento futuro), ele conclui o segundo estágio processo de recebimento chamando o método **delete\_subscription\_message()** e fornecendo a mensagem a ser excluída como um parâmetro. O método **delete\_subscription\_message()** marcará a mensagem como consumida e a removerá da assinatura.

Se o parâmetro **:peek\_lock** estiver definido como **false**, a leitura e a exclusão da mensagem se tornam o modelo mais simples e funcionam melhor em cenários nos quais o aplicativo pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Barramento de Serviço terá marcado a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e processadas usando **receive\_subscription\_message ()**. O exemplo primeiro recebe e exclui uma mensagem da assinatura "low-messages" usando **: peek\_lock** definido para **false**, em seguida, ele recebe outra mensagem de "high-messages" e exclui a mensagem usando **delete\_queue\_message()**:

    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "low-messages", { :peek_lock => false })
    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "high-messages")
    azure_service_bus_service.delete_subscription_message(message)

## Como tratar falhas do aplicativo e mensagens ilegíveis

O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele chamará o método **unlock\_subscription\_message()** no objeto **Azure::ServiceBusService**. Isso fará com que o Service Bus desbloqueie a mensagem na assinatura e disponibilize-a para ser recebida novamente, pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na assinatura, e se o aplicativo falhar em processar a mensagem antes da expiração do tempo limite do bloqueio (por exemplo, se o aplicativo falhar), o service bus desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que o método **delete\_subscription\_message()** seja chamado, a mensagem será fornecida novamente ao aplicativo quando reiniciar. Isso é frequentemente chamado de **Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez mas, em algumas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega de mensagem duplicada. Isso geralmente é obtido com a propriedade **message\_id** da mensagem, que permanecerá constante nas tentativas da entrega.

## Como excluir tópicos e assinaturas

Os tópicos e as assinaturas são persistentes e devem ser explicitamente excluídos por meio do [Portal do Azure](https://manage.windowsazure.com) ou de forma programática. O exemplo a seguir demonstra como excluir o tópico denominado "test-topic".

	azure_service_bus_service.delete_topic("test-topic")

A exclusão de um tópico também excluirá todas as assinaturas registradas com o tópico. As assinaturas também podem ser excluídas de forma independente. O código a seguir demonstra como excluir a assinatura denominada "high-messages" do tópico "test-topic":

	azure_service_bus_service.delete_subscription("test-topic", "high-messages")

## Próximas etapas

Agora que você já sabe os princípios dos tópicos do Barramento de Serviço, acesse estes links para saber mais.

-   Confira [Filas, tópicos e assinaturas](service-bus-queues-topics-subscriptions.md).
-   Referência da API para [SqlFilter](http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx).
-	Visite o repositório do [SDK do Azure para o Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) no GitHub.
 

<!---HONumber=Oct15_HO3-->