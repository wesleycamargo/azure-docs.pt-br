<properties 
   pageTitle="Práticas recomendadas para melhorar o desempenho usando o Barramento de Serviço | Microsoft Azure"
   description="Descreve como usar o Barramento de Serviço do Azure para otimizar o desempenho na troca de mensagens agenciadas."
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

# Práticas recomendadas para melhorias de desempenho usando o sistema de mensagens agenciado do Barramento de Serviço

Este tópico descreve como usar o Barramento de Serviço do Azure para otimizar o desempenho na troca de mensagens agenciadas. A primeira parte deste tópico descreve os diferentes mecanismos oferecidos para ajudar a melhorar o desempenho. A segunda parte oferece orientação sobre como usar o Barramento de Serviço de uma maneira que possa oferecer o melhor desempenho em um determinado cenário.

Ao longo deste tópico, o termo "cliente" refere-se a qualquer entidade que acesse o Barramento de Serviço. Um cliente pode assumir a função de um remetente ou de um receptor. O termo "remetente" é usado para um cliente de fila ou de tópico do Barramento de Serviço que envia mensagens para uma fila ou um tópico do Barramento de Serviço. O termo "receptor" refere-se a um cliente de fila ou de assinatura do Barramento de Serviço que recebe mensagens de uma fila ou uma assinatura do Barramento de Serviço.

Estas seções apresentam vários conceitos usados pelo Barramento de Serviço para melhorar o desempenho.

## Protocolos

O Barramento de Serviço permite que os clientes enviem e recebam mensagens por meio de dois protocolos: o protocolo de cliente do Barramento de Serviço e o HTTP (REST). O protocolo de cliente do Barramento de Serviço é mais eficiente porque manterá a conexão com o serviço Barramento de Serviço enquanto a fábrica de mensagens existir. Ele também implementa o envio em lote e a pré-busca. O protocolo de cliente do Barramento de Serviço está disponível para aplicativos .NET usando as APIs do .NET.

A menos que mencionado explicitamente, todo o conteúdo deste tópico supõe o uso do protocolo de cliente do Barramento de Serviço.

## Reutilizando fábricas e clientes

Os objetos de cliente do Barramento de Serviço, como [QueueClient][] ou [MessageSender][], são criados por meio de um objeto [MessagingFactory][], que também oferece gerenciamento interno de conexões. Você não deve fechar fábricas do sistema de mensagens ou os clientes de fila, de tópico e de assinatura depois de enviar uma mensagem e então recriá-los ao enviar a próxima mensagem. Fechar uma fábrica do sistema de mensagens exclui a conexão com o serviço Barramento de Serviço e uma nova conexão é estabelecida na recriação da fábrica. O estabelecimento de uma conexão é uma operação cara que você pode evitar usando a mesma fábrica e objetos de cliente para diversas operações.

## Operações simultâneas

A execução de uma operação (enviar, receber, excluir etc.) leva algum tempo. Esse tempo inclui o processamento da operação pelo serviço do barramento de serviço, além da latência da solicitação e resposta. Para aumentar o número de operações por hora, elas devem ser executadas simultaneamente. Você pode fazer isso de várias maneiras diferentes:

-   **Operações assíncronas**: o cliente agenda operações realizando operações assíncronas. A próxima solicitação é iniciada antes que a solicitação anterior seja concluída. Este é um exemplo de uma operação de envio assíncrono:

	```
	BrokeredMessage m1 = new BrokeredMessage(body);
	BrokeredMessage m2 = new BrokeredMessage(body);
	
	Task send1 = queueClient.SendAsync(m1).ContinueWith((t) => 
	  {
	    Console.WriteLine("Sent message #1");
	  });
	Task send2 = queueClient.SendAsync(m2).ContinueWith((t) => 
	  {
	    Console.WriteLine("Sent message #2");
	  });
	Task.WaitAll(send1, send2);
	Console.WriteLine("All messages sent");
	```

	Este é um exemplo de uma operação de recebimento assíncrono:
	
	```
	Task receive1 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
	Task receive2 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
	
	Task.WaitAll(receive1, receive2);
	Console.WriteLine("All messages received");
	
	async void ProcessReceivedMessage(Task<BrokeredMessage> t)
	{
	  BrokeredMessage m = t.Result;
	  Console.WriteLine("{0} received", m.Label);
	  await m.CompleteAsync();
	  Console.WriteLine("{0} complete", m.Label);
	}
	```

-   **Diversas fábricas**: todos os clientes (remetentes e receptores) criados pela mesma fábrica compartilham uma conexão TCP. A taxa de transferência máxima de mensagens é limitada pelo número de operações que podem passar por essa conexão TCP. A taxa de transferência que pode ser obtida com uma única fábrica varia muito de acordo com os tempos de viagem de ida e volta do TCP e com o tamanho da mensagem. Para obter taxas de transferência mais altas, você deverá usar diversas fábricas do sistema de mensagens.

## Modo de recebimento

Ao criar um cliente de fila ou de assinatura, você poderá especificar um modo de recebimento: *Bloqueio de pico* ou *Receber e Excluir*. O modo de recebimento padrão é [PeekLock][]. Ao operar nesse modo, o cliente envia uma solicitação para receber uma mensagem do Barramento de Serviço. Depois que o cliente tiver recebido a mensagem, ele enviará uma solicitação para concluir a mensagem.

Ao definir o modo de recebimento como [ReceiveAndDelete][], as duas etapas serão combinadas em uma única solicitação. Isso reduz o número total de operações e pode melhorar a taxa de transferência geral da mensagem. Este ganho de desempenho vem com o risco de perda de mensagens.

O Barramento de Serviço não dá suporte a transações para operações de receber e excluir. Além disso, a semântica do bloqueio de pico é necessária para todos os cenários em que o cliente queira adiar uma mensagem ou enviá-la para as mensagens mortas.

## Envio em lote no lado do cliente

O envio em lote no lado do cliente permite que um cliente de fila ou de tópico atrase o envio de uma mensagem por um determinado período. Se o cliente enviar mensagens adicionais durante esse período, ele transmitirá as mensagens em um único lote. O envio em lote no lado do cliente também faz com que um cliente de fila/assinatura agrupe em lote diversas solicitações **Concluir** em uma única solicitação. O envio em lote está disponível apenas para operações **Enviar** e **Concluir** assíncronas. As operações síncronas são imediatamente enviadas para o serviço Barramento de Serviço. O envio em lote não ocorre para as operações de pico ou de recebimento, e também não ocorre entre clientes.

Se o lote exceder o tamanho máximo da mensagem, a última mensagem será removida do lote e o cliente enviará o lote imediatamente. A última mensagem se tornará a primeira mensagem do próximo lote. Por padrão, um cliente usa um intervalo de lote de 20 ms. Você pode alterar o intervalo de lote definindo a propriedade [BatchFlushInterval][] antes de criar a fábrica de mensagens. Essa configuração afeta todos os clientes criados por essa fábrica.

Para desabilitar o envio em lote, defina a propriedade [BatchFlushInterval][] como **TimeSpan.Zero**. Por exemplo:

```
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

O envio em lote não afeta o número de operações faturáveis do sistema de mensagens e está disponível somente para o protocolo de cliente do Barramento de Serviço. O protocolo HTTP não dá suporte ao envio em lote.

## Acesso ao repositório do envio em lote

Para aumentar a taxa de transferência de uma fila/tópico/assinatura, o Barramento de Serviço agrupa várias mensagens ao gravar em seu repositório interno. Se habilitada em uma fila ou tópico, a gravação de mensagens no repositório ocorrerá em lote. Se habilitada em uma fila ou assinatura, a exclusão de mensagens no repositório ocorrerá em lote. Se o acesso ao repositório em lote estiver habilitado para uma entidade, o Barramento de Serviço atrasará uma operação de gravação do repositório em relação àquela entidade por até 20 ms. As operações de armazenamento adicionais que ocorrerem durante esse intervalo serão adicionadas ao lote. O acesso ao repositório em lote só afetará as operações **Enviar** e **Concluir**; as operações de receber não são afetadas. O acesso ao repositório em lote é uma propriedade em uma entidade. O envio em lote ocorrerá em todas as entidades que permitirem o acesso ao repositório em lote.

Quando uma nova fila, um novo tópico ou uma nova assinatura for criada, o acesso ao repositório em lote será habilitado por padrão. Para desabilitar o acesso ao repositório em lote, defina a propriedade [EnableBatchedOperations][] como **false** antes de criar a entidade. Por exemplo:

```
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

O acesso ao repositório em lote não afeta o número de operações faturáveis do sistema de mensagens faturáveis e é uma propriedade de uma fila, um tópico ou uma assinatura. É independente do modo de recebimento e do protocolo usado entre um cliente e o serviço Barramento de Serviço.

## Pré-busca

A pré-busca permite que o cliente de fila ou de assinatura carregue mensagens adicionais do serviço ao executar uma operação de recebimento. O cliente armazena essas mensagens em um cache local. O tamanho do cache é determinado pelas propriedades [QueueClient.PrefetchCount][] e [SubscriptionClient.PrefetchCount][]. Cada cliente que permite a pré-busca mantém seu próprio cache. Um cache não é compartilhado entre os clientes. Se o cliente iniciar uma operação de recebimento e se o cache estiver vazio, o serviço transmitirá um lote de mensagens. O tamanho do lote é igual ao tamanho do cache ou a 256 KB, o que for menor. Se o cliente iniciar uma operação de recebimento e se o cache contiver uma mensagem, a mensagem será retirada do cache.

Quando uma mensagem for pré-buscada, ela será bloqueada pelo serviço. Ao fazer isso, a mensagem pré-buscada não poderá ser recebida por um receptor diferente. Se o receptor não puder concluir a mensagem antes da expiração do bloqueio, a mensagem ficará disponível para outros destinatários. A cópia pré-buscada da mensagem permanecerá no cache. O receptor que consumir a cópia armazenada em cache expirada receberá uma exceção ao tentar concluir essa mensagem. Por padrão, o bloqueio da mensagem expira após 60 segundos. Esse valor pode ser estendido para 5 minutos. Para impedir o consumo de mensagens expiradas, o tamanho do cache sempre deverá ser menor do que o número de mensagens que podem ser consumidas por um cliente no intervalo de tempo limite de bloqueio.

Ao usar a expiração de bloqueio padrão de 60 segundos, um bom valor para [SubscriptionClient.PrefetchCount][] será de 20 vezes as taxas máximas de processamento de todos os receptores da fábrica. Por exemplo, uma fábrica cria 3 receptores e cada receptor pode processar até 10 mensagens por segundo. A contagem de pré-busca não deve exceder 20*3*10 = 600. Por padrão, [QueueClient.PrefetchCount][] é definido como 0, o que significa que nenhuma mensagem adicional será buscada do serviço.

A pré-busca de mensagens aumenta a taxa de transferência geral de uma fila ou uma assinatura porque reduz o número geral de operações de mensagem, ou as viagens de ida e volta. A busca da primeira mensagem, no entanto, demorará mais (devido ao tamanho maior da mensagem). O recebimento de mensagens pré-buscadas será mais rápido porque essas mensagens já foram baixadas pelo cliente.

A propriedade de vida útil (TTL) de uma mensagem é verificada pelo servidor no momento em que o servidor envia a mensagem para o cliente. O cliente não verifica a propriedade TTL da mensagem quando a mensagem é recebida. Em vez disso, a mensagem poderá ser recebida mesmo se a TTL da mensagem tiver passado enquanto a mensagem estava armazenada em cache pelo cliente.

A pré-busca não afeta o número de operações faturáveis do sistema de mensagens e está disponível somente para o protocolo de cliente do Barramento de Serviço. O protocolo HTTP não dá suporte à pré-busca. A pré-busca está disponível para as operações de recebimento síncrono e assíncrono.

## Filas e tópicos expressos

As entidades expressas permitem a existência de cenários de alta taxa de transferência e de latência reduzida. Com as entidades expressas, se uma mensagem for enviada a uma fila ou a um tópico, ela não será armazenada imediatamente no repositório de mensagens. Em vez disso, a mensagem será armazenada em cache na memória. Se uma mensagem permanecer na fila por mais de alguns segundos, será automaticamente gravada em armazenamento estável, que o protegerá contra perda devido a uma interrupção. A gravação da mensagem em um cache de memória aumenta a taxa de transferência e reduz a latência porque não há nenhum acesso ao armazenamento estável no momento em que a mensagem é enviada. As mensagens consumidas em alguns segundos não são gravadas no repositório de mensagens. O exemplo a seguir cria um tópico expresso.

```
TopicDescription td = new TopicDescription(TopicName);
td.EnableExpress = true;
namespaceManager.CreateTopic(td);
```

Se uma mensagem com informações importantes que não devem ser perdidas for enviada para uma entidade expressa, o remetente poderá impor o Barramento de Serviço para persistir imediatamente a mensagem em armazenamento estável ao definir a propriedade [ForcePersistence][] como **true**.

## Uso de filas ou tópicos particionados

Internamente, o Barramento de Serviço usa o mesmo nó e o repositório de mensagens para processar e armazenar todas as mensagens para uma entidade de mensagens (fila ou tópico). Uma fila ou tópico particionado, por outro lado, é distribuído entre vários nós e repositórios de mensagens. As filas e tópicos particionados não só geram uma taxa de transferência mais alta do que as filas e os tópicos normais, como também exibem disponibilidade superior. Para criar uma entidade particionada, defina a propriedade [EnablePartitioning][] como **true**, como mostrado no exemplo a seguir. Para obter mais informações sobre entidades particionadas, consulte [Entidades de mensagens particionadas][].

```
// Create partitioned queue.
QueueDescription qd = new QueueDescription(QueueName);
qd.EnablePartitioning = true;
namespaceManager.CreateQueue(qd);
```

## Uso de várias filas

Se não for possível usar uma fila ou tópico particionado, ou se a carga esperada não puder ser manipulada por uma única fila ou tópico particionada, você deverá usar várias entidades do sistema de mensagens. Ao usar várias entidades, crie um cliente dedicado para cada entidade em vez de usar o mesmo cliente para todas as entidades.

## Cenários

As seções a seguir descrevem cenários típicos de mensagens e as configurações preferenciais do Barramento de Serviço. As taxas de transferência são classificadas como pequena (menos de 1 mensagem/segundo), moderada (1 mensagem/segundo ou mais, mas menos de 100 mensagens por segundo) e alta (100 mensagens/segundo ou mais). O número de clientes é classificado como pequeno (5 ou menos), moderado (de 5 a 20) e grande (mais de 20).

### Fila de alta taxa de transferência

Meta: maximizar a taxa de transferência de uma única fila. O número de remetentes e de receptores é pequeno.

-   Use uma fila particionada para obter desempenho e disponibilidade aprimorados.

-   Para aumentar a taxa geral de envio para a fila, use diversas fábricas de mensagens para criar remetentes. Para cada remetente, use operações assíncronas ou vários threads.

-   Para aumentar a taxa geral de recebimento da fila, use diversas fábricas de mensagens para criar receptores.

-   Use operações assíncronas para tirar proveito do envio em lote no lado do cliente.

-   Defina o intervalo de envio em lote como 50 ms para reduzir o número de transmissões de protocolo de cliente do Barramento de Serviço. Se forem usados vários remetentes, aumente o intervalo de envio em lote para 100 ms.

-   Deixe o acesso ao repositório em lote habilitado. Isso aumenta a taxa geral em que as mensagens podem ser gravadas na fila.

-   Defina a contagem de pré-busca como 20 vezes as taxas máximas de processamento de todos os receptores de uma fábrica. Isso reduz o número de transmissões de protocolo de cliente do Barramento de Serviço.

### Várias filas de alta taxa de transferência

Meta: maximize a taxa de transferência geral de diversas filas. A taxa de transferência de uma fila individual é moderada ou alta.

Para obter a taxa de transferência máxima em várias filas, use as configurações descritas para maximizar a taxa de transferência de uma única fila. Além disso, use fábricas diferentes para criar clientes que enviem ou recebam de filas diferentes.

### Fila de baixa latência

Meta: minimize a latência de ponta a ponta de uma fila ou um tópico. O número de remetentes e de receptores é pequeno. A taxa de transferência da fila é pequena ou moderada.

-   Use uma fila particionada para obter disponibilidade aprimorada.

-   Desabilite o envio em lote no lado do cliente. O cliente envia uma mensagem imediatamente.

-   Desabilite o acesso ao repositório em lote. O serviço grava imediatamente a mensagem no repositório.

-   Se estiver usando um único cliente, defina a contagem de pré-busca como 20 vezes a taxa de processamento do receptor. Se várias mensagens chegarem à fila ao mesmo tempo, o protocolo de cliente do Barramento de Serviço transmite todas ao mesmo tempo. Quando o cliente receber a próxima mensagem, essa mensagem já estará no cache local. O cache deve ser pequeno.

-   Se você estiver usando vários clientes, defina a contagem de pré-busca como 0. Ao fazer isso, o segundo cliente poderá receber a segunda mensagem enquanto o primeiro cliente ainda estiver processando a primeira mensagem.

### Fila com um grande número de remetentes

Meta: maximizar a taxa de transferência de uma fila ou tópico com um grande número de remetentes. Cada remetente envia mensagens com uma taxa moderada. O número de receptores é pequeno.

O Barramento de Serviço permite até 1000 conexões simultâneas a uma entidade do sistema de mensagens (ou 5000 usando AMQP). Esse limite é imposto no nível de namespace e tópicos/filas/assinaturas são controlados pelo limite de conexões simultâneas por namespace. Para filas, esse número é compartilhado entre remetentes e receptores. Se todas as 1000 conexões forem necessárias aos remetentes, você deverá substituir a fila por um tópico e uma única assinatura. Um tópico aceita até 1000 conexões simultâneas dos remetentes, enquanto a assinatura aceita 1000 conexões simultâneas adicionais dos receptores. Se mais de 1000 remetentes simultâneos forem necessários, os remetentes deverão enviar mensagens para o protocolo do Barramento de Serviço via HTTP.

Para maximizar a taxa de transferência, faça o seguinte:

-   Use uma fila particionada para obter desempenho e disponibilidade aprimorados.

-   Se cada remetente residir em um processo diferente, use somente uma única fábrica por processo.

-   Use operações assíncronas para tirar proveito do envio em lote no lado do cliente.

-   Use o intervalo de lote padrão de 20 ms para reduzir o número de transmissões de protocolo de cliente do Barramento de Serviço.

-   Deixe o acesso ao repositório em lote habilitado. Isso aumenta a taxa geral em que as mensagens podem ser gravadas na fila ou no tópico.

-   Defina a contagem de pré-busca como 20 vezes as taxas máximas de processamento de todos os receptores de uma fábrica. Isso reduz o número de transmissões de protocolo de cliente do Barramento de Serviço.

### Fila com um grande número de receptores

Meta: maximizar a taxa de recebimento de uma fila ou de uma assinatura com um grande número de receptores. Cada receptor recebe mensagens a uma taxa moderada. O número de remetentes é pequeno.

O Barramento de Serviço permite até 1000 conexões simultâneas a uma entidade. Se uma fila exigir mais de 1000 receptores, você deverá substituir a fila por um tópico e várias assinaturas. Cada assinatura pode dar suporte a até 1000 conexões simultâneas. Como alternativa, os receptores poderão acessar a fila por meio do protocolo HTTP.

Para maximizar a taxa de transferência, faça o seguinte:

-   Use uma fila particionada para obter desempenho e disponibilidade aprimorados.

-   Se cada receptor residir em um processo diferente, use somente uma única fábrica por processo.

-   Os receptores poderão usar operações síncronas ou assíncronas. Dada a taxa de recebimento moderada de um receptor individual, o envio em lote no lado do cliente de uma solicitação Concluir não afetará a taxa de transferência do receptor.

-   Deixe o acesso ao repositório em lote habilitado. Isso reduz a carga geral da entidade. Isso também reduz a taxa geral em que as mensagens podem ser gravadas na fila ou no tópico.

-   Defina a contagem de pré-busca como um valor pequeno (por exemplo, PrefetchCount = 10). Isso impede que os receptores fiquem ociosos enquanto outros receptores tenham grandes quantidades de mensagens armazenadas em cache.

### Tópico com um pequeno número de assinaturas

Meta: maximizar a taxa de transferência de um tópico com um pequeno número de assinaturas. Uma mensagem é recebida por muitas assinaturas, o que significa que a taxa de recebimento combinada em todas as assinaturas é maior do que a taxa de envio. O número de remetentes é pequeno. O número de receptores por assinatura é pequeno.

Para maximizar a taxa de transferência, faça o seguinte:

-   Use um tópico particionado para obter desempenho e disponibilidade aprimorados.

-   Para aumentar a taxa geral de envio para o tópico, use diversas fábricas de mensagens para criar remetentes. Para cada remetente, use operações assíncronas ou vários threads.

-   Para aumentar a taxa geral de recebimento de uma assinatura, use diversas fábricas de mensagens para criar receptores. Para cada receptor, use operações assíncronas ou vários threads.

-   Use operações assíncronas para tirar proveito do envio em lote no lado do cliente.

-   Use o intervalo de lote padrão de 20 ms para reduzir o número de transmissões de protocolo de cliente do Barramento de Serviço.

-   Deixe o acesso ao repositório em lote habilitado. Isso aumenta a taxa geral em que as mensagens podem ser gravadas no tópico.

-   Defina a contagem de pré-busca como 20 vezes as taxas máximas de processamento de todos os receptores de uma fábrica. Isso reduz o número de transmissões de protocolo de cliente do Barramento de Serviço.

### Tópico com um grande número de assinaturas

Meta: maximizar a taxa de transferência de um tópico com um grande número de assinaturas. Uma mensagem é recebida por várias assinaturas, o que significa que a taxa de recebimento combinada em todas as assinaturas é muito maior do que a taxa de envio. O número de remetentes é pequeno. O número de receptores por assinatura é pequeno.

Os tópicos com um grande número de assinaturas normalmente terão uma taxa de transferência geral baixa se todas as mensagens forem roteadas a todas as assinaturas. Isso é causado pelo fato de que cada mensagem é recebida várias vezes e todas as mensagens contidas em um tópico e todas as suas assinaturas são armazenadas no mesmo repositório. Supõe-se que o número de remetentes e o número de receptores por assinatura é baixo. O Barramento de Serviço dá suporte a até 2.000 assinaturas por tópico.

Para maximizar a taxa de transferência, faça o seguinte:

-   Use um tópico particionado para obter desempenho e disponibilidade aprimorados.

-   Use operações assíncronas para tirar proveito do envio em lote no lado do cliente.

-   Use o intervalo de lote padrão de 20 ms para reduzir o número de transmissões de protocolo de cliente do Barramento de Serviço.

-   Deixe o acesso ao repositório em lote habilitado. Isso aumenta a taxa geral em que as mensagens podem ser gravadas no tópico.

-   Defina a contagem de pré-busca como 20 vezes a taxa de recebimento esperada em segundos. Isso reduz o número de transmissões de protocolo de cliente do Barramento de Serviço.

## Próximas etapas

Para saber mais sobre a otimização do desempenho do Barramento de Serviço, confira [Entidades de mensagens particionadas][].

  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [MessageSender]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [PeekLock]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx
  [ReceiveAndDelete]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx
  [BatchFlushInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.netmessagingtransportsettings.batchflushinterval.aspx
  [EnableBatchedOperations]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations.aspx
  [QueueClient.PrefetchCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.prefetchcount.aspx
  [SubscriptionClient.PrefetchCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.prefetchcount.aspx
  [ForcePersistence]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.forcepersistence.aspx
  [EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
  [Entidades de mensagens particionadas]: service-bus-partitioning.md
  

<!---HONumber=AcomDC_0107_2016-->