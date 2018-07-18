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
ms.date: 06/29/2018
ms.author: anwestg
ms.openlocfilehash: ce57e153dcab6a386150ebefe1ecb4a018514247
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130363"
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>Como redistribuir o serviço de aplicativo do Azure na pilha do Azure em domínios de falha

*Aplica-se a: sistemas integrados de pilha do Azure*

Com a atualização 1802, pilha do Azure agora oferece suporte a distribuição de cargas de trabalho entre domínios de falha, um recurso que é essencial para alta disponibilidade.

>[!IMPORTANT]
>Para tirar proveito de domínios de falha, você deve atualizar seu sistema de pilha do Azure integradas para 1802. Este documento só se aplica a implantações de provedor de recursos do serviço de aplicativo que foram concluídas antes da atualização 1802. Se você tiver implantado o serviço de aplicativo na pilha do Azure após a atualização 1802 foi aplicada à pilha do Azure, o provedor de recursos já é distribuído entre domínios de falha.

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>Reequilibrar um provedor de recursos do serviço de aplicativo em domínios de falha

Para redistribuir os conjuntos de escala implantados para o provedor de recursos do serviço de aplicativo, você deve executar as etapas neste artigo para cada conjunto de escala. Por padrão, os nomes de scaleset são:

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

>[!NOTE]
> Se você não tiver instâncias implantadas em alguns dos conjuntos de escala da camada de trabalho, você não precisa reequilibrar esses conjuntos de escala. Os conjuntos de escala serão balanceados corretamente quando você escala-los no futuro.

Para expandir os conjuntos de escala, siga estas etapas:

1. Entrar no portal do administrador de pilha do Azure.
2. Escolha **Mais serviços**.
3. Em computação, selecione **conjuntos de escala de máquina Virtual**. Conjuntos de escala existentes implantados como parte da implantação do serviço de aplicativo serão listados com informações de contagem de instância. A captura de tela a seguir mostra um exemplo de conjuntos de escala.

      ![Conjuntos de escala de serviço de aplicativo do Azure listado no UX de conjuntos de escala de máquina Virtual][1]

4. Expandir cada conjunto. Por exemplo, se você tiver três instâncias existentes no conjunto de escala você deve expandir a 6 para que as três novas instâncias são implantadas em domínios de falha. O exemplo a seguir do PowerShell mostra para expandir o conjunto de escala.

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

5. Em **funções de administração do serviço de aplicativo**, monitorar o status de novas instâncias de função. Para verificar o status de uma instância de função, selecione o tipo de função na lista

    ![Serviço de aplicativo do Azure em funções de pilha do Azure][2]

6. Quando o status de novas instâncias de função é **pronto**, volte para **conjunto de escala de máquinas virtuais** e **excluir** as instâncias de função antigo.

7. Repita estas etapas para **cada** conjunto de escalas da máquina virtual.

## <a name="next-steps"></a>Próximas etapas

Você também pode experimentar outros [plataforma como um serviço (PaaS) serviços](azure-stack-tools-paas-services.md).

* [Provedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Provedor de recursos MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
