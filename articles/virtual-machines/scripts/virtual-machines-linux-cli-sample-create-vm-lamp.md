---
title: "Amostra de script da CLI do Azure – Implantar a Pilha LAMP em um conjunto de dimensionamento de máquinas virtuais com balanceamento de carga | Microsoft Docs"
description: "Use uma extensão de script personalizado para implantar a Pilha LAMP em um conjunto de dimensionamento de máquinas virtuais com balanceamento de carga no Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2017
ms.author: allclark
ms.translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: 68cb08a8b40220639f880c83a44014b81598d45a
ms.contentlocale: pt-br
ms.lasthandoff: 04/06/2017

---

# <a name="deploy-the-lamp-stack-in-a-load-balanced-virtual-machine-scale-set"></a>Implantar a pilha LAMP em um conjunto de dimensionamento de máquinas virtuais com balanceamento de carga

Este exemplo cria um conjunto de dimensionamento de máquinas virtuais e aplica uma extensão que executa um script personalizado para implantar a pilha LAMP em cada máquina virtual no conjunto de dimensionamento.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/build-stack.sh "Criar um conjunto de dimensionamento de máquinas virtuais com a pilha LAMP")]

## <a name="connect"></a>Connect

Use este código para ver como se conectar a suas VMs e seu conjunto de dimensionamento.

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/how-to-access.sh "Acessar o conjunto de dimensionamento de máquinas virtuais")]

## <a name="clean-up-deployment"></a>Limpar implantação 

Execute o comando a seguir para remover o grupo de recursos, o conjunto de dimensionamento e as VMs e todos os recursos relacionados.

```azurecli
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar um grupo de recursos, uma máquina virtual, um conjunto de disponibilidade, um balanceador de carga e todos os recursos relacionados. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss#create) | Criar um conjunto de dimensionamento de máquinas virtuais |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#create) | Adicionar um ponto de extremidade com balanceamento de carga |
| [az vmss extension set](https://docs.microsoft.com/cli/azure/vmss/extension#set) | Criar a extensão que executa o script personalizado na implantação de uma VM |
| [az vmss update-instances](https://docs.microsoft.com/cli/azure/vmss#update-instances) | Execute o script personalizado nas instâncias de VM que foram implantadas antes de a extensão ser aplicada ao conjunto de dimensionamento. |
| [az vmss scale](https://docs.microsoft.com/cli/azure/vmss#scale) | Escale verticalmente o conjunto de dimensionamento adicionando mais instâncias de VM. O script personalizado é executado nelas quando elas são implantadas. |
| [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip#list) | Obtenha os endereços IP das VMs criadas pela amostra. |
| [az network lb show](https://docs.microsoft.com/cli/azure/network/lb#show) | Obtenha as portas de front-end e back-end usadas pelo balanceador de carga. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Os exemplos de script da CLI de máquina virtual adicionais podem ser encontrados na [documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

