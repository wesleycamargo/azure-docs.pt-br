---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
origin.date: 05/17/2018
ms.date: 11/12/2018
ms.author: v-biyu
ms.custom: include file
ms.openlocfilehash: f189843e865863d9b4088363e691ebc42ad9f2a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60865714"
---
### <a name="network-interfaces"></a>Interfaces de Rede

|  |  |
|---------|---------|
| [NSG X em cada NIC](../articles/governance/policy/samples/nsg-on-nic.md) | Exige que um grupo de segurança de rede específico seja usado com cada adaptador de rede virtual. Você especifica a ID do grupo de segurança de rede a ser usada. |
| [Usar sub-rede aprovada para adaptadores de rede de VM](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | Exige que os adaptadores de rede usem uma sub-rede aprovada. Especifique a ID da sub-rede aprovada. |
| [Usar vNet aprovada para adaptadores de rede de VM](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | Exige que os adaptadores de rede usem uma rede virtual aprovada. Especifique a ID da rede virtual aprovada. |