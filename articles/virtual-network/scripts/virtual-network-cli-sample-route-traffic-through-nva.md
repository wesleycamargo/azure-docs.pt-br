---
title: Exemplo de script da CLI do Azure - Rotear o tráfego por meio de uma solução de virtualização de rede | Microsoft Docs
description: Exemplo de script da CLI do Azure - Rotear o tráfego por meio de uma solução de virtualização de rede de firewall.solução de virtualização.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: 381cd913df41a0bbe03b6c12c44c62ec5654d639
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2018
ms.locfileid: "41918039"
---
# <a name="route-traffic-through-a-network-virtual-appliance-script-sample"></a>Rotear o tráfego por meio de um exemplo de script de solução de virtualização de rede

Este exemplo de script cria uma rede virtual com sub-redes de front-end e back-end. Ele também cria uma VM com o encaminhamento de IP habilitado para rotear o tráfego entre as duas sub-redes. Depois de executar o script, você pode implantar o software de rede, como um aplicativo de firewall, à VM.

Você pode executar o script do Azure [Cloud Shell](https://shell.azure.com/bash) ou de uma instalação local da CLI do Azure. Se você usar a CLI localmente, este script requer que você esteja executando a versão 2.0.28 ou posterior. Para localizar a versão instalada, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). Se estiver executando a CLI localmente, você também precisará executar o `az login` para criar uma conexão com o Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados:

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para criar um grupo de recursos, uma rede virtual e grupos de segurança de rede. Cada comando na tabela a seguir redireciona para a documentação específica do comando:

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Cria uma rede virtual do Azure e uma sub-rede front-end. |
| [az network subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Cria as sub-redes back-end e DMZ. |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | Cria um endereço IP público para acessar a VM da Internet. |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | Cria uma interface de rede virtual e habilita o encaminhamento IP nela. |
| [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) | Cria um grupo de segurança de rede (NSG). |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Cria regras de NSG que permitem as portas HTTP e HTTPS de entrada para a VM. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)| Associa os NSGs e tabelas de rotas às sub-redes. |
| [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create)| Cria uma tabela de rotas com todas as rotas. |
| [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create)| Cria as rotas para rotear o tráfego entre sub-redes e a Internet por meio da VM. |
| [az vm create](/cli/azure/vm#az_vm_create) | Cria uma máquina virtual e anexa o NIC a ela. Este comando também especifica a imagem de máquina virtual a ser usada e as credenciais administrativas. |
| [az group delete](/cli/azure/group#az_group_delete) | Exclui um grupo de recursos e todos os seus recursos contidos nele. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Amostras de script CLI de rede virtual adicionais podem ser encontradas em [Amostras de CLI de rede virtual](../cli-samples.md).
