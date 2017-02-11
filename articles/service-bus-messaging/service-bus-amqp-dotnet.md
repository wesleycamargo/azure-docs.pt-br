---
title: "Barramento de Serviço com o .NET e AMQP 1.0 | Microsoft Docs"
description: "Usando o Barramento de Serviço do .NET com AMQP"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 332bcb13-e287-4715-99ee-3d7d97396487
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0cf8282d40c60fe9887dcbf43e2a526ffe34cc22


---
# <a name="using-service-bus-from-net-with-amqp-10"></a>Usando o Barramento de Serviço do .NET com AMQP 1.0
[!INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

## <a name="downloading-the-service-bus-sdk"></a>Baixar o SDK do Barramento de Serviço
O suporte para o AMQP 1.0 está disponível na versão 2.1 ou posterior do SDK do Barramento de Serviço. É possível garantir que você tem a versão mais recente baixando os bits do Barramento de Serviço do [NuGet][NuGet].

## <a name="configuring-net-applications-to-use-amqp-10"></a>Configurando aplicativos .NET para usar o AMQP 1.0
Por padrão, a biblioteca de cliente do .NET do Barramento de Serviço se comunica com o serviço do Barramento de Serviço usando um protocolo dedicado baseado em SOAP. O uso do AMQP 1.0 em vez do protocolo padrão requer a configuração explícita na cadeia de conexão do Service Bus, conforme descrito na próxima seção. Além dessa alteração, o código do aplicativo permanece inalterado ao usar o AMQP 1.0.

Na versão atual, existem alguns outros recursos da API que não são suportados com o uso do AMQP. Esses recursos sem suporte são listados posteriormente na seção [Recursos sem suporte, restrições e diferenças de comportamento](#unsupported-features-restrictions-and-behavioral-differences). Algumas das definições de configuração avançadas também apresentam um significado diferente com o uso do AMQP.

### <a name="configuration-using-appconfig"></a>Configuração usando App.config
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

Em que `[namespace]` e `SharedAccessKey` são obtidos no [Portal do Azure][Portal do Azure]. Para obter mais informações, consulte [Introdução às filas do Barramento de Serviço][Introdução às filas do Barramento de Serviço].

Ao usar AMQP, anexe a cadeia de conexão com `;TransportType=Amqp`. Essa notação informa à biblioteca de cliente para fazer sua conexão com o Barramento de Serviço usando o AMQP 1.0.

## <a name="message-serialization"></a>Serialização de mensagem
Ao usar o protocolo padrão, o comportamento de serialização padrão da biblioteca de cliente .NET é usar o tipo [DataContractSerializer][DataContractSerializer] para serializar uma instância [BrokeredMessage][BrokeredMessage] para transporte entre a biblioteca de cliente e o serviço de Barramento de Serviço. Ao usar o modo de transporte do AMQP, a biblioteca de cliente usa o sistema do tipo AMQP para a serialização da [mensagem agenciada][BrokeredMessage] em uma mensagem do AMQP. Essa serialização permite que a mensagem seja recebida e interpretada por um aplicativo receptor que está potencialmente em execução em uma plataforma diferente, por exemplo, um aplicativo Java que usa a API JMS para acessar o Barramento de Serviço.

Quando você constrói uma instância [BrokeredMessage][BrokeredMessage], é possível fornecer um objeto .NET como um parâmetro para o construtor para funcionar como o corpo da mensagem. Para objetos que podem ser mapeados para tipos primitivos do AMQP, o corpo é serializado em tipos de dados do AMQP. Se o objeto não pode ser mapeado diretamente para um tipo primitivo do AMQP; ou seja, um tipo personalizado definido pelo aplicativo, então, o objeto é serializado usando o [DataContractSerializer][DataContractSerializer], e os bytes serializados são enviados em uma mensagem de dados AMQP.

Para facilitar a interoperabilidade com clientes não .NET, use somente tipos .NET que podem ser serializados diretamente em tipos do AMQP para o corpo da mensagem. A tabela a seguir detalha os tipos e o mapeamento correspondente ao sistema de tipos do AMQP.

| Tipo de objeto de corpo .NET | Tipo do AMQP mapeado | Tipo de seção de corpo do AMQP |
| --- | --- | --- |
| bool |Booliano |Valor do AMQP |
| byte |ubyte |Valor do AMQP |
| ushort |ushort |Valor do AMQP |
| uint |uint |Valor do AMQP |
| ulong |ulong |Valor do AMQP |
| sbyte |byte |Valor do AMQP |
| short |short |Valor do AMQP |
| int |int |Valor do AMQP |
| longo |longo |Valor do AMQP |
| flutuante |flutuante |Valor do AMQP |
| double |double |Valor do AMQP |
| decimal |decimal128 |Valor do AMQP |
| char |char |Valor do AMQP |
| DateTime |timestamp |Valor do AMQP |
| Guid |uuid |Valor do AMQP |
| byte[] |binário |Valor do AMQP |
| string |string |Valor do AMQP |
| System.Collections.IList |list |Valor AMQP: os itens contidos na coleção só podem ser aqueles definidos nesta tabela. |
| System.Array |array |Valor AMQP: os itens contidos na coleção só podem ser aqueles definidos nesta tabela. |
| System.Collections.IDictionary |map |Valor AMQP: os itens contidos na coleção só podem ser aqueles definidos nesta tabela. Observação: apenas as chaves de cadeia de caracteres são suportadas. |
| Uri |Cadeia de caracteres descrita (consulte a tabela a seguir) |Valor do AMQP |
| DateTimeOffset |Longo descrito (consulte a tabela a seguir) |Valor do AMQP |
| TimeSpan |Longo descrito (consulte a seguir) |Valor do AMQP |
| Fluxo |binário |Dados do AMQP (podem ser múltiplos). As seções de Dados contêm os bytes brutos lidos do objeto Stream. |
| Outro Objeto |binário |Dados do AMQP (podem ser múltiplos). Contém o binário serializado do objeto que usa o DataContractSerializer ou um serializador fornecido pelo aplicativo. |

| Tipo .NET | Tipo descrito do AMQP mapeado | Observações |
| --- | --- | --- |
| Uri |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |URI.AbsoluteUri |
| Datetimeoffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| TimeSpan |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> ` |TimeSpan.Ticks |

## <a name="unsupported-features-restrictions-and-behavioral-differences"></a>Recursos sem suporte, restrições e diferenças de comportamento
Os seguintes recursos da API .NET do Barramento de Serviço não são atualmente suportados com o uso do AMQP:

* Transações
* Enviar por meio de destino da transferência

Também há algumas pequenas diferenças no comportamento da API .NET do Barramento de Serviço ao usar AMQP, em comparação com o protocolo padrão:

* A propriedade [OperationTimeout][OperationTimeout] será ignorada.
* `MessageReceiver.Receive(TimeSpan.Zero)` é implementado como `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.
* Só é possível concluir mensagens com tokens de bloqueio pelos destinatários de mensagem que inicialmente receberam as mensagens.

## <a name="controlling-amqp-protocol-settings"></a>Controlando configurações do protocolo AMQP
As APIs .NET expõem diversas configurações que controlam o comportamento do protocolo AMQP:

* **MessageReceiver.PrefetchCount**: Controla o crédito inicial aplicado a um link. O padrão é 0.
* **MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize**: Controla o tamanho máximo do quadro do AMQP oferecido durante a negociação em tempo de conexão aberta. O padrão é de 65.536 bytes.
* **MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval**: Caso as transferências possam ser em lote, este valor determina o atraso máximo para envio de disposições. Herdado pelos remetentes/destinatários por padrão. O remetente/destinatário individual pode substituir o padrão, que é 20 milissegundos.
* **MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity**: Controla se as conexões do AMQP são estabelecidas em uma conexão SSL. O padrão é **true**.

## <a name="next-steps"></a>Próximas etapas
Está pronto(a) para saber mais? Visite os links a seguir:

* [Visão geral do Barramento de Serviço para AMQP]
* [Suporte a AMQP 1.0 para filas e tópicos particionados do Barramento de Serviço]
* [AMQP no Barramento de Serviço para Windows Server]

[Introdução às filas do Barramento de Serviço]: service-bus-dotnet-get-started-with-queues.md
[DataContractSerializer]: https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx
[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: https://msdn.microsoft.com/library/azure/jj657638.aspx
[Microsoft.ServiceBus.Messaging.MessagingFactory.CreateMessageSender(System.String,System.String)]: https://msdn.microsoft.com/library/azure/jj657703.aspx
[OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
[NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[Portal do Azure]: https://portal.azure.com
[Visão geral do Barramento de Serviço para AMQP]: service-bus-amqp-overview.md
[Suporte a AMQP 1.0 para filas e tópicos particionados do Barramento de Serviço]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP no Barramento de Serviço para Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx



<!--HONumber=Nov16_HO3-->


