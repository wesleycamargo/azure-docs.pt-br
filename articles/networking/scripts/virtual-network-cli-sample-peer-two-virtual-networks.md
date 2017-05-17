---
title: Exemplo de Script da CLI do Azure - Emparelhar duas redes virtuais | Microsoft Docs
description: Exemplo de Script da CLI do Azure - Emparelhar duas redes virtuais
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 04/19/2017
ms.author: kumud
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c63e65148056f06c24d41f3a47014e272562a078
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---

# <a name="peer-two-virtual-networks"></a>Emparelhar duas redes virtuais

Este script cria e conecta duas redes virtuais na mesma região através da rede do Azure. Depois de executar o script, você criará um emparelhamento entre duas redes virtuais.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[principal](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Emparelhar duas redes")]

## <a name="clean-up-deployment"></a>Limpar implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para criar um grupo de recursos, uma máquina virtual e todos os recursos relacionados. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) | Cria uma sub-rede e uma rede virtual do Azure. |
| [criar emparelhamento de VNET de rede virtual az](https://docs.microsoft.com/cli/azure/network/vnet/peering#create) | Cria um emparelhamento entre duas redes virtuais.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos adicionais de script de CLI de rede podem ser encontrados na [Documentação de visão geral da rede do Azure](../cli-samples.md).

