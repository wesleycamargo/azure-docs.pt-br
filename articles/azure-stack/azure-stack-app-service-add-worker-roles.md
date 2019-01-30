---
title: Expansão de funções de trabalho nos serviços de aplicativos - Azure Stack | Microsoft Docs
description: Orientações detalhadas para dimensionar os serviços de aplicativo do Azure Stack
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2018
ms.author: anwestg
ms.reviewer: sethm
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: 26981d50239ddd7da79919291f2accd8b8ed4fc0
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241926"
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>Serviço de aplicativo no Azure Stack: Adicionar mais funções de trabalho ou infraestrutura

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*  

Este documento fornece instruções sobre como dimensionar o serviço de aplicativo nas funções de infraestrutura e de trabalho do Azure Stack. Ele contém etapas para a criação de funções de trabalho adicional para dar suporte a aplicativos de qualquer tamanho.

> [!NOTE]
> Se seu ambiente de pilha do Azure não tem mais de 96 GB de RAM, você pode ter dificuldades para adicionar capacidade adicional.

Serviço de aplicativo no Azure Stack, por padrão, dá suporte a camadas de trabalhador gratuitos e compartilhados. Para adicionar outras camadas de trabalho, você precisa adicionar mais funções de trabalho.

Se você não tiver certeza de que foi implantado com o serviço de aplicativo padrão na instalação do Azure Stack, você pode examinar informações adicionais no [serviço de aplicativo na visão geral do Azure Stack](azure-stack-app-service-overview.md).

Serviço de aplicativo do Azure no Azure Stack implanta todas as funções usando conjuntos de dimensionamento de máquina Virtual e como tal, aproveita os recursos de dimensionamento dessa carga de trabalho. Portanto, todo o dimensionamento das camadas de trabalhador é feito por meio do administrador do serviço de aplicativo.

> [!IMPORTANT]
> Atualmente, não é possível dimensionar conjuntos de dimensionamento de máquina virtual no portal conforme identificado nas notas de versão do Azure Stack, portanto, usar o exemplo do PowerShell para escalar horizontalmente.
>
>

## <a name="add-additional-workers-with-powershell"></a>Adicionar trabalhadores adicionais com o PowerShell

1. [Configurar o ambiente de administração do Azure Stack no PowerShell](azure-stack-powershell-configure-admin.md)

2. Use este exemplo para escalar horizontalmente o conjunto de dimensionamento:
   ```powershell
   
    ##### Scale out the AppService Role instances ######
   
    # Set context to AzureStack admin.
    Login-AzureRmAccount -EnvironmentName AzureStackAdmin
                                                 
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

3. Monitorar o status de novas instâncias de função na administração de serviço de aplicativo, para verificar o status de uma instância de função individuais clique no tipo de função na lista.

## <a name="add-additional-workers-directly-within-the-app-service-resource-provider-admin"></a>Adicionar trabalhadores adicionais diretamente dentro do provedor de administrador do aplicativo serviço de recursos.

1. Entre no portal de administração do Azure Stack como administrador de serviços.

2. Navegue até **serviços de aplicativos**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Clique em **Funções**. Veja aqui a divisão de todas as funções de serviço de aplicativo implantado.

4. Clique com o botão direito na linha do tipo que você deseja dimensionar e, em seguida, clique em **ScaleSet**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Clique em **Scaling**, selecione o número de instâncias que você deseja dimensionar e, em seguida, clique em **salvar**.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. Serviço de aplicativo no Azure Stack agora será adicionar outras VMs, configurá-los, instale todos os softwares necessários e marcá-los como pronto quando esse processo for concluído. Esse processo pode levar aproximadamente 80 minutos.

7. Você pode monitorar o progresso da preparação das novas funções, exibindo os trabalhadores na **funções** folha.

## <a name="result"></a>Result

Depois que eles são totalmente implantado e estiver pronto, os trabalhadores são disponibilizados para os usuários implantem sua carga de trabalho neles. O exemplo a seguir mostra um exemplo de vários tipos de preço disponíveis por padrão. Se não houver nenhum processador disponível para uma camada de trabalho específica, a opção de escolher o tipo de preço correspondente não está disponível.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Para expandir o gerenciamento, as funções de Front-End ou publicador adicionam que você deve escalar horizontalmente o tipo de função correspondente. 
>
>

Para expandir o gerenciamento, Front-End ou publicador funções, siga as mesmas etapas, selecionando o tipo de função apropriada. Controladores não são implantados como conjuntos de dimensionamento e, portanto, dois devem ser implantados no momento da instalação para todas as implantações de produção.

### <a name="next-steps"></a>Próximas etapas

[Configurar fontes de implantação](azure-stack-app-service-configure-deployment-sources.md)
