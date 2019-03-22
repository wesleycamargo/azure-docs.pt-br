---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: acf5a7e8d7feebaac3d82629c5b561a22112de0f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553234"
---
| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| [Funções de trabalho ou Web por implantação](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Pontos de extremidade de entrada de instância](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) por implantação |25 |25 |
| [Pontos de extremidade de entrada](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) por implantação |25 |25 |
| [Pontos de extremidade internos](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) por implantação |25 |25 |
| [Certificados de serviços hospedados](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) por implantação |199 |199 |

<sup>1</sup>cada serviço de nuvem do Azure com funções web ou de trabalho pode ter duas implantações, um para produção e uma de preparo. Esse limite se refere ao número de funções distintas, ou seja, a configuração. Esse limite não se refere ao número de instâncias por função, ou seja, dimensionamento.

