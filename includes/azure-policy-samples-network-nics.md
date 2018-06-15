---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 388d6641fb77d09c1706623b84ec256573807747
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664393"
---
### <a name="network-interfaces"></a>Interfaces de Rede

|  |  |
|---------|---------|
| [NSG X em cada NIC](../articles/azure-policy/scripts/nsg-on-nic.md) | Exige que um grupo de segurança de rede específico seja usado com cada adaptador de rede virtual. Você especifica a ID do grupo de segurança de rede a ser usada. |
| [Usar sub-rede aprovada para adaptadores de rede de VM](../articles/azure-policy/scripts/use-approved-subnet-vm-nics.md) | Exige que os adaptadores de rede usem uma sub-rede aprovada. Especifique a ID da sub-rede aprovada. |
| [Usar vNet aprovada para adaptadores de rede de VM](../articles/azure-policy/scripts/use-approved-vnet-vm-nics.md) | Exige que os adaptadores de rede usem uma rede virtual aprovada. Especifique a ID da rede virtual aprovada. |