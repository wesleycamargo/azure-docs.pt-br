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
ms.openlocfilehash: eadff6f61bd9e5979ae835230de53562ff8542d1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003596"
---
### <a name="virtual-machines"></a>Máquinas Virtuais

|  |  |
|---------|---------|
| [Permitir imagem de VM personalizada de um Grupo de Recursos](../articles/governance/policy/samples/allow-custom-vm-image.md) |  Requer que imagens personalizadas venha de um grupo de recursos aprovado. Especifique o nome do grupo de recursos aprovado. |
| [SKUs permitidas para Contas de Armazenamento e Máquinas Virtuais](../articles/governance/policy/samples/allowed-skus-storage.md) | Exige que contas de armazenamento e máquinas virtuais usem SKUs aprovadas. Usa as políticas internas para garantir SKUs aprovadas. Especifique uma matriz de SKUs de máquinas virtuais aprovadas e uma matriz de SKUs de conta de armazenamento aprovadas. |
| [Imagens de VM aprovadas](../articles/governance/policy/samples/allowed-custom-images.md) | Exige que apenas imagens personalizadas aprovadas sejam implantadas no seu ambiente. Especifique uma matriz de IDs de imagens aprovadas. |
| [Auditar se a extensão não existir](../articles/governance/policy/samples/audit-ext-not-exist.md) | Audita se uma extensão não estiver implantada com uma máquina virtual. Especifique o distribuidor da extensão e o tipo para verificar se ela foi implantada. |
| [Extensões de VM não permitidas](../articles/governance/policy/samples/not-allowed-vm-ext.md) | Proíbe o uso de extensões especificadas. Especifique uma matriz que contém os tipos de extensão proibidos. |