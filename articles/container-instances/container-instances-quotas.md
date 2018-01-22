---
title: "Cotas para Instâncias de Contêiner do Azure e disponibilidade de região"
description: "As cotas padrão e a disponibilidade de região do serviço de Instâncias de Contêiner do Azure."
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: overview
ms.date: 01/11/2018
ms.author: marsma
ms.openlocfilehash: baf93d4a2a4ba1e05bbf558d0c056fa3aa833fef
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="quotas-and-region-availability-for-azure-container-instances"></a>Cotas e disponibilidade de região para Instâncias de Contêiner do Azure

Todos os serviços do Azure incluem certos limites padrão e cotas de recursos. As seções a seguir detalham os limites de recursos padrão de vários recursos de Instâncias de Contêiner do Azure (ACI), bem como a disponibilidade do serviço ACI em regiões do Azure.

## <a name="service-quotas-and-limits"></a>Cotas e limites de serviço

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="region-availability"></a>Disponibilidade de região

As Instâncias de Contêiner do Azure estão disponíveis nas seguintes regiões com os limites de memória e CPU especificados.

| Local padrão | SO | CPU | Memória (GB) |
| -------- | -- | :---: | :-----------: |
| Europa Ocidental, Oeste dos EUA, Leste dos EUA, Sudeste Asiático | Linux | 4 | 14 |
| Europa Ocidental, Oeste dos EUA, Leste dos EUA, Sudeste Asiático  | Windows | 4 | 14 |

As instâncias de contêiner criadas dentro desses limites de recursos estão sujeitas à disponibilidade dentro da região de implantação. Quando uma região está sob carga pesada, você pode enfrentar uma falha durante a implantação de instâncias. Para atenuar essa falha de implantação, tente implantar instâncias com configurações de memória e CPU inferiores ou tente implantar em um momento posterior.

Para obter mais informações sobre como solucionar problemas de implantação de instâncias de contêiner, consulte [Solucionar problemas de implantação com Instâncias de Contêiner do Azure](container-instances-troubleshooting.md).

## <a name="next-steps"></a>Próximas etapas

Determinados limites e cotas padrão podem ser aumentados. Para solicitar um aumento de um ou mais recursos que oferecem suporte a esse aumento, envie uma [solicitação de suporte do Azure][azure-support] (selecione "Cota" em **Tipo de emissão**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
