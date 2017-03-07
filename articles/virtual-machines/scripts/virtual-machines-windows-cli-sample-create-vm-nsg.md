---
title: Exemplo de script da CLI do Azure - Criar duas VMs com um NSG interno e um externo | Microsoft Docs
description: Exemplo de script da CLI do Azure - Criar duas VMs com um NSG interno e um externo
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: a172d73732354d31d717d8e2f3a5c5c43cbbd6dc
ms.openlocfilehash: 9ef17544582e944fda2ab8ea8b8098bb83e2ab57
ms.lasthandoff: 03/01/2017

---

# <a name="secure-network-traffic-between-virtual-machines"></a>Proteger o tráfego de rede entre máquinas virtuais

Este script cria duas máquinas virtuais e protege o tráfego de entrada para ambas. Uma máquina virtual está acessível na internet e um grupo de segurança de rede (NSG) configurado para permitir tráfego na porta 3389 e a porta 80. A segunda máquina virtual não está acessível na Internet e tem um NSG configurado para permitir apenas o tráfego da primeira máquina virtual. 

Antes de executar esse script, certifique-se de que uma conexão com o Azure foi criada usando o comando `az login`. Além disso, você deve alterar a variável $AdminPassword no início do script para atender aos requisitos exclusivos de complexidade da senha.

Esse exemplo funciona no shell Bash. Para opções sobre como executar scripts da CLI do Azure no Windows, veja [Execução da CLI do Azure no Windows](../virtual-machines-windows-cli-options.md).

## <a name="create-vm-sample-with-a-network-security-group"></a>Criar o exemplo VM com um grupo de segurança de rede

[!code-azurecli[principal](../../../cli_scripts/virtual-machine/create-vm-nsg/create-windows-vm-nsg.sh "Criar VM com NSG")]

## <a name="clean-up-deployment"></a>Limpar implantação 

Após a execução do exemplo de script, o comando a seguir pode ser usado para remover o Grupo de Recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para criar um grupo de recursos, uma máquina virtual e todos os recursos relacionados. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) | Cria uma sub-rede e uma rede virtual do Azure. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#create) | Cria uma sub-rede. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Cria a máquina virtual e a conecta a placa de rede, a rede virtual, a sub-rede e o NSG. Este comando também especifica a imagem de máquina virtual a ser usada e as credenciais administrativas.  |
| [az network nsg rule update](https://docs.microsoft.com/cli/azure/network/nsg/rule#update) | Atualiza uma regra NSG. Neste exemplo, a regra de back-end é atualizada para a passagem de tráfego somente da sub-rede de front-end. |
| [az network nsg rule list](https://docs.microsoft.com/cli/azure/network/nsg/rule#list) | Retorna informações sobre uma regra de grupo de segurança de rede. Neste exemplo, o nome da regra é armazenado em uma variável para uso posterior no script. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script CLI máquinas virtuais adicionais podem ser encontrados na [documentação da VM do Windows do Azure](../virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

