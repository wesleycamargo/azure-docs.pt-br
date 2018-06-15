---
title: Monitoramento do Lote do Azure | Microsoft Docs
description: Saiba mais sobre os serviços de monitoramento, métrica, logs de diagnóstico do Azure e outros recursos de monitoramento para Lote do Azure.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: danlep
ms.openlocfilehash: 29ac86ed5c744d37150b0f1b2db17f60306fe77e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
ms.locfileid: "31799797"
---
# <a name="monitor-batch-solutions"></a>Monitorar soluções do Lote

O Azure e o serviço do Lote fornecem uma variedade de serviços, ferramentas e APIs para monitorar suas soluções em Lote. Este artigo de visão geral ajuda-o a escolher uma abordagem de monitoramento que atenda às suas necessidades.

Para obter uma visão geral dos componentes e serviços do Azure disponíveis para monitorar recursos do Azure, consulte [Monitorar aplicativos e recursos do Azure](../monitoring-and-diagnostics/monitoring-overview.md).

## <a name="subscription-level-monitoring"></a>Monitoramento no nível da assinatura

No nível da assinatura, que inclui as contas do Lote, o [Log de atividades](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) coleta dados do evento operacional em [várias categorias](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md#categories-in-the-activity-log).

Para as contas do Lote e especificamente, o log de atividades coleta eventos relacionados à criação e exclusão de contas e ao gerenciamento de chaves.

Uma maneira de recuperar eventos do log de atividades é usar o Portal do Azure. Clique em **Todos os serviços** > **Log de Atividades**. Ou consulte os eventos usando as CLI do Azure, os cmdlets do PowerShell ou a API REST do Azure Monitor. Você também pode exportar o log de atividades ou configurar os [alertas do log de atividades](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md).

## <a name="batch-account-level-monitoring"></a>Monitoramento no nível de conta do Lote

Monitore cada conta do Lote usando os recursos do [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). O Azure Monitor coleta [métricas](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e, opcionalmente, [logs de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) para recursos com escopo no nível de uma conta do Lote, como pools, trabalhos e tarefas. Colete e consuma esses dados manualmente ou programaticamente para monitorar atividades na conta do Lote e para diagnosticar problemas. Para obter detalhes, consulte [Logs, alertas e métricas do Lote para avaliação e monitoramento de diagnóstico](batch-diagnostics.md).
 
> [!NOTE]
> As métricas estão disponíveis por padrão na conta do Lote sem configuração adicional e têm um histórico de 30 dias. Você deve habilitar o log de diagnóstico para uma conta do Lote e pode incorrer em custos adicionais para armazenar ou processar dados de log de diagnóstico. 

## <a name="batch-resource-monitoring"></a>Monitoramento de recursos do Lote

Nos aplicativo do Lote, use as APIs de Lote para monitorar ou consultar o status dos recursos, incluindo trabalhos, tarefas, nós e pools. Por exemplo: 

* [Contagem de tarefas por estado](batch-get-task-counts.md)
* [Criar consultas para listar os recursos do Lote de forma eficiente](batch-efficient-list-queries.md)
* [Criar dependências de tarefas](batch-task-dependencies.md)
* Use uma [tarefa do gerenciador de trabalhos](/rest/api/batchservice/job/add#jobmanagertask)
* Monitore o [estado da tarefa](/rest/api/batchservice/task/list#taskstate)
* Monitore o [estado do nó](/rest/api/batchservice/computenode/list#computenodestate)
* Monitore o [estado do pool](/rest/api/batchservice/pool/get#poolstate)
* Monitore o [uso do pool na conta](/rest/api/batchservice/pool/listusagemetrics)
* [Contagem de nós do pool por estado](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>Contadores de desempenho de VM e monitoramento de aplicativos

* O [Application Insights](../application-insights/app-insights-overview.md) é um serviço do Azure que você pode usar para monitorar programaticamente a disponibilidade, o desempenho e o uso dos trabalhos e tarefas do Lote. Obtenha facilmente contadores de desempenho de nós de computação (VMs) e informações personalizadas para tarefas fora das VMs. 

  Para um exemplo, consulte [Monitorar e depurar um aplicativo .NET do Lote com o Application Insights](monitor-application-insights.md) e o [exemplo de código](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights) que acompanha.

  > [!NOTE]
  > Custos adicionais poderão incorrer para usar o Application Insights. Consulte [opções de preços](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* O [BatchLabs](https://github.com/Azure/BatchLabs) é uma ferramenta de cliente autônoma, gratuita e com recursos avançados para ajudar a criar, depurar e monitorar aplicativos do Lote do Azure. Baixe um [pacote de instalação](https://azure.github.io/BatchLabs/) para Mac, Linux ou Windows. Opcionalmente, configure a solução do Lote para [exibir os dados do Application Insights](https://github.com/Azure/batch-insights) como os contadores de desempenho da VM no BatchLabs.


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre as [Ferramentas e APIs do Lote](batch-apis-tools.md) disponíveis para a criação de soluções do Lote.
* Saiba mais sobre [log de diagnósticos](batch-diagnostics.md) com Lote.