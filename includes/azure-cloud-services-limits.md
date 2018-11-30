---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2b6ba1dcddd435c42ad864b8e87175d0e98c9b3a
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279434"
---
| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| [Funções Web/de trabalho por implantação](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Pontos de extremidade de entrada de instância](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) por implantação |25 |25 |
| [Pontos de extremidade de entrada](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) por implantação |25 |25 |
| [Pontos de extremidade internos](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) por implantação |25 |25 |

<sup>1</sup>Cada serviço de nuvem com funções de trabalho/Web pode ter duas implantações, uma para produção e uma de preparo. Observe também que esse limite se refere ao número de funções distintas (configuração) e não ao número de instâncias por função (escala).

