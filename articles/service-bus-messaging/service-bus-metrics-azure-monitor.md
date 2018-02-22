---
title: "Métricas do Barramento de Serviço no Azure Monitor (versão prévia) | Microsoft Docs"
description: "Use o Monitoramento do Azure para monitorar entidades de Barramento de Serviço"
services: service-bus-messaging
documentationcenter: .NET
author: christianwolf42
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/05/2018
ms.author: sethm
ms.openlocfilehash: 20115897bb5ae2638588e79d80700fa8ece06104
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-service-bus-metrics-in-azure-monitor-preview"></a>Métricas do Barramento de Serviço do Azure no Azure Monitor (versão prévia)

As métricas do Barramento de Serviço fornecem o estado dos recursos na sua assinatura do Azure. Com um amplo conjunto de dados de métricas, você pode avaliar a integridade geral dos seus recursos de Barramento de Serviço não apenas no nível de namespace, mas também no nível de entidade. Essas estatísticas podem ser importantes, pois elas ajudam você a monitorar o estado do Barramento de Serviço. As métricas também podem ajudar a solucionar problemas de causa raiz sem a necessidade de entrar em contato com o suporte do Azure.

O Azure Monitor fornece interfaces de usuário unificadas para monitoramento entre os diferentes serviços do Azure. Para obter mais informações, consulte [Monitoramento no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) e o exemplo [Recuperar métricas do Azure Monitor com o .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) no GitHub.

## <a name="access-metrics"></a>Métricas de acesso

O Azure Monitor fornece várias maneiras de acessar as métricas. Você pode acessar as métricas por meio do [Portal do Azure](https://portal.azure.com) ou usar as APIs do Azure Monitor (REST e .NET) e as soluções de análise como o Operation Management Suite (OMS) e os Hubs de Eventos. Para obter mais informações, consulte [Métricas do Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

As métricas estão habilitadas por padrão e você pode acessar os dados dos últimos 30 dias. Se você precisar manter os dados por um período de tempo maior, você pode arquivar os dados de métrica em uma conta de Armazenamento do Azure. Isso pode ser configurado em [configurações de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) no Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Acessar as métricas no portal

É possível monitorar as métricas ao longo do tempo no [Portal do Azure](https://portal.azure.com). O exemplo a seguir mostra como exibir solicitações bem-sucedidas e solicitações de entrada no nível da conta:

![][1]

Você também pode acessar as métricas diretamente por meio do namespace. Para fazer isso, selecione seu namespace e clique em **Métricas (Versão Prévia)**. Para exibir as métricas filtradas para o escopo da entidade, selecione a entidade e clique em **Métricas (versão prévia)**.

![][2]

Para métricas com suporte para dimensões, você deve filtrar pelo valor da dimensão desejado.

## <a name="billing"></a>Cobrança

O uso de métricas no Azure Monitor é grátis durante a versão prévia. No entanto, se você usar outras soluções que ingerem dados de métricas, você poderá ser cobrado por essas soluções. Por exemplo, você será cobrado pelo Armazenamento do Azure se você arquivar dados de métrica para uma conta de Armazenamento do Azure. Você também será cobrado pelo OMS (Operation Management Suite) se você transmitir dados de métricas para o OMS para análise avançada.

As métricas a seguir oferecem uma visão geral da integridade do seu serviço. 

> [!NOTE]
> Diversas métricas estão sendo preteridas à medida que são transferidas para um nome diferente. Isso pode exigir que você atualize suas referências. Métricas marcadas com a palavra-chave "preterida" não terão suporte no futuro.

Todos os valores de métricas são enviados para o Azure Monitor a cada minuto. A granularidade de tempo define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de tempo com suporte para todas as métricas do Barramento de Serviço é de 1 minuto.

## <a name="request-metrics"></a>Métricas de solicitação

Conta o número de solicitações de gerenciamento de dados e de operações.

| Nome da métrica | DESCRIÇÃO |
| ------------------- | ----------------- |
| Solicitações de entrada (versão prévia) | O número de solicitações feitas ao serviço de Barramento de Serviço durante um período específico. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Solicitações bem-sucedidas (versão prévia)|O número de solicitações bem-sucedidas feitas ao serviço de Barramento de Serviço durante um período específico.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Erros do servidor (versão prévia)|O número de solicitações não processadas devido a um erro no serviço de Barramento de Serviço durante um período específico.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Erros do usuário (versão prévia)|O número de solicitações não processadas devido a erros do usuário durante um período específico.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Solicitações restritas (versão prévia)|O número de solicitações que foram restringidas porque o uso foi excedido.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="message-metrics"></a>Métricas de mensagens

| Nome da métrica | DESCRIÇÃO |
| ------------------- | ----------------- |
|Mensagens de entrada (versão prévia)|O número de eventos ou mensagens enviadas para o Barramento de Serviço durante um período específico.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Mensagens de saída (versão prévia)|O número de eventos ou mensagens recebidas do Barramento de Serviço durante um período específico.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="connection-metrics"></a>Métricas de conexão

| Nome da métrica | DESCRIÇÃO |
| ------------------- | ----------------- |
|ActiveConnections (versão prévia)|O número de conexões ativas em um namespace, bem como em uma entidade.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Conexões abertas (versão prévia)|O número de conexões abertas.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Conexões fechadas (versão prévia)|O número de conexões fechadas.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName |

## <a name="resource-usage-metrics"></a>Métricas de uso de recurso

| Nome da métrica | DESCRIÇÃO |
| ------------------- | ----------------- |
|Uso da CPU por namespace (versão prévia)|A porcentagem de uso da CPU do namespace.<br/><br/> Unidade: Porcentagem <br/> Tipo de agregação: Máximo <br/> Dimensão: EntityName|
|Uso do tamanho da memória por namespace (versão prévia)|A porcentagem de uso de memória do namespace.<br/><br/> Unidade: Porcentagem <br/> Tipo de agregação: Máximo <br/> Dimensão: EntityName|

## <a name="metrics-dimensions"></a>Dimensões das métricas

O Barramento de Serviço do Azure dá suporte às seguintes dimensões para métricas no Azure Monitor. Adicionar dimensões às métricas é opcional. Se você não adicionar dimensões, as métricas serão especificadas no nível de namespace. 

|Nome da dimensão|DESCRIÇÃO|
| ------------------- | ----------------- |
|EntityName| O Barramento de Serviço dá suporte a entidades de mensagens no namespace.|

## <a name="next-steps"></a>Próximas etapas

Consulte [Visão geral do Monitoramento do Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


