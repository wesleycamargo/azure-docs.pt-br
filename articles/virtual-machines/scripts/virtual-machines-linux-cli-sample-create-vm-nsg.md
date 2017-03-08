---
title: Exemplo de script da CLI do Azure - Criar duas VMs com um NSG interno e um externo | Microsoft Docs
description: Exemplo de script da CLI do Azure - Criar duas VMs com um NSG interno e um externo
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: a172d73732354d31d717d8e2f3a5c5c43cbbd6dc
ms.openlocfilehash: 864c59488ef68a2da04bb9bedacf24dff4f59a25
ms.lasthandoff: 03/01/2017

---

# <a name="secure-network-traffic-between-virtual-machines"></a>Proteger o tráfego de rede entre máquinas virtuais

Este script cria duas máquinas virtuais e protege o tráfego de entrada para ambas. Uma máquina virtual está acessível na internet e um grupo de segurança de rede (NSG) configurado para permitir tráfego na porta 22 e na porta 80. A segunda máquina virtual não está acessível na Internet e tem um NSG configurado para permitir apenas o tráfego da primeira máquina virtual. 

Antes de executar esse script, certifique-se de que uma conexão com o Azure foi criada usando o comando `az login`.

Este exemplo funciona em um shell Bash. Para opções sobre como executar scripts da CLI do Azure no cliente Windows, veja [Execução da CLI do Azure no Windows](../virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[principal](../../../cli_scripts/virtual-machine/create-vm-nsg/create-vm-nsg.sh "Criar VM com NSG")]

## <a name="clean-up-deployment"></a>Limpar implantação 

Após a execução do exemplo de script, o comando a seguir pode ser usado para remover o Grupo de Recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para criar um grupo de recursos, uma máquina virtual e todos os recursos relacionados. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) | Cria uma sub-rede e uma rede virtual do Azure. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#create) | Cria uma sub-rede. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Cria a máquina virtual e a conecta a placa de rede, a rede virtual, a sub-rede e o NSG. Este comando também especifica a imagem de máquina virtual a ser usada e as credenciais administrativas.  |
| [az network nsg rule list](https://docs.microsoft.com/cli/azure/network/nsg/rule#list) | Retorna informações sobre uma regra de grupo de segurança de rede. Neste exemplo, o nome da regra é armazenado em uma variável para uso posterior no script. |
| [az network nsg rule update](https://docs.microsoft.com/cli/azure/network/nsg/rule#update) | Atualiza uma regra NSG. Neste exemplo, a regra de back-end é atualizada para a passagem de tráfego somente da sub-rede de front-end. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Os exemplos de script da CLI de máquina virtual adicionais podem ser encontrados na [documentação da VM Linux do Azure](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

