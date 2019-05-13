---
title: Visão geral dos hubs de eventos dedicados – Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral dos Hubs de eventos do Azure dedicado, que oferece implantações de único locatário de hubs de eventos.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 4f721dc4fda5bef002c794d79dfd2f054f9eaf38
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65511187"
---
# <a name="overview-of-event-hubs-dedicated"></a>Visão geral de Hubs de Eventos Dedicados

*Clusters de Hubs de eventos* oferecem implantações de único locatário para clientes com necessidades mais exigentes streaming. Essa oferta de locatário único tem um SLA de 99,99% garantido e está disponível apenas em nosso dedicado que tipo de preço. Um cluster de Hubs de eventos possível inserir milhões de eventos por segundo com latência de subsegundos e capacidade garantida. Namespaces e hubs de eventos criados dentro do cluster dedicado incluem todos os recursos da oferta Standard e muito mais, mas sem limites de entrada. Ele também inclui a popular [captura de Hubs de eventos](event-hubs-capture-overview.md) recurso sem custo adicional, permitindo a você automaticamente em lotes e log de fluxos de dados para o armazenamento do Azure ou Azure Data Lake. 

Clusters são provisionados e cobrados por **unidades de capacidade (CUs)**, uma quantidade pré-alocada de recursos de CPU e memória. Você pode comprar 1, 2, 4, 8, 12, 16 ou 20 UCS para cada cluster. Quanto você pode inserir e transmitir por CU depende de uma variedade de fatores, como o número de produtores e consumidores, a forma de carga, saída de taxa (veja os resultados do benchmark do abaixo para obter mais detalhes). 

> [!NOTE]
> Todos os clusters de Hubs de eventos são ativados para Kafka por padrão e dar suporte a pontos de extremidade de Kafka que podem ser usados por existente Kafka com base em aplicativos. Tendo Kafka habilitado no seu cluster não afeta seus casos de uso de não-Kafka; Não há nenhuma opção nem a necessidade de desabilitar o Kafka em um cluster.

## <a name="why-dedicated"></a>Por isso que dedicado?

Hubs de eventos dedicados oferece três benefícios interessantes para clientes que precisam da capacidade de nível empresarial:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Garante a capacidade para melhorar o desempenho de um locatário

Um cluster dedicado garante a capacidade em larga escala, e pode ingresso até gigabytes de dados de streaming com latência de subsegundos e armazenamento durável para acomodar qualquer intermitência de tráfego. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Inclusivo e exclusivo de acesso a recursos 
A oferta dedicada inclui recursos como capturar em nenhum custo adicional, bem como acesso exclusivo a recursos futuros, como BYOK. O serviço também gerencia o balanceamento de carga, OS atualizações, patches de segurança e de particionamento para o cliente, para que possam passar menos tempo na manutenção da infraestrutura e mais tempo na criação de recursos do lado do cliente.  

#### <a name="cost-savings"></a>Economia de custos
Em volumes de alta de entrada (> 100 TUs), um cluster os custos significativamente menor por hora que a compra de uma quantidade comparável de unidades de taxa de transferência na oferta padrão.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Os Hubs de eventos dedicados, cotas e limites

A oferta de Hubs de eventos dedicados é cobrada por um preço mensal fixo, com um mínimo de 4 horas de uso. A camada dedicada oferece todos os recursos do plano Standard, mas com limites e capacidade em escala empresarial para clientes com cargas de trabalho exigentes. 

| Recurso | Standard | Dedicado |
| --- |:---:|:---:|
| Largura de banda | 20 TUs (até 40 TUs) | 20 CUs |
| Namespaces |  1 | 50 por CU |
| Hubs de Eventos |  10 | Nenhum limite para os hubs de eventos/tópicos |
| Eventos de entrada | Pagamento por milhão de eventos | Incluso |
| Tamanho da mensagem | 1 milhão de Bytes | 1 milhão de Bytes |
| Partições | 40 por namespace | 2000 por CU |
| Grupos de consumidores | 20 por Hub de eventos | Nenhum limite por CU, 1000 por hub de eventos |
| Conexões orientadas | 1000 incluídos | 100.000 incluídos |
| Retenção de mensagem | 7 dias, 84 GB incluído por TU | 90 dias, 10 TB incluído por CU |
| Captura | Pagamento por hora | Incluso |

## <a name="how-to-onboard"></a>Como fazer a integração

Para integrar os Hubs de eventos dedicados, entre em contato com o [equipe de Hubs de eventos](mailto:askeventhubs@microsoft.com). O plano dedicado é exclusivo porque oferece uma integração mais prática da equipe de produto dos Hubs de Eventos para lhe dar a implantação flexível ideal. 

## <a name="faqs"></a>Perguntas frequentes

#### <a name="what-can-i-achieve-with-a-cluster"></a>O que pode obter com um cluster?

Para um cluster de Hubs de eventos, quanto você pode inserir e transmitir depende de vários fatores, como seu produtores, os consumidores, a taxa na qual você está ingestão e processamento e muito mais. 

A tabela a seguir mostra os resultados do parâmetro de comparação obtidos durante o teste:

| Forma da carga | Destinatários | Largura de banda de entrada| Mensagens de entrada | Largura de banda de saída | Mensagens de saída | Total de TUs | TUs por CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Lotes de 100x1KB | 2 | 400 MB/s | 400 mil mensagens/s | 800 MB/s | 800 mil mensagens/s | 400 TUs | 100 TUs | 
| Lotes de 10x10KB | 2 | 666 MB/s | 66.6 mil mensagens/s | 1,33 GB/s | 133 mil mensagens/s | 666 TUs | 166 TUs |
| Lotes de 6x32KB | 1 | 1,05 GB/s | mensagens de 34 k / s | 1,05 GB/s | 34 mil mensagens/s | 1000 TUs | 250 TUs |

No teste, usamos os critérios a seguir:

- Um cluster de Hubs de eventos de camada dedicada com quatro unidades de capacidade (CUs) foi usado. 
- O hub de eventos usado para ingestão tinha 200 partições. 
- Os dados ingeridos foram recebidos por dois aplicativos destinatários que recebem de todas as partições.

#### <a name="can-i-scale-down-my-cluster"></a>Pode dimensionar verticalmente meu cluster?

Após a criação, os clusters são cobrados por um mínimo de 4 horas de uso. A versão de visualização da experiência de autoatendimento, você pode enviar uma [solicitação de suporte](https://ms.portal.azure.com/#create/Microsoft.Support) para a equipe de Hubs de eventos em *técnico > cota > solicitação para escalar verticalmente ou dedicada para baixo Cluster*. Pode levar até 7 dias para concluir a solicitação para dimensionar o cluster. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Como a recuperação de desastres geográfica trabalharão com meu cluster?

É possível que um namespace em um cluster de camada dedicada com outro namespace em um cluster de camada dedicada de par de área geográfica. Não recomendamos o emparelhamento de um namespace de camada dedicada com um namespace em nossa oferta padrão, uma vez que o limite de taxa de transferência serão incompatíveis que resultará em erros. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Posso migrar meu namespaces padrão para pertencer a um cluster de camada dedicada?
Não suportamos um processo de migração automatizada para migrar seus dados de hubs de eventos de um namespace padrão para um dedicado um. Para migrar para um cluster de camada dedicada, é recomendável drenagem esquerda quaisquer mensagens em seus hubs de eventos de camada Standard e substituindo os pontos de extremidade de conexão com a do seu namespace dedicado.

## <a name="next-steps"></a>Próximas etapas

Entre em contato com seu representante de vendas da Microsoft ou com o Suporte da Microsoft para obter mais detalhes sobre a capacidade dos Hubs de Eventos Dedicados. Você também pode saber mais sobre os tipos de preços dos Hubs de Eventos visitando os links a seguir:

- [Preço de Hubs de Eventos Dedicados](https://azure.microsoft.com/pricing/details/event-hubs/). Você também pode entrar em contato com seu representante de vendas da Microsoft ou com o Suporte da Microsoft para obter mais detalhes sobre a capacidade dos Hubs de Eventos Dedicados.
- As [perguntas frequentes sobre Hubs de Eventos](event-hubs-faq.md) contêm informações sobre preços e tiram algumas dúvidas sobre os Hubs de Eventos.
