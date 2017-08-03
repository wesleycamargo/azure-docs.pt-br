---
title: "Criar registro privado do contêiner do Docker - CLI do Azure | Microsoft Docs"
description: "Introdução à criação e gerenciamento de registros de contêiner privado do Docker com a CLI do Azure 2.0"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: na
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: nepeters
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: c7cdb1b13bf32388d18c2a25af28337a81861c1e
ms.contentlocale: pt-br
ms.lasthandoff: 07/12/2017

---

# <a name="create-a-managed-container-registry-using-the-azure-cli"></a>Criar um registro de contêiner gerenciado usando a CLI do Azure

O Registro de Contêiner do Azure é um serviço de registro de contêiner Docker gerenciado usado para armazenar imagens de contêiner de Docker particulares. Este guia detalha a criação de uma instância gerenciada do Registro de Contêiner do Azure usando a CLI do Azure.

Os registros de contêiner gerenciados do Azure estão em versão prévia e não estão disponíveis em todas as regiões.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este guia de início rápido exigirá a execução da CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *westcentralus*.

```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-container-registry"></a>Criar um registro de contêiner

Crie uma instância ACR usando o comando [az acr create](/cli/azure/acr#create).

> [!NOTE]
> Ao criar um registro, especifique um nome de domínio de nível superior exclusivo que contenha apenas letras e números.

 O nome do registro no exemplo é *myContainerRegistry1*, substitua por um nome exclusivo.

```azurecli
az acr create --name myContainerRegistry1 --resource-group myResourceGroup --sku Managed_Standard
```

Quando o registro é criado, o resultado é semelhante ao seguinte:

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-29T04:50:28.607134+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry1",
  "location": "westcentralus",
  "loginServer": "mycontainerregistry1.azurecr.io",
  "name": "myContainerRegistry1",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Managed_Standard",
    "tier": "Managed"
  },
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="log-in-to-acr-instance"></a>Fazer logon na instância ACR

Antes de enviar por push e pull imagens de contêiner, você deverá fazer logon na instância ACR. Para fazer isso, use o comando [az acr login](/cli/azure/acr#login).

```azurecli-interactive
az acr login --name myAzureContainerRegistry1
```

O comando retorna uma mensagem de 'Logon bem-sucedido' assim que ele for concluído.

## <a name="use-azure-container-registry"></a>Usar o Registro de Contêiner do Azure

### <a name="list-container-images"></a>Listar imagens de contêiner

Use os comandos de CLI `az acr` para consultar as imagens e marcas em um repositório.

> [!NOTE]
> Atualmente, o Registro de Contêiner não dá suporte ao comando `docker search` para consulta para imagens e marcas.

### <a name="list-repositories"></a>Listar repositórios

O seguinte exemplo lista os repositórios em um registro, no formato JSON (JavaScript Object Notation):

```azurecli
az acr repository list -n myContainerRegistry1 -o json
```

### <a name="list-tags"></a>Listar marcas

O seguinte exemplo lista as marcas no repositório **samples/nginx**, no formato JSON:

```azurecli
az acr repository show-tags -n myContainerRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou uma instância gerenciada do Registro de Contêiner do Azure usando a CLI do Azure.

> [!div class="nextstepaction"]
> [Enviar por push sua primeira imagem usando a CLI do Docker](container-registry-get-started-docker-cli.md)

