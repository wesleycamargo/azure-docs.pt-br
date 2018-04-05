---
title: Exemplo de Script da CLI do Azure - Emparelhar duas redes virtuais | Microsoft Docs
description: Exemplo de Script da CLI do Azure - Emparelhar duas redes virtuais.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: 244d7f6ff64643386c417d708f7fb1e9bbc34209
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="peer-two-virtual-networks"></a>Emparelhar duas redes virtuais

Este script cria e conecta duas redes virtuais na mesma região através da rede do Azure. Depois de executar o script, você terá um emparelhamento entre duas redes virtuais.

Você pode executar o script do Azure [Cloud Shell](https://shell.azure.com/bash) ou de uma instalação local da CLI do Azure. Se você usar a CLI localmente, este script requer que você esteja executando a versão 2.0.28 ou posterior. Para localizar a versão instalada, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). Se estiver executando a CLI localmente, você também precisará executar o `az login` para criar uma conexão com o Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Peer two networks")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados:

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para criar um grupo de recursos, uma máquina virtual e todos os recursos relacionados. Cada comando na tabela a seguir redireciona para a documentação específica do comando:

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Cria uma sub-rede e uma rede virtual do Azure. |
| [criar emparelhamento de VNET de rede virtual az](/cli/azure/network/vnet/peering#az_network_vnet_peering_create) | Cria um emparelhamento entre duas redes virtuais.  |
| [az group delete](/cli/azure/vm/extension#az_vm_extension_set) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Amostras de script CLI de rede virtual adicionais podem ser encontradas em [Amostras de CLI de rede virtual](../cli-samples.md).
