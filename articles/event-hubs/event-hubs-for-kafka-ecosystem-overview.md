---
title: Hubs de Eventos do Azure para ecossistemas Kafka | Microsoft Docs
description: Visão geral e introdução aos Hubs de Eventos do Azure habilitados para Kafka
services: event-hubs
documentationcenter: .net
author: djrosanova
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 05/07/2018
ms.author: darosa
ms.openlocfilehash: 51e32737be4eaf3c61cdbe50b82a05c205800ac4
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077599"
---
# <a name="event-hubs-for-kafka-ecosystems"></a>Hubs de Eventos para ecossistemas Kafka

Os Hubs de Eventos para ecossistemas Kafka fornecem um ponto de extremidade de Kafka que pode ser usado pelos aplicativos baseados em Kafka existentes como uma alternativa para executar seu próprio cluster Kafka. Os Hubs de Eventos para ecossistema Kafka dão suporte para [Apache Kafka 1.0](https://kafka.apache.org/10/documentation.html) e versões mais recentes do cliente e funcionam com os aplicativos Kafka existentes, incluindo MirrorMaker. Altere sua cadeia de conexão e comece a transmitir eventos de seus aplicativos que usam o protocolo Kafka nos Hubs de Eventos.

## <a name="what-does-event-hubs-for-kafka-ecosystems-provide"></a>O que os Hubs de Eventos para ecossistemas Kafka fornecem?

Os Hubs de Eventos para o ecossistema Kafka fornecem um cabeçalho de protocolo com base nos Hubs de Eventos do Azure que são binários e compatíveis com versões Kafka 1.0 e posteriores para leitura a partir de tópicos do Kafta e gravação neles. Conceitualmente, Kafka e Hubs de Eventos são quase idênticos: ambos são logs particionados criados para a transmissão de dados. A tabela a seguir mapeia conceitos entre Kafka e Hubs de Eventos.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Mapeamento conceitual de Kafka e Hub de Eventos

| Conceito de Kafka | Conceito de Hubs de Eventos|
| --- | --- |
| HDInsight | Namespace |
| Tópico | Hubs de Eventos |
| Partition | Partition|
| Grupo de Consumidores | Grupo de Consumidores |
| Deslocamento | Deslocamento|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Principais diferenças entre Kafka e Hubs de Eventos

Embora o [Kafka Apache](https://kafka.apache.org/) seja um software, que pode ser executado sempre que você quiser, os Hubs de Eventos são um serviço de nuvem semelhante ao Armazenamento de Blobs do Azure. Não há nenhum servidor ou rede para gerenciar e nenhum agente para configurar. Crie um namespace, que é um FQDN no qual seus tópicos residem e crie Hubs de Eventos ou tópicos dentro desse namespace. Para saber mais sobre Hubs de Eventos e namespaces, consulte [O que são Hubs de Eventos?](event-hubs-what-is-event-hubs.md). Como um serviço de nuvem, os Hubs de Eventos usam um único endereço IP virtual estável como o ponto de extremidade, para que os clientes não precisem saber sobre os agentes ou computadores em um cluster. 

A escala em Hubs de Eventos é controlada pelo número de unidades de produtividade que você adquirir, com cada unidade de produtividade lhe dando direito a 1 MB por segundo ou 1000 eventos por segundo de entrada. Por padrão, os Hubs de Eventos escala verticalmente as unidades de produtividade quando você atinge o limite com o recurso [Inflar Automaticamente](event-hubs-auto-inflate.md); esse recurso também funciona com Hubs de Eventos para ecossistemas Kafka. 

### <a name="security-and-authentication"></a>Segurança e autenticação

Os Hubs de Eventos do Azure requerem SSL ou TLS para toda a comunicação e usam Assinaturas de Acesso Compartilhado (SAS) para autenticação. Esse requisito também é verdadeiro para um ponto de extremidade Kafka em Hubs de Eventos. Para compatibilidade com Kafka, os Hubs de Eventos usam SASL PLAIN para autenticação e SASL SSL para segurança de transporte. Para obter mais informações sobre a segurança em Hubs de Eventos, consulte [Autenticação e segurança de Hubs de Eventos](event-hubs-authentication-and-security-model-overview.md).

## <a name="other-event-hubs-features-available-for-kafka"></a>Outros recursos de Hubs de Eventos disponíveis para Kafka

O ecossistema de Hubs de Eventos para Kafka permite que você grave com um protocolo e leia com outro, para que seus produtores de Kafka atuais possam continuar publicando por meio de Kafka e você possa adicionar leitores com Hubs de Eventos, como Azure Stream Analytics ou Azure Functions. Além disso, os recursos de Hubs de Eventos, como [Capturar](event-hubs-capture-overview.md) e [Recuperação de Desastre Geográfico](event-hubs-geo-dr.md) também funcionam com Hubs de Eventos para ecossistemas Kafka.

## <a name="features-that-are-not-supported-in-the-preview"></a>Recursos que não são suportados na visualização

Para a visualização pública dos Hubs de Eventos para a integração de ecossistemas Kafka, não há suporte para os seguintes recursos de Kafka:

*   Produtor idempotente
*   Transação
*   Compactação
*   Retenção baseada em tamanho
*   Compactação do log
*   Adicionando partições a um tópico existente
*   Suporte a API de Kafta HTTP
*   Kafka Connect
*   Kafka Streams

## <a name="next-steps"></a>Próximas etapas

Este artigo apresentou uma introdução aos Hubs de Eventos para ecossistemas Kafka. Para saber mais, consulte os seguintes links:

* [Como criar Hubs de Eventos habilitados para Kafka](event-hubs-create-kafka-enabled.md)
* [Transmitir para Hubs de Eventos a partir de seus aplicativos Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* Introdução com um [Tutorial de Hubs de Eventos](event-hubs-dotnet-standard-getstarted-send.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)

 
 

