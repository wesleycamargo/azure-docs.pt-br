<properties
    pageTitle="Encaminhar status do trabalho e fluxos de trabalho da Automação para o Log Analytics (OMS) | Microsoft Azure"
    description="Este artigo demonstra como enviar status de trabalho e fluxos de trabalho de runbook para Gerenciamento e Log Analytics do Microsoft Operations Management Suite para fornecer informações adicionais."
    services="automation"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor="tysonn" />
<tags
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="08/08/2016"
    ms.author="magoedte" />

# Encaminhar status do trabalho e fluxos de trabalho de Automação para Log Analytics (OMS)

A Automação pode enviar o status do trabalho de runbook e fluxos de trabalho para seu espaço de trabalho de Log Analytics do Microsoft Operations Management Suite (OMS). Embora você possa exibir essas informações no portal do Azure ou com o PowerShell por status de trabalho individual ou todos os trabalhos para uma conta de Automação específica, qualquer item avançado para dar suporte às suas necessidades operacionais requer a criação de scripts personalizados do PowerShell. Agora, com o Log Analytics, você pode:

- Obter informações sobre os trabalhos de Automação
- Disparar um email ou um alerta com base no status do trabalho de runbook (por exemplo, com falha ou suspenso)
- Escrever consultas avançadas em seus fluxos de trabalho
- Correlacionar trabalhos em contas de Automação
- Visualizar o histórico de trabalho ao longo do tempo

## Pré-requisitos e considerações de implantação

Para começar a enviar seus logs de Automação para Log Analytics, você deve ter o seguinte:

1. Uma assinatura do OMS. Para obter informações adicionais, confira [Introdução ao Log Analytics](../log-analytics/log-analytics-get-started.md).

    >[AZURE.NOTE]O espaço de trabalho do OMS e a conta de Automação precisam estar na mesma assinatura do Azure para que essa configuração funcione corretamente.
  
2. Uma [conta do Armazenamento do Azure](../storage/storage-create-storage-account.md).
   
    >[AZURE.NOTE]A conta de Armazenamento *deve* estar na mesma região que a conta de Automação.
 
3. Azure PowerShell com versão 1.0.8 ou mais recente dos cmdlets do Insights Operacionais. Para saber mais sobre esta versão e como instalá-la, veja [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).
4. Diagnóstico do Azure e Log Analytics do PowerShell. Para obter mais informações sobre essa versão e como instalá-la, confira [Diagnóstico e Log Analytics do Azure](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/0.1).
5. Baixe o script do PowerShell **Enable-AzureDiagnostics.ps1** do [PowerShell Gallery](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/1.0/DisplayScript). Esse script configurará o seguinte:
 - Uma conta de armazenamento para conter o status de trabalho do runbook e dados de fluxo para uma conta de Automação que você especificar.
 - Habilite a coleta desses dados de sua conta de Automação para armazená-los em uma conta de armazenamento de Blobs do Azure no formato JSON.
 - Configure a coleta de dados de sua conta de Armazenamento de Blobs para Log Analytics do OMS.
 - Habilite a solução de Log Analytics de Automação em seu espaço de trabalho do OMS.

O script **Enable-AzureDiagnostics.ps1** requer os seguintes parâmetros durante a execução:

- *AutomationAccountName* - o nome de sua conta de Automação
- *LogAnalyticsWorkspaceName* - o nome de seu espaço de trabalho do OMS

Para localizar os valores para *AutomationAccountName*, no portal do Azure, selecione sua conta de Automação na folha **conta de Automação** e selecione **Todas as configurações**. Na folha **Todas as configurações**, em **Configurações de Conta**, selecione **Propriedades**. Na folha **Propriedades**, você pode observar esses valores.<br> ![Propriedades da Conta de Automação](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).


## Configurar a integração com o Log Analytics

1. Em seu computador, inicie o ** Windows PowerShell** na tela **Inicial**.
2. No shell de linha de comando do PowerShell, navegue até a pasta que contém o script baixado e execute-o alterando os valores dos parâmetros *- AutomationAccountName* e *- LogAnalyticsWorkspaceName*.

    >[AZURE.NOTE] Você deverá se autenticar com o Azure depois de executar o script. Você **deve** fazer logon com uma conta que seja membro da função Administradores de Assinatura e coadministrador da assinatura.
    
        .\Enable-AzureDiagnostics -AutomationAccountName <NameofAutomationAccount> `
        -LogAnalyticsWorkspaceName <NameofOMSWorkspace> `

3. Após executar esse script, você deve ver registros no Log Analytics cerca de 30 minutos após a gravação dos novos dados de diagnóstico no armazenamento. Se os registros não estiverem disponíveis depois dessa vez, confira a seção de solução de problemas em [Arquivos JSON no armazenamento de blobs](../log-analytics/log-analytics-azure-storage-json.md#troubleshooting-configuration-for-azure-diagnostics-written-to-blob-in-json).

### Verificar a configuração

Para confirmar se o script configurou sua conta de Automação e o espaço de trabalho do OMS com êxito, você pode executar as etapas a seguir no PowerShell. Antes de fazer isso, para localizar os valores do nome do espaço de trabalho do OMS e o nome do grupo de recursos, no portal do Azure, navegue até Log Analytics (OMS) e, na folha de Log Analytics (OMS), anote o valor para **Nome** e **Grupo de Recursos**.<br> ![Lista de espaço de trabalho de Log Analytics do OMS](media/automation-manage-send-joblogs-log-analytics/oms-la-workspaces-list-blade.png) Usaremos esses dois valores quando verificarmos a configuração em seu espaço de trabalho do OMS usando o cmdlet do PowerShell [Get-AzureRmOperationalInsightsStorageInsight](https://msdn.microsoft.com/library/mt603567.aspx).

1.  No portal do Azure, navegue até Contas de Armazenamento e procure a seguinte conta de armazenamento, que usa a convenção de nomenclatura *AutomationAccountNameomsstorage*. Após um trabalho de runbook ser concluído, logo depois você verá dois contêineres de Blob criados: **insights-logs-joblogs** e **insights-logs-jobstreams**.

2.  No PowerShell, execute o código do PowerShell a seguir, alterando os valores dos parâmetros **ResourceGroupName** e **WorkspaceName** que você copiou ou anotou anteriormente.

    Login-AzureRmAccount Get-AzureRmSubscription -SubscriptionName 'SubscriptionName' | Set-AzureRmContext Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName "OMSResourceGroupName" ` -Workspace "OMSWorkspaceName"

    Isso retornará a percepção de armazenamento para o espaço de trabalho do OMS especificado. Queremos confirmar se a percepção de armazenamento para a conta de Automação que especificamos anteriormente existe e se o objeto **Estado** mostra o valor **OK**.<br> ![Resultados do cmdlet Get-AzureRmOperationalInsightsStorageInsights](media/automation-manage-send-joblogs-log-analytics/automation-posh-getstorageinsights-results.png).

## Exibir Logs de Automação no Log Analytics 

.Agora que você começou a enviar logs de trabalho de Automação para Log Analytics, vamos ver o que você pode fazer com esses logs no OMS.

### Enviar um email quando um trabalho de runbook falhar ou for suspenso 

Uma das principais solicitações de nossos clientes é a capacidade de enviar um email ou uma mensagem de texto quando algo dá errado em um trabalho de runbook.

Para criar uma regra de alerta, você começa criando uma pesquisa de log para os registros de trabalhos de runbook que devem invocar o alerta. O botão **alerta** estará disponível para que você possa criar e configurar a regra de alerta.

1.	Na página de Visão Geral do OMS, clique em **Pesquisa de Log**.
2.	Crie uma consulta de pesquisa de log para o alerta digitando o seguinte no campo de consulta: `Category=JobLogs (ResultType=Failed || ResultType=Suspended)`. Você também pode agrupar pelo RunbookName usando: `Category=JobLogs (ResultType=Failed || ResultType=Suspended) | measure Count() by RunbookName_s`.
  
    Se você tiver configurado logs de mais de uma conta de Automação ou assinatura para o espaço de trabalho, também poderá se interessar por agrupar os alertas por assinatura ou conta de Automação. O nome da conta de Automação pode ser derivado do campo de Recurso na pesquisa de JobLogs.

3.	Clique em **Alerta** na parte superior da página para abrir a tela **Adicionar Regra de Alerta**. Para obter mais detalhes sobre as opções para configurar o alerta, confira [Alertas no Log Analytics](../log-analytics/log-analytics-alerts.md#creating-an-alert-rule).

### Localizar todos os trabalhos que foram concluídos com erros 

Além de alertas com base em falhas, você provavelmente deseja saber quando um trabalho de runbook tem um erro não fatal (o PowerShell produz um fluxo de erro, mas erros que não causam a finalização não fazem com que seu trabalho seja suspenso ou tenha uma falha).

1. No portal do OMS, clique em **Pesquisa de Log**.
2. No campo de consulta, digite `Category=JobStreams StreamType_s=Error | measure count() by JobId_g` e clique em **Pesquisar**.


### Exibir fluxos de trabalho para um trabalho  

Ao depurar um trabalho, talvez você também queira examinar os fluxos de trabalho. A consulta abaixo mostra todos os fluxos para um único trabalho com GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`Category=JobStreams JobId_g="2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort TimeGenerated | select ResultDescription`

### Exibir o status do histórico de trabalho 

Finalmente, talvez você queira visualizar o histórico de trabalho ao longo do tempo. Você pode usar essa consulta para pesquisar o status dos trabalhos ao longo do tempo.

`Category=JobLogs NOT(ResultType="started") | measure Count() by ResultType interval 1day` <br> ![Gráfico de status de trabalho histórico do OMS](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## Resumo

Enviando seus dados de status e fluxo de trabalho de Automação para Log Analytics, você pode obter uma noção melhor do status de seus trabalhos de Automação, configurar alertas para notificá-lo quando houver um problema e painéis personalizados usando consultas avançadas para visualizar os resultados de runbook, o status do trabalho de runbook e outros indicadores-chave ou métricas relacionados. Isso ajudará a fornecer maior visibilidade operacional e os incidentes serão tratados mais rapidamente.


## Próximas etapas

- Para saber mais sobre como construir consultas de pesquisa diferentes e examinar os logs de trabalho de Automação com o Log Analytics, confira [Efetuar pesquisas no Log Analytics](../log-analytics/log-analytics-log-searches.md)
- Para entender como criar e recuperar mensagens de erro e de saída de runbooks, confira [Saída e mensagens de Runbook](automation-runbook-output-and-messages.md)
- Para saber mais sobre a execução de runbooks, como monitorar trabalhos de runbook e outros detalhes técnicos, confira [Acompanhar um trabalho de runbook](automation-runbook-execution.md)
- Para saber mais sobre o Log Analytics do OMS e fontes de coleta de dados, confira [Coletar dados do Armazenamento do Azure na visão geral do Log Analytics](../log-analytics/log-analytics-azure-storage.md)

<!---HONumber=AcomDC_0810_2016-->