<properties urlDisplayName="Service Bus Queues" pageTitle="Como usar filas do barramento de serviço (Python) - Azure" metaKeywords="filas do barramento de serviço do Azure, filas do Azure, sistema de mensagens do Azure, filas do Azure em Python" description="Aprenda a usar as filas do barramento de serviço no Azure. Exemplos de códigos escritos em Python." metaCanonical="" services="service-bus" documentationCenter="Python" title="How to Use Service Bus Queues" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/19/2014" ms.author="huvalo" />




# Como usar as filas do Service Bus
Este guia mostra como usar as filas do Barramento de Serviço. Os exemplos são
escritos em Python e usam o módulo Python do Azure. Os cenários
Os cenários abrangidos incluem **criar filas, enviar e receber mensagens** e
**excluir filas**. Para obter mais informações sobre filas, consulte a seção [Próximas etapas][] .

## Sumário

-   [O que são as filas do Service Bus?][]
-   [Criar um namespace de serviço][]
-   [Obter as credenciais de gerenciamento padrão do namespace][]
-   [Como: Criar uma fila][]
-   [Como: Enviar mensagens para uma fila][]
-   [Como: Receber mensagens de uma fila][]
-   [Como: Tratar falhas do aplicativo e mensagens ilegíveis][]
-   [Próximas etapas][]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

**Observação:** se você precisar instalar o Python ou as bibliotecas de cliente, consulte o [Guia de instalação do Python (a página pode estar em inglês)](../python-how-to-install/).


## <a name="create-queue"> </a>Como criar uma fila

O objeto **ServiceBusService** permite que você trabalhe com filas. Adicione o seguinte próximo à parte superior de qualquer arquivo Python no qual você deseja acessar o Barramento de Serviço do Azure programaticamente:

	from azure.servicebus import ServiceBusService, Message, Queue

O código a seguir cria um objeto **ServiceBusService**. Substitua 'mynamespace', 'sharedaccesskeyname' e 'sharedaccesskey' por o namespace real, nome chave e valor de assinatura de acesso compartilhado (SAS).

	bus_service = ServiceBusService(
		service_namespace='mynamespace',
		shared_access_key_name='sharedaccesskeyname',
		shared_access_key_value='sharedaccesskey')

Os valores para o nome chave e valor da SAS podem ser encontrados na informação de conexão do Portal do Azure, ou na janela das Propriedades do Visual Studio ao selecionar o namespace do service bus no Gerenciador de Serviços (conforme mostrado na seção anterior).

	bus_service.create_queue('taskqueue')

**create_queue** também dá suporte a opções adicionais, que
permitem substituir as configurações de fila padrão, como tempo de vida da mensagem
ou tamanho máximo da fila. O exemplo a seguir mostra a configuração do
tamanho máximo da fila para 5 GB e uma vida útil de um minuto:

	queue_options = Queue()
	queue_options.max_size_in_megabytes = '5120'
	queue_options.default_message_time_to_live = 'PT1M'

	bus_service.create_queue('taskqueue', queue_options)

## <a name="send-messages"> </a>Como enviar mensagens para uma fila

Para enviar uma mensagem a uma fila do Service Bus, seu aplicativo chamará o
**O método send\_queue\_message** no objeto **ServiceBusService**.

O exemplo a seguir demonstra como enviar uma mensagem de teste para
a fila chamada *taskqueue using* **send\_queue\_message**:

	msg = Message(b'Test Message')
	bus_service.send_queue_message('taskqueue', msg)

Filas de barramento de serviço têm suporte para um tamanho máximo da mensagem de 256 KB (o cabeçalho,
que inclui as propriedades padrão e personalizadas do aplicativo, pode ter
um tamanho máximo de 64 KB). Não há nenhum limite ao número de mensagens
mantidas em uma fila, mas há um limite ao tamanho total das mensagens
mantidas por uma fila. O tamanho da fila é definido no momento da criação, com um
limite superior de 5 GB.

## <a name="receive-messages"> </a>Como receber mensagens de uma fila

As mensagens recebidas de uma fila usando o método **receive\_queue\_message**
no objeto **ServiceBusService**:

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
	print(msg.body)

As mensagens são excluídas da fila conforme elas são lidas quando o parâmetro
**peek\_lock** é definido para **False**. Você pode ler (espiar) e bloquear a
mensagem sem excluí-la da fila definindo o parâmetro
**peek\_lock** para **True**.

O comportamento de leitura e exclusão da mensagem como parte da
operação de receber é o modelo mais simples e funciona melhor em cenários em
que o aplicativo pode tolerar o não processamento de uma mensagem no caso
de uma falha. Para entender isso, considere um cenário no qual o
consumidor emite a solicitação de recebimento e então falha antes de processá-la
. Uma vez que o barramento de serviço terá marcado a mensagem como tendo sido consumida,
então, quando o aplicativo reiniciar e começar a consumir mensagens novamente,
ele terá perdido a mensagem que foi consumida antes da falha.


Se o parâmetro **peek\_lock** for definido para **True**, o recebimento se torna
uma operação de dois estágios, que possibilita o suporte a aplicativos
que não podem tolerar mensagens ausentes. Quando o barramento de serviço recebe uma
solicitação, ele encontra a próxima mensagem a ser consumida, bloqueia-a para evitar
que outros consumidores a recebam e retorna para o aplicativo.
Depois que o aplicativo termina de processar a mensagem (ou a armazena
de modo confiável para processamento futuro), ele conclui a segunda etapa do
processo de recebimento chamando o método **excluir** no objeto **Mensagem**
. O método **excluir** marcará a mensagem como tendo sido consumida
e remova-a da fila.

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
	print(msg.body)

	msg.delete()

## <a name="handle-crashes"> </a>Como tratar falhas do aplicativo e mensagens ilegíveis

O barramento de serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos
erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um
aplicativo de receptor não puder processar a mensagem por algum motivo,
ele pode chamar o método **unlock** no
objeto **Message**. Isso fará com que o barramento de serviço desbloqueie a
mensagem na fila e disponibilize-a para ser recebida novamente,
seja pelo mesmo aplicativo de consumo ou por outro aplicativo de consumo
aplicativo.

Também há um tempo limite associado a uma mensagem bloqueada na
fila e, se o aplicativo falhar em processar a mensagem antes de
o tempo limite de bloqueio expirar (por exemplo, se o aplicativo falhar), o barramento de serviço
desbloqueará a mensagem automaticamente e a disponibilizará para ser
recebida novamente.

No caso de o aplicativo falhar após processar a mensagem
mas antes de o método **excluir** ser chamado, a mensagem será
entregue novamente ao aplicativo quando ele for reiniciado. Isso normalmente é chamado de
**Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada
pelo menos uma vez, mas, em determinadas situações, a mesma mensagem pode ser
entregue novamente. Se o cenário não tolerar processamento duplicado,
os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo
para tratar a entrega de mensagens duplicadas. Isso geralmente é feito usando a propriedade
**MessageId** da mensagem, que permanecerá constante em todas as
tentativas de entrega.

## <a name="next-steps"> </a>Próximas etapas

Agora que você aprendeu os conceitos básicos das filas do barramento de serviço, siga estes
links para saber mais.

-   Consultar a referência de MSDN: [Filas, tópicos e assinaturas.][]

  [Próximas etapas]: #next-steps
  [O que são as filas do Service Bus?]: #what-are-service-bus-queues
  [Criar um namespace de serviço]: #create-a-service-namespace
  [Obter as credenciais de gerenciamento padrão do namespace]: #obtain-default-credentials
  [Como: Criar uma fila]: #create-queue
  [Como: Enviar mensagens para uma fila]: #send-messages
  [Como: Receber mensagens de uma fila]: #receive-messages
  [Como: Tratar falhas do aplicativo e mensagens ilegíveis]: #handle-crashes
  [Conceitos de fila]: ../../../DevCenter/dotNet/Media/sb-queues-08.png
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  
  
  
  
  
  [Filas, tópicos e assinaturas.]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh367516.aspx
