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
ms.openlocfilehash: b6587a3928c2ddf0d00c90e07643525314690e42
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318145"
---
### <a name="virtual-machines"></a>Máquinas Virtuais

|  |  |
|---------|---------|
| [Permitir imagem de VM personalizada de um Grupo de Recursos](../articles/governance/policy/samples/allow-custom-vm-image.md) |  Requer que imagens personalizadas venha de um grupo de recursos aprovado. Especifique o nome do grupo de recursos aprovado. |
| [SKUs permitidas para Contas de Armazenamento e Máquinas Virtuais](../articles/governance/policy/samples/allowed-skus-storage.md) | Exige que contas de armazenamento e máquinas virtuais usem SKUs aprovadas. Usa as políticas internas para garantir SKUs aprovadas. Especifique uma matriz de SKUs de máquinas virtuais aprovadas e uma matriz de SKUs de conta de armazenamento aprovadas. |
| [Imagens de VM aprovadas](../articles/governance/policy/samples/allowed-custom-images.md) | Exige que apenas imagens personalizadas aprovadas sejam implantadas no seu ambiente. Especifique uma matriz de IDs de imagens aprovadas. |
| [Auditar se a extensão não existir](../articles/governance/policy/samples/audit-extension-not-exist.md) | Audita se uma extensão não estiver implantada com uma máquina virtual. Especifique o distribuidor da extensão e o tipo para verificar se ela foi implantada. |
| [Extensões de VM não permitidas](../articles/governance/policy/samples/not-allowed-vm-extension.md) | Proíbe o uso de extensões especificadas. Especifique uma matriz que contém os tipos de extensão proibidos. |