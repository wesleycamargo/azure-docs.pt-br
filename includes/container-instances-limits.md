---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: 44bdaec78e1fad574f29a5945b07041b588aaff8
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51571757"
---
| Recurso | Limite padrão |
| --- | :--- |
| Grupos de contêiner por [assinatura](../articles/billing-buy-sign-up-azure-subscription.md) | 100<sup>1</sup> |
| Número de contêineres por grupo de contêineres | 60 |
| Número de volumes por grupo de contêineres | 20 |
| Portas por IP | 5 |
| Criações de contêiner por hora |300<sup>1</sup> |
| Criações de contêiner a cada 5 minutos | 100<sup>1</sup> |
| Exclusões de contêiner por hora | 300<sup>1</sup> |
| Exclusões de contêiner a cada 5 minutos | 100<sup>1</sup> |
| Vários contêineres por grupo de contêineres | Somente Linux<sup>2</sup> |
| Volumes de Arquivos do Azure | Somente Linux<sup>2</sup> |
| Volumes GitRepo | Somente Linux<sup>2</sup> |
| Volumes secretos | Somente Linux<sup>2</sup> |

<sup>1</sup> Crie uma [solicitação de suporte do Azure][azure-support] para solicitar um aumento de limite.<br />
<sup>2</sup> O suporte do Windows para este recurso está planejado.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
