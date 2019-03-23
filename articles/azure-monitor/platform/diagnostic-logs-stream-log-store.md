---
title: Transmitir logs de diagnóstico do Azure para o Log Analytics
description: Saiba como transmitir logs de diagnóstico do Azure para um workspace do Log Analytics.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: bd760fca20a602127e7d33913547dcb2c6bc95f6
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351535"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>Transmitir logs de diagnóstico do Azure para o Log Analytics

**[Os Logs de diagnósticos do Azure](diagnostic-logs-overview.md)** podem ser transmitidos quase em tempo real para o Azure Log Analytics usando o portal, cmdlets do PowerShell ou a CLI do Azure.

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>O que você pode fazer com os logs de diagnóstico no Log Analytics

O Azure Log Analytics é uma ferramenta de análise e pesquisa de logs flexível que permite que você obtenha informações sobre os dados brutos de log gerados de recursos do Azure. Algumas funcionalidades incluem:

* **Pesquisa de logs** -gravar de consultas avançadas sobre os dados de log, correlacionar logs de várias origens e até mesmo gerar gráficos que podem ser fixados no painel do Azure.
* **Alerta** -detectar quando um ou mais eventos corresponderem a uma consulta específica e ser notificados com uma chamada de webhook ou email.
* **Soluções** -usar exibições predefinidas e painéis que fornecem ideias imediatas sobre seus dados de log.
* **Análise avançada** – aplicar o aprendizado de máquina e algoritmos de correspondência de padrão para identificar possíveis problemas revelados por seus logs.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>Habilitar o streaming de logs de diagnóstico para o Log Analytics

Você pode habilitar programaticamente o streaming de logs de diagnóstico por meio do portal ou usando a [API REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). De qualquer forma, você cria uma configuração de diagnóstico na qual especifica um workspace do Log Analytics e as categorias de log e as métricas que deseja enviar para esse workspace. Uma **categoria de log** de diagnóstico é um tipo de log que um recurso pode fornecer.

O workspace do Log Analytics não precisa estar na mesma assinatura que o recurso que emite os logs, contanto que o usuário que define a configuração tenha acesso RBAC apropriado a ambas as assinaturas.

> [!NOTE]
> Atualmente, não há suporte para o envio da métrica multidimensional por meio das configurações de diagnóstico. As métricas com dimensões são exportadas como métricas dimensionais simples, agregadas nos valores da dimensão.
>
> *Por exemplo*: A métrica “Mensagens de Entrada” em um Hub de Eventos pode ser explorada e mapeada por nível da fila. No entanto, quando exportada por meio das configurações de diagnóstico, a métrica será representada como todas as mensagens de entrada em todas as filas no Hub de Eventos.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Transmitir logs de diagnóstico usando o portal
1. No portal, navegue até o Azure Monitor e clique em **Configurações de Diagnóstico**

    ![Seção de monitoramento do Azure Monitor](media/diagnostic-logs-stream-log-store/diagnostic-settings-blade.png)

2. Se desejar filtrar a lista por tipo de recurso ou grupo de recursos, clique no recurso para o qual você deseja definir uma configuração de diagnóstica.

3. Se nenhuma configuração existir no recurso que você selecionou, será solicitada a criação de uma configuração. Clique em “Ativar diagnóstico”.

   ![Adicionar configuração de diagnóstico - nenhuma configuração existente](media/diagnostic-logs-stream-log-store/diagnostic-settings-none.png)

   Se houver configurações existentes no recurso, você verá uma lista de configurações já definidas nesse recurso. Clique em "Adicionar configuração de diagnóstico".

   ![Adicionar configuração de diagnóstico - configurações existentes](media/diagnostic-logs-stream-log-store/diagnostic-settings-multiple.png)

3. Dê um nome à sua configuração e marque a caixa **Enviar para o Log Analytics**, em seguida, selecione um workspace do Log Analytics.

   ![Adicionar configuração de diagnóstico - configurações existentes](media/diagnostic-logs-stream-log-store/diagnostic-settings-configure.png)

4. Clique em **Salvar**.

Após alguns instantes, a nova configuração aparece na lista de configurações para esse recurso e os logs de diagnóstico são transmitidos para esse workspace assim que os novos dados de evento são gerados. Observe que pode haver até quinze minutos entre quando um evento é emitido e quando ele é exibido no Log Analytics.

### <a name="via-powershell-cmdlets"></a>Via Cmdlets do PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para habilitar o streaming por meio de [Cmdlets do Azure PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md), use o cmdlet `Set-AzDiagnosticSetting` com estes parâmetros:

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Observe que a propriedade workspaceID usa a ID de recurso completa do Azure do workspace, não a ID/chave do workspace mostrada no portal do Log Analytics.

### <a name="via-azure-cli"></a>Via CLI do Azure

Para habilitar o streaming por meio da [CLI do Azure](../../azure-monitor/platform/cli-samples.md), você pode usar o comando [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

É possível adicionar categorias adicionais ao log de diagnóstico, adicionando dicionários à matriz JSON transmitida como o parâmetro `--logs`.

O argumento `--resource-group` somente será necessário se `--workspace` não for uma ID de objeto.

## <a name="how-do-i-query-the-data-in-log-analytics"></a>Como faço para consultar os dados do Log Analytics?

Na folha de Pesquisa de Logs no portal ou na experiência do Advanced Analytics como parte do Log Analytics, você pode consultar os logs de diagnóstico como parte da solução de Gerenciamento de Log na tabela do AzureDiagnostics. Também há [várias soluções para recursos do Azure](../../azure-monitor/insights/solutions.md) que podem ser instaladas para obter informações imediatas sobre os dados de log que você está enviando para o Log Analytics.

### <a name="known-limitation-column-limit-in-azurediagnostics"></a>Limitação conhecida: limite de coluna em AzureDiagnostics
Porque muitos recursos enviar todos os tipos de dados são enviados para a mesma tabela (_AzureDiagnostics_), o esquema da tabela é o conjunto de superusuários dos esquemas de todos os tipos de dados diferentes que estão sendo coletados. Por exemplo, se você tiver criado as configurações de diagnóstico para a coleção dos seguintes tipos de dados, todos sendo enviados para o mesmo espaço de trabalho:
- Registros de recurso 1 (com um esquema consiste em colunas A, B e C) de auditoria  
- Logs de erros de 2 de recurso (com um esquema consiste em colunas, D, E e F)  
- Logs de fluxo de dados de 3 de recurso (com um esquema consiste em colunas G, H e eu)  
 
A tabela do AzureDiagnostics será semelhante ao seguinte, com alguns dados de exemplo:  
 
| ResourceProvider | Categoria | O  | b | C | D | E | F | G | H | I |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
| Microsoft.Resource2 | ErrorLogs | | | | q1 | W1 | e1 |
| Microsoft.Resource3 | DataFlowLogs | | | | | | | j1 | k1 | l1|
| Microsoft.Resource2 | ErrorLogs | | | | q2 | w2 | e2 |
| Microsoft.Resource3 | DataFlowLogs | | | | | | | j3 | k3 | l3|
| Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
| ... |
 
Há um limite explícito de qualquer tabela de Log do Azure determinada não precisar mais de 500 colunas. Uma vez atingido, quaisquer linhas que contêm dados com qualquer outra coluna fora os primeiros 500 serão removidas em tempo de ingestão. A tabela do AzureDiagnostics é especialmente suscetíveis ser afetado esse limite. Isso geralmente acontece porque uma grande variedade de fontes de dados são enviados para o mesmo espaço de trabalho, ou várias fontes de dados bastante detalhado que estão sendo enviados para o mesmo espaço de trabalho. 
 
#### <a name="azure-data-factory"></a>Fábrica de dados do Azure  
O Azure Data Factory, devido a um conjunto muito detalhado dos logs, é um recurso que é conhecido por ser especialmente afetados por esse limite. Especificamente:  
- *Parâmetros do usuário definidos em relação a qualquer atividade em seu pipeline*: haverá uma nova coluna criada para cada parâmetro de usuário nomeado exclusivamente em relação a qualquer atividade. 
- *Atividade de entradas e saídas*: elas variam de atividade para atividade e gerar uma grande quantidade de colunas devido à sua natureza detalhada. 
 
Como com as mais amplas propostas de solução alternativa abaixo, é recomendável isolar os logs do ADF em seu próprio espaço de trabalho para minimizar a chance desses logs que afetam outros tipos de log que estão sendo coletados em seus espaços de trabalho. Esperamos ter coletado os logs do Azure Data Factory pela abril de 2019.
 
#### <a name="workarounds"></a>Soluções alternativas
Curto prazo, até que o limite de 500 coluna será redefinido, é recomendável separar os tipos de dados detalhados em espaços de trabalho separados para reduzir a possibilidade de atingir o limite.
 
Longo prazo, o diagnóstico do Azure mudará para fora de um esquema unificado, esparso em tabelas individuais por cada tipo de dados; emparelhado com o suporte para tipos dinâmicos, isso melhorará muito a usabilidade dos dados de entrada dos Logs do Azure por meio do mecanismo de diagnóstico do Azure. Você já pode ver isso para selecionar tipos de recursos do Azure, por exemplo [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) ou [Intune](https://docs.microsoft.com/intune/review-logs-using-azure-monitor) logs. Consulte para ler notícias sobre novos tipos de recursos no Azure que dão suporte a esses logs estruturados na [atualizações do Azure](https://azure.microsoft.com/updates/) blog!


## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre os Logs de Diagnóstico do Azure](diagnostic-logs-overview.md)

