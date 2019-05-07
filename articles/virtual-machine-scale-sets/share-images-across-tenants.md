---
title: Compartilhar imagens da Galeria entre locatários no Azure | Microsoft Docs
description: Aprenda a compartilhar imagens de VM entre locatários do Azure usando Shared galerias de imagens.
services: virtual-machine-scale-sets
author: cynthn
manager: jeconnoc
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: cbaaac629fd013602eed75cc7dc357f13a62e3b1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160119"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Compartilhar imagens da Galeria de VM entre locatários do Azure

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Criar um conjunto de dimensionamento usando CLI do Azure

Entrar a entidade de serviço para o locatário 1 usando a appID, a chave do aplicativo e a ID do locatário 1. Você pode usar `az account show --query "tenantId"` para obter IDs de locatário se necessário.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Conecte-se a entidade de serviço para o locatário 2 usando o appID, a chave do aplicativo e a ID do locatário 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Criar o conjunto de dimensionamento. Substitua as informações no exemplo pelos seus próprios.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Próximas etapas

Se você tiver algum problema, você poderá [solucionar problemas de galerias de imagens compartilhadas](troubleshooting-shared-images.md).