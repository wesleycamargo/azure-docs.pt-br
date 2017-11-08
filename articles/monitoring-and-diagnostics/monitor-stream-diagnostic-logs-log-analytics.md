---
title: "Transmitir logs de diagnóstico do Azure para o Log Analytics | Microsoft Docs"
description: "Saiba como transmitir logs de diagnóstico do Azure para um espaço de trabalho do Log Analytics."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: johnkem
ms.openlocfilehash: 9440bd7f872914887c1f6e50f08a3c273536fcf8
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2017
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>Transmitir logs de diagnóstico do Azure para o Log Analytics
**[Os Logs de diagnósticos do Azure](monitoring-overview-of-diagnostic-logs.md)** podem ser transmitidos quase em tempo real para o Azure Log Analytics usando o portal, cmdlets do PowerShell ou a CLI do Azure.

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>O que você pode fazer com os logs de diagnóstico no Log Analytics

O Azure Log Analytics é uma ferramenta de análise e pesquisa de logs flexível que permite que você obtenha informações sobre os dados brutos de log gerados de recursos do Azure. Algumas funcionalidades incluem:

* **Pesquisa de logs** -gravar de consultas avançadas sobre os dados de log, correlacionar logs de várias origens e até mesmo gerar gráficos que podem ser fixados no painel do Azure.
* **Alerta** -detectar quando um ou mais eventos corresponderem a uma consulta específica e ser notificados com uma chamada de webhook ou email.
* **Soluções** -usar exibições predefinidas e painéis que fornecem ideias imediatas sobre seus dados de log.
* **Análise avançada** – aplicar o aprendizado de máquina e algoritmos de correspondência de padrão para identificar possíveis problemas revelados por seus logs.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>Habilitar o streaming de logs de diagnóstico para o Log Analytics
Você pode habilitar programaticamente o streaming de logs de diagnóstico por meio do portal ou usando a [API REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/servicediagnosticsettings). De qualquer forma, você cria uma configuração de diagnóstico na qual especifica um espaço de trabalho do Log Analytics e as categorias de log e as métricas que deseja enviar para esse espaço de trabalho. Uma **categoria de log** de diagnóstico é um tipo de log que um recurso pode fornecer.

O espaço de trabalho do Log Analytics não precisa estar na mesma assinatura que o recurso que emite os logs, contanto que o usuário que define a configuração tenha acesso RBAC apropriado a ambas as assinaturas.

## <a name="stream-diagnostic-logs-using-the-portal"></a>Transmitir logs de diagnóstico usando o portal
1. No portal, navegue até o Azure Monitor e clique em **Configurações de Diagnóstico**

    ![Seção de monitoramento do Azure Monitor](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-blade.png)

2. Se desejar filtrar a lista por tipo de recurso ou grupo de recursos, clique no recurso para o qual você deseja definir uma configuração de diagnóstica.

3. Se nenhuma configuração existir no recurso que você selecionou, será solicitada a criação de uma configuração. Clique em “Ativar diagnóstico”.

   ![Adicionar configuração de diagnóstico - nenhuma configuração existente](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-none.png)

   Se houver configurações existentes no recurso, você verá uma lista de configurações já definidas nesse recurso. Clique em "Adicionar configuração de diagnóstico".

   ![Adicionar configuração de diagnóstico - configurações existentes](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-multiple.png)

3. Dê um nome à sua configuração e marque a caixa **Enviar para o Log Analytics**, em seguida, selecione um espaço de trabalho do Log Analytics.
   
   ![Adicionar configuração de diagnóstico - configurações existentes](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-configure.png)

4. Clique em **Salvar**.

Após alguns instantes, a nova configuração aparece na lista de configurações para esse recurso e os logs de diagnóstico são transmitidos para esse espaço de trabalho assim que os novos dados de evento são gerados. Observe que pode haver até quinze minutos entre quando um evento é emitido e quando ele é exibido no Log Analytics.

### <a name="via-powershell-cmdlets"></a>Via Cmdlets do PowerShell
Para habilitar o streaming por meio de [Cmdlets do Azure PowerShell](insights-powershell-samples.md), use o cmdlet `Set-AzureRmDiagnosticSetting` com estes parâmetros:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Observe que a propriedade workspaceID usa a ID de recurso completa do Azure do espaço de trabalho, não a ID/chave do espaço de trabalho mostrada no portal do Log Analytics.

### <a name="via-azure-cli"></a>Via CLI do Azure
Para habilitar o streaming por meio da [CLI do Azure](insights-cli-samples.md), use o comando `insights diagnostic set` da seguinte forma:

```azurecli
azure insights diagnostic set --resourceId <resourceID> --workspaceId <workspace resource ID> --categories <list of categories> --enabled true
```

Observe que a propriedade workspaceId usa a ID de recurso completa do Azure do espaço de trabalho, não a ID/chave do espaço de trabalho mostrada no portal do Log Analytics.

## <a name="how-do-i-query-the-data-in-log-analytics"></a>Como faço para consultar os dados do Log Analytics?

Na folha de Pesquisa de Logs no portal ou na experiência do Advanced Analytics como parte do Log Analytics, você pode consultar os logs de diagnóstico como parte da solução de Gerenciamento de Log na tabela do AzureDiagnostics. Também há [várias soluções para recursos do Azure](../log-analytics/log-analytics-add-solutions.md) que podem ser instaladas para obter informações imediatas sobre os dados de log que você está enviando para o Log Analytics.


## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre os Logs de Diagnóstico do Azure](monitoring-overview-of-diagnostic-logs.md)
