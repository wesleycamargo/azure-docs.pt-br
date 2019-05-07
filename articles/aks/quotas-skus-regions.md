---
title: Cotas, SKU e a disponibilidade de região no serviço de Kubernetes do Azure (AKS)
description: Saiba mais sobre as cotas padrão, a tamanhos de SKU de VM de nó restrito e a disponibilidade de região do serviço de Kubernetes do Azure (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: iainfou
ms.openlocfilehash: abeb9ef6e467b62cf7332e01e1b77c710b9ba4f4
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072862"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Cotas, restrições de tamanho de máquina virtual e a disponibilidade de região no serviço de Kubernetes do Azure (AKS)

Todos os serviços do Azure incluem certos limites padrão e cotas de recursos. Para o tamanho de nó, determinada máquina virtual (VM) SKUs são, em seguida, restrito para uso.

Este artigo fornece detalhes sobre os limites de recursos padrão para recursos do serviço de Kubernetes do Azure (AKS), bem como a disponibilidade do serviço AKS em regiões do Azure.

## <a name="service-quotas-and-limits"></a>Cotas e limites de serviço

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Infraestrutura provisionada

Todas as outras limitações de rede, computação e armazenamento se aplicam à infraestrutura provisionada. Consulte [Assinatura do Azure e limites de serviço](../azure-subscription-service-limits.md) para os limites relevantes.

## <a name="restricted-vm-sizes"></a>Tamanhos de VM restritos

Cada nó em um cluster AKS contém uma quantidade fixa de recursos de computação, como memória e vCPU. Se um nó do AKS contém recursos de computação insuficiente, os pods podem não funcionar corretamente. Para garantir que o necessário *kube-system* pods e seus aplicativos de forma confiável podem ser agendados, os seguintes SKUs de VM não pode ser usados no AKS:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Para obter mais informações sobre tipos de VM e seus recursos de computação, consulte [tamanhos para máquinas virtuais no Azure][vm-skus].

## <a name="region-availability"></a>Disponibilidade de região

Para obter a lista mais recente em que você pode implantar e executar clusters, consulte [disponibilidade de região do AKS][region-availability].

## <a name="next-steps"></a>Próximas etapas

Determinados limites e cotas padrão podem ser aumentados. Para solicitar um aumento de um ou mais recursos que oferecem suporte a esse aumento, envie uma [solicitação de suporte do Azure][azure-support] (selecione "Cota" em **Tipo de emissão**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
