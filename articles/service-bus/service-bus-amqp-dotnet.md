<properties 
   pageTitle="Barramento de Serviço com o .NET e AMQP 1.0 | Microsoft Azure"
   description="Usando o Barramento de Serviço do .NET com AMQP."
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
   ms.date="01/26/2016"
   ms.author="sethm" />

# Usando o Barramento de Serviço do .NET com AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

## Baixar o SDK do Barramento de Serviço

O suporte para o AMQP 1.0 está disponível na versão 2.1 ou posterior do SDK do Barramento de Serviço. Você pode baixar os bits mais recentes do Barramento de Serviço do [NuGet][].

## Configurando aplicativos .NET para usar o AMQP 1.0

Por padrão, a biblioteca de cliente do .NET do Barramento de Serviço se comunica com o serviço do Barramento de Serviço usando um protocolo dedicado baseado em SOAP. O uso do AMQP 1.0 em vez do protocolo padrão requer a configuração explícita na cadeia de conexão do Service Bus, conforme descrito na próxima seção. Além dessa alteração, o código do aplicativo permanece basicamente inalterado ao usar o AMQP 1.0.

Na versão atual, existem alguns outros recursos da API que não são suportados com o uso do AMQP. Esses recursos sem suporte são listados posteriormente na seção [Recursos sem suporte, restrições e diferenças de comportamento](#unsupported-features-restrictions-and-behavioral-differences). Algumas das definições de configuração avançadas também apresentam um significado diferente com o uso do AMQP.

### Configuração usando App.config

É uma boa prática que os aplicativos usem o arquivo de configuração do App.config para armazenar as configurações. Para os aplicativos do Barramento de Serviço, você pode usar o App.config para armazenar as configurações para o valor de **ConnectionString** do Barramento de Serviço. Um exemplo do arquivo App.config é mostrado abaixo:

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <appSettings>
	        <add key="Microsoft.ServiceBus.ConnectionString"
	             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
	    </appSettings>
	</configuration>

O valor da configuração `Microsoft.ServiceBus.ConnectionString` é a cadeia de conexão do Barramento de Serviço usada para configurar a conexão ao Barramento de Serviço. O formato é o seguinte:

	Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp

Em que `[namespace]` e `SharedAccessKey` são obtidos no [portal clássico do Azure][]. Para obter mais informações, consulte [Como usar as Filas do Barramento de Serviço][].

Ao usar AMQP, anexe a cadeia de conexão com `;TransportType=Amqp`. Essa notação informa à biblioteca de cliente para fazer sua conexão com o Barramento de Serviço usando o AMQP 1.0.

## Serialização de mensagem

Ao usar o protocolo padrão, o comportamento de serialização padrão da biblioteca de cliente .NET é usar o tipo [DataContractSerializer][] para serializar uma instância [BrokeredMessage][] para transporte entre a biblioteca de cliente e o serviço de Barramento de Serviço. Ao usar o modo de transporte do AMQP, a biblioteca de cliente usa o sistema do tipo AMQP para a serialização da [mensagem agenciada][BrokeredMessage] em uma mensagem do AMQP. Essa serialização permite que a mensagem seja recebida e interpretada por um aplicativo receptor que está potencialmente em execução em uma plataforma diferente, por exemplo, um aplicativo Java que usa a API JMS para acessar o Barramento de Serviço.

Quando você constrói uma instância [BrokeredMessage][], você pode fornecer um objeto .NET como um parâmetro para o construtor para servir como o corpo da mensagem. Para objetos que podem ser mapeados para tipos primitivos do AMQP, o corpo é serializado em tipos de dados do AMQP. Se o objeto não pode ser mapeado diretamente para um tipo primitivo do AMQP; ou seja, um tipo personalizado definido pelo aplicativo, então, o objeto é serializado usando o [DataContractSerializer][], e os bytes serializados são enviados em uma mensagem de dados AMQP.

Para facilitar a interoperabilidade com clientes não .NET, use somente tipos .NET que podem ser serializados diretamente em tipos do AMQP para o corpo da mensagem. A tabela a seguir detalha os tipos e o mapeamento correspondente ao sistema de tipos do AMQP.

| Tipo de objeto de corpo .NET | Tipo do AMQP mapeado | Tipo de seção de corpo do AMQP |
|--------------------------------|-------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| bool | Booliano | Valor do AMQP |
| byte | ubyte | Valor do AMQP |
| ushort | ushort | Valor do AMQP |
| uint | uint | Valor do AMQP |
| ulong | ulong | Valor do AMQP |
| sbyte | byte | Valor do AMQP |
| short | short | Valor do AMQP |
| int | int | Valor do AMQP |
| longo | longo | Valor do AMQP |
| flutuante | flutuante | Valor do AMQP |
| double | double | Valor do AMQP |
| decimal | decimal128 | Valor do AMQP |
| char | char | Valor do AMQP |
| DateTime | timestamp | Valor do AMQP |
| Guid | uuid | Valor do AMQP |
| byte | binário | Valor do AMQP |
| cadeia de caracteres | cadeia de caracteres | Valor do AMQP |
| System.Collections.IList | list | Valor AMQP: os itens contidos na coleção só podem ser aqueles definidos nesta tabela. |
| System.Array | array | Valor AMQP: os itens contidos na coleção só podem ser aqueles definidos nesta tabela. |
| System.Collections.IDictionary | map | Valor AMQP: os itens contidos na coleção só podem ser aqueles definidos nesta tabela. Observação: apenas as chaves de cadeia de caracteres são suportadas. |
| Uri | Cadeia de caracteres descrita (consulte a tabela a seguir) | Valor do AMQP |
| DateTimeOffset | Longo descrito (consulte a tabela a seguir) | Valor do AMQP |
| TimeSpan | Longo descrito (consulte a seguir) | Valor do AMQP |
| Fluxo | binário | Dados do AMQP (podem ser múltiplos). As seções de Dados contêm os bytes brutos lidos do objeto Stream. |
| Outro Objeto | binário | Dados do AMQP (podem ser múltiplos). Contém o binário serializado do objeto que usa o DataContractSerializer ou um serializador fornecido pelo aplicativo. |

| Tipo .NET | Tipo descrito do AMQP mapeado | Observações |
|----------------|-------------------------------------------------------------------------------------------------------------------------|-------------------------|
| Uri | `<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` | URI.AbsoluteUri |
| DateTimeOffset | `<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` | DateTimeOffset.UtcTicks |
| TimeSpan | `<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> ` | TimeSpan.Ticks |

## Recursos sem suporte, restrições e diferenças de comportamento

Os seguintes recursos da API .NET do Barramento de Serviço não são atualmente suportados com o uso do AMQP:

-   Transações.

-   Enviar por meio de destino da transferência.

-   Receber pelo número sequencial da mensagem.

-   Procurar mensagens e sessões.

-   Estado de sessão.

-   APIs baseadas em lote.

-   Recebimento dimensionado.

-   Manipulação do tempo de execução das regras de assinatura.

-   Renovação de bloqueio da sessão.

Especificamente, as seguintes APIs não são suportadas atualmente ao usar o AMQP:

- [Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession][]
- [Microsoft.ServiceBus.Messaging.MessagingFactory.CreateMessageSender(System.String,System.String)][]

- [Microsoft.ServiceBus.Messaging.MessageSender.SendBatch(System.Collections.Generic.IEnumerable{Microsoft.ServiceBus.Messaging.BrokeredMessage})][]

- [Microsoft.ServiceBus.Messaging.MessageReceiver.Receive(System.Int64)][]
- [(Microsoft.ServiceBus.Messaging.MessageReceiver.ReceiveBatch)][]
- [Microsoft.ServiceBus.Messaging.MessageReceiver.CompleteBatch(System.Collections.Generic.IEnumerable{System.Guid})][]
- [Microsoft.ServiceBus.Messaging.MessageReceiver.Peek][]
- [Microsoft.ServiceBus.Messaging.MessageReceiver.PeekBatch][]

- [Microsoft.ServiceBus.Messaging.QueueClient.Peek][]
- [Microsoft.ServiceBus.Messaging.QueueClient.PeekBatch][]

- [Microsoft.ServiceBus.Messaging.TopicClient.SendBatch(System.Collections.Generic.IEnumerable{Microsoft.ServiceBus.Messaging.BrokeredMessage})][]

- [Microsoft.ServiceBus.Messaging.SubscriptionClient.Receive(System.Int64)][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.ReceiveBatch][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.CompleteBatch(System.Collections.Generic.IEnumerable{System.Guid})][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.Peek][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.PeekBatch][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.AddRule][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.RemoveRule(System.String)][]

- [Microsoft.ServiceBus.Messaging.MessageSession.GetState][]
- [Microsoft.ServiceBus.Messaging.MessageSession.SetState(System.IO.Stream)][]
- [Microsoft.ServiceBus.Messaging.MessageSession.RenewLock][]

- [Microsoft.ServiceBus.Messaging.BrokeredMessage.RenewLock][]

Também há algumas pequenas diferenças no comportamento da API .NET do Barramento de Serviço ao usar AMQP, em comparação com o protocolo padrão:

-   A propriedade [OperationTimeout][] será ignorada.

-   `MessageReceiver.Receive(TimeSpan.Zero)` é implementado como `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.

## Controlando configurações do protocolo AMQP

As APIs .NET expõem diversas configurações que controlam o comportamento do protocolo AMQP:

-   **MessageReceiver.PrefetchCount**: Controla o crédito inicial aplicado a um link. O padrão é 0.

-   **MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize**: Controla o tamanho máximo do quadro do AMQP oferecido durante a negociação em tempo de conexão aberta. O padrão é de 65.536 bytes.

-   **MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval**: Caso as transferências possam ser em lote, este valor determina o atraso máximo para envio de disposições. Herdado pelos remetentes/destinatários por padrão. O remetente/destinatário individual pode substituir o padrão, que é 20 milissegundos.

-   **MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity**: Controla se as conexões do AMQP são estabelecidas em uma conexão SSL. O padrão é **true**.

## Próximas etapas

Está pronto(a) para saber mais? Visite os links a seguir:

- [Visão geral do AMQP do Barramento de Serviço]
- [Suporte a AMQP 1.0 para filas e tópicos particionados do Barramento de Serviço]
- [AMQP no Barramento de Serviço para Windows Server]

  [Como usar as Filas do Barramento de Serviço]: service-bus-dotnet-how-to-use-queues.md
  [DataContractSerializer]: https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: https://msdn.microsoft.com/library/azure/jj657638.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.CreateMessageSender(System.String,System.String)]: https://msdn.microsoft.com/library/azure/jj657703.aspx
  [Microsoft.ServiceBus.Messaging.MessageSender.SendBatch(System.Collections.Generic.IEnumerable{Microsoft.ServiceBus.Messaging.BrokeredMessage})]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.sendbatch.aspx
  [Microsoft.ServiceBus.Messaging.MessageReceiver.Receive(System.Int64)]: https://msdn.microsoft.com/library/azure/hh322665.aspx
  [(Microsoft.ServiceBus.Messaging.MessageReceiver.ReceiveBatch)]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.receivebatch.aspx
  [Microsoft.ServiceBus.Messaging.MessageReceiver.CompleteBatch(System.Collections.Generic.IEnumerable{System.Guid})]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.completebatch.aspx
  [Microsoft.ServiceBus.Messaging.MessageReceiver.Peek]: https://msdn.microsoft.com/library/azure/jj908731.aspx
  [Microsoft.ServiceBus.Messaging.MessageReceiver.PeekBatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.peekbatch.aspx
  [Microsoft.ServiceBus.Messaging.QueueClient.Peek]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peek.aspx
  [Microsoft.ServiceBus.Messaging.QueueClient.PeekBatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx
  [Microsoft.ServiceBus.Messaging.TopicClient.SendBatch(System.Collections.Generic.IEnumerable{Microsoft.ServiceBus.Messaging.BrokeredMessage})]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.sendbatch.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.Receive(System.Int64)]: https://msdn.microsoft.com/library/azure/hh293110.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.ReceiveBatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.receivebatch.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.CompleteBatch(System.Collections.Generic.IEnumerable{System.Guid})]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.completebatch.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.Peek]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.peek.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.PeekBatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.peekbatch.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.AddRule]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.addrule.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.RemoveRule(System.String)]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.removerule.aspx
  [Microsoft.ServiceBus.Messaging.MessageSession.GetState]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx
  [Microsoft.ServiceBus.Messaging.MessageSession.SetState(System.IO.Stream)]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx
  [Microsoft.ServiceBus.Messaging.MessageSession.RenewLock]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.renewlock.aspx
  [Microsoft.ServiceBus.Messaging.BrokeredMessage.RenewLock]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
[NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[portal clássico do Azure]: http://manage.windowsazure.com
[Visão geral do AMQP do Barramento de Serviço]: service-bus-amqp-overview.md
[Suporte a AMQP 1.0 para filas e tópicos particionados do Barramento de Serviço]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP no Barramento de Serviço para Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

<!---HONumber=AcomDC_0128_2016-->