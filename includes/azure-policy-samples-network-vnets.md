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
ms.openlocfilehash: b7bbf1fb8ffc1f57a53682d3baf288077b144572
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664396"
---
### <a name="virtual-networks"></a>Redes Virtuais

|  |  |
|---------|---------|
| [SKUs de Gateway de Aplicativo aprovadas](../articles/azure-policy/scripts/allowed-app-gate-sku.md) | Exige que os gateways de aplicativo usem uma SKU aprovada. Especifique uma matriz de SKUs aprovados. |
| [Nenhum emparelhamento de rede para a rede ER](../articles/azure-policy/scripts/no-peering-er-net.md) | Proíbe associar um emparelhamento de rede a uma rede em um grupo de recursos especificado. Use para impedir a conexão à infraestrutura de rede gerenciada central. Especifique o nome do grupo de recursos para impedir a associação. |
| [Nenhuma Tabela de Rotas Definida pelo Usuário](../articles/azure-policy/scripts/no-user-def-route-table.md)  |Proíbe que redes virtuais sejam implantadas com uma tabela de rotas definida pelo usuário. |
| [NSG X em cada sub-rede](../articles/azure-policy/scripts/nsg-on-subnet.md) | Exige que um grupo de segurança de rede específico seja usado em cada sub-rede virtual. Você especifica a ID do grupo de segurança de rede a ser usada. |
| [Usar sub-rede aprovada para adaptadores de rede de VM](../articles/azure-policy/scripts/use-approved-subnet-vm-nics.md) | Exige que os adaptadores de rede usem uma sub-rede aprovada. Especifique a ID da sub-rede aprovada. |
| [Usar vNet aprovada para adaptadores de rede de VM](../articles/azure-policy/scripts/use-approved-vnet-vm-nics.md) | Exige que os adaptadores de rede usem uma rede virtual aprovada. Especifique a ID da rede virtual aprovada. |