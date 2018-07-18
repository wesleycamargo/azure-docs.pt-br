---
title: Exemplos da CLI do Azure 2.0 – Usar uma imagem de VM personalizada | Microsoft Docs
description: Exemplos da CLI do Azure 2.0
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 056a1ff59ca5bcaf460d98db7d50fd3566f11427
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-virtual-machine-scale-set-from-a-custom-vm-image-with-the-azure-cli-20"></a>Criar um conjunto de dimensionamento de máquinas virtuais de uma imagem de VM personalizada com a CLI do Azure 2.0
Esse script cria um conjunto de dimensionamento de máquinas virtuais que usa uma imagem de VM personalizada como a origem para as instâncias de VM.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/use-custom-vm-image/use-custom-vm-image.sh "Create a virtual machine scale set with a custom VM image")]

## <a name="clean-up-deployment"></a>Limpar a implantação
Execute o comando a seguir para remover o grupo de recursos, o conjunto de dimensionamento e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script
Este script usa os comandos a seguir para criar um grupo de recursos, um conjunto de dimensionamento de máquinas virtuais e todos os recursos relacionados. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/ad/group#az_ad_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | Cria um conjunto de dimensionamento de máquinas virtuais e a conecta à rede virtual, à sub-rede e ao grupo de segurança de rede. Um balanceador de carga também é criado para distribuir o tráfego para as diversas instâncias de VM. Esse comando também especifica a imagem da VM a ser usada e as credenciais administrativas.  |
| [az group delete](/cli/azure/ad/group#delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre a CLI do Azure 2.0, consulte a [documentação da CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/overview).

Outros exemplos de script da CLI do Azure 2.0 de conjunto de dimensionamento de máquinas virtuais podem ser encontrados na [documentação do conjunto de dimensionamento de máquinas virtuais do Azure](../cli-samples.md).