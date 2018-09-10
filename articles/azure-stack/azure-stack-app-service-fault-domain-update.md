---
title: 'Serviço de aplicativo no Azure Stack: atualização de domínio de falha | Microsoft Docs'
description: Como redistribuir o serviço de aplicativo do Azure no Azure Stack em domínios de falha
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
ms.date: 09/05/2018
ms.author: anwestg
ms.openlocfilehash: acadd1adec93d10d64712a2fbedb89e098998294
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2018
ms.locfileid: "44025955"
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>Como redistribuir o serviço de aplicativo do Azure no Azure Stack em domínios de falha

*Aplica-se a: sistemas integrados do Azure Stack*

Com a atualização 1802, Azure Stack agora dá suporte a distribuição de cargas de trabalho entre domínios de falha, um recurso que é essencial para alta disponibilidade.

>[!IMPORTANT]
>Para tirar proveito do suporte de domínio de falha, você deve atualizar seu sistema integrado do Azure Stack para o 1802. Este documento só se aplica a implantações de provedor de recursos do serviço de aplicativo que foram concluídas antes da atualização 1802. Se você implantou o serviço de aplicativo no Azure Stack depois que a atualização 1802 foi aplicada para o Azure Stack, o provedor de recursos já é distribuído entre domínios de falha.

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>Reequilibrar um provedor de recursos do serviço de aplicativo entre domínios de falha

Para redistribuir os conjuntos de dimensionamento implantados para o provedor de recursos do serviço de aplicativo, você deve executar as etapas neste artigo para cada conjunto de dimensionamento. Por padrão, os nomes de conjunto de dimensionamento são:

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

>[!NOTE]
> Se você não tiver instâncias implantadas em alguns dos conjuntos de dimensionamento da camada de trabalho, você não precisa rebalancear esses conjuntos de dimensionamento. Os conjuntos de dimensionamento serão balanceados corretamente quando você dimensiona-los no futuro.

Para escalar horizontalmente os conjuntos de dimensionamento, siga estas etapas:

1. Entrar no Portal do administrador de pilha do Azure.
1. Selecione **Todos os serviços**.
2. No **COMPUTE** categoria, selecione **conjuntos de dimensionamento de máquina Virtual**. Conjuntos de dimensionamento existente implantados como parte da implantação do serviço de aplicativo serão listados com informações de contagem de instância. A captura de tela a seguir mostra um exemplo de conjuntos de dimensionamento.

      ![Conjuntos de dimensionamento de serviço de aplicativo do Azure listados na UX de conjuntos de dimensionamento de máquina Virtual][1]

1. Expandir cada conjunto. Por exemplo, se você tiver três instâncias existentes no conjunto de dimensionamento você deve escalar horizontalmente a 6 para que as três novas instâncias são implantadas em domínios de falha. O exemplo a seguir do PowerShell mostra-out para escalar horizontalmente o conjunto de dimensionamento.

   ```powershell
   Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

   # Get current scale set
   $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

   # Set and update the capacity of your scale set
   $vmss.sku.capacity = 6
   Update-AzureRmVmss -ResourceGroupName AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
   ```

   >[!NOTE]
   >Esta etapa pode levar várias horas para ser concluída, dependendo do tipo de função e o número de instâncias.

1. Na **funções de administração do serviço de aplicativo**, monitorar o status de novas instâncias de função. Para verificar o status de uma instância de função, selecione o tipo de função na lista

    ![Serviço de aplicativo do Azure em funções do Azure Stack][2]

1. Quando o status de novas instâncias de função for **pronto**, retorne à **conjunto de dimensionamento de máquinas virtuais** e **excluir** as instâncias de função antiga.

1. Repita essas etapas para **cada** conjunto de dimensionamento de máquina virtual.

## <a name="next-steps"></a>Próximas etapas

Você também pode experimentar outras [plataforma como um serviço (PaaS) serviços](azure-stack-tools-paas-services.md).

* [Provedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Provedor de recursos MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
