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
ms.openlocfilehash: 5d4d591c465cae91c59e8f86ea9d3e421db0f952
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58112102"
---
# <a name="monitoring-metrics-for-front-door"></a>Métricas de monitoramento do Front Door

Usando o serviço de porta da frente do Azure, você pode monitorar os recursos das seguintes maneiras:
* [Logs](#diagnostic-logging): Os logs permitem que o desempenho, o acesso e outros dados sejam salvos ou consumidos de um recurso para fins de monitoramento.

* [Métrica](#metrics): O Gateway de Aplicativo atualmente tem sete métricas para exibir os contadores de desempenho.

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

## <a name="activity-log"></a>Logs de atividade

Logs de atividades fornecem informações sobre as operações que foram executadas em sua frente. Usando logs de atividade, você pode determinar a "o que, quem e quando" para quaisquer operações de gravação (PUT, POST, DELETE) realizadas na sua frente.

> [!NOTE]
> Os logs de atividades não incluem operações de leitura (GET) ou operações realizadas no portal do Azure ou usando as APIs de gerenciamento original.

Você pode acessar os logs de atividade em sua porta de entrada ou acessar logs de todos os seus recursos do Azure no Azure Monitor. 

Para exibir logs de atividade:

1. Selecione sua instância de porta da frente.
2. Clique em **Log de atividades**.

    ![log de atividades](./media/front-door-diagnostics/activity-log.png)

3. Selecione o escopo de filtragem desejado e clique em **Aplicar**.

## <a name="diagnostic-logging"></a>Logs de diagnóstico
Os logs de diagnóstico fornecem informações avançadas sobre operações e erros importantes para auditoria, bem como para fins de solução de problemas. Os logs de diagnóstico são diferentes dos logs de atividades. Os logs de atividades fornecem informações sobre as operações realizadas em seus recursos do Azure. Os Logs de Diagnóstico fornecem informações em operações que o recurso realizou. Saiba mais sobre [logs de diagnóstico do Azure Monitor](../azure-monitor/platform/diagnostic-logs-overview.md). 

Para configurar logs de diagnóstico para sua frente:

1. Selecione a instância de serviço do APIM.
2. Clique em **Configurações do Diagnóstico**.

    ![logs de diagnóstico](./media/front-door-diagnostics/diagnostic-log.png)

3. Clique em **Ativar diagnóstico**. Você pode arquivar os logs de diagnóstico junto com as métricas em uma conta de armazenamento, transmiti-los para um Hub de Eventos ou enviá-los para os logs do Azure Monitor. 

Serviço de porta da frente do Azure atualmente fornece diagnóstico logs (agrupados por hora) sobre API individual de solicitação com cada entrada tem o esquema a seguir:

| Propriedade  | DESCRIÇÃO |
| ------------- | ------------- |
| ClientIp | Endereço IP do cliente que fez a solicitação. |
| ClientPort | A porta do IP do cliente que fez a solicitação. |
| HttpMethod | Método HTTP usado pela solicitação. |
| HttpStatusCode | O código de status HTTP retornado do proxy. |
| HttpStatusDetails | Status resultante na solicitação. Significado deste valor de cadeia de caracteres pode ser encontrado em uma tabela de referência de Status. |
| HttpVersion | Tipo da solicitação ou da conexão. |
| RequestBytes | O tamanho da mensagem de solicitação HTTP em bytes, incluindo os cabeçalhos de solicitação e o corpo da solicitação. |
| RequestUri | URI da solicitação recebida. |
| ResponseBytes | Bytes enviados pelo servidor de back-end, como a resposta.  |
| RoutingRuleName | O nome da regra de roteamento de solicitação correspondente. |
| SecurityProtocol | A versão do protocolo TLS/SSL não usado pela solicitação ou nulo se nenhuma criptografia. |
| TimeTaken | O período de tempo que a ação tomou em milissegundos. |
| UserAgent | O tipo de navegador que o cliente usou |
| TrackingReference | A cadeia de caracteres de referência exclusivo que identifica uma solicitação no servidas por porta da frente, também é enviado como o cabeçalho X-Azure-Ref para o cliente. Necessário para pesquisar detalhes nos logs de acesso para uma solicitação específica. |

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
