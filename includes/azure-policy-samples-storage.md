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
ms.openlocfilehash: b967558828c42331b3702fe90ce842fd1c0032bd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003577"
---
## <a name="storage"></a>Armazenamento

|  |  |
|---------|---------|
| [SKUs permitidas para Contas de Armazenamento e Máquinas Virtuais](../articles/governance/policy/samples/allowed-skus-storage.md) | Exige que contas de armazenamento e máquinas virtuais usem SKUs aprovadas. Usa as políticas internas para garantir SKUs aprovadas. Especifique uma matriz de SKUs de máquinas virtuais aprovadas e uma matriz de SKUs de conta de armazenamento aprovadas. |
| [SKUs de conta de armazenamento permitidas](../articles/governance/policy/samples/allowed-stor-acct-skus.md) | Exige que contas de armazenamento usem uma SKU aprovada. Especifique uma matriz de SKUs aprovados. |
| [Negar camadas de acesso esporádico para contas de armazenamento](../articles/governance/policy/samples/deny-cool-access-tiering.md) | Proíbe o uso de camadas de acesso esporádico para contas de armazenamento de blobs.  |
| [Assegurar tráfego https somente para contas de armazenamento](../articles/governance/policy/samples/ensure-https-stor-acct.md) | Exige que contas de armazenamento usem tráfego HTTPS.  |
| [Assegurar a criptografia de arquivo de armazenamento](../articles/governance/policy/samples/ensure-store-file-enc.md) | Exige que a criptografia de arquivo esteja habilitada para contas de armazenamento.  |
| [Exigir criptografia de conta de armazenamento](../articles/governance/policy/samples/req-store-acct-enc.md) | Exige que a conta de armazenamento use criptografia de blob.  |