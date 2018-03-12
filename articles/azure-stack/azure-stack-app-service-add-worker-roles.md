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
ms.openlocfilehash: 680cb70777574d0ed88c5f83fb0a6fa20263b951
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
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
    ## e.g. if your VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  

    # Get current scale set
    $vmss = Get-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName

    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
   ```    

   > [!NOTE]
   > Esta etapa pode levar a um número de horas para ser concluída dependendo do tipo de função e o número de instâncias.
   >
   >

3. Monitorar o status de novas instâncias de função na administração do serviço de aplicativo, para verificar o status de uma instância de função individual clique no tipo de função na lista.

## <a name="add-additional-workers-directly-within-the-app-service-resource-provider-admin"></a>Adicionar operadores adicionais diretamente no provedor de administrador do aplicativo serviço recursos.

1. Faça logon portal de administração do Azure pilha como o administrador de serviço.

2. Navegue até **serviços de aplicativos**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Clique em **Funções**. Veja aqui a análise de todas as funções de serviço de aplicativo implantado.

4. Clique com o botão direito na linha do tipo que você deseja dimensionar e, em seguida, clique em **ScaleSet**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Clique em **Scaling**, selecione o número de instâncias que você deseja dimensionar para e, em seguida, clique em **salvar**.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. Serviço de aplicativo na pilha do Azure agora será adicionar outras VMs, configurá-los, instalar todos os softwares necessários e marcá-los como pronto quando esse processo é concluído. Esse processo pode levar aproximadamente 80 minutos.

7. Você pode monitorar o progresso da preparação das novas funções, exibindo os trabalhadores no **funções** folha.

## <a name="result"></a>Result

Depois que eles são totalmente implantado e estiver pronto, os trabalhadores ficam disponíveis para os usuários implantar suas cargas de trabalho para eles. O exemplo a seguir mostra um exemplo de várias camadas de preços disponíveis por padrão. Se não houver nenhum operadores disponíveis para uma camada de trabalhador específico, a opção de escolher a camada de preços correspondente não está disponível.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Para expandir gerenciamento, funções de Front-End ou publicador adicionam que você deve expandir o tipo de função correspondente. 
>
>

Para expandir gerenciamento, Front-End ou publicador funções, siga as mesmas etapas que selecionando o tipo de função apropriada. Controladores não são implantados como conjuntos de escala e, portanto, duas devem ser implantadas no momento da instalação para todas as implantações de produção.

### <a name="next-steps"></a>Próximas etapas

[Configurar fontes de implantação](azure-stack-app-service-configure-deployment-sources.md)
