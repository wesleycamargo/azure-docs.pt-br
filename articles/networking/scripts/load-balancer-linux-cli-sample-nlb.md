---
title: "Exemplo de Script CLI do Azure - Balancear o tráfego de VMs para alta disponibilidade | Microsoft Docs"
description: "Exemplo de Script CLI do Azure - Balancear o tráfego de VMs para alta disponibilidade"
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 04/21/2017
ms.author: kumud
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5fc71943c2fdf1a6a64bf96176deb5f6bc4e26d7
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---

# <a name="load-balance-traffic-to-vms-for-high-availability"></a>Balancear o tráfego de VMs para alta disponibilidade

Este exemplo de script cria todos os componentes necessários para executar várias máquinas virtuais do Ubuntu configuradas em uma alta disponibilidade e configuração de balanceamento de carga. Após a execução do script, você terá três máquinas virtuais, associadas a um Conjunto de Disponibilidade do Azure, e acessíveis por meio de um Azure Load Balancer. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[principal](../../../cli_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.sh "Criação rápida de VM")]

## <a name="clean-up-deployment"></a>Limpar implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar um grupo de recursos, uma máquina virtual, um conjunto de disponibilidade, um balanceador de carga e todos os recursos relacionados. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) | Cria uma sub-rede e uma rede virtual do Azure. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#create) | Cria um endereço IP público com um endereço IP estático e um nome DNS associado. |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#create) | Cria um balanceador de carga de rede (NLB) do Azure. |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#create) | Cria uma investigação NLB. Uma investigação NLB é usada para monitorar cada VM no conjunto do NLB. Se qualquer VM ficar inacessível, o tráfego não é roteado para a máquina virtual. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#create) | Cria uma regra NLB. Neste exemplo, uma regra é criada para a porta 80. Conforme o tráfego HTTP chega ao NLB, ele é roteado para a porta 80, uma das VMs no conjunto do NLB. |
| [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#create) | Cria uma regra de conversão de endereços de rede (NAT) do NLB.  Regras de NAT mapeiam uma porta do NLB para uma porta em uma máquina virtual. Neste exemplo, é criada uma regra de NAT para o tráfego SSH para cada VM no conjunto do NLB.  |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#create) | Cria um grupo de segurança de rede (NSG), que é um limite de segurança entre a Internet e a máquina virtual. |
| [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule#create) | Cria uma regra NSG para permitir o tráfego de entrada. Neste exemplo, a porta 22 é aberta para o tráfego SSH. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic#create) | Cria uma placa de rede virtual e a anexa à rede virtual, à sub-rede e ao NSG. |
| [az vm availability-set create](https://docs.microsoft.com/cli/azure/network/lb/rule#create) | Cria um conjunto de disponibilidade. Conjuntos de disponibilidade garantem o funcionamento de aplicativos, distribuindo as máquinas virtuais em recursos físicos, de modo que, se ocorrer uma falha, todo o conjunto não é afetado. |
| [az vm create](/cli/azure/vm#create) | Cria a máquina virtual e a conecta a placa de rede, a rede virtual, a sub-rede e o NSG. Este comando também especifica a imagem da máquina virtual a ser usada e as credenciais administrativas.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos adicionais de script de CLI de rede do Azure podem ser encontrados na [Documentação de rede do Azure](../cli-samples.md).
