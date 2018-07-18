---
title: Cotas para Instâncias de Contêiner do Azure e disponibilidade de região
description: As cotas padrão e a disponibilidade de região do serviço de Instâncias de Contêiner do Azure.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 02/27/2018
ms.author: marsma
ms.openlocfilehash: 22be921c7ab437e08d8ea5032befb8671dc90ed0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32163360"
---
# <a name="quotas-and-region-availability-for-azure-container-instances"></a>Cotas e disponibilidade de região para Instâncias de Contêiner do Azure

Todos os serviços do Azure incluem certos limites padrão e cotas de recursos. As seções a seguir detalham os limites de recursos padrão de vários recursos de Instâncias de Contêiner do Azure (ACI), bem como a disponibilidade do serviço ACI em regiões do Azure.

## <a name="service-quotas-and-limits"></a>Cotas e limites de serviço

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="region-availability"></a>Disponibilidade de região

As Instâncias de Contêiner do Azure estão disponíveis nas seguintes regiões com os limites de memória e CPU especificados.

| Local padrão | SO | CPU | Memória (GB) |
| -------- | -- | :---: | :-----------: |
| Oeste dos EUA, Leste dos EUA, Europa Ocidental, Europa Setentrional | Linux | 4 | 14 |
| Oeste dos EUA 2, Sudeste Asiático | Linux | 2 | 7 |
| Oeste dos EUA, Leste dos EUA, Europa Ocidental, Europa Setentrional | Windows | 4 | 14 |
| Oeste dos EUA 2, Sudeste Asiático | Windows | 2 | 3,5 |

As instâncias de contêiner criadas dentro desses limites de recursos estão sujeitas à disponibilidade dentro da região de implantação. Quando uma região está sob carga pesada, você pode enfrentar uma falha durante a implantação de instâncias. Para atenuar essa falha de implantação, tente implantar instâncias com configurações de memória e CPU inferiores ou tente implantar em um momento posterior.

Informe à equipe de regiões adicionais necessárias ou limites de CPU/memória aumentados em [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Para obter mais informações sobre como solucionar problemas de implantação de instâncias de contêiner, consulte [Solucionar problemas de implantação com Instâncias de Contêiner do Azure](container-instances-troubleshooting.md).

## <a name="next-steps"></a>Próximas etapas

Determinados limites e cotas padrão podem ser aumentados. Para solicitar um aumento de um ou mais recursos que oferecem suporte a esse aumento, envie uma [solicitação de suporte do Azure][azure-support] (selecione "Cota" em **Tipo de emissão**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
