---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 10/19/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: a1645a8471f75f1d56f3e61c0adfc3fadee14560
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553238"
---
| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| VMs por [assinatura](../articles/billing-buy-sign-up-azure-subscription.md) |25.000<sup>1</sup> por região. |25.000 por região. |
| Total de núcleos da VM por [assinatura](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> por região. | Entre em contato com o suporte. |
| Núcleos de VM por séries como Dv2 e F, por [assinatura](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> por região. | Entre em contato com o suporte. |
| [Coadministradores](../articles/billing-add-change-azure-subscription-administrator.md) por assinatura |Ilimitado. |Ilimitado. |
| [Contas de armazenamento](../articles/storage/common/storage-quickstart-create-account.md) por região e assinatura |200 |200<sup>2</sup> |
| [Grupos de recursos](../articles/azure-resource-manager/resource-group-overview.md) por assinatura |980 |980 |
| [Conjuntos de disponibilidade](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) por assinatura |2.000 por região. |2.000 por região. |
| Tamanho de solicitação de API do Gerenciador de recursos do Azure |4,194,304 bytes. |4,194,304 bytes. |
| Marcas por assinatura<sup>3</sup> |Ilimitado. |Ilimitado. |
| Cálculos de marca exclusivos por assinatura<sup>3</sup> | 10.000 | 10.000 |
| [Serviços de nuvem](../articles/cloud-services/cloud-services-choose-me.md) por assinatura |N/D<sup>4</sup> |N/D<sup>4</sup> |
| [Grupos de afinidade](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) por assinatura |N/D<sup>4</sup> |N/D<sup>4</sup> |
| [Implantações de nível de assinatura](../articles/azure-resource-manager/deploy-to-subscription.md) por local | 800 | 800 |

<sup>1</sup>limites padrão variam por tipo de categoria de oferta, como avaliação gratuita e paga conforme o uso e séries como Dv2, F e G.

<sup>2</sup>contas de armazenamento tanto Standard e Premium são incluídas. Se você precisar de mais de 200 contas de armazenamento, faça uma solicitação por meio [suporte do Azure](https://azure.microsoft.com/support/faq/). A equipe de armazenamento do Azure analisa o seu caso de negócios e poderá aprovar até 250 contas de armazenamento.

<sup>3</sup>Você pode aplicar um número ilimitado de marcas por assinatura. O número de marcas por recurso ou grupo de recursos é limitado a 15. O Resource Manager retorna um [lista de valores e nomes de marca exclusivos](/rest/api/resources/tags) na assinatura somente quando o número de marcas é 10.000 ou menos. Você ainda pode encontrar um recurso por marca quando o número excede 10.000.  

<sup>4</sup>esses recursos não são mais necessários com o Gerenciador de recursos e grupos de recursos do Azure.

> [!NOTE]
> Núcleos de máquina virtual tem um limite regional total. Eles também têm um limite regional série por tamanho, como Dv2 e F. Esses limites são aplicados separadamente. Por exemplo, considere uma assinatura com uma VM do Leste dos EUA com um limite total de núcleos de 30, um limite de núcleos da série A de 30 e um limite de núcleos da série D de 30. Esta assinatura pode implantar 30 VMs A1, ou 30 VMs D1 ou uma combinação dos dois para não exceder um total de 30 núcleos. Um exemplo de uma combinação é 10 VMs A1 e 20 VMs D1.  
> <!-- -->
> 
> 

