---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 04/13/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 3157f5db58be5735fa7b194393b0ab6d0e143a91
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33830423"
---
## <a name="create-a-media-services-account"></a>Criar uma conta de Serviços de Mídia

Primeiro, você precisa criar uma conta dos Serviços de Mídia. Esta seção mostra o que é necessário para criar a conta usando a CLI 2.0.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos usando o comando a seguir. Um grupo de recursos do Azure é um contêiner lógico em que recursos, como contas dos Serviços de Mídia do Azure e contas de armazenamento associadas, são implantados e gerenciados.

```azurecli-interactive
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Ao criar uma conta dos Serviços de Mídia, você precisa fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está conectada à sua conta de Serviços de Mídia. 

Você deve ter uma conta de armazenamento **Primária**, e pode ter quantas contas de armazenamento **Secundárias** você quiser associadas à sua conta dos Serviços de Mídia. Os Serviços de Mídia oferecem suporte a contas **v2 para fins gerais** (GPv2) ou contas **v1 para fins gerais** (GPv1). As contas somente blob não são permitidas como **Primárias**. Se quiser saber mais sobre as contas de armazenamento, confira [Opções de conta de Armazenamento do Azure](../articles/storage/common/storage-account-options.md). 

Para saber mais sobre como as contas de armazenamento são usadas nos Serviços de Mídia, confira [Contas de armazenamento](../articles/media-services/latest/storage-account-concept.md).

O comando a seguir cria uma conta de armazenamento que será associada à conta dos Serviços de Mídia. No script a seguir, você pode substituir `storageaccountforams` pelo seu valor. O nome da conta deve ter menos de 24 caracteres.

```azurecli-interactive
az storage account create --name storageaccountforams \  
--kind StorageV2 \
--sku Standard_RAGRS \
--resource-group amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Criar uma conta de Serviços de Mídia

O comando da CLI do Azure a seguir cria uma nova conta dos Serviços de Mídia. Você pode substituir os seguintes valores: `amsaccount` `storageaccountforams` (deve corresponder ao valor fornecido de conta de armazenamento) e `amsResourceGroup` (deve corresponder ao valor fornecido de grupo de recursos).

```azurecli-interactive
az ams account create --name amsaccount --resource-group amsResourceGroup --storage-account storageaccountforams
```
