<properties linkid="develop-python-service-bus-topics" urlDisplayName="Tópicos do Barramento de Serviço" pageTitle="Como usar tópicos do Barramento de Serviço (Python) - Azure" metaKeywords="Introdução tópicos do Barramento de Serviço do Azure publicação assinatura mensagens Python" description="Saiba como usar os tópicos e as assinaturas do Barramento de Serviço no Azure. Os exemplos de código são escritos para aplicativos Python." metaCanonical="" services="service-bus" documentationCenter="Python" title="Como usar tópicos/assinaturas do Barramento de Serviço" authors="" solutions="" manager="" editor="" />





# Como usar os tópicos/assinaturas do Barramento de Serviço
Este guia mostra como usar os tópicos e assinaturas do Barramento de Serviço
em aplicativos Python. Os cenários abrangidos incluem **criar tópicos e assinaturas, criar filtros de assinatura, enviar mensagens** a um tópico, **receber mensagens de uma assinatura** e
**excluir tópicos e assinaturas**. Para obter mais informações sobre tópicos e assinaturas, consulte a seção [Próximas etapas](#Next_Steps).

## Sumário

-   [O que são os tópicos e as assinaturas do Service Bus?](#what-are-service-bus-topics)
-   [Criar um namespace de serviço](#create-a-service-namespace)
-   [Obter as credenciais de gerenciamento padrão do namespace](#obtain-default-credentials)
-   [Como criar um tópico](#How_to_Create_a_Topic)
-   [Como criar assinaturas](#How_to_Create_Subscriptions)
-   [Como enviar mensagens a um tópico](#How_to_Send_Messages_to_a_Topic)
-   [Como receber mensagens de uma assinatura](#How_to_Receive_Messages_from_a_Subscription)
-   [Como tratar falhas do aplicativo e mensagens ilegíveis](#How_to_Handle_Application_Crashes_and_Unreadable_Messages)
-   [Como excluir tópicos e assinaturas](#How_to_Delete_Topics_and_Subscriptions)
-   [Próximas etapas](#Next_Steps)


[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

**Observação:** se você precisar instalar o Python ou as bibliotecas de cliente, consulte o [Guia de instalação do Python](../python-how-to-install/)


##<a name="How_to_Create_a_Topic"></a>Como criar um tópico

O objeto **ServiceBusService** permite que você trabalhe com tópicos. Adicione o seguinte próximo à parte superior de qualquer arquivo Python no qual você deseja acessar o Barramento de Serviço do Azure programaticamente:

	from azure.servicebus import *

O código a seguir cria um objeto **ServiceBusService**. Substitua 'mynamespace', 'mykey' e 'myissuer' pelo namespace, chave e emissor reais.

	bus_service = ServiceBusService(service_namespace='mynamespace', account_key='mykey', issuer='myissuer')

	bus_service.create_topic('mytopic')

**create\_topic** também oferece suporte a opções adicionais, que permitem substituir configurações padrão de tópicos, como a vida útil da mensagem ou o tamanho máximo do tópico. O exemplo a seguir mostra a definição do tamanho máximo do tópico para 5 GB e da vida útil para 1 minuto:

	topic_options = Topic()
	topic_options.max_size_in_megabytes = '5120'
	topic_options.default_message_time_to_live = 'PT1M'

	bus_service.create_topic('mytopic', topic_options)

##<a name="How_to_Create_Subscriptions"></a>Como criar assinaturas

As assinaturas do tópico também são criadas com o objeto **ServiceBusService**. As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens entregues à fila virtual da assinatura.

**Observação**: as assinaturas são persistentes e continuarão existindo até que elas ou o tópico ao qual estão associadas sejam excluídos.

### Criar uma assinatura com o filtro padrão (MatchAll)

O filtro **MatchAll** é o filtro padrão usado se nenhum filtro for especificado quando uma nova assinatura for criada. Quando o filtro **MatchAll** é usado, todas as mensagens publicadas no tópico são colocadas na fila virtual da assinatura. O seguinte exemplo cria uma assinatura denominada 'AllMessages' e usa o filtro padrão **MatchAll**.

	bus_service.create_subscription('mytopic', 'AllMessages')

### Criar assinaturas com filtros

Também é possível configurar filtros que permitam atribuir um escopo a quais mensagens enviadas a um tópico devem ser exibidas dentro de uma assinatura específica do tópico.

O tipo mais flexível de filtro suportado por assinaturas é o
**SqlFilter** que implementa um subconjunto do SQL92. Os filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para obter mais detalhes sobre as expressões que podem ser usadas com um filtro SQL, examine a sintaxe [SqlFilter.SqlExpression][].

Os filtros podem ser adicionados a uma assinatura usando o método **create\_rule** do objeto **ServiceBusService**. Este método permite que você adicione novos filtros a uma assinatura existente.

**Observação**: como o filtro padrão é aplicado automaticamente a todas as novas assinaturas, você deve primeiro remover o filtro padrão, ou o
**MatchAll** substituirá todos os outros filtros que você possa especificar. Você pode remover a regra padrão usando o método **delete\_rule** do objeto Azure::ServiceBusService
objeto **ServiceBusService**.

O exemplo a seguir cria uma assinatura chamada 'HighMessages' com um
**SqlFilter** que seleciona somente mensagens com uma propriedade
**messagenumber** personalizada maior que três:

	bus_service.create_subscription('mytopic', 'HighMessages')

	rule = Rule()
	rule.filter_type = 'SqlFilter'
	rule.filter_expression = 'messagenumber > 3'

	bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
	bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)

Da mesma forma, o seguinte exemplo cria uma assinatura denominada 'LowMessages' com um **SqlFilter** que seleciona apenas as mensagens que tenham uma propriedade **messagenumber** menor ou igual a 3:

	bus_service.create_subscription('mytopic', 'LowMessages')

	rule = Rule()
	rule.filter_type = 'SqlFilter'
	rule.filter_expression = 'messagenumber <= 3'

	bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
	bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)

Agora, quando uma mensagem é enviada para 'mytopic', ela sempre será entregue aos destinatários inscritos na assinatura do tópico 'AllMessages' e será entregue de forma seletiva para os destinatários inscritos nas assinaturas dos tópicos 'HighMessages' e 'LowMessages' (dependendo do conteúdo de mensagem).

##<a name="How_to_Send_Messages_to_a_Topic"></a>Como enviar mensagens a um tópico

Para enviar uma mensagem a um tópico do Barramento de Serviço, você deve usar o
método **send\_topic\_message** do objeto **ServiceBusService**.

O exemplo a seguir demonstra como enviar cinco mensagens de teste para 'mytopic'. Observe que o valor da propriedade **messagenumber** de cada mensagem varia de acordo com a iteração do loop (isso determinará quais assinaturas irão recebê-la):

	for i in range(5):
		msg = Message('Msg ' + str(i), custom_properties={'messagenumber':i})
		bus_service.send_topic_message('mytopic', msg)

Os tópicos do Service Bus suportam um tamanho máximo de 256 MB de mensagens (o cabeçalho, que inclui as propriedades padrão e personalizadas do aplicativo, pode ter um tamanho máximo de 64 MB). Não há nenhum limite sobre o número de mensagens mantidas em um tópico, mas há um limite de utilização para o tamanho total das mensagens mantidas por um tópico. O tamanho do tópico é definido no momento da criação, com um limite máximo de 5 GB.

##<a name="How_to_Receive_Messages_from_a_Subscription"></a>Como receber mensagens de uma assinatura

As mensagens são recebidas de uma assinatura, usando o
método **receive\_subscription\_message** do objeto **ServiceBusService**.

	msg = bus_service.receive_subscription_message('mytopic', 'LowMessages')
	print(msg.body)

As mensagens são excluídas da assinatura conforme são lidas. No entanto, você pode ler (espiar) e bloquear a mensagem sem excluí-la da assinatura definindo o parâmetro opcional **peek\_lock** como **true**.

O comportamento padrão da leitura e da exclusão da mensagem como parte da operação de recebimento é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo possa tolerar o não processamento de uma mensagem em caso de falha. Para entender como isso funciona, considere um cenário no qual o consumidor emite a solicitação de recebimento e ocorre uma falha antes de processá-la. Como o Service Bus terá marcado a mensagem
como consumida, quando o aplicativo for reiniciado e começar a consumir
as mensagens novamente, ele terá perdido a mensagem que
foi consumida antes da falha.

	
Se o parâmetro **peek\_lock** for definido como **True**, o processo de recebimento se tornará uma operação de duas etapas, o que torna possível o suporte a aplicativos que não podem tolerar mensagens ausentes. Quando o Service Bus recebe uma solicitação, ele localiza a próxima mensagem a ser consumida, ele a bloqueia para evitar que outros consumidores a recebam e depois a retorna para o aplicativo.
Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento chamando o método **delete** no objeto **Message**. O método **delete** marcará a mensagem como sendo consumida e a removerá da assinatura.

	msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
	print(msg.body)
	
	msg.delete()
	

##<a name="How_to_Handle_Application_Crashes_and_Unreadable_Messages"></a>Como tratar falhas do aplicativo e mensagens ilegíveis

O Service Bus proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros em seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele chamará o método **unlock** no
objeto **Message**. Isso fará com que o Service Bus desbloqueie a mensagem na assinatura e disponibilize-a para ser recebida novamente, pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na assinatura, e se o aplicativo falhar em processar a mensagem antes da expiração do tempo limite do bloqueio (por exemplo, se o aplicativo falhar), o Service Bus desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Se houver uma falha no aplicativo depois do processamento da mensagem, mas antes do método **delete** ser chamado, a mensagem será fornecida ao aplicativo novamente quando ele for reiniciado. Isso normalmente é chamado de
**Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez, mas, em determinadas situações, a mesma mensagem pode ser fornecida novamente. Se o cenário não tolerar processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega da mensagem duplicada. Isso geralmente é feito usando a
propriedade **MessageId** da mensagem, que permanecerá constante entre as tentativas de entrega.

##<a name="How_to_Delete_Topics_and_Subscriptions"></a>Como excluir tópicos e assinaturas

Os tópicos e as assinaturas são persistentes e devem ser explicitamente excluídos por meio do Portal de Gerenciamento do Azure ou de forma programática.
O exemplo a seguir demonstra como excluir o tópico chamado 'mytopic':

	bus_service.delete_topic('mytopic')

Excluir um tópico também excluirá todas as assinaturas que estiverem registradas com o tópico. As assinaturas também podem ser excluídas de forma independente. O código a seguir demonstra como excluir uma assinatura chamada 'HighMessages' do tópico 'mytopic':

	bus_service.delete_subscription('mytopic', 'HighMessages')

##<a name="Next_Steps"></a>Próximas etapas

Agora que você já sabe os princípios dos tópicos do Service Bus, acesse estes links para saber mais.

-   Consulte a Referência do MSDN: [Filas, tópicos e assinaturas][].
-   Referência de API para [SqlFilter][].

  [Próximas etapas]: #nextsteps
  [O que são os tópicos e as assinaturas do Service Bus?]: #what-are-service-bus-topics
  [Criar um namespace de serviço]: #create-a-service-namespace
  [Obter as credenciais de gerenciamento padrão do namespace]: #obtain-default-credentials
  [Como criar um tópico]: #How_to_Create_a_Topic
  [Como criar assinaturas]: #How_to_Create_Subscriptions
  [Como enviar mensagens a um tópico]: #How_to_Send_Messages_to_a_Topic
  [Como receber mensagens de uma assinatura]: #How_to_Receive_Messages_from_a_Subscription
  [Como tratar falhas do aplicativo e mensagens ilegíveis]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [Como excluir tópicos e assinaturas]: #How_to_Delete_Topics_and_Subscriptions
  
  [Conceitos de tópico:]: ../../../DevCenter/dotNet/Media/sb-topics-01.png
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  
  
  
  
  
  
  [Filas, tópicos e assinaturas]: http://msdn.microsoft.com/pt-br/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/pt-br/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx

