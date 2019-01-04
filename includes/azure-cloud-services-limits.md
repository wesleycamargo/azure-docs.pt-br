---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: eb1fe7f83ed83efe078be0aadf26cc7db6f498e2
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52886299"
---
| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| [Funções Web/de trabalho por implantação](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Pontos de extremidade de entrada de instância](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) por implantação |25 |25 |
| [Pontos de extremidade de entrada](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) por implantação |25 |25 |
| [Pontos de extremidade internos](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) por implantação |25 |25 |
| [Certificados de serviços hospedados](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) por implantação |199 |199 |

<sup>1</sup>Cada serviço de nuvem com funções de trabalho/Web pode ter duas implantações, uma para produção e uma de preparo. Observe também que esse limite se refere ao número de funções distintas (configuração) e não ao número de instâncias por função (escala).

