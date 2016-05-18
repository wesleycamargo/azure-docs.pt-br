<properties
   pageTitle="Solucionando problemas de implantações com o PowerShell | Microsoft Azure"
   description="Descreve como usar o Azure PowerShell para detectar e corrigir problemas de implantação do Gerenciador de Recursos."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="03/21/2016"
   ms.author="tomfitz"/>

# Solucionando problemas de implantações de grupo de recursos com o Azure PowerShell

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI do Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Se você recebeu um erro durante a implantação de recursos no Azure, será necessário solucionar esse erro. O Azure PowerShell fornece cmdlets que permitem a fácil localização dos erros e determinar as possíveis correções.

É possível solucionar problemas da implantação examinando os logs de auditoria ou as operações de implantação. Este tópico mostra ambos os métodos.

É possível evitar alguns erros validando o modelo e a infraestrutura antes da implantação. Para obter mais informações, veja [Implantar um grupo de recursos com o modelo do Azure Resource Manager](resource-group-template-deploy.md).

## Usar os logs de auditoria para solucionar problemas

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Para ver os erros de uma implantação, use as seguintes etapas:

1. Para recuperar entradas de log, execute o comando **Get-AzureRmLog**. É possível usar os parâmetros **ResourceGroup** e **Status** para retornar apenas os eventos que falharam para um único grupo de recursos. Caso você não especifique uma hora de início e término, serão retornadas as entradas da última hora. Por exemplo, para recuperar as operações com falha na última hora de execução:

        Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed

    É possível especificar um timespan específico. No próximo exemplo, buscaremos as ações com falha nos últimos 14 dias.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Status Failed
      
    Caso contrário, é possível definir uma hora exata de início e término para as ações com falha:

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed

2. Caso esse comando retorne um número excessivo de entradas e propriedades, você poderá concentrar seus esforços de auditoria recuperando a propriedade **Properties**. Também incluiremos o parâmetro **DetailedOutput** para ver as mensagens de erro.

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties
        
    O que retorna as propriedades das entradas de log no seguinte formato:
        
        Content
        -------
        {}
        {[statusCode, Conflict], [statusMessage, {"Code":"Conflict","Message":"Website with given name mysite already exists...
        {[statusCode, Conflict], [serviceRequestId, ], [statusMessage, {"Code":"Conflict","Message":"Website with given name...

3. É possível refinar ainda mais os resultados examinando a mensagem de status de determinada entrada.

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json
        
    O que retorna a mensagem de status no seguinte formato:
        
        Code       : Conflict
        Message    : Website with given name mysite already exists.
        Target     :
        Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
        Innererror :


## Usar operações de implantação para solucionar problemas

1. Para obter o status geral de uma implantação, use e comando **Get-AzureRmResourceGroupDeployment**. É possível filtrar os resultados para exibir apenas as implantações que falharam.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
        
    O que retorna as implantações com falha no seguinte formato:
        
        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleGroup
        ProvisioningState : Failed
        Timestamp         : 8/27/2015 8:03:34 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
        Name             Type                       Value
        ===============  =========================  ==========
        siteName         String                     ExampleSite
        hostingPlanName  String                     ExamplePlan
        siteLocation     String                     West US
        sku              String                     Free
        workerSize       String                     0
        
        Outputs           :

2. Geralmente, cada implantação é composta por várias operações, e cada operação representa uma etapa no processo de implantação. Para descobrir o que deu errado com uma implantação, geralmente você precisa ver os detalhes sobre as operações de implantação. É possível ver o status das operações com **Get-AzureRmResourceGroupDeploymentOperation**.

        Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment | Format-List
        
    O que retorna as operações no seguinte formato:
        
        Id          : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/ExampleDeployment/operations/8518B32868A437C8
        OperationId : 8518B32868A437C8
        Properties  : @{ProvisioningOperation=Create; ProvisioningState=Failed; Timestamp=2016-03-16T20:05:37.2638161Z;
                      Duration=PT2.8834832S; TrackingId=192fbfbf-a2e2-40d6-b31d-890861f78ed3; StatusCode=Conflict;
                      StatusMessage=; TargetResource=}

3. Para obter mais detalhes sobre a operação, recupere o objeto **Properties**.

        (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties
        
    O que retorna as propriedades da operação no seguinte formato:
        
        ProvisioningOperation : Create
        ProvisioningState     : Failed
        Timestamp             : 2016-03-16T20:05:37.2638161Z
        Duration              : PT2.8834832S
        TrackingId            : 192fbfbf-a2e2-40d6-b31d-890861f78ed3
        StatusCode            : Conflict
        StatusMessage         : @{Code=Conflict; Message=Website with given name mysite already exists.; Target=;
        Details=System.Object[]; Innererror=}
        TargetResource        : @{Id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
        Microsoft.Web/Sites/mysite; ResourceType=Microsoft.Web/Sites; ResourceName=mysite}

4. Para se concentrar na mensagem de status das operações com falha, use o seguinte comando:

        ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage
        
    O que retorna a mensagem de status no seguinte formato:
        
        Code       : Conflict
        Message    : Website with given name mysite already exists.
        Target     :
        Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
        Innererror :

## Próximas etapas

- Para saber mais sobre como usar os logs de auditoria para monitorar outros tipos de ações, consulte [Operações de auditoria com o Gerenciador de Recursos](resource-group-audit.md).
- Para validar sua implantação antes de executá-la, consulte [Implantar recursos com modelos do Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0420_2016-->