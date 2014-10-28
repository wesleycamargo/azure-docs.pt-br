<properties linkid="develop-python-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (Python) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Python" description="Learn how to use Service Bus queues in Azure. Code samples written in Python." metaCanonical="" services="service-bus" documentationCenter="Python" title="How to Use Service Bus Queues" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"></tags>

# Como usar as filas do Service Bus

Este guia mostrará como usar as filas do Service Bus. Os exemplos são
escritos em Python e usam o módulo Python Azure. Os cenários
abrangidos incluem **criar filas, enviar e receber mensagens** e
**excluir filas**. Para obter mais informações sobre filas, consulte a seção [Próximas etapas][Próximas etapas].

## Sumário

-   [O que são as filas do Service Bus?][O que são as filas do Service Bus?]
-   [Criar um namespace de serviço][Criar um namespace de serviço]
-   [Obter as credenciais de gerenciamento padrão do namespace][Obter as credenciais de gerenciamento padrão do namespace]
-   [Como: Criar uma fila][Como: Criar uma fila]
-   [Como: Enviar mensagens para uma fila][Como: Enviar mensagens para uma fila]
-   [Como: Receber mensagens de uma fila][Como: Receber mensagens de uma fila]
-   [Como: Tratar falhas do aplicativo e mensagens ilegíveis][Como: Tratar falhas do aplicativo e mensagens ilegíveis]
-   [Próximas etapas][Próximas etapas]

[WACOM.INCLUDE [howto-service-bus-queues][howto-service-bus-queues]]

**Observação:** se você precisar instalar o Python ou as bibliotecas de cliente, consulte o [Guia de instalação do Python (a página pode estar em inglês)][Guia de instalação do Python (a página pode estar em inglês)].

## <a name="create-queue"> </a>Como criar uma fila

O objeto **ServiceBusService** permite que você trabalhe com filas. Adicione o seguinte próximo à parte superior de qualquer arquivo Python no qual você deseja acessar o Barramento de Serviço do Azure programaticamente:

    from azure.servicebus import *

O código a seguir cria um objeto **ServiceBusService**. Substitua 'mynamespace', 'sharedaccesskeyname' e 'sharedaccesskey' com o namespace real, nome chave e valor da assinatura de acesso compartilhada (SAS).

    bus_service = ServiceBusService(
        service_namespace='mynamespace',
        shared_access_key_name='sharedaccesskeyname',
        shared_access_key_value='sharedaccesskey')

Os valores para o nome chave e valor da SAS podem ser encontrados na informação de conexão do Portal do Azure, ou na janela das Propriedades do Visual Studio ao selecionar o namespace do service bus no Gerenciador de Serviços (conforme mostrado na seção anterior).

    bus_service.create_queue('taskqueue')

**create\_queue** também oferece suporte para opções adicionais, que
permitem a substituição de configurações padrão da fila, como a vida útil da mensagem
ou o tamanho máximo da fila. O seguinte exemplo mostra a definição do
tamanho máximo da fila para 5 GB e a vida útil para 1 minuto:

    queue_options = Queue()
    queue_options.max_size_in_megabytes = '5120'
    queue_options.default_message_time_to_live = 'PT1M'

    bus_service.create_queue('taskqueue', queue_options)

## <a name="send-messages"> </a>Como enviar mensagens para uma fila

Para enviar uma mensagem para uma fila de Service Bus, seu aplicativo chamará o método
**send\_queue\_message** no objeto **ServiceBusService**.

O exemplo a seguir demonstra como enviar uma mensagem de teste à
fila chamada *taskqueue usando* **send\_queue\_message**:

    msg = Message('Test Message')
    bus_service.send_queue_message('taskqueue', msg)

As filas de Service Bus oferecem suporte a um tamanho máximo de mensagem de 256 KB (o cabeçalho,
que inclui as propriedades do aplicativo padrão e personalizadas, pode ter
um tamanho máximo de 64 KB). Não há nenhum limite no número de mensagens
mantidas em uma fila, mas há uma capacidade do tamanho total das mensagens
mantidas por uma fila. O tamanho da fila é definido no momento da criação, com um
limite superior de 5 GB.

## <a name="receive-messages"> </a>Como receber mensagens de uma fila

As mensagens são recebidas de uma fila usando o método de **receive\_queue\_message**
 no objeto **ServiceBusService**.

    msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
    print(msg.body)

As mensagens são excluídas da fila conforme elas são lidas quando o parâmetro
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
processo de recebimento chamando o **Delete** no objeto da **mensagem**
. O método **delete** marcará a mensagem como sendo consumida
e a removerá da fila.

    msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
    print(msg.body)

    msg.delete()

## <a name="handle-crashes"> </a>Como tratar falhas do aplicativo e mensagens ilegíveis

O Service Bus proporciona funcionalidade para ajudá-lo a se recuperar normalmente
dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um
aplicativo receptor não puder processar a mensagem por algum motivo,
ele chamará o método **unlock** no objeto
**Mensagem**. Isso fará com que o Service Bus desbloqueie a
mensagem na fila e disponibilize-a para que ela possa ser recebida novamente
pelo mesmo aplicativo de consumo ou por
outro.

Também há um tempo limite associado a uma mensagem bloqueada na
fila e, se o aplicativo não conseguir processar a mensagem antes da
expiração do tempo limite do bloqueio (por exemplo, em caso de falha do aplicativo), o Service
Bus desbloqueará a mensagem automaticamente e a disponibilizará para ser
recebida novamente.

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

## <a name="next-steps"> </a>Próximas etapas

Agora que você já sabe as noções básicas das filas de Service Bus, siga estes
links para saber mais.

-   Consulte a referência de MSDN: [Filas, tópicos e assinaturas.][Filas, tópicos e assinaturas.]

  [Próximas etapas]: #next-steps
  [O que são as filas do Service Bus?]: #what-are-service-bus-queues
  [Criar um namespace de serviço]: #create-a-service-namespace
  [Obter as credenciais de gerenciamento padrão do namespace]: #obtain-default-credentials
  [Como: Criar uma fila]: #create-queue
  [Como: Enviar mensagens para uma fila]: #send-messages
  [Como: Receber mensagens de uma fila]: #receive-messages
  [Como: Tratar falhas do aplicativo e mensagens ilegíveis]: #handle-crashes
  [howto-service-bus-queues]: ../includes/howto-service-bus-queues.md
  [Guia de instalação do Python (a página pode estar em inglês)]: ../python-how-to-install/
  [Filas, tópicos e assinaturas.]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh367516.aspx
