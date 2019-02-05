---
title: Amostra de script da CLI do Azure – Implantar a Pilha LAMP em um conjunto de dimensionamento de máquinas virtuais com balanceamento de carga | Microsoft Docs
description: Use uma extensão de script personalizado para implantar a Pilha LAMP em um conjunto de dimensionamento de máquinas virtuais com balanceamento de carga no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: ceda12b71a58153995a23eabace37b4373b57ef2
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155146"
---
# <a name="deploy-the-lamp-stack-in-a-load-balanced-virtual-machine-scale-set"></a>Implantar a pilha LAMP em um conjunto de dimensionamento de máquinas virtuais com balanceamento de carga

Este exemplo cria um conjunto de dimensionamento de máquinas virtuais e aplica uma extensão que executa um script personalizado para implantar a pilha LAMP em cada máquina virtual no conjunto de dimensionamento.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/build-stack.sh "Create virtual machine scale set with LAMP stack")]

## <a name="connect"></a>Connect

Use este código para ver como se conectar a suas VMs e seu conjunto de dimensionamento.

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/how-to-access.sh "Access the virtual machine scale set")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, o conjunto de dimensionamento e as VMs e todos os recursos relacionados.

```azurecli-interactive 
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar um grupo de recursos, uma máquina virtual, um conjunto de disponibilidade, um balanceador de carga e todos os recursos relacionados. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss#az_vmss_create) | Criar um conjunto de dimensionamento de máquinas virtuais |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Adicionar um ponto de extremidade com balanceamento de carga |
| [az vmss extension set](https://docs.microsoft.com/cli/azure/vmss/extension#az_vmss_extension_set) | Criar a extensão que executa o script personalizado na implantação de uma VM |
| [az vmss update-instances](https://docs.microsoft.com/cli/azure/vmss#az_vmss_update_instances) | Execute o script personalizado nas instâncias de VM que foram implantadas antes de a extensão ser aplicada ao conjunto de dimensionamento. |
| [az vmss scale](https://docs.microsoft.com/cli/azure/vmss#az_vmss_scale) | Escale verticalmente o conjunto de dimensionamento adicionando mais instâncias de VM. O script personalizado é executado nelas quando elas são implantadas. |
| [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip) | Obtenha os endereços IP das VMs criadas pela amostra. |
| [az network lb show](https://docs.microsoft.com/cli/azure/network/lb#az_network_lb_show) | Obtenha as portas de front-end e back-end usadas pelo balanceador de carga. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Os exemplos de script da CLI de máquina virtual adicionais podem ser encontrados na [documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
