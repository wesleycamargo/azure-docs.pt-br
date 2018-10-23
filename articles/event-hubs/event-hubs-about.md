---
title: O que é Hub de Eventos do Azure? | Microsoft Docs
description: Saiba mais sobre Hubs de Eventos do Azure, um serviço de streaming de Big Data que ingere milhões de eventos por segundo.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: overview
ms.custom: mvc
ms.date: 08/01/2018
ms.author: shvija
ms.openlocfilehash: 336756e72c6c07f52101ddd0cd99a6ef9744d3c3
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49351754"
---
# <a name="what-is-azure-event-hubs"></a>O que é Hub de Eventos do Azure?

Os Hubs de Eventos do Azure são uma plataforma de streaming de Big Data e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos pelos dispositivos e software distribuídos. Os dados enviados para um Hub de Eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. 

Os Hubs de eventos são usados em alguns dos cenários comuns a seguir:

- Detecção de anomalias (fraude/exceções)
- Registro em log do aplicativo
- Pipelines de análise, como a sequência de cliques
- Criação de painéis em tempo real
- Arquivamento de dados
- Processamento de transação
- Processamento de telemetria do usuário
- Streaming de telemetria do dispositivo 

## <a name="why-use-event-hubs"></a>Por que usar Hubs de Eventos?

Dados são valiosos apenas quando há uma maneira fácil de processar e obter análises em tempo hábil das fontes de dados. Os Hubs de Eventos fornecem uma plataforma de processamento de fluxo distribuído com baixa latência e integração perfeita, com serviços de análise e dados dentro e fora do Azure para criar um pipeline completo de Big Data.

Os Hubs de Evento representam a "porta da frente" de um pipeline de evento, geralmente chamado de *ingestor de eventos* em arquiteturas da solução. Um ingestor de eventos é um componente ou serviço que fica entre os editores de eventos e consumidores de eventos para desacoplar a produção de uma transmissão de eventos do consumo desses eventos. Os Hubs de Eventos fornecem uma plataforma unificada de streaming com o buffer de retenção de tempo, desacoplando os produtores de eventos dos consumidores de evento. 

As seções abaixo descrevem os principais recursos do serviço Hubs de Eventos do Azure: 

## <a name="fully-managed-paas"></a>PaaS totalmente gerenciado 

Os Hubs de Eventos são um serviço gerenciado com pouquíssima configuração ou gerenciamento de sobrecarga, para que você se concentre em suas soluções de negócios. [Os Hubs de Eventos para ecossistemas do Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) proporcionam a experiência de PaaS Kafka sem a necessidade de gerenciar, configurar ou executar seus clusters.

## <a name="support-for-real-time-and-batch-processing"></a>Suporte a processamento em lotes e em tempo real

Ingerir, armazenar em buffer, armazenar e processar seu fluxo em tempo real para obter análises acionáveis. Os Hubs de Eventos usam um [modelo de consumidor particionado](event-hubs-features.md#partitions), permitindo que vários aplicativos processem o fluxo simultaneamente e permitindo que você controle a velocidade de processamento.

[Capture](event-hubs-capture-overview.md) seus dados em tempo quase real em um [armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) ou [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/)  para processamento de microlotes ou de retenção de longo prazo. Você pode obter isso no mesmo fluxo que você usa para obter a análise em tempo real. A configuração da Captura é rápida, não há custos administrativos para executá-la e ela é dimensionada automaticamente com as  [unidades de produtividade](event-hubs-features.md#throughput-units) dos Hubs de Eventos. A Captura dos Hubs de Eventos permite que você se concentre no processamento de dados e não na captura de dados.

Os Hubs de Eventos do Azure também se integram com o [Azure Functions](/azure/azure-functions/) para uma arquitetura sem servidor.

## <a name="scalable"></a>Escalonável 

Com os Hubs de eventos, você pode iniciar com fluxos de dados em megabytes e aumentar para gigabytes ou terabytes. O recurso [Expandir automaticamente](event-hubs-auto-inflate.md) é uma das muitas opções disponíveis para aumentar o número de unidades de produtividade para atender às suas necessidades de uso. 

## <a name="rich-ecosystem"></a>Ecossistema avançado

[Os Hubs de Eventos para ecossistemas do Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) permitem que os clientes e aplicativos [Apache Kafka (1.0 e acima)](https://kafka.apache.org/) se comuniquem com os Hubs de Eventos sem a necessidade de gerenciar os clusters.
 
Com uma enorme variedade disponível em várias [linguagens (.NET, Java, Python, Go, Node.js)](https://github.com/Azure/azure-event-hubs), você pode iniciar facilmente o processamento dos seus fluxos nos Hubs de Eventos. Todos as linguagens com suporte do cliente fornecem integração de baixo nível.

## <a name="key-architecture-components"></a>Principais componentes da arquitetura

Os Hubs de Eventos fornecem um recurso de manipulação de fluxo de mensagens, mas têm características que são diferentes das mensagens corporativas tradicionais. Os recursos de Hubs de Eventos são criados em torno de cenários de alta produtividade e de processamento de eventos. Os Hubs de Eventos contêm os seguintes [componentes principais](event-hubs-features.md):

- **Produtores de eventos**: Uma entidade que envia dados para um hub de eventos. Os editores de eventos podem publicar eventos usando HTTPS ou AMQP 1.0 ou Apache Kafka (1.0 e acima)
- **Partições**: Cada consumidor lê somente um subconjunto específico, ou partição, do fluxo de mensagens.
- **Grupos de consumidores**: Uma exibição (estado, posição ou deslocamento) de todo um hub de eventos. Os grupos de consumidores permitem que vários aplicativos de consumo tenham um modo de exibição separado do fluxo de eventos e leiam o fluxo de forma independente em seu próprio ritmo e com seus próprios deslocamentos.
- **Unidades de produtividade**: Unidades de capacidade que controlam a capacidade de produtividade dos Hubs de Eventos.
- **Receptores de evento**: Qualquer entidade que leia dados de evento de um hub de eventos. Todos os consumidores de Hubs de Eventos se conectam por meio de sessão do AMQP 1.0, e os eventos são entregues por meio da sessão à medida que são disponibilizados. Todos os consumidores do Kafka se conectam por meio do protocolo Kafka 1.0 e posterior.

A figura a seguir mostra a arquitetura de processamento de fluxo dos Hubs de Eventos:

![Hubs de Eventos](./media/event-hubs-about/event_hubs_architecture.png)


## <a name="next-steps"></a>Próximas etapas

Para começar a usar os Hubs de Eventos, consulte os artigos a seguir:

* [Ingerir nos Hubs de Eventos](event-hubs-quickstart-portal.md)
* [Visão geral dos recursos de Hubs de Eventos](event-hubs-features.md)
* [Perguntas frequentes](event-hubs-faq.md)


