---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ad4b244b58d741ad45463297df5bd358f3ae9918
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449893"
---
Antes de atribuir uma função RBAC para uma entidade de segurança, determine o escopo de acesso que a entidade de segurança deve ter. As práticas recomendadas ditam que sempre é melhor conceder o escopo mais restrito possível.

A lista a seguir descreve os níveis em que você pode definir o escopo de acesso aos recursos de blob e fila do Azure, começando com o escopo mais restrito:

- **Um contêiner individual.** A este escopo, uma entidade de segurança tem acesso a todos os blobs no contêiner, bem como propriedades do contêiner e metadados.
- **Uma fila individual.** A este escopo, uma entidade de segurança tem acesso às mensagens na fila, bem como propriedades da fila e metadados.
- **A conta de armazenamento.** A este escopo, uma entidade de segurança tem acesso a todos os contêineres e seus blobs, ou para todas as filas e suas mensagens.
- **O grupo de recursos.** A este escopo, uma entidade de segurança tem acesso a todos os contêineres ou filas em todas as contas de armazenamento no grupo de recursos.
- **A assinatura.** A este escopo, uma entidade de segurança tem acesso a todos os contêineres ou filas em todas as contas de armazenamento em todos os grupos de recursos na assinatura.
