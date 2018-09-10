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
ms.openlocfilehash: eedce1ef3a4e7d56cfaf3142a72e370c96f9f575
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664387"
---
### <a name="virtual-machines"></a>Máquinas Virtuais

|  |  |
|---------|---------|
| [Permitir imagem de VM personalizada de um Grupo de Recursos](../articles/azure-policy/scripts/allow-custom-vm-image.md) |  Requer que imagens personalizadas venha de um grupo de recursos aprovado. Especifique o nome do grupo de recursos aprovado. |
| [SKUs permitidas para Contas de Armazenamento e Máquinas Virtuais](../articles/azure-policy/scripts/allowed-skus-storage.md) | Exige que contas de armazenamento e máquinas virtuais usem SKUs aprovadas. Usa as políticas internas para garantir SKUs aprovadas. Especifique uma matriz de SKUs de máquinas virtuais aprovadas e uma matriz de SKUs de conta de armazenamento aprovadas. |
| [Imagens de VM aprovadas](../articles/azure-policy/scripts/allowed-custom-images.md) | Exige que apenas imagens personalizadas aprovadas sejam implantadas no seu ambiente. Especifique uma matriz de IDs de imagens aprovadas. |
| [Auditar se a extensão não existir](../articles/azure-policy/scripts/audit-ext-not-exist.md) | Audita se uma extensão não estiver implantada com uma máquina virtual. Especifique o distribuidor da extensão e o tipo para verificar se ela foi implantada. |
| [Extensões de VM não permitidas](../articles/azure-policy/scripts/not-allowed-vm-ext.md) | Proíbe o uso de extensões especificadas. Especifique uma matriz que contém os tipos de extensão proibidos. |