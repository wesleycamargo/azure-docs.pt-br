---
title: "Métricas do Hub de Eventos do Azure no Azure Monitor (versão prévia) | Microsoft Docs"
description: Use o Monitoramento do Azure para monitorar Hubs de Eventos
services: event-hubs
documentationcenter: .NET
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: sethm
ms.openlocfilehash: c3ee6a87592145801167b35ff281c04062e525da
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2017
---
# <a name="azure-event-hubs-metrics-in-azure-monitor-preview"></a>Métricas do Hub de Eventos do Azure no Azure Monitor (versão prévia)

As métricas de Hubs de evento fornecem o estado dos recursos de Hubs de Eventos na sua assinatura do Azure. Com um amplo conjunto de dados de métricas, você pode avaliar a integridade geral dos seus hubs de eventos não apenas no nível de namespace, mas também no nível de entidade. Essas estatísticas podem ser importantes, pois elas ajudam você a monitorar o estado de seus hubs de eventos. As métricas também podem ajudar a solucionar problemas de causa raiz sem a necessidade de entrar em contato com o suporte do Azure.

O Azure Monitor fornece interfaces de usuário unificadas para monitoramento entre os diferentes serviços do Azure. Para obter mais informações, consulte [Monitoramento no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) e o exemplo [Recuperar métricas do Azure Monitor com o .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) no GitHub.

## <a name="access-metrics"></a>Métricas de acesso

O Azure Monitor fornece várias maneiras de acessar as métricas. Você pode acessar as métricas por meio do [Portal do Azure](https://portal.azure.com) ou usar as APIs do Azure Monitor (REST e .NET) e as soluções de análise como o Operation Management Suite e os Hubs de Eventos. Para obter mais informações, consulte [Métricas do Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

As métricas estão habilitadas por padrão e você pode acessar os dados dos últimos 30 dias. Se você precisar manter os dados por um período de tempo maior, você pode arquivar os dados de métrica em uma conta de Armazenamento do Azure. Isso pode ser configurado em [configurações de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) no Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Acessar as métricas no portal

É possível monitorar as métricas ao longo do tempo no [Portal do Azure](https://portal.azure.com). O exemplo a seguir mostra como exibir solicitações bem sucedidas e solicitações de entrada no nível da conta:

![][1]

Você também pode acessar as métricas diretamente por meio do namespace. Para fazer isso, selecione seu namespace e clique em **Métricas (Versão Prévia)**. Para exibir as métricas filtradas para o escopo do hub de eventos, selecione o hub de eventos e clique em **Métricas (versão prévia)**.

Para métricas com suporte a dimensões, será necessário filtrar o valor da dimensão desejado, como mostrado no exemplo a seguir:

![][2]

## <a name="billing"></a>Cobrança

O uso de métricas no Azure Monitor é gratuito no momento durante a versão prévia. No entanto, se você usar outras soluções que ingerem dados de métricas, você poderá ser cobrado por essas soluções. Por exemplo, você será cobrado pelo Armazenamento do Azure se você arquivar dados de métrica para uma conta de Armazenamento do Azure. Você também será cobrado pelo OMS (Operation Management Suite) se você transmitir dados de métricas para o OMS para análise avançada.

As métricas a seguir oferecem uma visão geral da integridade do seu serviço. 

> [!NOTE]
> Diversas métricas estão sendo preteridas à medida que são transferidas para um nome diferente. Isso pode exigir que você atualize suas referências. Métricas marcadas com a palavra-chave "preterida" não terão suporte no futuro.

Todos os valores de métricas são enviados para o Azure Monitor a cada minuto. A granularidade o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de tempo com suporte para todas as métricas do Hubs de Eventos é 1 minuto.

## <a name="request-metrics"></a>Métricas de solicitação

Conta o número de solicitações de operações de gerenciamento e de dados.

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
| Solicitações de entrada (versão prévia) | O número de solicitações feitas ao serviço de Hubs de Eventos do Azure durante um período específico. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName |
| Solicitações bem sucedidas (versão prévia)   | O número de solicitações bem sucedidas feitas ao serviço de Hubs de Eventos do Azure durante um período específico. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName |
| Erros do servidor (versão prévia) | O número de solicitações não processadas devido a um erro no serviço de Hubs de Eventos do Azure durante um período específico. <br/><br/>Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName |
|Erros do usuário (versão prévia)|O número de solicitações não processadas devido a erros do usuário durante um período específico.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Solicitações restritas (versão prévia)|O número de solicitações que foram restringidas porque o uso de unidades da taxa de transferência foi excedido.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Erros de cota excedida (versão prévia)|O número de solicitações que excedeu a cota disponível. Consulte [este artigo](event-hubs-quotas.md) para ver mais informações sobre as cotas dos Hubs de Eventos.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="throughput-metrics"></a>Métricas de taxa de transferência

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
|Solicitações restritas (versão prévia)|O número de solicitações que foram restringidas porque o uso de unidades da taxa de transferência foi excedido.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="message-metrics"></a>Métricas de mensagens

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
|Mensagens de entrada (versão prévia)|O número de eventos ou mensagens enviadas para os Hubs de Eventos durante um período específico.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Mensagens de saída (versão prévia)|O número de eventos ou mensagens recuperadas dos Hubs de Eventos durante um período específico.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Bytes de entrada (versão prévia)|O número de bytes enviado ao serviço de Hubs de Eventos do Azure durante um período específico.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Bytes de saída (versão prévia)|O número de bytes recuperado do serviço de Hubs de Eventos do Azure durante um período específico.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="connection-metrics"></a>Métricas de conexão

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
|ActiveConnections (versão prévia)|O número de conexões ativas em um namespace, bem como em uma entidade.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Conexões abertas (versão prévia)|O número de conexões abertas.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Conexões fechadas (versão prévia)|O número de conexões fechadas.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="event-hubs-capture-metrics"></a>Métricas de captura de Hubs de Eventos

Monitore as métricas de captura de Hubs de Eventos habilitando o recurso de Captura para seus Hubs de Eventos. As métricas a seguir descrevem o que você pode monitorar com a Captura habilitada.

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
|Capturar lista de pendências (versão prévia)|O número de bytes que ainda estão pendentes de captura para o destino escolhido.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Mensagens capturadas (versão prévia)|O número de mensagens ou eventos que são capturados para o destino escolhido durante um período específico.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Bytes capturados (versão prévia)|O número de bytes que são capturados para o destino escolhido durante um período específico.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="metrics-dimensions"></a>Dimensões de métricas

Os Hubs de Evento do Azure dão suporte às seguintes dimensões para métricas no Azure Monitor. Adicionar dimensões às métricas é opcional. Se você não adicionar dimensões, as métricas serão especificadas no nível de namespace. 

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
|EntityName| Os Hubs de Eventos dão suporte às entidades de hub de evento sob o namespace.|

## <a name="next-steps"></a>Próximas etapas

* Consulte [Visão geral do Monitoramento do Azure](../monitoring-and-diagnostics/monitoring-overview.md).
* Exemplo de como [Recuperar métricas do Azure Monitor com o .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) no GitHub. 

Para saber mais sobre Hubs de Eventos, acesse os seguintes links:

* Introdução com um [Tutorial de Hubs de Eventos](event-hubs-dotnet-standard-getstarted-send.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)
* [Aplicativos de exemplo que usam Hub de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



