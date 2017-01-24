---
title: "Visão geral das camadas de preço do Sistema de mensagens Premium e Standard do Barramento de Serviço | Microsoft Docs"
description: "Sistema de mensagens Premium e Standard do Barramento de Serviço"
services: service-bus-messaging
documentationcenter: .net
author: djrosanova
manager: timlt
editor: 
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/21/2016
ms.author: darosa,sethm
translationtype: Human Translation
ms.sourcegitcommit: d36b40444af4ba68b016351f9ff016351e9fe58c
ms.openlocfilehash: a4ccfdbc079a989477a80af7ac701dc77dce5a4f


---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Camadas de sistema de mensagens Premium e Standard do Barramento de Serviço
O Sistema de Mensagens do Barramento de Serviço, que inclui entidades de mensagens, como filas e tópicos, combina recursos corporativos de mensagens com rica semântica de assinatura para publicação na escala de nuvem. O Sistema de Mensagens do Barramento de Serviço é usado como o backbone de comunicação para muitas soluções de nuvem sofisticadas.

A camada *Premium* do Sistema de Mensagens do Barramento de Serviço atende às solicitações comuns dos clientes em torno da escala, do desempenho e da disponibilidade para aplicativos de missão crítica. Embora os conjuntos de recursos sejam quase idênticos, essas duas camadas do Sistema de Mensagens do Barramento de Serviço foram desenvolvidas para atender a diferentes casos de uso.

Algumas diferenças de alto nível são destacadas na tabela a seguir.

| Premium | Standard |
| --- | --- |
| Alta taxa de transferência |Taxa de transferência variável |
| Desempenho previsível |Latência variável |
| Preços previsíveis |Preço pré-pago variável |
| Capacidade de escalar verticalmente a carga de trabalho |N/D |
| Tamanho de mensagem com mais de 256 KB |O tamanho da mensagem é de 256 KB |

O **Sistema de Mensagens Premium do Barramento de Serviço** fornece isolamento de recursos na camada de CPU e memória, de modo que a carga de trabalho do cliente seja executada isoladamente. Esse contêiner de recurso é chamado de *unidade do sistema de mensagens*. Cada namespace premium é alocado para pelo menos uma unidade do sistema de mensagens. Você pode adquirir 1, 2 ou 4 unidades do sistema de mensagens para cada namespace Premium do Barramento de serviço. Uma única carga de trabalho ou entidade pode abranger várias unidades do sistema de mensagens, e o número de unidades do sistema de mensagens pode ser alterado à vontade, embora a cobrança seja feita por taxas diárias ou de 24 horas. O resultado é um desempenho previsível e repetível para sua solução baseada no Barramento de Serviço.

Esse desempenho não é apenas o mais previsível e disponível, mas também o mais rápido. O Sistema de Mensagens Premium do Barramento de Serviço se baseia no mecanismo de armazenamento introduzido nos [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/). Com o Sistema de Mensagens Premium, o desempenho de pico é muito mais rápido do que com a camada Standard.

## <a name="premium-messaging-technical-differences"></a>Diferenças técnicas do sistema de mensagens Premium
A seguir, algumas diferenças entre as camadas dos sistemas de mensagens Premium e Standard.

### <a name="partitioned-queues-and-topics"></a>Filas e tópicos particionados
Há suporte para filas e tópicos no Sistema de Mensagens Premium, mas elas não funcionam da mesma forma nas camadas Standard e Basic do Sistema de Mensagens do Barramento de Serviço. O Sistema de Mensagens Premium não usa o SQL como um armazenamento de dados e não tem mais a possível competição por recurso associada a uma plataforma compartilhada. Consequentemente, o particionamento não é necessário. Além disso, a contagem de partições foi alterada, de 16 partições no Sistema de Mensagens Standard para duas partições no Premium. Ter duas partições garante disponibilidade, além de ser um número mais apropriado para o ambiente de tempo de execução Premium. Para saber mais sobre o particionamento, confira as [Filas e tópicos particionados](service-bus-partitioning.md).

### <a name="express-entities"></a>Entidades expressas
Como o sistema de mensagens Premium é executado em um ambiente de tempo de execução totalmente isolado, não há suporte para as entidades expressas em namespaces Premium. Para saber mais sobre o recurso expresso, consulte a propriedade [QueueDescription.EnableExpress](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress).

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o Sistema de Mensagens do Barramento de Serviço, confira os tópicos a seguir.

* [Introdução ao Sistema de Mensagens Premium do Barramento de Serviço do Azure (postagem de blog)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introdução ao Sistema de Mensagens Premium do Barramento de Serviço do Azure (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Visão geral do Sistema de Mensagens do Barramento de Serviço](service-bus-messaging-overview.md)
* [Como usar filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)




<!--HONumber=Dec16_HO4-->


