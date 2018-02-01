---
title: "Responder aos alertas do Azure com um runbook de automação | Microsoft Docs"
description: "Saiba como disparar um runbook para executar quando os alertas do Azure são criados."
services: automation
keywords: 
author: georgewallace
ms.author: gwallace
ms.date: 01/11/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 486a3c80c9d6c19ca0a7ba7d4e3cdcde927053b9
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="respond-to-azure-alerts-with-an-automation-runbook"></a>Responder aos alertas do Azure com um runbook de automação

O [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md?toc=%2fazure%2fautomation%2ftoc.json) fornece logs e métricas de nível básico para a maioria dos serviços no Microsoft Azure. Os runbooks de automação do Azure podem ser chamados utilizando [grupos de ações](../monitoring-and-diagnostics/monitoring-action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) ou a partir de alertas clássicos para automatizar tarefas com base nos alertas. Este artigo mostra como configurar e executar um runbook com base nesses alertas.

## <a name="alert-types"></a>Tipos de alerta

Runbooks são ações com suporte em todos os três tipos de alertas. Quando um alerta chama o runbook, a chamada real é uma solicitação HTTP POST para o webhook. O corpo da solicitação POST contém um objeto com o formato JSON que contém propriedades úteis relacionadas ao alerta. A tabela a seguir contém links para o esquema de conteúdo para cada tipo de alerta:

|Alerta  |DESCRIÇÃO|Esquema de conteúdo  |
|---------|---------|---------|
|[Alerta de métrica clássico](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |Receba uma notificação quando qualquer métrica no nível da plataforma atender a uma condição específica (por exemplo, a % de CPU em uma VM for maior do que 90 nos últimos cinco minutos).| [Esquema de conteúdo](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[Alerta do log de atividades](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Receba uma notificação quando qualquer evento novo no Log de Atividades do Azure corresponder a condições específicas (por exemplo, quando uma operação "Excluir VM" ocorrer em myProductionResourceGroup ou quando um novo evento de Integridade do Serviço com status "Ativo" for exibido).| [Esquema de conteúdo](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)        |
|[Alertas de métrica quase em tempo real](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Receba uma notificação mais rapidamente do que os alertas de métrica quando uma ou mais métricas no nível da plataforma atender às condições especificadas (por exemplo, o % de CPU em uma VM é maior que 90 e a Entrada de Rede é maior que 500 MB nos últimos 5 minutos).| [Esquema de conteúdo](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

Como os dados fornecidos por cada alerta são diferentes, cada alerta precisa ser tratado de forma diferente. Na próxima seção, você aprenderá a criar um runbook para lidar com esses diferentes tipos de alertas.

## <a name="create-a-runbook-to-handle-alerts"></a>Criar um runbook para tratar alertas

Para usar a automação com alertas, será necessário um runbook que tenha lógica para gerenciar o conteúdo JSON de alerta que é passado ao runbook. O seguinte exemplo de runbook deverá ser chamado a partir de um alerta do Azure. Conforme descrito na seção anterior, cada tipo de tipo de alerta possui um esquema diferente. O script considera os dados do webhook no parâmetro de entrada do runbook `WebhookData` de um alerta e avalia o conteúdo JSON para determinar qual tipo de alerta foi usado. O exemplo a seguir seria usado em um alerta de uma VM. Ele recupera os dados da VM do conteúdo e utiliza essa informação para parar a VM. A conexão deve ser configurada na conta de Automação onde o runbook é executado.

O runbook usa o **AzureRunAsConnection** [executado como conta](automation-create-runas-account.md) para se autenticar com o Azure para executar a ação de gerenciamento na VM.

O seguinte script do PowerShell pode ser alterado para uso com muitos recursos diferentes.

Considere o exemplo a seguir e crie um runbook chamado **Stop-AzureVmInResponsetoVMAlert** com o exemplo do PowerShell.

1. Abra sua conta da Automação.
1. Clique em **Runbooks** em **AUTOMAÇÃO DE PROCESSO**. A lista de runbooks é exibida.
1. Clique np botão **Adicionar um runbook** encontrado na parte superior da lista. Na **página Adicionar Runbook**, selecione **Criação Rápida**.
1. Insira "Stop-AzureVmInResponsetoVMAlert" para o **Nome** do runbook e selecione **PowerShell** para o **Tipo de Runbook**. Clique em **Criar**.
1. Copie o exemplo a seguir do PowerShell no painel de edição. Clique em **Publicar** para salvar e publicar o runbook.

```powershell-interactive
<#
.SYNOPSIS
  This runbook will stop a resource management VM in response to an Azure alert trigger.

.DESCRIPTION
  This runbook will stop an resource management VM in response to an Azure alert trigger.
  Input is alert data with information needed to identify which VM to stop.
  
  DEPENDENCIES
  - The runbook must be called from an Azure alert via a webhook.
  
  REQUIRED AUTOMATION ASSETS
  - An Automation connection asset called "AzureRunAsConnection" that is of type AzureRunAsConnection.
  - An Automation certificate asset called "AzureRunAsCertificate".

.PARAMETER WebhookData
   Optional (user does not need to enter anything, but the service will always pass an object)
   This is the data that is sent in the webhook that is triggered from the alert.

.NOTES
   AUTHOR: Azure Automation Team
   LASTEDIT: 2017-11-22
#>

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Get the info needed to identify the VM (depends on the payload schema)
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {
        # This is the near-real-time Metric Alert schema
        $AlertContext = [object] ($WebhookBody.data).context
        $ResourceName = $AlertContext.resourceName
        $status = ($WebhookBody.data).status
    }
    elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
        # This is the Activity Log Alert schema
        $AlertContext = [object] (($WebhookBody.data).context).activityLog
        $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
        $status = ($WebhookBody.data).status
    }
    elseif ($schemaId -eq $null) {
        # This is the original Metric Alert schema
        $AlertContext = [object] $WebhookBody.context
        $ResourceName = $AlertContext.resourceName
        $status = $WebhookBody.status
    }
    else {
        # Schema not supported
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    Write-Verbose "status: $status" -Verbose
    if ($status -eq "Activated")
    {
        $ResourceType = $AlertContext.resourceType
        $ResourceGroupName = $AlertContext.resourceGroupName
        $SubId = $AlertContext.subscriptionId
        Write-Verbose "resourceType: $ResourceType" -Verbose
        Write-Verbose "resourceName: $ResourceName" -Verbose
        Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
        Write-Verbose "subscriptionId: $SubId" -Verbose

        # Do work only if this is a resource management VM
        if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
        {
            # This is the VM
            Write-Verbose "This is a resource management VM." -Verbose

            # Authenticate to Azure with service principal and certificate and set subscription
            Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
            $ConnectionAssetName = "AzureRunAsConnection"
            Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
            $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Verbose "Authenticating to Azure with service principal." -Verbose
            Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
            Write-Verbose "Setting subscription to work against: $SubId" -Verbose
            Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

            # Stop the VM
            Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
            Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
            # [OutputType(PSAzureOperationResponse")]
        }
        else {
            # ResourceType not supported
            Write-Error "$ResourceType is not a supported resource type for this runbook."
        }
    }
    else {
        # The alert status was not 'Activated' so no action taken
        Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
    }
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="create-an-action-group"></a>Criar um grupo de ações

Um grupo de ação é uma coleção de ações que são tomadas com base em um alerta. Runbooks são apenas uma das muitas ações disponíveis com grupos de ações.

1. No portal, selecione **Monitor**.

1. Selecione **Grupos de ações** em **CONFIGURAÇÕES**.

1. Selecione **Adicionar grupo de ações** e preencha os campos.

1. Insira um nome na caixa de nome do Grupo de ações e insira um nome na caixa de Nome curto. O nome curto é usado no lugar de um nome de grupo de ação completo quando as notificações são enviadas usando esse grupo.

1. A caixa de Assinatura é automaticamente preenchida com a sua assinatura atual. Esta assinatura é aquela na qual o grupo de ação é salvo.
Selecione o Grupo de recursos no qual o grupo de ações é salvo.

Para esse exemplo, você cria duas ações, uma ação de runbook e uma ação de notificação.

### <a name="runbook-action"></a>Ação de runbook

As etapas a seguir criam uma ação de runbook dentro do grupo de ações.

1. Em **Ações**, nomeie a ação.

1. Selecione **Runbook de Automação** para o **Tipo de Ação**.

1. Em **Detalhes** selecione, **Editar detalhes**

1. Na página **Configurar Runbook**, selecione **Usuário** em **Origem de Runbook**.

1. Escolha sua **Assinatura** e **Conta de automação** e, finalmente, selecione o runbook criado na etapa anterior chamado "Stop-AzureVmInResponsetoVMAlert".

1. Quando terminar, clique em **OK**.

### <a name="notification-action"></a>Ação de notificação

As etapas a seguir criam uma ação de notificação dentro do grupo de ações.

1. Em **Ações**, nomeie a ação.

1. Selecione **Email** para o **Tipo de Ação**.

1. Em **Detalhes** selecione, **Editar detalhes**

1. Na página de **Email**, insira o endereço de email para notificar e clique em **OK**. Adicionar um endereço de email, assim como o runbook como uma ação é útil porque você será notificado quando o runbook for iniciado. O grupo de ações deverá ser semelhando à seguinte imagem:

   ![Adicionar página do grupo de ações](./media/automation-create-alert-triggered-runbook/add-action-group.png)

1. Clique em **OK** para criar o grupo de ações.

Com o grupo de ações criado, você poderá criar [Alertas do log de atividades](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) ou [alertas quase em tempo real](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal) e usar o grupo de ações criado.

## <a name="classic-alert"></a>Alerta clássico

Os alertas clássicos são baseados em métricas e não usam grupos de ações, mas possuem ações de runbook com base neles. Para criar um alerta clássico, execute as etapas a seguir:

1. Selecione **+ Adicionar alerta de métrica**.

1. Nomeie seu alerta de métrica 'myVMCPUAlert' e forneça uma breve descrição para o alerta.

1. Defina a Condição do alerta de métrica como 'Maior que', defina o Limite como '10' e defina o Período como 'Nos últimos 5 minutos'.

1. Em **Executar uma ação**, selecione **Executar um runbook a partir deste alerta**

1. Na página **Configurar Runbook**, selecione **Usuário** para **Origem de Runbook**. Escolha sua conta de automação e selecione o runbook **Stop-AzureVmInResponsetoVMAlert**. Clique em **OK**e, em seguida, clique em **OK** novamente para salvar a regra de alerta.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como iniciar runbooks de automação com webhooks, consulte [Iniciar um runbook a partir de um webhook](automation-webhooks.md)
* Para obter detalhes sobre diferentes maneiras de iniciar um runbook, confira [Iniciar um runbook](automation-starting-a-runbook.md).
* Para saber mais sobre como criar um alerta de log de atividades, consulte [Criar alertas do log de atividades](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)
* Para saber como criar um alerta de quase em tempo real, visite [Criar uma regra de alerta com o Portal do Azure](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal).