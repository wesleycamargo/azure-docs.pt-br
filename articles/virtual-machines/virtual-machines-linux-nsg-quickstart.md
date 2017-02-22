---
title: Abrir portas para uma VM do Linux com a CLI 2.0 do Azure | Microsoft Docs
description: "Saiba como abrir uma porta e criar um ponto de extremidade para a VM Linux usando o modelo de implantação do Azure Resource Manager e a CLI 2.0 do Azure (Visualização)"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: eef9842b-495a-46cf-99a6-74e49807e74e
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/8/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 652a8a0dcb3216e9b762b85de56673beda74edc4
ms.openlocfilehash: aae141307a0ea68f0d9e481cf22a9055216c98c0


---
# <a name="opening-ports-and-endpoints-to-a-linux-vm-in-azure-using-the-azure-cli-20"></a>Abrindo portas e pontos de extremidade para uma VM Linux no Azure usando a CLI 2.0 do Azure
No Azure, você abre uma porta, ou cria um ponto de extremidade, para uma VM (máquina virtual) criando um filtro de rede ou uma sub-rede ou interface de rede de VM. Coloque os filtros, que controlam o tráfego de entrada e saída, em um Grupo de Segurança de Rede anexado ao recurso que recebe o tráfego. Vamos usar um exemplo comum de tráfego da Web na porta 80. Este artigo mostra como abrir uma porta para uma VM usando a CLI 2.0 do Azure (Visualização).


## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa
Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- [CLI do Azure 1.0](virtual-machines-linux-nsg-quickstart-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos
- [CLI 2.0 do Azure (Visualização)](#quick-commands) – nossa CLI da próxima geração para o modelo de implantação de gerenciamento de recursos (este artigo)


## <a name="quick-commands"></a>Comandos rápidos
Para criar regras e um Grupo de Segurança de Rede, é necessário ter a última [CLI 2.0 do Azure (Visualização)](/cli/azure/install-az-cli2) instalada e conectada a uma conta do Azure usando [az login](/cli/azure/#login).

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluem `myResourceGroup`, `myNetworkSecurityGroup` e `myVnet`.

Crie o grupo de segurança de rede com [az network nsg create](/cli/azure/network/nsg#create). O seguinte exemplo cria um grupo de segurança de rede chamado `myNetworkSecurityGroup` na localização `westus`:

```azurecli
az network nsg create --resource-group myResourceGroup --location westus \
    --name myNetworkSecurityGroup
```

Adicione uma regra com [az network nsg rule create](/cli/azure/network/nsg/rule#create) para permitir o tráfego HTTP para o servidor Web (ou faça ajustes para seu próprio cenário, como acesso ao SSH ou conectividade de banco de dados). O exemplo a seguir cria uma regra denominada `myNetworkSecurityGroupRule` para permitir o tráfego TCP na porta 80:

```azurecli
az network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1000 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 80 --access allow
```

Associe o Grupo de Segurança de Rede à NIC (adaptador de rede) da VM com [az network nic update](/cli/azure/network/nic#update). O exemplo a seguir associa uma NIC existente denominada `myNic` ao Grupo de Segurança de Rede denominado `myNetworkSecurityGroup`:

```azurecli
az network nic update --resource-group myResourceGroup --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Como alternativa, você pode associar o Grupo de Segurança de Rede a uma sub-rede de uma rede virtual com [az network vnet subnet update](/cli/azure/network/vnet/subnet#update) em vez de apenas ao adaptador de rede em uma única VM. O exemplo a seguir associa uma sub-rede existente denominada `mySubnet` na rede virtual `myVnet` ao Grupo de Segurança de Rede denominado `myNetworkSecurityGroup`:

```azurecli
az network vnet subnet update --resource-group myResourceGroup \
    --vnet-name myVnet --name mySubnet --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Mais informações sobre os Grupos de Segurança de Rede
Os comandos rápidos aqui permitem que você coloque tudo em funcionamento com o tráfego que flui para sua VM. Os Grupos de Segurança de Rede fornecem muitos recursos excelentes e granularidade para controlar o acesso aos recursos. Você pode ler mais sobre a [criação de um Grupo de Segurança de Rede e as regras ACL aqui](../virtual-network/virtual-networks-create-nsg-arm-cli.md).

Você pode definir Grupos de Segurança de Rede e regras de ACL como parte dos modelos do Azure Resource Manager. Leia mais sobre a [criação de Grupos de Segurança de Rede com modelos](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Se precisar usar o encaminhamento de porta para mapear uma porta externa exclusiva para uma porta interna em sua VM, use um balanceador de carga e regras de NAT (Conversão de Endereços de Rede). Por exemplo, talvez você queira expor a porta TCP 8080 externamente e direcionar o tráfego para a porta TCP 80 em uma VM. Você pode aprender sobre a [criação de um balanceador de carga para a Internet](../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="next-steps"></a>Próximas etapas
Neste exemplo, você criou uma regra simples para permitir o tráfego HTTP. Você pode encontrar informações sobre a criação de ambientes mais detalhados nos seguintes artigos:

* [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [O que é um NSG (grupo de segurança de rede)?](../virtual-network/virtual-networks-nsg.md)
* [Visão Geral do Azure Resource Manager para Balanceadores de Carga](../load-balancer/load-balancer-arm.md)




<!--HONumber=Feb17_HO3-->


