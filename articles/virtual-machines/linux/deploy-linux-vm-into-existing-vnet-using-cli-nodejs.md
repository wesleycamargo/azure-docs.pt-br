---
title: Implantar VMs Linux em uma rede existente com a CLI do Azure 1.0 | Microsoft Docs
description: Como implantar uma VM Linux em uma Rede Virtual existente usando a CLI do Azure 1.0
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
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 767a3f7cadba6b1e71e5a8f5995a9db090e419dd
ms.contentlocale: pt-br
ms.lasthandoff: 05/11/2017


---

# <a name="how-to-deploy-a-linux-virtual-machine-into-an-existing-azure-virtual-network-with-the-azure-cli-10"></a>Como implantar uma máquina virtual Linux em uma Rede Virtual do Azure com a CLI do Azure 1.0

Este artigo mostra como usar a CLI do Azure 1.0 para implantar uma VM (máquina virtual) em uma VNet (Rede Virtual) existente. Esses requisitos são:

- [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/)
- [arquivos de chave SSH pública e privada](mac-create-ssh-keys.md)


## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa
Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- [CLI 1.0 do Azure](#quick-commands) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos (este artigo)
- [CLI 2.0 do Azure](deploy-linux-vm-into-existing-vnet-using-cli.md) – nossa última geração de CLI para o modelo de implantação de gerenciamento de recursos


## <a name="quick-commands"></a>Comandos rápidos

Se você precisar executar a tarefa rapidamente, a seção a seguir fornecerá detalhes dos comandos necessários. Mais informações detalhadas e contexto para cada etapa podem ser encontrados no restante do documento, [começando aqui](deploy-linux-vm-into-existing-vnet-using-cli.md#detailed-walkthrough).

Pré-requisitos: Grupo de Recursos, VNet, NSG com SSH de entrada e Sub-rede. Substitua os exemplos por suas próprias configurações.

### <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Implantar a VM na infra-estrutura de rede virtual

```azurecli
azure vm create myVM \
    -g myResourceGroup \
    -l eastus \
    -y linux \
    -Q Debian \
    -o mystorageaccount \
    -u myAdminUser \
    -M ~/.ssh/id_rsa.pub \
    -n myVM \
    -F myVNet \
    -j mySubnet \
    -N myVNic
```

## <a name="detailed-walkthrough"></a>Passo a passo detalhado

Os ativos do Azure como VNets e grupos de segurança de rede devem ser recursos estáticos e de longa duração que raramente são implantados. Após a implantação de uma VNET, ela pode ser reutilizada por novas implantações sem nenhum efeito negativo sobre a infraestrutura. Pense em uma rede virtual como sendo um comutador de rede tradicionais de hardware. Você não precisaria definir uma nova chave de hardware com cada implantação. Com uma VNET configurada corretamente, você pode continuar implantando novos servidores nela repetidamente e com pouca ou nenhuma alteração necessária durante a vida útil da VNET.

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Primeiro, crie um grupo de recursos para organizar tudo o que você criou neste passo a passo. Para obter mais informações sobre os grupos de recursos, consulte [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)

```azurecli
azure group create myResourceGroup --location eastus
```

## <a name="create-the-vnet"></a>Criar a VNET

A primeira etapa é criar uma VNET na qual as VMs serão iniciadas. A VNET contém uma sub-rede para este passo a passo. Para obter mais informações sobre as VNETs do Azure, consulte [Criar uma rede virtual usando a CLI do Azure](../../virtual-network/virtual-networks-create-vnet-arm-cli.md)

```azurecli
azure network vnet create myVNet \
    --resource-group myResourceGroup \
    --address-prefixes 10.10.0.0/24 \
    --location eastus
```

## <a name="create-the-network-security-group"></a>Crie o grupo de segurança de rede

A Sub-rede é criada por trás de um grupo de segurança de rede existente, portanto, crie o grupo de segurança de rede antes da sub-rede. Os grupos de segurança de rede do Azure são equivalentes a um firewall na camada de rede. Para saber mais sobre os grupos de segurança de rede do Azure, confira [Como criar grupos de segurança de rede na CLI do Azure](../../virtual-network/virtual-networks-create-nsg-arm-cli.md)

```azurecli
azure network nsg create myNetworkSecurityGroup \
    --resource-group myResourceGroup \
    --location eastus
```

## <a name="add-an-inbound-ssh-allow-rule"></a>Adicionar uma regra de permissão de SSH de entrada

A VM precisa de acesso da Internet e, portanto, é necessária uma regra permitindo que o tráfego da porta 22 de entrada passe pela rede para a porta 22 na VM.

```azurecli
azure network nsg rule create inboundSSH \
    --resource-group myResourceGroup \
    --nsg-name myNSG \
    --access Allow \
    --protocol Tcp \
    --direction Inbound \
    --priority 100 \
    --source-address-prefix Internet \
    --source-port-range 22 \
    --destination-address-prefix 10.10.0.0/24 \
    --destination-port-range 22
```

## <a name="add-a-subnet-to-the-vnet"></a>Adicionar uma sub-rede à VNET

As VMs na VNET devem estar localizadas em uma sub-rede. Cada VNET pode ter várias sub-redes. Crie a sub-rede e associe ao grupo de segurança de rede.

```azurecli
azure network vnet subnet create mySubNet \
    --resource-group myResourceGroup \
    --vnet-name myVNet \
    --address-prefix 10.10.0.0/26 \
    --network-security-group-name myNetworkSecurityGroup
```

Agora, a sub-rede é adicionada à VNet e associada à regra e ao grupo de segurança de rede.


## <a name="add-a-vnic-to-the-subnet"></a>Adicionar uma VNic à sub-rede

As VNics (placas de rede virtual) são importantes uma vez que você pode reutilizá-las, conectando-as a VMs diferentes. Essa abordagem mantém a VNic como um recurso estático, enquanto as VMs podem ser temporárias. Crie uma VNic e associe-a à sub-rede criada na etapa anterior.

```azurecli
azure network nic create myVNic \
    --resource-group myResourceGroup \
    --location eastus \
    ---subnet-vnet-name myVNet \
    --subnet-name mySubNet
```

## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>Implantar a VM na VNET e no NSG

Agora você tem uma VNet e uma sub-rede dentro dessa VNet e um grupo de segurança de rede agindo para proteger a sub-rede bloqueando todo o tráfego de entrada, exceto pela porta 22 para o SSH. Agora a VM pode ser implantada nessa infraestrutura de rede existente.

Usando a CLI do Azure e o comando `azure vm create`, a VM do Linux é implantada no Grupo de Recursos do Azure, na VNET, na Sub-rede e na VNic existentes. Para obter mais informações sobre como usar a CLI para implantar uma VM completa, consulte [Criar um ambiente completo do Linux usando a CLI do Azure](create-cli-complete.md)

```azurecli
azure vm create myVM \
    --resource-group myResourceGroup \
    --location eastus \
    --os-type linux \
    --image-urn Debian \
    --storage-account-name mystorageaccount \
    --admin-username myAdminUser \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --vnet-name myVNet \
    --vnet-subnet-name mySubnet \
    --nic-name myVNic
```

Ao usar sinalizadores da CLI para chamar os recursos existentes, você instrui o Azure a implantar a VM na rede existente. Depois que uma VNET e uma sub-rede forem implantadas, elas poderão ser mantidas como recursos estáticos ou permanentes na região do Azure.  

## <a name="next-steps"></a>Próximas etapas

* [Usar um modelo do Azure Resource Manager para criar uma implantação específica](../windows/cli-deploy-templates.md)
* [Criar seu próprio ambiente personalizado para uma VM do Linux usando os comandos da CLI do Azure diretamente](create-cli-complete.md)
* [Criar uma VM do Linux no Azure usando modelos](create-ssh-secured-vm-from-template.md)

