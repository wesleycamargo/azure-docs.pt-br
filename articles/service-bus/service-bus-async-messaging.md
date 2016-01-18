<properties 
   pageTitle="Serviço de mensagens assíncrono do Barramento de Serviço | Microsoft Azure"
   description="Descrição do sistema de mensagens agenciadas assíncrono do Barramento de Serviço"
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
   ms.date="09/18/2015"
   ms.author="sethm" />

# Padrões de mensagens assíncronas e alta disponibilidade

O serviço de mensagens assíncrono pode ser implementado de diversas maneiras diferentes. Com filas, tópicos e assinaturas, coletivamente chamados de entidades de mensagens, o Barramento de Serviço do Azure dá suporte a assincronia por meio de um mecanismo de encaminhamento e repositório. Na operação normal (síncrona), você envia mensagens a filas e tópicos e recebe mensagens de filas e assinaturas. Os aplicativos escritos por você dependem de essas entidades estarem sempre disponíveis. Quando a integridade da entidade é alterada, devido a diversas circunstâncias, é necessário ter uma maneira de fornecer uma entidade de capacidade reduzida que possa atender à maioria das necessidades.

Os aplicativos normalmente usam padrões de mensagens assíncronas para habilitar diversos cenários de comunicação. Você pode criar aplicativos nos quais clientes podem enviar mensagens a serviços, mesmo quando o serviço não está em execução. Para os aplicativos que têm intermitência de comunicações, uma fila pode ajudar a normalizar a carga, fornecendo um lugar para um buffer de comunicações. Finalmente, você pode obter um balanceador de carga simples, mas eficaz, para distribuir mensagens em vários computadores.

Para manter a disponibilidade de qualquer uma dessas entidades, considere as várias formas diferentes pelas quais essas entidades podem parecer indisponíveis para um sistema de mensagens durável. Em termos gerais, vemos a entidade se tornar indisponível para os aplicativos que escrevemos das seguintes maneiras:

1.  Não é possível enviar mensagens.

2.  Não é possível receber mensagens.

3.  Não é possível administrar as entidades (criar, recuperar, atualizar ou excluir entidades).

4.  Não é possível entrar em contato com o serviço.

Para cada uma dessas falhas, existem modos de falhas diferentes que habilitam um aplicativo a continuar a executar o trabalho em algum nível de capacidade reduzida. Por exemplo, um sistema que pode enviar mensagens, mas não recebê-las, ainda pode receber pedidos de clientes, mas não pode processar esses pedidos. Este tópico discute problemas potenciais que podem ocorrer e como esses problemas são minimizados. O Barramento de Serviço introduziu diversas mitigações que você deve aceitar, e este tópico também descreve as regras que regem o uso dessas mitigações que exigem aceitação.

## Confiabilidade no Barramento de Serviço

Há várias maneiras de lidar com problemas de mensagens e entidades, e há diretrizes que regem o uso apropriado dessas mitigações. Para entender as diretrizes, primeiro você deve entender o que pode falhar no Barramento de Serviço. Devido ao design de sistemas do Azure, todos esses problemas tendem a ter vida curta. Em um alto nível, as diferentes causas de indisponibilidade ocorrem da seguinte maneira:

-   A limitação de um sistema externo do qual o Barramento de Serviço depende. A limitação ocorre devido a interações com os recursos de computação e armazenamento.

-   Problema para um sistema do qual o Barramento de Serviço depende. Por exemplo, determinado armazenamento pode encontrar problemas.

-   Falha do Barramento de Serviço em um único subsistema. Nessa situação, um nó de computação pode entrar em um estado inconsistente e deve se reiniciar sozinho, fazendo com que todas as entidades a que ele serve tenham a carga equilibrada para outros nós. Por sua vez, isso pode causar um curto período de processamento de mensagens lento.

-   Falha do Barramento de Serviço em um datacenter do Azure. Essa é a clássica "falha catastrófica" durante a qual o sistema não pode ser acessado por vários minutos ou algumas horas.

> [AZURE.NOTE]O termo **armazenamento** pode significar Armazenamento do Azure e SQL Azure.

O Barramento de Serviço contém várias mitigações para os problemas acima. As seções a seguir discutem cada problema e as respectivas mitigações.

### Limitação

Com o Barramento de Serviço, a limitação permite o gerenciamento cooperativo de taxa de mensagens. Cada nó individual do Barramento de Serviço hospeda várias entidades. Cada uma dessas entidades faz demandas ao sistema em termos de CPU, memória, armazenamento e outros aspectos. Quando qualquer um desses aspectos detecta uso que excede os limites definidos, o Barramento de Serviço pode negar determinada solicitação. O chamador recebe um [ServerBusyException][] e se recupera após 10 segundos.

Como uma mitigação, o código deve ler o erro e interromper repetições da mensagem por pelo menos 10 segundos. Como o erro pode ocorrer em partes do aplicativo cliente, espera-se que cada parte dele execute independentemente a lógica de repetição. O código pode reduzir a probabilidade de ser limitado habilitando o particionamento em uma fila ou um tópico.

### Problema para uma dependência do Azure

Ocasionalmente, outros componentes do Azure podem ter problemas de serviço. Por exemplo, quando um sistema que usa o Barramento de Serviço está sendo atualizado, esse sistema pode ter recursos reduzidos temporariamente. Para resolver esses tipos de problemas, o Barramento de Serviço investiga e implementa mitigações regularmente. Ocorrem efeitos colaterais dessas mitigações. Por exemplo, para lidar com problemas temporários com armazenamento, o Barramento de Serviço implementa um sistema que permite que operações de envio de mensagem funcionem constantemente. Devido à natureza da mitigação, uma mensagem enviada pode levar até 15 minutos para aparecer na fila ou assinatura afetada e estar pronta para uma operação de recebimento. Em termos gerais, a maioria das entidades não enfrentará esse problema. No entanto, devido ao número de entidades no Barramento de Serviço no Azure, essa mitigação às vezes é necessária para um pequeno subconjunto de clientes do Barramento de Serviço.

### Falha do Barramento de Serviço em um único subsistema

Com qualquer aplicativo, circunstâncias podem fazer com que um componente interno do Barramento de Serviço se torne inconsistente. Quando o Barramento de Serviço detecta isso, ele coleta dados do aplicativo para ajudar a diagnosticar o que aconteceu. Depois que os dados são coletados, o aplicativo é reiniciado em uma tentativa de retorná-lo a um estado consistente. Esse processo acontece rapidamente e resulta em uma entidade que parece estar disponível por alguns minutos, embora os tempos de inatividade típicos sejam mais curtos.

Nesses casos, o aplicativo cliente gera uma exceção do tipo [System. TimeoutException][] ou [MessagingException][]. O SDK do Barramento de Serviço do .NET contém uma mitigação para esse problema na forma de lógica de repetição de cliente automatizada. Depois que o período de repetição se esgota e a mensagem não é entregue, você pode explorar usando outros recursos, como namespaces emparelhados. Namespaces emparelhados têm outras restrições que são abordadas mais tarde neste documento.

### Falha do Barramento de Serviço em um datacenter do Azure

O motivo mais provável para uma falha em um datacenter do Azure é uma implantação de atualização com falha do Barramento de Serviço ou um sistema dependente. À medida que a plataforma amadureceu, a probabilidade desse tipo de falha foi reduzida. Uma falha do datacenter também pode acontecer por razões que incluem o seguinte:

-   Falta de energia (a fonte de energia e a geração de energia ficam indisponíveis).
-   Conectividade (interrupção da Internet entre o cliente e o Azure).

Em ambos os casos, um desastre natural ou humano causou o problema. Para solucionar esse problema e garantir que ainda possa enviar mensagens, você pode usar namespaces emparelhados para permitir que as mensagens sejam enviadas a um segundo local enquanto o local principal se torna íntegro novamente. Para obter mais informações, consulte [Práticas recomendadas para isolar aplicativos do Barramento de Serviço contra interrupções de serviço e desastres][].

## Namespaces emparelhados

O recurso de namespaces emparelhados dá suporte a cenários em que uma entidade do Barramento de Serviço ou uma implantação em um datacenter se torna indisponível. Embora esse evento ocorra raramente, sistemas distribuídos ainda devem estar preparados para lidar com os piores cenários. Normalmente, esse evento acontece porque algum elemento dos quais o Barramento de Serviço depende está enfrentando um problema de curta duração. Para manter a disponibilidade do aplicativo durante uma interrupção, os usuários do Barramento de Serviço podem usar dois namespaces separados, preferencialmente em datacenters separados, para hospedar suas entidades de mensagens. O restante desta seção usa a seguinte terminologia:

-   Namespace principal: o namespace com o qual seu aplicativo interage para operações de envio e recebimento.

-   Namespace secundário: o namespace que atua como um backup para o namespace principal. A lógica de aplicativo não interage com esse namespace.

-   Intervalo de failover: o tempo necessário para aceitar falhas normais antes que o aplicativo alterne do namespace principal para o namespace secundário.

Namespaces emparelhados dão suporte à disponibilidade de envio. A disponibilidade de envio concentra-se em preservar a capacidade de enviar mensagens. Para usar a disponibilidade de envio, seu aplicativo deve atender aos seguintes requisitos:

1.  As mensagens são recebidas apenas do namespace principal.

2.  As mensagens enviadas a determinada fila/tópico podem chegar fora de ordem.

3.  Se seu aplicativo usa sessões: as mensagens em uma sessão podem chegar fora de ordem. Essa é uma falha de funcionalidade normal das sessões. Isso significa que seu aplicativo usa sessões para mensagens agrupadas logicamente. O estado da sessão é mantido somente no namespace principal.

4.  As mensagens em uma sessão podem chegar fora de ordem. Essa é uma falha de funcionalidade normal das sessões. Isso significa que seu aplicativo usa sessões para mensagens agrupadas logicamente.

5.  O estado da sessão é mantido somente no namespace principal.

6.  A fila principal pode ficar online e começar a aceitar mensagens antes de a fila secundária entregar todas as mensagens à fila principal.

As seções a seguir discutem as APIs e como as APIs são implementadas e mostra um código de exemplo que usa o recurso. Observe que há implicações de cobranças associadas a esse recurso.

### A API MessagingFactory.PairNamespaceAsync

O recurso de namespaces emparelhados introduz o novo método a seguir na classe [Microsoft.ServiceBus.Messaging.MessagingFactory][]\:

```
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

Quando a tarefa é concluída, o emparelhamento do namespace também é concluído e está pronto para atuar em qualquer [MessageReceiver][], [QueueClient][], ou [TopicClient][] criado com [MessagingFactory][]. [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions][] é a classe base para os diferentes tipos de emparelhamento que estão disponíveis com um [MessagingFactory][]. Atualmente, a única classe derivada é uma que se chama [SendAvailabilityPairedNamespaceOptions][], que implementa os requisitos de disponibilidade de envio. [SendAvailabilityPairedNamespaceOptions][] tem um conjunto de construtores que se baseiam uns nos outros. Observando o construtor com a maioria dos parâmetros, você pode compreender o comportamento de outros construtores.

```
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

Esses parâmetros têm os seguintes significados:

-   *secondaryNamespaceManager*: uma instância inicializada de [NamespaceManager][] para o namespace secundário que o método [PairNamespaceAsync][] pode usar para configurar o namespace secundário. O gerenciador será usado para obter a lista de filas no namespace e garantir que as filas obrigatórias de lista de pendências existam. Se essas filas não existirem, elas serão criadas. [NamespaceManager][] requer a capacidade de criar um token com a declaração **Manage**.

-   *messagingFactory*: a instância de [MessagingFactory][] para o namespace secundário. O [MessagingFactory][] é usado para enviar e, se o [EnableSyphon][] estiver definida como **true**, receber mensagens de filas de lista de pendências.

-   *backlogQueueCount*: o número de filas de lista de pendências a serem criadas. Esse valor deve ser pelo menos 1. Ao serem enviadas mensagens à lista de pendências, umas dessas filas é escolhida aleatoriamente. Se você definir o valor como 1, somente uma fila poderá ser usada. Quando isso acontece e a fila de lista de pendências gera erros, o cliente não pode tentar uma fila de lista de pendências diferente e pode não enviar a mensagem. É recomendável definir esse valor como algum valor maior e usar como padrão o valor de 10. Você pode alterar isso para um valor maior ou menor, dependendo de quantos dados seu aplicativo envia por dia. Cada fila de lista de pendências pode manter até 5 GB de mensagens.

-   *failoverInterval*: o tempo pelo qual você aceitará falhas no namespace principal antes de alternar qualquer entidade para o namespace secundário. Os failovers ocorrem em entidades individuais. Entidades em um único namespace frequentemente estão em nós diferentes no Barramento de Serviço. Uma falha em uma entidade não implica falha em outra. Você pode definir esse valor como [System.TimeSpan.Zero][] para realizar o failover para o secundário imediatamente após a primeira falha não transitória. Falhas que disparam o temporizador de failover são qualquer [MessagingException][] em que a propriedade [IsTransient][] é falsa ou um [System.TimeoutException][]. Outras exceções, como [UnauthorizedAccessException][], não causam o failover, pois indicam que o cliente está configurado incorretamente. Um [ServerBusyException][] não causa failover porque o padrão correto é esperar 10 segundos e, em seguida, enviar a mensagem novamente.

-   *enableSyphon*: indica que esse emparelhamento específico também deve encaminhar mensagens do namespace secundário de volta ao namespace primário. Em geral, aplicativos que enviam mensagens devem definir esse valor como **false**; aplicativos que recebem mensagens devem definir esse valor como **true**. A razão disso é que, com frequência, há menos receptores de mensagens do que remetentes de mensagens. Dependendo do número de destinatários, você pode optar por ter uma única instância do aplicativo lidando com as tarefas de encaminhamento. Usar muitos receptores tem implicações de cobranças para cada fila de lista de pendências.

Para usar o código, crie uma instância principal de [MessagingFactory][], uma instância secundária de [MessagingFactory][], uma instância secundária de [NamespaceManager][] e uma instância de [SendAvailabilityPairedNamespaceOptions][]. A chamada pode ser tão simples quanto o seguinte exemplo:

```
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions =
    new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

Quando a tarefa retornada pelo método [PairNamespaceAsync][] for concluída, tudo está configurado e pronto para uso. Antes que a tarefa seja retornada, você pode não ter concluído todo o trabalho de segundo plano necessário para que o emparelhamento funcione corretamente. Como resultado, você não deve começar a enviar mensagens até que a tarefa seja retornada. Se alguma falha ocorrer, como credenciais inválidas ou falha ao criar filas de listas de pendências, essas exceções serão geradas quando a tarefa for concluída. Depois que a tarefa for retornada, verifique se as filas foram encontradas ou criadas examinando a propriedade [BacklogQueueCount][] em sua instância de [SendAvailabilityPairedNamespaceOptions][]. No código anterior, essa operação aparece da seguinte maneira:

```
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## Próximas etapas

Agora que você aprendeu os conceitos básicos de mensagens assíncronas no Barramento de Serviço, leia mais detalhes sobre [namespaces emparelhados e implicações de custo].

  [ServerBusyException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx
  [System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
  [System. TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [Práticas recomendadas para isolar aplicativos do Barramento de Serviço contra interrupções de serviço e desastres]: service-bus-outages-disasters.md
  [Microsoft.ServiceBus.Messaging.MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [MessageReceiver]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [SendAvailabilityPairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [EnableSyphon]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.enablesyphon.aspx
  [System.TimeSpan.Zero]: https://msdn.microsoft.com/library/azure/system.timespan.zero.aspx
  [IsTransient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.istransient.aspx
  [UnauthorizedAccessException]: https://msdn.microsoft.com/library/azure/system.unauthorizedaccessexception.aspx
  [BacklogQueueCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.backlogqueuecount.aspx
  [namespaces emparelhados e implicações de custo]: service-bus-paired-namespaces.md

<!---HONumber=AcomDC_1210_2015-->