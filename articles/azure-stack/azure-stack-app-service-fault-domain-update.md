---
title: 'Serviço de aplicativo na pilha do Azure: falha de atualização do domínio | Microsoft Docs'
description: Como redistribuir o serviço de aplicativo do Azure na pilha do Azure em domínios de falha
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: anwestg
ms.openlocfilehash: 42adef66fb1b1141ab44aab3a1ccdaae022202b5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>Como redistribuir o serviço de aplicativo do Azure na pilha do Azure em domínios de falha

*Aplica-se a: sistemas integrados de pilha do Azure*

Com a atualização 1802, pilha do Azure agora oferece suporte a distribuição de cargas de trabalho entre domínios de falha, um recurso, que é essencial para alta disponibilidade.

> [!IMPORTANT]
> Deve atualizada do sistema de pilha do Azure integradas para 1802 ser capazes de aproveitar o suporte de domínio de falha.  Este documento só se aplica a implantações de provedor de recursos do serviço de aplicativo que foram concluídas antes da atualização 1802.  Se você tiver implantado o serviço de aplicativo na pilha do Azure após a atualização 1802 foi aplicada à pilha do Azure, o provedor de recursos já é distribuído entre domínios de falha.
>
>

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>Reequilibrar um provedor de recursos do serviço de aplicativo em domínios de falha

Para redistribuir os conjuntos de escala implantados para o provedor de recursos do serviço de aplicativo, você deve executar as seguintes etapas para cada conjunto de escala.  Por padrão, os nomes de scaleset são:

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

> [!NOTE]
> Se você não têm instâncias implantadas em alguns dos conjuntos de escala da camada de trabalho, você não precisa reequilibrar esses conjuntos de escala.  Os conjuntos de escala serão balanceados corretamente quando você escala-los no futuro.
>
>

1. Navegue até os conjuntos de escala de máquina Virtual no Portal do administrador de pilha do Azure.  Conjuntos de escala existentes implantados como parte da implantação do serviço de aplicativo serão listados com informações de contagem de instância.

    ![Conjuntos de escala de serviço de aplicativo do Azure listado no UX de conjuntos de escala de máquina Virtual][1]

2. Cada conjunto de expansão próximo.  Por exemplo, se você tiver três instâncias existentes no conjunto de escala você deve expandir a 6 para que as três novas instâncias serão provisionadas em domínios de falha.
    a. [Configurar o ambiente de administração de pilha do Azure no PowerShell](azure-stack-powershell-configure-admin.md) b. Use este exemplo para expandir o conjunto de escala:
        ```powershell
                Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

                # Get current scale set
                $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

                # Set and update the capacity of your scale set
                $vmss.sku.capacity = 6
                Update-AzureRmVmss -ResourceGroupName "AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
        '''
> [!NOTE]
> Esta etapa pode levar a um número de horas para ser concluída dependendo do tipo de função e o número de instâncias.
>
>

3. Monitore o status de novas instâncias de função na folha de funções de administração do serviço de aplicativo.  Verificar o status de uma instância de função individual clicando no tipo de função na lista

    ![Serviço de aplicativo do Azure em funções de pilha do Azure][2]

4. Um novas instâncias estão em um **pronto** estado, volte para a folha de conjunto de escala de máquinas virtuais e **excluir** instâncias antigas.

5. Repita estas etapas para **cada** conjunto de escalas da máquina virtual.

## <a name="next-steps"></a>Próximas etapas

Você também pode experimentar outros [plataforma como um serviço (PaaS) serviços](azure-stack-tools-paas-services.md).

* [Provedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Provedor de recursos MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
