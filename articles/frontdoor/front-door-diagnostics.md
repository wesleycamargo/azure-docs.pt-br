---
title: Azure Front Door Service – Métricas e registro em log | Microsoft Docs
description: Este artigo ajuda você a entender as diferentes métricas e logs de acesso compatíveis com o Azure Front Door Service
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: 643ae03a9350868b172d99b2af7ce23e34fedf47
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997991"
---
# <a name="monitoring-metrics-for-front-door"></a>Métricas de monitoramento do Front Door

Usando o Azure Front Door Service, é possível monitorar suas principais métricas para validar a configuração do Front Door, juntamente com o uso, integridade e desempenho ou seu Front Door.

## <a name="metrics"></a>Métricas

Métricas são um recurso para alguns recursos do Azure, nas quais você pode exibir os contadores de desempenho no portal. Para o Front Door, estão disponíveis as seguintes métricas:

| Métrica | Nome de exibição da métrica | Unidade | Dimensões | DESCRIÇÃO |
| --- | --- | --- | --- | --- |
| RequestCount | Contagem de solicitações | Contagem | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O número de solicitações de cliente atendidas pelo Front Door.  |
| RequestSize | Tamanho da solicitação | Bytes | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O número de bytes enviados como solicitações de clientes para o Front Door. |
| ResponseSize | Tamanho da resposta | Bytes | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O número de bytes enviados como respostas do Front Door para clientes. |
| TotalLatency | Latência total | Milissegundos | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O tempo calculado de quando a solicitação do cliente foi recebida pelo Front Door até o cliente confirmar o último byte de resposta do Front Door. |
| BackendRequestCount | Contagem de solicitações de back-end | Contagem | HttpStatus</br>HttpStatusGroup</br>Back-end | O número de solicitações enviadas do Front Door aos back-ends. |
| BackendRequestLatency | Latência de solicitação de back-end | Milissegundos | Back-end | O tempo calculado de quando a solicitação foi enviada pelo Front Door ao back-end até o Front Door receber o último byte de resposta do back-end. |
| BackendHealthPercentage | Percentual de integridade do back-end | Porcentagem | Back-end</br>BackendPool | O percentual de investigações de integridade bem-sucedidas do Front Door aos back-ends. |
| WebApplicationFirewallRequestCount | Contagem de solicitações do Firewall de Aplicativo Web | Contagem | PolicyName</br>RuleName</br>Ação | O número de solicitações de cliente processadas pela segurança da camada de aplicativo do Front Door. |


## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
