---
title: Encaminhar dados de relatório da Configuração do Estado de Automação do Azure para o Log Analytics
description: Este artigo demonstra como enviar dados de relatórios para a Configuração do Estado Desejado (DSC) da Configuração do Estado de Automação do Azure para o Log Analytics para entregar insight e gerenciamento adicional.
services: automation
ms.service: automation
ms.component: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: aa543ad119716b25b7f3ab00d49efb9fe6fb6c3f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51244229"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-log-analytics"></a>Encaminhar dados de relatório da Configuração do Estado de Automação do Azure para o Log Analytics

A Configuração de Estado de Automação do Azure pode enviar dados de status do nó de Configuração de Estado Desejada (DSC) para seu workspace do Log Analytics. O status de conformidade é visível no portal do Microsoft Azure, ou com o PowerShell, para nós e recursos individuais de DSC em configurações de nó. Com o Log Analytics, você pode:

- Obter informações de conformidade para nós gerenciados e recursos individuais
- Disparar um email ou alerta com base no status de conformidade
- Escrever consultas avançadas em seus nós gerenciados
- Correlacionar o status de conformidade em contas de Automação
- Visualizar o histórico de conformidade do nó ao longo do tempo

## <a name="prerequisites"></a>Pré-requisitos

Para começar a enviar seus relatórios de Configuração do Estado de Automação para Log Analytics, você precisará do seguinte:

- Versão de novembro 2016 ou posterior do [Azure PowerShell](/powershell/azure/overview) ( versão 2.3.0).
- Uma conta de Automação do Azure. Para saber mais, veja [Introdução à Automação do Azure](automation-offering-get-started.md)
- Um workspace de Log Analytics com uma oferta de serviço **Automação e Controle**. Para saber mais, confira [Introdução ao Log Analytics](../log-analytics/log-analytics-get-started.md).
- No mínimo, um nó de Configuração do Estado de Automação do Azure. Para saber mais, veja [Máquinas de integração para o gerenciamento pela Configuração do Estado de Automação do Azure](automation-dsc-onboarding.md)

## <a name="set-up-integration-with-log-analytics"></a>Configurar a integração com o Log Analytics

Para começar a importar dados do DSC de Automação do Azure para Log Analytics, conclua as seguintes etapas:

1. Faça logon em sua conta do Azure no PowerShell. Veja [Fazer logon com o Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.0.0)
1. Obtenha o _ResourceId_ de sua conta de automação executando o seguinte comando do PowerShell: (se você tiver mais de uma conta de automação, escolha o _ResourceID_ para a que deseja configurar).

  ```powershell
  # Find the ResourceId for the Automation Account
  Get-AzureRmResource -ResourceType 'Microsoft.Automation/automationAccounts'
  ```

1. Obtenha o _ResourceId_ de seu workspace do Log Analytics executando o seguinte comando do PowerShell: (se você tiver mais de um workspace, escolha o _ResourceID_ para o que deseja configurar).

  ```powershell
  # Find the ResourceId for the Log Analytics workspace
  Get-AzureRmResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
  ```

1. Execute o seguinte comando do PowerShell, substituindo `<AutomationResourceId>` e `<WorkspaceResourceId>` pelos valores de _ResourceId_ de cada uma das etapas anteriores:

  ```powershell
  Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Categories 'DscNodeStatus'
  ```

Se você quiser parar de importar dados da Configuração do Estado de Automação do Azure para o Log Analytics, execute o seguinte comando do PowerShell:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Categories 'DscNodeStatus'
```

## <a name="view-the-state-configuration-logs"></a>Exibir os logs de configuração de estado

Depois de configurar a integração com o Log Analytics para os seus dados de DSC de Automação, um botão **Pesquisa de log** aparecerá na folha **Nós da Configuração do Estado**  de sua conta de automação. Clique no botão **Pesquisa de Log** para exibir os logs para dados do nó DSC.

![Botão Pesquisar Log](media/automation-dsc-diagnostics/log-search-button.png)

A folha **Pesquisa de Log** é aberta e você verá uma operação **DscNodeStatusData** para cada nó da Configuração do Estado e uma operação **DscResourceStatusData** para cada [Recurso DSC](/powershell/dsc/resources) chamado na configuração do nó aplicada a esse nó.

A operação **DscResourceStatusData** contém informações de erro para todos os recursos DSC que falharam.

Clique em cada operação na lista para ver os dados para essa operação.

Você também pode exibir os logs [pesquisando no Log Analytics. Veja [Localizar dados usando pesquisas de logs](../log-analytics/log-analytics-log-searches.md).
Digite a consulta a seguir para localizar os logs da Configuração do Estado: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus'`

Você também pode restringir a consulta pelo nome da operação. Por exemplo: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus' OperationName='DscNodeStatusData'`

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Enviar um email quando uma verificação de conformidade da Configuração do Estado falhar

Uma das principais solicitações de nossos clientes é a capacidade de enviar um email ou uma mensagem de texto quando algo dá errado com uma configuração de DSC.

Para criar uma regra de alerta, você começa criando uma pesquisa de log para os registros do relatório da Configuração do Estado que devem invocar o alerta. Clique no botão **+ Nova Regra de Alerta** para criar e configurar a regra de alerta.

1. Na página Visão geral do Log Analytics, clique em **Pesquisa de Logs**.
1. Crie uma consulta de pesquisa de log para o alerta digitando a seguinte pesquisa no campo de consulta: `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Se você tiver configurado logs de mais de uma Conta de automação ou assinatura para o workspace, também poderá agrupar os alertas por assinatura e por conta de Automação.  
   O nome da conta de Automação pode ser derivado do campo de Recurso na pesquisa de DscNodeStatusData.  
1. Para abrir a tela **Criar regra**, clique em **+ Nova regra de alerta** na parte superior da página. Para obter mais informações sobre as opções para configurar o alerta, consulte [ Criar um rulelert de alerta ](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Encontrar recursos DSC com falha em todos os nós

Uma vantagem de usar o Log Analytics é que você pode pesquisar falhas de verificações em nós.
Para localizar todas as instâncias de recursos DSC que falharam.

1. Na página Visão geral do Log Analytics, clique em **Pesquisa de Logs**.
1. Crie uma consulta de pesquisa de log para o alerta digitando a seguinte pesquisa no campo de consulta: `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Exibir status do nó DSC histórico

Finalmente, talvez você queira visualizar o histórico de status do nó DSC ao longo do tempo.  
Você pode usar essa consulta para pesquisar o status do nó DSC ao longo do tempo.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  

Isso exibirá um gráfico do status do nó ao longo do tempo.

## <a name="log-analytics-records"></a>Registros do Log Analytics

O diagnóstico da Automação do Azure cria duas categorias de registros no Log Analytics.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Propriedade | DESCRIÇÃO |
| --- | --- |
| TimeGenerated |Data e hora quando a verificação de conformidade foi executada. |
| OperationName |DscNodeStatusData |
| ResultType |Se o nó está em conformidade. |
| NodeName_s |O nome do nó gerenciado. |
| NodeComplianceStatus_s |Se o nó está em conformidade. |
| DscReportStatus |Se a verificação de conformidade foi executada com êxito. |
| ConfigurationMode | Como a configuração é aplicada ao nó. Os valores possíveis são __"ApplyOnly"__,__"ApplyandMonitior"__ e __"ApplyandAutoCorrect"__. <ul><li>__ApplyOnly__: o DSC aplica-se à configuração e não faz nada além disso, a menos que uma nova configuração seja enviada para o nó de destino ou quando é efetuado pull de uma nova configuração de um servidor. Depois da aplicação inicial de uma nova configuração, o DSC não procura descompasso de um estado previamente configurado. O DSC tenta aplicar a configuração até que seja bem-sucedida antes __ApplyOnly__ entrar em vigor. </li><li> __ApplyAndMonitor__: é o valor padrão. O LCM aplica as novas configurações. Depois da aplicação inicial de uma nova configuração, se o nó de destino tiver um descompasso do estado desejado, o DSC relatará a discrepância nos logs. O DSC tenta aplicar a configuração até que seja bem-sucedida antes __ApplyAndMonitor__ entrar em vigor.</li><li>__ApplyAndAutoCorrect__: o DSC aplica as novas configurações. Depois da aplicação inicial de uma nova configuração, se o nó de destino tiver um descompasso do estado desejado, o DSC relatará a discrepância nos logs e, em seguida, reaplicará a configuração atual.</li></ul> |
| HostName_s | O nome do nó gerenciado. |
| IPAddress | O endereço IPv4 do nó gerenciado. |
| Categoria | DscNodeStatus |
| Recurso | O nome da conta de Automação do Azure. |
| Tenant_g | GUID que identifica o locatário para o Chamador. |
| NodeId_g |A GUID que identifica o nó gerenciado. |
| DscReportId_g |A GUID que identifica o relatório. |
| LastSeenTime_t |Data e hora em que o relatório foi exibido pela última vez. |
| ReportStartTime_t |Data e hora em que o relatório foi iniciado. |
| ReportEndTime_t |Data e hora em que o relatório foi concluído. |
| NumberOfResources_d |O número de recursos de DSC chamado na configuração aplicada ao nó. |
| SourceSystem | Como o Log Analytics coletou os dados. Sempre *Azure* para o Diagnóstico do Azure. |
| ResourceId |Especifica a conta de Automação do Azure. |
| ResultDescription | A descrição para esta operação. |
| SubscriptionId | O GUID (ID de assinatura do Azure) para a Conta de automação. |
| ResourceGroup | Nome do grupo de recursos para a Conta de automação. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |O GUID que é a Id de correlação do relatório de conformidade. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Propriedade | DESCRIÇÃO |
| --- | --- |
| TimeGenerated |Data e hora quando a verificação de conformidade foi executada. |
| OperationName |DscResourceStatusData|
| ResultType |Se o recurso está em conformidade. |
| NodeName_s |O nome do nó gerenciado. |
| Categoria | DscNodeStatus |
| Recurso | O nome da conta de Automação do Azure. |
| Tenant_g | GUID que identifica o locatário para o Chamador. |
| NodeId_g |A GUID que identifica o nó gerenciado. |
| DscReportId_g |A GUID que identifica o relatório. |
| DscResourceId_s |O nome da instância do recurso de DSC. |
| DscResourceName_s |O nome do recurso de DSC. |
| DscResourceStatus_s |Se o recurso de DSC está em conformidade. |
| DscModuleName_s |O nome do módulo do PowerShell que contém o recurso de DSC. |
| DscModuleVersion_s |A versão do módulo do PowerShell que contém o recurso de DSC. |
| DscConfigurationName_s |O nome da configuração aplicada ao nó. |
| ErrorCode_s | O código de erro se o recurso tiver falhado. |
| ErrorMessage_s |A mensagem de erro se o recurso tiver falhado. |
| DscResourceDuration_d |O tempo, em segundos, em que o recurso de DSC foi executado. |
| SourceSystem | Como o Log Analytics coletou os dados. Sempre *Azure* para o Diagnóstico do Azure. |
| ResourceId |Especifica a conta de Automação do Azure. |
| ResultDescription | A descrição para esta operação. |
| SubscriptionId | O GUID (ID de assinatura do Azure) para a Conta de automação. |
| ResourceGroup | Nome do grupo de recursos para a Conta de automação. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |O GUID que é a Id de correlação do relatório de conformidade. |

## <a name="summary"></a>Resumo

Ao enviar seus dados da Configuração do Estado de Automação para o Log Analytics, você poderá ter mais informações sobre o status de seus nós do Estado de Configuração do Estado:

- Configurando alertas para notificá-lo quando houver um problema
- Usando exibições personalizadas e consultas de pesquisa para visualizar os resultados de runbook, o status do trabalho de runbook e outros principais indicadores ou métricas relacionadas.  

O Log Analytics oferece maior visibilidade operacional para os dados da Configuração do Estadov de Automação e pode ajudar a tratar de incidentes mais rapidamente.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [Configuração de Estado da Automação do Azure](automation-dsc-overview.md)
- Para começar, consulte [Introdução à Configuração de Estado da Automação do Azure](automation-dsc-getting-started.md)
- Para saber como compilar configurações de DSC para que possam ser atribuídas a nós de destino, consulte [Compilar configurações na Configuração de Estado da Automação do Azure](automation-dsc-compile.md)
- Para referência de cmdlet do PowerShell, consulte [Cmdlets da Configuração de Estado da Automação do Azure](/powershell/module/azurerm.automation/#automation)
- Para obter informações sobre preços, consulte [Preço da Configuração de Estado da Automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de uso da Configuração de Estado da Automação do Azure em um pipeline de implantação contínua, consulte [Implantação contínua usando Configuração de Estado da Automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
- Para saber mais sobre como construir consultas de pesquisa diferentes e examinar os logs da Configuração do Estado de Automação com o Log Analytics, confira [Efetuar pesquisas no Log Analytics](../log-analytics/log-analytics-log-searches.md)
- Para saber mais sobre o Log Analytics e fontes de coleta de dados, confira [Coletar dados do Armazenamento do Azure na visão geral do Log Analytics](../log-analytics/log-analytics-azure-storage.md)