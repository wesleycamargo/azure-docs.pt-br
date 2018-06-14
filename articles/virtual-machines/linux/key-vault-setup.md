---
title: Configurar o Azure Key Vault para VMs do Linux | Microsoft Docs
description: Como configurar o Key Vault para uso com uma máquina virtual do Azure Resource Manager com a CLI 2.0.
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/24/2017
ms.author: singhkay
ms.openlocfilehash: 6bd039225062ac6010d432b930f601fe4678ed2c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
ms.locfileid: "30907144"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli-20"></a>Como configurar o Key Vault para máquinas virtuais com a CLI do Azure 2.0

Na pilha do Azure Resource Manager, os segredos/certificados são modelados como recursos que são fornecidos pelo Key Vault. Para saber mais sobre o Cofre de Chaves do Azure, consulte [O que é o Cofre de Chaves do Azure?](../../key-vault/key-vault-whatis.md) Para que o Key Vault seja usado com VMs do Azure Resource Manager, a propriedade *EnabledForDeployment* no Key Vault deverá ser definida como true. Este artigo mostra como configurar o Key Vault para uso com VMs (máquinas virtuais) do Azure usando a CLI do Azure 2.0. Você também pode executar essas etapas com a [CLI do Azure 1.0](key-vault-setup-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para realizar essas etapas, é preciso ter a [CLI do Azure 2.0](/cli/azure/install-az-cli2) mais recente instalada e conectada a uma conta do Azure usando o [logon az](/cli/azure/reference-index#az_login).

## <a name="create-a-key-vault"></a>Criar um cofre de chaves
Crie um Key Vault e atribua a política de implantação com [az keyvault create](/cli/azure/keyvault#az_keyvault_create). O exemplo a seguir cria um Key Vault chamado `myKeyVault` no grupo de recursos `myResourceGroup`:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Atualizar um Key Vault para uso com VMs
Defina a política de implantação em um Key Vault existente com [az keyvault update](/cli/azure/keyvault#az_keyvault_update). O exemplo a seguir atualiza o cofre de chaves chamado `myKeyVault` no grupo de recursos `myResourceGroup`:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Usar modelos para configurar o Cofre de Chaves
Ao usar um modelo, você precisa definir a propriedade `enabledForDeployment` como `true` para o recurso do Key Vault da seguinte forma:

```json
{
    "type": "Microsoft.KeyVault/vaults",
    "name": "ContosoKeyVault",
    "apiVersion": "2015-06-01",
    "location": "<location-of-key-vault>",
    "properties": {
    "enabledForDeployment": "true",
    ....
    ....
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Para obter outras opções que você pode definir ao criar um Key Vault usando modelos, consulte [Create a key vault](https://azure.microsoft.com/documentation/templates/101-key-vault-create/) (Criar um Key Vault).
