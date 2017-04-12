---
title: Como implantar a VM do Linux em uma rede existente com a CLI do Azure 2.0 | Microsoft Docs
description: "Aprenda a implantar uma máquina virtual Linux em uma rede virtual existente usando a CLI do Azure 2.0"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: a43e740bde8d91a47b84787e4bf72e4667b84de6
ms.lasthandoff: 04/03/2017


---

# <a name="deploy-a-linux-vm-into-an-existing-virtual-network"></a>Implante uma VM Linux em uma rede virtual existente

Este artigo mostra como usar a CLI do Azure 2.0 para implantar uma máquina virtual (VM) em uma rede virtual existente. Esses requisitos são:

- [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/)
- [arquivos de chave SSH pública e privada](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Você também pode executar estas etapas com a [CLI do Azure 1.0](deploy-linux-vm-into-existing-vnet-using-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="quick-commands"></a>Comandos rápidos
Se você precisar executar a tarefa rapidamente, a seção a seguir fornecerá detalhes dos comandos necessários. Mais informações detalhadas e contexto para cada etapa podem ser encontrados no restante do documento, [começando aqui](#detailed-walkthrough).

Para criar esse ambiente personalizado, é necessário ter a [CLI do Azure 2.0](/cli/azure/install-az-cli2) mais recente instalada e conectada a uma conta do Azure usando [az login](/cli/azure/#login).

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluem `myResourceGroup`, `myVnet` e `myVM`.

**Pré-requisitos:** grupo de recursos do Azure, rede virtual e sub-rede, grupo de segurança de rede com o SSH de entrada e uma placa de interface de rede virtual.

### <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Implantar a VM na infra-estrutura de rede virtual

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Debian \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic
```

## <a name="detailed-walkthrough"></a>Passo a passo detalhado

Recomendamos que os ativos do Azure, como as redes virtuais e os grupos de segurança de rede, sejam recursos estáticos e de longa duração que raramente são implantados. Após a implantação de uma rede virtual, ela pode ser reutilizada por novas implantações sem nenhum efeito negativo sobre a infraestrutura. Imagine uma rede virtual como um comutador de rede de hardware tradicional, você não precisa configurar um novo comutador de hardware a cada implantação. Com uma rede virtual configurada corretamente, podemos continuar implantando novas VMs nela repetidamente com pouca ou nenhuma alteração necessária durante a vida útil da rede virtual.

Para criar esse ambiente personalizado, é necessário ter a [CLI do Azure 2.0](/cli/azure/install-az-cli2) mais recente instalada e conectada a uma conta do Azure usando [az login](/cli/azure/#login).

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluem `myResourceGroup`, `myVnet` e `myVM`.

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Primeiro, criamos um grupo de recursos do Azure para organizar tudo o que criamos neste passo a passo. Para saber mais sobre grupos de recursos, confira [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Crie o grupo de recursos com [az group create](/cli/azure/group#create). O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `westus`:

```azurecli
az group create \
    --name myResourceGroup \
    --location westus
```

## <a name="create-the-virtual-network"></a>Criar a rede virtual

Permite criar uma rede virtual do Azure para iniciar as VMs. Para saber mais sobre as redes virtuais do Azure, confira [Criar uma rede virtual usando a CLI do Azure](../../virtual-network/virtual-networks-create-vnet-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Crie a rede virtual com [az network vnet create](/cli/azure/network/vnet#create). O exemplo a seguir cria uma rede virtual chamada `myVnet` e uma sub-rede chamada `mySubnet`:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --location westus \
    --name myVnet \
    --address-prefix 10.10.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.10.1.0/24
```

## <a name="create-the-network-security-group"></a>Crie o grupo de segurança de rede

Os grupos de segurança de rede do Azure são equivalentes a um firewall na camada de rede. Para saber mais sobre os grupos de segurança de rede, confira [Como criar grupos de segurança de rede na CLI do Azure](../../virtual-network/virtual-networks-create-nsg-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Crie o grupo de segurança de rede com [az network nsg create](/cli/azure/network/nsg#create). O seguinte exemplo cria um grupo de segurança de rede chamado `myNetworkSecurityGroup`:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location westus \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-ssh-allow-rule"></a>Adicionar uma regra de permissão de SSH de entrada

A VM do Linux precisa de acesso da Internet e, portanto, é necessária uma regra permitindo que o tráfego da porta 22 de entrada passe pela rede para a porta 22 na VM do Linux. Adicione uma regra de entrada para o grupo de segurança de rede com [az network nsg rule create](/cli/azure/network/nsg/rule#create). O exemplo a seguir cria uma regra chamada `myNetworkSecurityGroupRuleSSH`:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="attach-the-subnet-to-the-network-security-group"></a>Anexar a sub-rede para o grupo de segurança de rede

As regras do grupo de segurança de rede podem ser aplicadas a uma sub-rede ou interface de rede virtual específica. Vamos anexar o grupo de segurança de rede à nossa sub-rede. Anexe a sua sub-rede ao grupo de segurança de rede com [az network vnet subnet update](/cli/azure/network/vnet/subnet#update):

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="add-a-virtual-network-interface-card-to-the-subnet"></a>Adicionar uma placa de interface de rede virtual à sub-rede

Placas de interface de rede virtual (VNics) são importantes uma vez que você pode reutilizá-las, conectando-as em VMs diferentes. Essa reutilização permite que você mantenha a VNic como um recurso estático, enquanto as VMs podem ser temporárias. Crie uma VNic e associe-a à sub-rede com [az network nic create](/cli/azure/network/nic#create). O exemplo a seguir cria uma VNic chamada `myNic`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --location westus \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Implantar a VM na infra-estrutura de rede virtual

Agora temos uma rede virtual, uma sub-rede e um grupo de segurança de rede atuando como um firewall, para proteger nossa sub-rede bloqueando todo o tráfego de entrada, exceto pela porta 22 para o SSH. Agora a VM pode ser implantada nessa infraestrutura de rede existente.

Crie a sua VM com [az vm create](/cli/azure/vm#create). Para saber mais sobre como usar a CLI do Azure 2.0 para implantar uma VM completa, confira [Criar um ambiente completo do Linux usando a CLI do Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

O exemplo a seguir cria uma VM usando o Azure Managed Disks. Esses discos são tratados pela plataforma do Azure e não exigem nenhuma preparação ou local para armazenamento. Para saber mais sobre discos gerenciados, veja [Visão geral dos Azure Managed Disks](../../storage/storage-managed-disks-overview.md). Se você quiser usar discos não gerenciados, consulte a observação adicional abaixo.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Debian \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic
```

Se você usar discos gerenciados, ignore esta etapa. Se desejar usar discos não gerenciados, inclua os seguintes parâmetros adicionais no comando anterior para criar os discos não gerenciados na conta de armazenamento denominada `mystorageaccount`: 

```azurecli
    --use-unmanaged-disk \
    --storage-account mystorageaccount
```

Ao usar sinalizadores da CLI para chamar os recursos existentes, instruímos o Azure a implantar a VM na rede existente. Em outras palavras, depois que uma rede virtual e uma sub-rede são implantadas, elas podem ser mantidas como recursos estáticos ou permanentes na sua região do Azure. Neste exemplo, nós não criamos nem atribuímos um endereço IP público à VNic, portanto essa VM não é publicamente acessível pela Internet. Para saber mais, confira [Criar uma VM com um IP público estático usando a CLI do Azure](../../virtual-network/virtual-network-deploy-static-pip-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as maneiras de criar máquinas virtuais no Azure, confira os seguintes recursos:

* [Usar um modelo do Azure Resource Manager para criar uma implantação específica](../windows/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar seu próprio ambiente personalizado para uma VM do Linux usando os comandos da CLI do Azure diretamente](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM do Linux no Azure usando modelos](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

