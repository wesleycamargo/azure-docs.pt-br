---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 841b95e3e1cff09a15b4158bc55d46f782d32d41
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003567"
---
### <a name="network-interfaces"></a>Interfaces de Rede

|  |  |
|---------|---------|
| [NSG X em cada NIC](../articles/governance/policy/samples/nsg-on-nic.md) | Exige que um grupo de segurança de rede específico seja usado com cada adaptador de rede virtual. Você especifica a ID do grupo de segurança de rede a ser usada. |
| [Usar sub-rede aprovada para adaptadores de rede de VM](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | Exige que os adaptadores de rede usem uma sub-rede aprovada. Especifique a ID da sub-rede aprovada. |
| [Usar vNet aprovada para adaptadores de rede de VM](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | Exige que os adaptadores de rede usem uma rede virtual aprovada. Especifique a ID da rede virtual aprovada. |