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
ms.openlocfilehash: 3009948c72b0cad6ba603a3ccbe35db8d93fe2c7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664413"
---
## <a name="storage"></a>Armazenamento

|  |  |
|---------|---------|
| [SKUs permitidas para Contas de Armazenamento e Máquinas Virtuais](../articles/azure-policy/scripts/allowed-skus-storage.md) | Exige que contas de armazenamento e máquinas virtuais usem SKUs aprovadas. Usa as políticas internas para garantir SKUs aprovadas. Especifique uma matriz de SKUs de máquinas virtuais aprovadas e uma matriz de SKUs de conta de armazenamento aprovadas. |
| [SKUs de conta de armazenamento permitidas](../articles/azure-policy/scripts/allowed-stor-acct-skus.md) | Exige que contas de armazenamento usem uma SKU aprovada. Especifique uma matriz de SKUs aprovados. |
| [Negar camadas de acesso esporádico para contas de armazenamento](../articles/azure-policy/scripts/deny-cool-access-tiering.md) | Proíbe o uso de camadas de acesso esporádico para contas de armazenamento de blobs.  |
| [Assegurar tráfego https somente para contas de armazenamento](../articles/azure-policy/scripts/ensure-https-stor-acct.md) | Exige que contas de armazenamento usem tráfego HTTPS.  |
| [Assegurar a criptografia de arquivo de armazenamento](../articles/azure-policy/scripts/ensure-store-file-enc.md) | Exige que a criptografia de arquivo esteja habilitada para contas de armazenamento.  |
| [Exigir criptografia de conta de armazenamento](../articles/azure-policy/scripts/req-store-acct-enc.md) | Exige que a conta de armazenamento use criptografia de blob.  |