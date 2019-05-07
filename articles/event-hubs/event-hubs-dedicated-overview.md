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
ms.openlocfilehash: e8a2d8321a42e8b3d090c1ce1fdb3fd9a7ee3714
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138656"
---
# <a name="overview-of-event-hubs-dedicated"></a>Visão geral de Hubs de Eventos Dedicados

Clusters de Hubs de eventos do Azure oferecem implantações de único locatário para clientes com necessidades mais exigentes streaming. Essa oferta de locatário único tem um SLA de 99,99% garantido. Ele está disponível apenas em de dedicado que tipo de preço.

Um cluster de Hubs de eventos possível inserir milhões de eventos por segundo com latência de subsegundos e capacidade garantida. Namespaces e hubs de eventos criados dentro do cluster dedicado incluem todos os recursos da oferta Standard e muito mais, mas sem limites de entrada. Ele também inclui o [captura de Hubs de eventos](event-hubs-capture-overview.md) recurso sem nenhum custo adicional. Você pode usá-lo automaticamente em lotes e log de fluxos de dados para o armazenamento do Azure ou Azure Data Lake.

Clusters dedicados são provisionados e cobrados por unidades de capacidade (CUs). CUs são uma quantidade pré-alocado de recursos de CPU e memória. Você pode adquirir 1, 2, 4, 8, 12, 16 ou 20 UCS para cada cluster. Quanto você pode ingerir e fluxo por CU depende de fatores como o número de produtores e consumidores, a forma de carga e a taxa de saída.

Para obter mais informações, consulte a tabela com os resultados do parâmetro de comparação.

## <a name="why-use-event-hubs-dedicated"></a>Por que usar Hubs de eventos dedicados?

Hubs de eventos dedicados oferece três benefícios para clientes que precisam da capacidade de nível empresarial.

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Garante a capacidade para melhorar o desempenho de um locatário

Um cluster dedicado garante capacidade em larga escala. É possível inserir até gigabytes de dados de streaming com latência de subsegundos e armazenamento durável para acomodar picos de tráfego.

#### <a name="inclusive-and-exclusive-access-to-features"></a>Inclusivo e exclusivo de acesso a recursos 
A oferta dedicada inclui recursos como captura sem nenhum custo adicional. Ele também oferece acesso exclusivo a recursos futuros, como BYOK. O serviço também gerencia o balanceamento de carga, OS atualizações, patches de segurança e particionamento. Com esses recursos, você pode gastar menos tempo na manutenção da infraestrutura e mais tempo na criação de recursos do lado do cliente.

#### <a name="cost-savings"></a>Economia de custos
Em volumes de alta de entrada, um cluster custos significativamente menor por hora que se você comprar uma quantidade comparável de taxa de transferência TUs (unidades) na oferta padrão. Um alto volume for > 100 TUs.


## <a name="event-hubs-standard-vs-dedicated"></a>Vs Standard dos Hubs de eventos. Dedicado

A tabela a seguir compara as camadas de serviço disponíveis dos Hubs de Eventos. A oferta de Hubs de eventos dedicados é cobrada por um preço mensal fixo, comparado ao preço de uso para a maioria dos recursos do Standard. A camada dedicada oferece todos os recursos do plano Standard, mas com capacidade de escala empresarial para clientes com cargas de trabalho exigentes.

| Recurso | Standard | Dedicado |
| --- |:---:|:---:|
| Eventos de entrada | Pagamento por milhão de eventos | Incluso |
| Unidade de taxa de transferência (entrada de 1 MB/s, saída de 2 MB/s) | Pagamento por hora | Incluso |
| Tamanho da mensagem | 1 MB | 1 MB |
| Partições | 40 por namespace | 2.000 por CU |
| Grupos de consumidores | 20 por hub de eventos | 1.000 por hub de eventos |
| Largura de banda máxima | 20 TUs (até 40 TUs) | 20 CUs |
| Conexões orientadas | 1000 incluídos | 100.000 incluídos |
| Retenção de mensagem | Um dia incluído | Até sete dias incluídos |
| Captura | Pagamento por hora | Incluso |

## <a name="what-can-i-achieve-with-a-cluster"></a>O que pode obter com um cluster?

Para um cluster de Hubs de eventos, quanto você pode inserir e transmitir depende de seu produtores, seus consumidores, a taxa em que você ingira e processe e muito mais.

A tabela a seguir mostra os resultados de benchmark que foram obtidos durante o teste.

| Forma da carga | Destinatários | Largura de banda de entrada| Mensagens de entrada | Largura de banda de saída | Mensagens de saída | Total de TUs | TUs por CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Lotes de 100x1KB | 2 | 400 MB/s | 400.000 mensagens/s | 800 MB/s | mensagens de 800.000/s | 400 TUs | 100 TUs | 
| Lotes de 10x10KB | 2 | 666 MB/s | mensagens 66,600/s | 1,33 GB/s | mensagens 133,000/s | 666 TUs | 166 TUs |
| Lotes de 6x32KB | 1 | 1,05 GB/s | mensagens 34.000/s | 1,05 GB/s | mensagens 34.000/s | 1.000 TUs | 250 TUs |

No teste, os critérios a seguir foram usados:

- Um cluster de Hubs de eventos de camada dedicada com quatro unidades de capacidade foi usado.
- O hub de eventos usado para ingestão tinha 200 partições.
- Os dados que foi ingeridos foi recebidos por dois aplicativos do receptor. Eles receberam dados de todas as partições.

## <a name="use-event-hubs-dedicated"></a>Usar Hubs de eventos dedicados

Usar Hubs de eventos dedicados, [entre em contato com o suporte de cobrança](https://ms.portal.azure.com/#create/Microsoft.Support) ou seu representante da Microsoft. Você pode dimensionar sua capacidade para cima ou para baixo ao longo do mês a fim de atender às suas necessidades, adicionando ou removendo CUs. A equipe de produto dos Hubs de eventos ajuda você a obter a implantação flexível ideal para você.

## <a name="next-steps"></a>Próximas etapas

Entre em contato com seu representante de vendas da Microsoft ou o Microsoft Support para obter informações adicionais sobre a capacidade de Hubs de eventos dedicados. Para saber mais sobre tipos de preço de Hubs de eventos, use os links a seguir:

- [Preço de Hubs de Eventos Dedicados](https://azure.microsoft.com/pricing/details/event-hubs/). Você também pode contatar seu representante de vendas da Microsoft ou o Microsoft Support para obter informações adicionais sobre a capacidade de Hubs de eventos dedicados.
- As [perguntas frequentes sobre Hubs de Eventos](event-hubs-faq.md) contêm informações sobre preços e tiram algumas dúvidas sobre os Hubs de Eventos.
