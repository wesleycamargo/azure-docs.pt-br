---
title: Exemplo da CLI – Criar um registro e uma zona DNS para um nome de domínio – Azure | Microsoft Docs
description: Este exemplo de script da CLI do Azure mostra como criar um registro e uma zona DNS para um nome de domínio
services: load-balancer
documentationcenter: traffic-manager
author: vhorne
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/30/2018
ms.author: victorh
ms.openlocfilehash: 5015bd59a417481303c6ef2156feec9092a95a5b
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54464588"
---
# <a name="azure-cli-script-example-create-a-dns-zone-and-record"></a>Exemplo de script da CLI do Azure: Criar uma zona e registro de DNS

Este exemplo de script da CLI do Azure cria um registro e uma zona DNS para um nome de domínio. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

```azurecli-interactive

# Create a resource group.
az group create \
  -n myResourceGroup \
  -l eastus

# Create a DNS zone. Substitute zone name "contoso.com" with the values for your own.

az network dns zone create \
  -g MyResourceGroup \
  -n contoso.com

# Create a DNS record. Substitute zone name "contoso.com" and IP address "1.2.3.4* with the values for your own.

az network dns record-set a add-record \
  -g MyResourceGroup \
  -z contoso.com \
  -n www \
  -a 1.2.3.4

# Get a list the DNS records in your zone
az network dns record-set list \
  -g MyResourceGroup \ 
  -z contoso.com
```

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, a zona DNS e todos os recursos relacionados.

```azurecli
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar um grupo de recursos, uma máquina virtual, um conjunto de disponibilidade, um balanceador de carga e todos os recursos relacionados. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) | Cria uma zona DNS do Azure. |
| [az network dns record-set a add-record](/cli/azure/network/dns/record-set) | Adiciona um registro *A* a uma zona DNS. |
| [az network dns record-set list](/cli/azure/network/dns/record-set#az-network-dns-record-set-a-list) | Lista todos os conjuntos de registros *A* em uma zona DNS. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

