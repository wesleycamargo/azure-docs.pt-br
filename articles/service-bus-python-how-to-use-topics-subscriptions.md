<properties linkid="develop-python-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (Python) - Azure" metaKeywords="Get started Azure Service Bus topics publising subscribe messaging Python" description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for Python applications." metaCanonical="" services="service-bus" documentationCenter="Python" title="How to Use Service Bus Topics/Subscriptions" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# Como usar os tópicos e as assinaturas do Service Bus

Este guia mostrará como usar os tópicos e as assinaturas do Service Bus
dos aplicativos do Python. Os cenários abordados incluem a **criação
de tópicos e assinaturas, a criação de filtros de assinatura, o envio
de mensagens** para um tópico, o **recebimento de mensagens de uma assinatura** e a
**exclusão de tópicos e assinaturas**. Para obter mais informações sobre tópicos
e assinaturas, consulte a seção [Próximas etapas][Próximas etapas].

## Sumário

-   [O que são os tópicos e as assinaturas do Service Bus?][O que são os tópicos e as assinaturas do Service Bus?]
-   [Criar um namespace de serviço][Criar um namespace de serviço]
-   [Obter as credenciais de gerenciamento padrão do namespace][Obter as credenciais de gerenciamento padrão do namespace]
-   [Como: Criar um tópico][Como: Criar um tópico]
-   [Como: criar assinaturas][Como: criar assinaturas]
-   [Como: enviar mensagens para um tópico][Como: enviar mensagens para um tópico]
-   [Como: Receber mensagens de uma assinatura][Como: Receber mensagens de uma assinatura]
-   [Como: Tratar falhas do aplicativo e mensagens ilegíveis][Como: Tratar falhas do aplicativo e mensagens ilegíveis]
-   [Como: excluir tópicos e assinaturas][Como: excluir tópicos e assinaturas]
-   [Próximas etapas][Próximas etapas]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

**Observação:** se você precisar instalar o Python ou as bibliotecas de cliente, consulte o [Guia de instalação do Python (a página pode estar em inglês)][Guia de instalação do Python (a página pode estar em inglês)].

## <a name="How_to_Create_a_Topic"></a>Como criar um tópico

O objeto **ServiceBusService** permite que você trabalhe com tópicos. Adicione o seguinte próximo à parte superior de qualquer arquivo Python no qual você deseja acessar o Barramento de Serviço do Azure programaticamente:

    from azure.servicebus import *

O código a seguir cria um objeto **ServiceBusService**. Substitua 'mynamespace', 'sharedaccesskeyname' e 'sharedaccesskey' com o namespace real, nome chave e valor da assinatura de acesso compartilhada (SAS).

    bus_service = ServiceBusService(
        service_namespace='mynamespace',
        shared_access_key_name='sharedaccesskeyname',
        shared_access_key_value='sharedaccesskey')

Os valores para o nome chave e valor da SAS podem ser encontrados na informação de conexão do Portal do Azure, ou na janela das Propriedades do Visual Studio ao selecionar o namespace do service bus no Gerenciador de Serviços (conforme mostrado na seção anterior).

    bus_service.create_topic('mytopic')

**create\_topic** também oferece suporte para opções adicionais, que
permitem a substituição de configurações padrão do tópico, como a vida útil da mensagem
ou o tamanho máximo do tópico. O exemplo a seguir mostra a definição
do tamanho máximo do tópico para 5 GB e da vida útil para 1 minuto:

    topic_options = Topic()
    topic_options.max_size_in_megabytes = '5120'
    topic_options.default_message_time_to_live = 'PT1M'

    bus_service.create_topic('mytopic', topic_options)

## <a name="How_to_Create_Subscriptions"></a>Como criar assinaturas

As assinaturas do tópico também são criadas com o objeto **ServiceBusService**
. As assinaturas são nomeadas e podem ter um filtro opcional que
restringe o conjunto de mensagens entregues à
fila virtual da assinatura.

**Observação**: As assinaturas são persistentes e continuarão existindo até
que elas ou o tópico ao qual estão associadas sejam excluídos.

### Criar uma assinatura com o filtro padrão (MatchAll)

**MatchAll** será o filtro padrão usado se nenhum filtro
for especificado quando uma nova assinatura for criada. Quando o filtro **MatchAll**
 é usado, todas as mensagens publicadas no tópico são colocadas na
fila virtual da assinatura. O seguinte exemplo cria uma
assinatura denominada 'AllMessages' e usa o filtro padrão **MatchAll**
.

    bus_service.create_subscription('mytopic', 'AllMessages')

### Criar assinaturas com os filtros

Você também pode configurar filtros que permitem atribuir um escopo a quais mensagens enviadas
a um tópico devem aparecer dentro de uma assinatura específica do tópico.

O tipo de filtro mais flexível compatível com as assinaturas é o
**SqlFilter**, que implementa um subconjunto do SQL92. Os filtros SQL operam
nas propriedades das mensagens que são publicadas no tópico. Para
obter mais detalhes sobre as expressões que podem ser usadas com um filtro SQL,
examine a sintaxe [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Os filtros podem ser adicionados a uma assinatura usando o método **create\_rule**
 do objeto **ServiceBusService**. Este método permite que você
adicione novos filtros a uma assinatura existente.

**Observação**: Como o filtro padrão é aplicado automaticamente em todas as
assinaturas novas, você deve primeiro remover o filtro padrão ou o filtro
**MatchAll** substituirá todos os outros filtros que você possa especificar. É possível
remover a regra padrão usando o método **delete\_Rule** do objeto
**ServiceBusService**.

O exemplo abaixo cria uma assinatura denominada 'HighMessages' com um
**SqlFilter** que seleciona apenas as mensagens que tenham uma propriedade
**messagenumber** personalizada maior que 3:

    bus_service.create_subscription('mytopic', 'HighMessages')

    rule = Rule()
    rule.filter_type = 'SqlFilter'
    rule.filter_expression = 'messagenumber > 3'

    bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
    bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)

Da mesma forma, o seguinte exemplo cria uma assinatura denominada
'LowMessages' com um **SqlFilter** que seleciona apenas as mensagens que tenham
uma propriedade **messagenumber** menor ou igual a 3:

    bus_service.create_subscription('mytopic', 'LowMessages')

    rule = Rule()
    rule.filter_type = 'SqlFilter'
    rule.filter_expression = 'messagenumber <= 3'

    bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
    bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)

Quando uma mensagem é agora enviada para 'MyTopic', ela sempre será fornecida aos
destinatários inscritos na assinatura do tópico 'AllMessages' e
será fornecida de forma seletiva para os destinatários inscritos nas assinaturas dos tópicos 'HighMessages' e
'LowMessages' (dependendo do conteúdo de mensagem).

## <a name="How_to_Send_Messages_to_a_Topic"></a>Como enviar mensagens a um tópico

Para enviar uma mensagem a um tópico do Service Bus, seu aplicativo deve usar o método
**send\_topic\_message** do objeto **ServiceBusService**.

O exemplo a seguir demonstra como enviar cinco mensagens de teste para
'MyTopic'. Observe que o valor da propriedade **messagenumber** de cada
mensagem varia de acordo com a iteração do loop (isso determinará qual
assinatura o receberá):

    for i in range(5):
        msg = Message('Msg ' + str(i), custom_properties={'messagenumber':i})
        bus_service.send_topic_message('mytopic', msg)

Os tópicos de Service Bus suportam um tamanho máximo de mensagem de 256 MB (o cabeçalho,
incluindo as propriedades padrão e personalizadas do aplicativo podem ter
um tamanho máximo de 64 MB). Não há nenhum limite no número de mensagens
mantidas em um tópico, mas há uma capacidade do tamanho total das mensagens
mantidas por um tópico. O tamanho do tópico é definido no momento da criação, com um
limite máximo de 5 GB.

## <a name="How_to_Receive_Messages_from_a_Subscription"></a>Como receber mensagens de uma assinatura

As mensagens são recebidas de uma assinatura usando o método de
**receive\_subscription\_message** no objeto **ServiceBusService**
.

    msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
    print(msg.body)

As mensagens são excluídas da assinatura conforme elas são lidas quando o parâmetro
**peek\_lock** é definido como **False**. Você pode ler (espiar) e bloquear a
mensagem sem excluí-la da fila definindo o parâmetro
**peek\_lock** para **True**.

O comportamento da leitura e da exclusão da mensagem como parte da
operação de recebimento é o modelo mais simples e funciona melhor em cenários nos
quais um aplicativo possa tolerar o não processamento de uma mensagem em caso
de falha. Para compreender isso, considere um cenário no qual o
consumidor emite a solicitação de recebimento e então falha antes de
processá-la. Como o Service Bus marcou a mensagem como consumida
e o aplicativo reiniciou e começou a consumir mensagens novamente,
ela perdeu a mensagem que foi consumida antes da falha.

Se o parâmetro **PeekLock** for definido como **true**, o processo de recebimento se torna
uma operação de duas etapas, o que torna possível o suporte a aplicativos
que não toleram mensagens ausentes. Quando o Service Bus recebe uma
solicitação, ele encontra a próxima mensagem a ser consumida, ela a bloqueia para evitar
que outros consumidores a recebam e a retorna para o aplicativo.
Depois que o aplicativo conclui o processamento da mensagem (ou a armazena
de forma segura para processamento futuro), ele conclui a segunda etapa do
processo de recebimento chamando o **Delete** no objeto da **mensagem**.
O método **delete** marcará a mensagem como sendo consumida e a removerá
da assinatura.

    msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
    print(msg.body)

    msg.delete()

\#\#<a name="How_to_Handle_Application_Crashes_and_Unreadable_Messages"></a>Como tratar falhas do aplicativo e mensagens ilegíveis

O Service Bus proporciona funcionalidade para ajudá-lo a se recuperar normalmente
dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um
aplicativo receptor não puder processar a mensagem por algum motivo,
ele chamará o método **unlock** no objeto
**Mensagem**. Isso fará com que o Service Bus desbloqueie a
mensagem na assinatura e disponibilize-a para que ela possa ser recebida novamente
pelo mesmo aplicativo de consumo ou por
outro.

Também há um tempo limite associado a uma mensagem bloqueada na
assinatura e, se o aplicativo não conseguir processar a mensagem antes da
expiração do tempo limite do bloqueio (por exemplo, em caso de falha do aplicativo), o
Service Bus desbloqueará a mensagem automaticamente e a disponibilizará para
ser recebida novamente.

Caso o aplicativo falhe após o processamento da mensagem,
mas antes que o método **delete** seja chamado, a mensagem
será fornecida novamente ao aplicativo quando reiniciar. Isso é frequentemente chamado de
**Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada
pelo menos uma vez mais, em algumas situações, a mesma mensagem poderá ser
entregue novamente. Se o cenário não tolerar o processamento duplicado, os
desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo
para tratar a entrega de mensagem duplicada. Isso geralmente é obtido com a propriedade
**Message\_id** da mensagem, que permanecerá constante nas
tentativas da entrega.

## <a name="How_to_Delete_Topics_and_Subscriptions"></a>Como excluir tópicos e assinaturas

Os tópicos e as assinaturas são persistentes e devem ser explicitamente excluídos
por meio do Portal de Gerenciamento do Azure ou de forma programática.
O exemplo a seguir demonstra como excluir o tópico chamado 'mytopic':

    bus_service.delete_topic('mytopic')

A exclusão de um tópico também excluirá todas as assinaturas registradas
com o tópico. As assinaturas também podem ser excluídas de forma independente. O
código a seguir demonstra como excluir uma assinatura denominada
'HighMessages' do tópico 'MyTopic':

    bus_service.delete_subscription('mytopic', 'HighMessages')

## <a name="Next_Steps"></a>Próximas etapas

Agora que você já sabe os princípios dos tópicos do Service Bus, acesse estes
links para saber mais.

-   Consulte a referência de MSDN: [Filas, tópicos e assinaturas][Filas, tópicos e assinaturas].
-   Referência da API para [SqlFilter][SqlFilter].

  [Próximas etapas]: #Next_Steps
  [Criar um namespace de serviço]: #create-a-service-namespace
  [Obter as credenciais de gerenciamento padrão do namespace]: #obtain-default-credentials
  [Como: Criar um tópico]: #How_to_Create_a_Topic
  [Como: criar assinaturas]: #How_to_Create_Subscriptions
  [Como: enviar mensagens para um tópico]: #How_to_Send_Messages_to_a_Topic
  [Como: Receber mensagens de uma assinatura]: #How_to_Receive_Messages_from_a_Subscription
  [Como: Tratar falhas do aplicativo e mensagens ilegíveis]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [Como: excluir tópicos e assinaturas]: #How_to_Delete_Topics_and_Subscriptions
  [howto-service-bus-topics]: ../includes/howto-service-bus-topics.md
  [Filas, tópicos e assinaturas]: http://msdn.microsoft.com/pt-br/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/pt-br/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
