<properties 
	pageTitle="Como usar os tópicos de Barramento de Serviço (Python) | Microsoft Azure" 
	description="Saiba como usar tópicos do barramento de serviço do Azure e assinaturas do Python." 
	services="service-bus" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="07/06/2015" 
	ms.author="huvalo"/>

# Como Usar tópicos do barramento de serviço e assinaturas

Este guia descreve como usar assinaturas e tópicos do barramento de serviço. Os exemplos são escritos em Python e usam o [pacote do Python Azure][]. Os cenários abordados incluem a **criação de tópicos e assinaturas, a criação de filtros de assinatura, o envio de mensagens** para um tópico, o **recebimento de mensagens de uma assinatura** e a **exclusão de tópicos e assinaturas**. Para saber mais sobre tópicos e assinaturas, confira a seção [Próximas etapas](#next-steps).

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

**Observação**: se você precisar instalar o Python ou o [pacote do Python Azure][], confira o [Guia de instalação do Python](../python-how-to-install.md).

## Como criar um tópico

O objeto **ServiceBusService** permite que você trabalhe com tópicos. Adicione o seguinte próximo à parte superior de qualquer arquivo Python no qual você deseja acessar o Barramento de Serviço do Azure programaticamente:

	from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME

O código a seguir cria um objeto **ServiceBusService**. Substitua `mynamespace`, `sharedaccesskeyname` e `sharedaccesskey` pelo namespace real, nome da chave e valor da SAS (Assinatura de Acesso Compartilhado).

	bus_service = ServiceBusService(
		service_namespace='mynamespace',
		shared_access_key_name='sharedaccesskeyname',
		shared_access_key_value='sharedaccesskey')

Você pode obter os valores para o nome da chave e valor de SAS nas informações de conexão do Portal de gerenciamento do Azure, ou na janela **Propriedades** do Visual Studio ao selecionar o namespace do Barramento de Serviço no Gerenciador de Serviços (conforme mostrado na seção anterior).

	bus_service.create_topic('mytopic')

**create\\\_topic** também oferece suporte para opções adicionais, que permitem a substituição de configurações padrão do tópico, como a vida útil da mensagem ou o tamanho máximo do tópico. O exemplo a seguir define o tamanho máximo do tópico como 5 GB, e a vida útil como um minuto:

	topic_options = Topic()
	topic_options.max_size_in_megabytes = '5120'
	topic_options.default_message_time_to_live = 'PT1M'

	bus_service.create_topic('mytopic', topic_options)

## Como criar assinaturas

As assinaturas do tópico também são criadas com o objeto **ServiceBusService**. As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens entregues à fila virtual da assinatura.

**Observação**: as assinaturas são persistentes e continuarão existindo até que elas ou o tópico ao qual estão associadas sejam excluídos.

### Criar uma assinatura com o filtro padrão (MatchAll)

**MatchAll** será o filtro padrão usado se nenhum filtro for especificado quando uma nova assinatura for criada. Quando o filtro **MatchAll** é usado, todas as mensagens publicadas no tópico são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma assinatura denominada 'AllMessages' e usa o filtro padrão **MatchAll**.

	bus_service.create_subscription('mytopic', 'AllMessages')

### Criar assinaturas com os filtros

Você também pode definir filtros que permitem especificar quais mensagens enviadas a um tópico devem aparecer dentro de uma assinatura específica do tópico.

O tipo de filtro mais flexível compatível com as assinaturas é o **SqlFilter**, que implementa um subconjunto do SQL92. Os filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para obter mais detalhes sobre as expressões que podem ser usadas com um filtro SQL, confira a sintaxe [SqlFilter.SqlExpression][].

Os filtros podem ser adicionados a uma assinatura usando o método **create\\\_rule** do objeto **ServiceBusService**. Este método permite que você adicione novos filtros a uma assinatura existente.

**Observação**: já que o filtro padrão é aplicado automaticamente em todas as assinaturas novas, você deve primeiro remover o filtro padrão, ou o filtro **MatchAll** substituirá todos os outros filtros que você possa especificar. Você pode remover a regra padrão usando o método **delete\\\_rule** do objeto **ServiceBusService**.

O exemplo abaixo cria uma assinatura denominada `HighMessages` com um **SqlFilter** que seleciona apenas as mensagens que tenham uma propriedade **messagenumber** personalizada maior do que 3:

	bus_service.create_subscription('mytopic', 'HighMessages')

	rule = Rule()
	rule.filter_type = 'SqlFilter'
	rule.filter_expression = 'messagenumber > 3'

	bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
	bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)

De maneira semelhante, o exemplo a seguir cria uma assinatura denominada `LowMessages` com um **SqlFilter** que seleciona apenas as mensagens que tenham uma propriedade **messagenumber** menor ou igual a 3:

	bus_service.create_subscription('mytopic', 'LowMessages')

	rule = Rule()
	rule.filter_type = 'SqlFilter'
	rule.filter_expression = 'messagenumber <= 3'

	bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
	bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)

Agora, quando uma mensagem for enviada para `mytopic`, ela sempre será fornecida aos destinatários inscritos na assinatura do tópico **AllMessages** e será fornecida de forma seletiva para os destinatários inscritos nas assinaturas dos tópicos **HighMessages** e **LowMessages** (dependendo do conteúdo da mensagem).

## Como enviar mensagens a um tópico

Para enviar uma mensagem a um tópico do Barramento de Serviço, o aplicativo deve usar o método **send\\\_topic\\\_message** do objeto **ServiceBusService**.

O exemplo a seguir demonstra como enviar cinco mensagens de teste para `mytopic`. Observe que o valor da propriedade **messagenumber** de cada mensagem varia de acordo com a iteração do loop (isso determina qual assinatura o receberá):

	for i in range(5):
		msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
		bus_service.send_topic_message('mytopic', msg)

Os tópicos de Service Bus oferecem suporte a um tamanho máximo de mensagem de 256 MB (o cabeçalho, que inclui as propriedades do aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 MB). Não há nenhum limite no número de mensagens mantidas em um tópico mas há uma capacidade do tamanho total das mensagens mantidas por um tópico. O tamanho do tópico é definido no momento da criação, com um limite máximo de 5 GB.

## Como receber mensagens de uma assinatura

As mensagens são recebidas de uma assinatura usando o método **receive\\\_subscription\\\_message** no objeto **ServiceBusService**:

	msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
	print(msg.body)

As mensagens são excluídas da assinatura conforme elas são lidas quando o parâmetro **peek\_lock** é definido como **False**. Você pode ler (peek) e bloquear a mensagem sem excluí-la da fila definindo o parâmetro **peek\\\_lock** como **True**.

O comportamento da leitura e da exclusão da mensagem como parte da operação de recebimento é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo possa tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Barramento de Serviço terá marcado a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.


Se o parâmetro **peek\_lock** estiver definido como **True**, o processo de recebimento se torna uma operação de duas etapas, o que torna possível o suporte a aplicativos que não toleram mensagens ausentes. Quando o Barramento de Serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, a bloqueia para evitar que outros clientes a recebam e a retorna para o aplicativo. Depois que o aplicativo conclui o processamento da mensagem (ou a armazenada de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento chamando o método **Delete** no objeto **Message**. O método **delete** marcará a mensagem como sendo consumida e a removerá da assinatura.

	msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
	print(msg.body)

	msg.delete()


## Como tratar falhas do aplicativo e mensagens ilegíveis

O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele chamará o método **unlock** no objeto **Message**. Isso fará com que o Barramento de Serviço desbloqueie a mensagem na assinatura e disponibilize-a para ser recebida novamente, pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na assinatura e, se houver falha no processamento da mensagem pelo aplicativo antes da expiração do tempo limite de bloqueio (por exemplo, se o aplicativo travar), o Barramento de Serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que o método **delete** seja chamado, a mensagem será fornecida novamente ao aplicativo quando ele reiniciar. Isso é frequentemente chamado de **Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez mas, em algumas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega de mensagem duplicada. Isso geralmente é obtido com a propriedade **Message\_id** da mensagem, que permanecerá constante nas tentativas da entrega.

## Como excluir tópicos e assinaturas

Os tópicos e as assinaturas são persistentes e devem ser explicitamente excluídos por meio do Portal de Gerenciamento do Azure ou de forma programática. O exemplo a seguir mostra como excluir o tópico chamado `mytopic`.

	bus_service.delete_topic('mytopic')

A exclusão de um tópico também exclui todas as assinaturas registradas com o tópico. As assinaturas também podem ser excluídas de forma independente. O código a seguir demonstra como excluir uma assinatura chamada `HighMessages` do tópico 'mytopic':

	bus_service.delete_subscription('mytopic', 'HighMessages')

## Próximas etapas

Agora que você já sabe os princípios dos tópicos do Barramento de Serviço, acesse estes links para saber mais.

-   Confira a Referência do MSDN: [Filas, tópicos e assinaturas][].
-   Referência para [Sqlfilter.Sqlexpression][].

[Azure Management Portal]: http://manage.windowsazure.com
[pacote do Python Azure]: https://pypi.python.org/pypi/azure
[Filas, tópicos e assinaturas]: http://msdn.microsoft.com/library/azure/hh367516.aspx
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
 

<!---HONumber=August15_HO7-->