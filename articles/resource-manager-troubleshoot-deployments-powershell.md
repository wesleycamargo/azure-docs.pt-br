<properties
   pageTitle="Exibir operações de implantação com o PowerShell | Microsoft Azure"
   description="Descreve como usar o Azure PowerShell para detectar e corrigir problemas de implantação do Resource Manager."
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
   ms.date="06/14/2016"
   ms.author="tomfitz"/>

# Exibir operações de implantação com o Azure PowerShell

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI do Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Você pode exibir as operações para uma implantação por meio do Azure PowerShell. Você pode estar mais interessado em ver as operações quando recebeu um erro durante a implantação para que este artigo foque em exibir as operações que falharam. O PowerShell fornece cmdlets que permitem encontrar facilmente os erros e determinar as possíveis correções.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

É possível evitar alguns erros validando o modelo e a infraestrutura antes da implantação. Você também pode registrar informações adicionais sobre solicitações e respostas durante a implantação que podem ser úteis mais tarde para a solução de problemas. Para obter informações sobre como validar e registrar informações de solicitação e resposta, consulte [Deploy a resource group with Azure Resource Manager template](resource-group-template-deploy.md) (Implantar um grupo de recursos com modelos do Azure Resource Manager).

## Usar operações de implantação para solucionar problemas

1. Para obter o status geral de uma implantação, use e comando **Get-AzureRmResourceGroupDeployment**. É possível filtrar os resultados para exibir apenas as implantações que falharam.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
        
    O que retorna as implantações com falha no seguinte formato:
        
        DeploymentName          : Microsoft.Template
        ResourceGroupName       : ExampleGroup
        ProvisioningState       : Failed
        Timestamp               : 6/14/2016 9:55:21 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                    Name                Type                 Value
                    ===============     ===================  ==========
                    storageAccountName  String               tfstorage9855
                    adminUsername       String               tfadmin
                    adminPassword       SecureString
                    dnsNameforLBIP      String               dns
                    vmNamePrefix        String               myVM
                    imagePublisher      String               MicrosoftWindowsServer
                    imageOffer          String               WindowsServer
                    imageSKU            String               2012-R2-Datacenter
                    lbName              String               myLB
                    nicNamePrefix       String               nic
                    publicIPAddressName String               myPublicIP
                    vnetName            String               myVNET
                    vmSize              String               Standard_D1

        Outputs                 :
        DeploymentDebugLogLevel :

2. Geralmente, cada implantação é composta por várias operações, e cada operação representa uma etapa no processo de implantação. Para descobrir o que deu errado com uma implantação, geralmente você precisa ver os detalhes sobre as operações de implantação. É possível ver o status das operações com **Get-AzureRmResourceGroupDeploymentOperation**.

        Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName Microsoft.Template
        
    Que retorna várias operações com cada uma no seguinte formato:
        
        Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
        OperationId    : A3EB2DA598E0A780
        Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                         duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                         serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
        PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                         serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}

3. Para obter mais detalhes sobre as operações com falha, recupere as propriedades das operações com o estado **Falha**.

        (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
        
    Que retorna todas as operações com falha com cada uma no seguinte formato:
        
        provisioningOperation : Create
        provisioningState     : Failed
        timestamp             : 2016-06-14T21:54:55.1468068Z
        duration              : PT3.1449887S
        trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
        serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
        statusCode            : BadRequest
        statusMessage         : @{error=}
        targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                                Microsoft.Network/publicIPAddresses/myPublicIP;
                                resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}

    Observe a ID de rastreamento da operação. Você usará isso na próxima etapa para focar em uma determinada operação.

4. Para obter a mensagem de status de uma determinada operação com falha, use o seguinte comando:

        ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
        
    Que retorna:
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}

## Usar os logs de auditoria para solucionar problemas

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Para ver os erros de uma implantação, use as seguintes etapas:

1. Para recuperar as entradas de log, execute o comando **Get-AzureRmLog**. É possível usar os parâmetros **ResourceGroup** e **Status** para retornar apenas os eventos que falharam para um único grupo de recursos. Caso você não especifique uma hora de início e término, serão retornadas as entradas da última hora. Por exemplo, para recuperar as operações com falha na última hora de execução:

        Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed

    É possível especificar um timespan específico. No próximo exemplo, buscaremos as ações com falha do último dia.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -Status Failed
      
    Caso contrário, é possível definir uma hora exata de início e término para as ações com falha:

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed

2. Se esse comando retornar entradas e propriedades demais, você poderá focar seus esforços de auditoria recuperando a propriedade **Properties**. Também incluiremos o parâmetro **DetailedOutput** para ver as mensagens de erro.

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -DetailedOutput).Properties
        
    O que retorna as propriedades das entradas de log no seguinte formato:
        
        Content
        -------
        {} 
        {[statusCode, BadRequest], [statusMessage, {"error":{"code":"DnsRecordInUse","message":"DNS record dns.westus.clouda...
        {[statusCode, BadRequest], [serviceRequestId, a426f689-5d5a-448d-a2f0-9784d14c900a], [statusMessage, {"error":{"code...

3. Com base nesses resultados, focaremos no segundo elemento. Além disso, você pode refinar ainda mais os resultados examinando a mensagem de status dessa entrada.

        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput -StartTime (Get-Date).AddDays(-1)).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
        
    Que retorna:
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}



## Próximas etapas

- Para obter ajuda com a resolução de determinados erros de implantação, consulte [Resolver erros comuns ao implantar recursos no Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Para saber mais sobre como usar os logs de auditoria para monitorar outros tipos de ações, consulte [Operações de auditoria com o Gerenciador de Recursos](resource-group-audit.md).
- Para validar sua implantação antes de executá-la, consulte [Implantar um grupo de recursos com um modelo do Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0622_2016-->