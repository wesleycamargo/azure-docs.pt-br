---
title: Cotas de AKS (Serviço de Kubernetes do Azure) e disponibilidade de região
description: As cotas padrão e a disponibilidade de região do AKS (Serviço de Kubernetes do Azure).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 06/13/2018
ms.author: iainfou
ms.openlocfilehash: 6d4fa9a06f3baeb51505c3e6060e9689428f8386
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100636"
---
# <a name="quotas-and-region-availability-for-azure-kubernetes-service-aks"></a>Cotas e disponibilidade de região para AKS (Serviço de Kubernetes do Azure)

Todos os serviços do Azure incluem certos limites padrão e cotas de recursos. As seções a seguir detalham os limites de recursos padrão de vários recursos do AKS (Serviço de Kubernetes do Azure), bem como a disponibilidade do serviço AKS em regiões do Azure.

## <a name="service-quotas-and-limits"></a>Cotas e limites de serviço

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Infraestrutura provisionada

Todas as outras limitações de rede, computação e armazenamento se aplicam à infraestrutura provisionada. Consulte [Assinatura do Azure e limites de serviço](../azure-subscription-service-limits.md) para os limites relevantes.

## <a name="region-availability"></a>Disponibilidade de região

O AKS (Serviço de Kubernetes do Azure) está disponível nas seguintes regiões:

- Leste da Austrália
- Canadá Central
- Leste do Canadá
- Centro dos EUA
- Leste dos EUA
- Norte da Europa
- Sul do Reino Unido
- Europa Ocidental
- Oeste dos EUA
- Oeste dos EUA 2

## <a name="next-steps"></a>Próximas etapas

Determinados limites e cotas padrão podem ser aumentados. Para solicitar um aumento de um ou mais recursos que oferecem suporte a esse aumento, envie uma [solicitação de suporte do Azure][azure-support] (selecione "Cota" em **Tipo de emissão**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
