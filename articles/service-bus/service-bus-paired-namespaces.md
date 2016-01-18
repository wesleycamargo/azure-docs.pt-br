<properties 
   pageTitle="Namespaces emparelhados do Barramento de Serviço| Microsoft Azure"
   description="Detalhes e custo de implementação do namespace emparelhado"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" /> 
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/28/2015"
   ms.author="sethm" />

# Detalhes e implicações de custo da implementação do namespace emparelhado

O método [PairNamespaceAsync][], que usa uma instância de [SendAvailabilityPairedNamespaceOptions][], realiza tarefas visíveis em seu nome. Como o uso do recurso envolve considerações sobre o custo, convém entender essas tarefas para que quando isso ocorrer você já saiba do que se trata. A API realiza o seguinte comportamento automático em seu nome:

-   Criação de filas de pendências.
-   Criação de um objeto [MessageSender][] que se comunica com filas ou tópicos.
-   Quando uma entidade de mensagem fica indisponível, ela envia mensagens de ping à entidade para tentar detectar quando a entidade ficará disponível novamente.
-   Opcionalmente, ela cria um conjunto de "propulsores de mensagens" que as movem das filas de pendências para as filas principais.
-   Coordena o fechamento/falha das instâncias primária e secundária de [MessagingFactory][].

Em alto nível, o recurso funciona da seguinte maneira: quando a entidade principal está íntegra, não ocorre qualquer alteração de comportamento. Após o término do [FailoverInterval][] sem que a entidade primária consiga envios bem-sucedidos após um [MessagingException][] ou [TimeoutException][] não transitório, o seguinte comportamento ocorrerá:

1.  As operações de envio para a entidade primária são desabilitadas e o sistema efetua pings na entidade primária até que os pings possam ser entregues com sucesso.

2.  Uma fila de pendências aleatória é selecionada.

3.  Os objetos [BrokeredMessage][] são roteados para a fila de pendências escolhida.

1.  Se uma operação de envio para a fila de pendências escolhida falhar, essa fila será retirada da rotação e uma nova fila será selecionada. Todos os remetentes da instância [MessagingFactory][] tomam conhecimento da falha.

As figuras a seguir ilustram a sequência. Primeiro, o remetente envia mensagens.

![Namespaces emparelhados][0]

Após a falha de envio para a fila primária, o remetente começa a enviar mensagens para uma fila de pendências escolhida aleatoriamente. Simultaneamente, ele inicia uma tarefa de ping.

![Namespaces emparelhados][1]

Neste ponto, as mensagens ainda estão na fila secundária e não foram entregues à fila primária. Quando a fila primária fica íntegra novamente, pelo menos um processo deve estar em execução no sifão. O sifão entrega as mensagens de todas as diversas filas de pendências para as entidades de destino apropriadas (filas e tópicos).

![Namespaces emparelhados][2]

O restante deste tópico discute os detalhes específicos de funcionamento dessas partes.

## Criação de filas de pendências

O objeto [SendAvailabilityPairedNamespaceOptions][] passado ao método [PairNamespaceAsync][] indica o número de filas de pendência que você deseja usar. Em seguida, cada fila de pendências é criada com as seguintes propriedades definidas explicitamente (todos os outros valores são definidos com os valores padrão de [QueueDescription][]):

| Caminho | [namespace primário]/x-servicebus-transfer/[índice] no qual [índice] é um valor em [0, BacklogQueueCount) |
|----------------------------------------|------------------------------------------------------------------------------------------------------|
| MaxSizeInMegabytes | 5120 |
| MaxDeliveryCount | int.MaxValue |
| DefaultMessageTimeToLive | TimeSpan.MaxValue |
| AutoDeleteOnIdle | TimeSpan.MaxValue |
| LockDuration | 1 minuto |
| EnableDeadLetteringOnMessageExpiration | verdadeiro |
| EnableBatchedOperations | verdadeiro |

Por exemplo, a primeira fila de pendências criada para o namespace **contoso** é chamada de `contoso/x-servicebus-transfer/0`.

Durante a criação das filas, primeiro o código verifica se essa fila já existe. Se a fila não existir, ela será criada. O código não limpa as filas de pendência "extras". Especificamente, se o aplicativo com o namespace primário **contoso** solicitar cinco filas de pendência, mas já existir uma fila de pendências com o caminho `contoso/x-servicebus-transfer/7`, essa fila de pendências extra ainda estará presente, mas não será usada. O sistema permite explicitamente a existência de filas de pendência extras que não serão usadas. Como proprietário do namespace, você é responsável por limpar quaisquer filas de pendência não usadas/não desejadas. O motivo dessa decisão é que o Barramento de Serviço não pode saber quais são as finalidades de todas as filas no namespace. Além disso, se houver uma fila com o nome especificado, mas que NÃO atender à suposta [QueueDescription][], você terá seus próprios motivos para alterar o comportamento padrão. Não há garantias para as modificações feitas por seu código nas filas de pendência. Teste suas alterações com cuidado.

## MessageSender personalizado

Durante o envio, todas as mensagens passam por um objeto [MessageSender][] interno que se comporta normalmente quando tudo funciona, e redireciona para a lista de pendências quando as coisas "dão errado". Ao receber uma falha não transitória, um timer é iniciado. Após um período de [TimeSpan][] composto pelo valor da propriedade [FailoverInterval][], durante o qual nenhuma mensagem é enviada, o failover é acionado. Nesse ponto, acontece o seguinte para cada entidade:

- Uma tarefa de ping é executada a cada [PingPrimaryInterval][], a fim de verificar se a entidade está disponível. Após o êxito dessa tarefa, todo código do cliente que utiliza a entidade passará imediatamente a enviar novas mensagens ao namespace primário.

- As solicitações futuras enviadas à mesma entidade de qualquer outro remetente resultarão no envio de [BrokeredMessage][] à fila de pendências para sofrer alteração. A alteração remove algumas propriedades do objeto [BrokeredMessage][] e as armazena em outro lugar. As propriedades a seguir são limpas e adicionadas com um novo alias, permitindo que o Barramento de Serviço e o SDK processem as mensagens de maneira uniforme:

| Nome antigo da propriedade | Novo nome da propriedade |
|-------------------------|-------------------|
| SessionId | x-ms-sessionid |
| TimeToLive | x-ms-timetolive |
| ScheduledEnqueueTimeUtc | x-ms-path |

O caminho de destino original também é armazenado dentro da mensagem como uma propriedade chamada x-ms-path. Esse design permite a coexistência de mensagens para muitas entidades em uma fila de pendências única. As propriedades são convertidas novamente pelo sifão.

O objeto [MessageSender][] personalizado poderá enfrentar problemas quando as mensagens se aproximarem do limite de 256 KB e o failover será acionado. O objeto [MessageSender][] personalizado armazena as mensagens de todas as filas e tópicos nas filas de pendência. Esse objeto combina mensagens de muitos primários nas filas de pendência. Para lidar com o balanceamento de carga entre vários clientes que não se conhecem, o SDK escolhe aleatoriamente uma fila de pendências para cada [QueueClient][] ou [TopicClient][] criado no código.

## Pings

Uma mensagem de ping é uma [BrokeredMessage][] vazia com a propriedade [ContentType][] definida como application/vnd.ms-servicebus-ping e com um valor de [TimeToLive][] de um segundo. Esse ping tem uma característica especial no Barramento de Serviço: o servidor nunca entrega um ping quando o chamador solicita uma [BrokeredMessage][]. Assim, não há a necessidade de aprender como receber e ignorar essas mensagens. Cada entidade (fila ou tópico exclusivo) por instância [MessagingFactory][] por cliente receberá um ping quando for considerada indisponível. Por padrão, isso ocorre uma vez por minuto. Mensagens de ping são consideradas mensagens normais do Barramento de Serviço e podem resultar em encargos de largura de banda e de mensagens. Assim que os clientes detectam que o sistema está disponível, as mensagens são interrompidas.

## O sifão

Pelo menos um programa executável no aplicativo deve executar ativamente o sifão. O sifão realiza uma longa recepção de sondagem que dura 15 minutos. Quando todas as entidades estão disponíveis e você tem 10 filas de pendência, o aplicativo que hospeda o sifão chama a operação de recebimento 40 vezes por hora, 960 vezes por dia e 28.800 vezes em 30 dias. Quando o sifão estiver movendo ativamente as mensagens da lista de pendências para a fila primária, cada mensagem sofrerá os seguintes encargos (os encargos padrão para o tamanho da mensagem e a largura de banda são aplicados em todos os estágios):

1.  Envio à lista de pendências.

2.  Recebimento da lista de pendências.

3.  Envio ao primário.

4.  Recebimento do primário.

## Comportamento de fechamento/falha

Em um aplicativo que hospeda o sifão, quando a [MessagingFactory][] primária ou secundária falha ou fecha sem que seu parceiro também apresente falha ou seja fechado, e quando o sifão detecta esse estado, ele age. Se a outra [MessagingFactory][] não for fechada dentro de cinco segundos, o sifão falhará na [MessagingFactory][] ainda aberta.

## Próximas etapas

Consulte [Padrões de sistema de mensagens assíncronas e alta disponibilidade] para obter uma discussão detalhada da mensagem assíncrona do Barramento de Serviço.

  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [SendAvailabilityPairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [MessageSender]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [FailoverInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.failoverinterval.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [TimeSpan]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
  [PingPrimaryInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.pingprimaryinterval.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [ContentType]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx
  [TimeToLive]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
  [Padrões de sistema de mensagens assíncronas e alta disponibilidade]: service-bus-async-messaging.md
  [0]: ./media/service-bus-paired-namespaces/IC673405.png
  [1]: ./media/service-bus-paired-namespaces/IC673406.png
  [2]: ./media/service-bus-paired-namespaces/IC673407.png

<!---HONumber=AcomDC_0107_2016-->