---
title: Cotas de vCPU do Azure | Microsoft Docs
description: Saiba mais sobre as cotas de vCPU do Azure.
keywords: 
services: virtual-machines-windows
documentationcenter: 
author: Drewm3
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: drewm
ms.openlocfilehash: b481299b62d452bc306c1f9c1fa2cdccd49b818e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="virtual-machine-vcpu-quotas"></a>Cotas de vCPU de máquina virtual

As cotas de vCPU para máquinas virtuais e conjuntos de escala de máquinas virtuais são organizadas em duas camadas para cada assinatura, em cada região. A primeira camada é dos vCPUs Regionais Totais, e a segunda camada é dos vários núcleos de família de tamanho de VM, como vCPUs de Família D Padrão. Sempre que uma nova VM for implantada, os vCPUs para a VM implantada recentemente não devem exceder a cota de vCPU para a família de tamanho de VM específica ou a cota de vCPU regional total. Se qualquer uma das cotas for excedida, a implantação de VM não será permitida. Também há uma cota para o número total de máquinas virtuais na região. Os detalhes sobre cada uma dessas cotas podem ser vistos na seção **uso + cotas** da página de **assinatura** no [portal do Azure](https://portal.azure.com), ou você pode consultar os valores usando o PowerShell.

 
## <a name="check-usage"></a>Verificar o uso

Você pode usar o cmdlet [Get-AzureRMVMUsage](/powershell/module/azurerm.compute/get-azurermvmusage) para verificar o seu uso de cotas.

```azurepowershell-interactive
Get-AzureRmVMUsage -Location "East US"
```

A saída parecerá com o seguinte:

```
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional vCPUs                         4   260 Count
Virtual Machines                             4 10000 Count
Virtual Machine Scale Sets                   1  2000 Count
Standard B Family vCPUs                      1    10 Count
Standard DSv2 Family vCPUs                   1   100 Count
Standard Dv2 Family vCPUs                    2   100 Count
Basic A Family vCPUs                         0   100 Count
Standard A0-A7 Family vCPUs                  0   250 Count
Standard A8-A11 Family vCPUs                 0   100 Count
Standard D Family vCPUs                      0   100 Count
Standard G Family vCPUs                      0   100 Count
Standard DS Family vCPUs                     0   100 Count
Standard GS Family vCPUs                     0   100 Count
Standard F Family vCPUs                      0   100 Count
Standard FS Family vCPUs                     0   100 Count
Standard NV Family vCPUs                     0    24 Count
Standard NC Family vCPUs                     0    48 Count
Standard H Family vCPUs                      0     8 Count
Standard Av2 Family vCPUs                    0   100 Count
Standard LS Family vCPUs                     0   100 Count
Standard Dv2 Promo Family vCPUs              0   100 Count
Standard DSv2 Promo Family vCPUs             0   100 Count
Standard MS Family vCPUs                     0     0 Count
Standard Dv3 Family vCPUs                    0   100 Count
Standard DSv3 Family vCPUs                   0   100 Count
Standard Ev3 Family vCPUs                    0   100 Count
Standard ESv3 Family vCPUs                   0   100 Count
Standard FSv2 Family vCPUs                   0   100 Count
Standard ND Family vCPUs                     0     0 Count
Standard NCv2 Family vCPUs                   0     0 Count
Standard NCv3 Family vCPUs                   0     0 Count
Standard LSv2 Family vCPUs                   0     0 Count
Standard Storage Managed Disks               2 10000 Count
Premium Storage Managed Disks                1 10000 Count

```


## <a name="reserved-vm-instances"></a>Instâncias de máquina virtual reservada
Instâncias de máquina virtual reservada, que têm o escopo voltado para uma assinatura única, adicionarão um novo aspecto às cotas de vCPU. Esses valores descrevem o número de instâncias de tamanho indicado que devem ser implantadas na assinatura. Elas funcionam como um espaço reservado no sistema de cota para garantir que essa cota seja reservada para garantir que instâncias reservadas sejam implantadas na assinatura. Por exemplo, se uma assinatura específica tiver 10 instâncias reservadas Standard_D1, os limites de uso para as instâncias reservadas Standard_D1 será 10. Isso fará com que o Azure garanta que haja sempre pelo menos 10 vCPUs disponíveis na cota de vCPUs Regionais Totais para serem usados para instâncias Standard_D1 e pelo menos 10 VCPUs disponíveis na cota de vCPU Família D padrão para serem usados para as instâncias Standard_D1.

Se um aumento de cota for necessário para adquirir um RI assinatura única, você pode [solicitar um aumento de cota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) na sua assinatura.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre cobrança e cotas, confira [Assinatura do Azure e limite de serviços, cotas e restrições](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).