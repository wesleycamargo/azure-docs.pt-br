---
title: "Expansão de funções de trabalho nos serviços de aplicativo - pilha do Azure | Microsoft Docs"
description: "Instruções detalhadas para dimensionar os serviços de aplicativos de pilha do Azure"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: d6471796863a80e69fdaf740b68fb27d59503453
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>Serviço de aplicativo na pilha do Azure: adicionar mais funções de infraestrutura ou de trabalho

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*  

Este documento fornece instruções sobre como dimensionar o serviço de aplicativo em funções de infraestrutura e de trabalho da pilha do Azure. Ele contém as etapas para a criação de funções de trabalho adicional para dar suporte a aplicativos de qualquer tamanho.

> [!NOTE]
> Se seu ambiente de pilha do Azure não tem mais de 96 GB de RAM, você pode ter dificuldade para adicionar capacidade adicional.

Serviço de aplicativo na pilha do Azure, por padrão, dá suporte a camadas de trabalhador gratuito e compartilhado. Para adicionar outros níveis de trabalho, você precisa adicionar mais funções de trabalho.

Se não tiver certeza de que foi implantado com o serviço de aplicativo padrão na instalação da pilha do Azure, você pode examinar informações adicionais de [do serviço de aplicativo na visão geral do Azure pilha](azure-stack-app-service-overview.md).

Serviço de aplicativo do Azure na pilha do Azure implanta todas as funções usando conjuntos de escala de máquina Virtual e como tal tira proveito dos recursos de dimensionamento dessa carga de trabalho. Portanto, todo o dimensionamento das camadas de trabalhador é feito por meio do administrador do serviço de aplicativo.

> [!IMPORTANT]
> No momento não é possível dimensionar conjuntos de escala de máquina virtual no portal de conforme identificado nas notas de versão de pilha do Azure, portanto, usar o exemplo do PowerShell para expansão.
>
>

## <a name="add-additional-workers-with-powershell"></a>Adicionar operadores adicionais com o PowerShell

1. [Configurar o ambiente de administração de pilha do Azure no PowerShell](azure-stack-powershell-configure-admin.md)
2. Use este exemplo para expandir o conjunto de escala:
   ```powershell
   
    ##### Scale out the AppService Role instances ######
   
    # Set context to AzureStack admin.
    Login-AzureRMAccount -EnvironmentName AzureStackAdmin
                                                 
    ## Name of the Resource group where AppService is deployed.
    $AppServiceResourceGroupName = "AppService.local"

    ## Name of the ScaleSet : e.g. FrontEndsScaleSet, ManagementServersScaleSet, PublishersScaleSet , LargeWorkerTierScaleSet,      MediumWorkerTierScaleSet, SmallWorkerTierScaleSet, SharedWorkerTierScaleSet
    $ScaleSetName = "SharedWorkerTierScaleSet"

    ## TotalCapacity is sum of the instances needed at the end of operation. 
    ## e.g. if you VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  

    # Get current scale set
    $vmss = Get-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName

    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
  
    '''

> [!NOTE]
> This step can take a number of hours to complete depending on the type of role and the number of instances.
>
>

3. Monitor the status of the new role instances in the App Service Administration, to check the status of an individual role instance click the role type in the list.

## Add additional workers directly within the App Service Resource Provider Admin.

1. Log in to the Azure Stack administration portal as the service administrator.

2. Browse to **App Services**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Click **Roles**. Here you see the breakdown of all App Service roles deployed.

4. Right click on the row of the type you want to scale and then click **ScaleSet**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Click **Scaling**, select the number of instances you want to scale to, and then click **Save**.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. App Service on Azure Stack will now add the additional VMs, configure them, install all the required software, and mark them as ready when this process is complete. This process can take approximately 80 minutes.

7. You can monitor the progress of the readiness of the new roles by viewing the workers in the **Roles** blade.

## Result

After they are fully deployed and ready, the workers become available for users to deploy their workload onto them. The following shows an example of the multiple pricing tiers available by default. If there are no available workers for a particular worker tier, the option to choose the corresponding pricing tier is unavailable.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> To scale out Management, Front End or Publisher roles add you must scale out the corresponding role type. 
>
>

To scale out Management, Front End, or Publisher roles, follow the same steps selecting the appropriate role type. Controllers are not deployed as Scale Sets and therefore two should be deployed at Installation time for all production deployments.

### Next steps

[Configure deployment sources](azure-stack-app-service-configure-deployment-sources.md)
