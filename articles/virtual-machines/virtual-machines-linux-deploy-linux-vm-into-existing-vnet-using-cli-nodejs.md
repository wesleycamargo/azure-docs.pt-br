---
title: Implantar VMs do Linux em redes existentes - CLI do Azure | Microsoft Docs
description: Implante uma VM do Linux em uma Rede Virtual existente usando a CLI.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: cce15fa7042b29de055a62b1b7a6efa4d0539acf
ms.openlocfilehash: fd73f2eae7f89c237a6a2ff75c6022cb0684e613


---

# <a name="deploy-a-linux-vm-into-an-existing-azure-virtual-network-using-the-cli"></a>Implantar uma VM do Linux em uma Rede Virtual existente do Azure usando a CLI

Este artigo mostra como usar marcas de CLI para implantar uma VM em uma VNet (Rede Virtual) existente.  Esses requisitos são:

- [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/)
- [arquivos de chave SSH pública e privada](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa
Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- [CLI 1.0 do Azure](#quick-commands) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos (este artigo)
- [CLI do Azure 2.0 (Visualização)](virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - nossa próxima geração de CLI para o modelo de implantação de gerenciamento de recursos


## <a name="quick-commands"></a>Comandos rápidos

Se você precisar executar a tarefa rapidamente, a seção a seguir fornecerá detalhes dos comandos necessários. Mais informações detalhadas e contexto para cada etapa podem ser encontrados no restante do documento, [começando aqui](virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#detailed-walkthrough).

Pré-requisitos: Grupo de Recursos, VNet, NSG com SSH de entrada e Sub-rede. Substitua os exemplos por suas próprias configurações.

### <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Implantar a VM na infra-estrutura de rede virtual

```azurecli
azure vm create myVM \
-g myResourceGroup \
-l westus \
-y linux \
-Q Debian \
-o myStorageAcct \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub \
-n myVM \
-F myVNet \
-j mySubnet \
-N myVNic
```

## <a name="detailed-walkthrough"></a>Passo a passo detalhado

É recomendável que os ativos do Azure como VNETs e NSGs sejam recursos estáticos e de longa duração que raramente são implantados.  Após a implantação de uma VNET, ela pode ser reutilizada por novas implantações sem nenhum efeito negativo sobre a infraestrutura.  Pense em uma rede virtual como sendo um comutador de rede tradicionais de hardware. Você não precisaria definir uma nova chave de hardware com cada implantação.  Com uma VNET configurada corretamente, podemos continuar implantando novos servidores nela repetidamente e com pouca ou nenhuma alteração necessária durante a vida útil da VNET.

## <a name="create-the-resource-group"></a>Criar o Grupo de recursos

Primeiro, criamos um Grupo de Recursos para organizar tudo o que criamos neste passo a passo.  Para obter mais informações sobre os Grupos de Recursos do Azure, consulte [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure group create myResourceGroup \
--location westus
```

## <a name="create-the-vnet"></a>Criar a VNET

A primeira etapa é criar uma VNET na qual as VMs serão iniciadas.  A VNET contém uma sub-rede para este passo a passo.  Para obter mais informações sobre as VNETs do Azure, consulte [Criar uma rede virtual usando a CLI do Azure](../virtual-network/virtual-networks-create-vnet-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure network vnet create myVNet \
--resource-group myResourceGroup \
--address-prefixes 10.10.0.0/24 \
--location westus
```

## <a name="create-the-nsg"></a>Criar o NSG

A Sub-rede é criada por trás de um Grupo de Segurança de Rede existente e, portanto, criamos o NSG antes da Sub-rede.  Os NSGs do Azure são equivalentes a um firewall na camada de rede.  Para obter mais informações sobre os NSGs do Azure, consulte [Como criar NSGs na CLI do Azure](../virtual-network/virtual-networks-create-nsg-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure network nsg create myNSG \
--resource-group myResourceGroup \
--location westus
```

## <a name="add-an-inbound-ssh-allow-rule"></a>Adicionar uma regra de permissão de SSH de entrada

A VM do Linux precisa de acesso da Internet e, portanto, é necessária uma regra permitindo que o tráfego da porta 22 de entrada passe pela rede para a porta 22 na VM do Linux.

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

As VMs na VNET devem estar localizadas em uma sub-rede.  Cada VNET pode ter várias sub-redes.  Crie a sub-rede e associe-a ao NSG para adicionar um firewall à sub-rede.

```azurecli
azure network vnet subnet create mySubNet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--address-prefix 10.10.0.0/26 \
--network-security-group-name myNSG
```

Agora, a Sub-rede é adicionada à VNET e associada ao NSG e à regra do NSG.


## <a name="add-a-vnic-to-the-subnet"></a>Adicionar uma VNic à sub-rede

As VNics (placas de rede virtual) são importantes, pois você pode reutilizá-las conectando-as a VMs diferentes, o que mantém a VNic como um recurso estático, enquanto as VMs podem ser temporárias.  Crie uma VNic e associe-a à Sub-rede criada na etapa anterior.

```azurecli
azure network nic create myVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet
```

## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>Implantar a VM na VNET e no NSG

Agora temos uma VNET, uma sub-rede nela e um NSG atuando como um firewall, para proteger nossa sub-rede bloqueando todo o tráfego de entrada, exceto pela porta 22 para o SSH.  Agora a VM pode ser implantada nessa infraestrutura de rede existente.

Usando a CLI do Azure e o comando `azure vm create`, a VM do Linux é implantada no Grupo de Recursos do Azure, na VNET, na Sub-rede e na VNic existentes.  Para obter mais informações sobre como usar a CLI para implantar uma VM completa, consulte [Criar um ambiente completo do Linux usando a CLI do Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure vm create myVM \
--resource-group myResourceGroup \
--location westus \
--os-type linux \
--image-urn Debian \
--storage-account-name mystorageaccount \
--admin-username myAdminUser \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--vnet-name myVNet \
--vnet-subnet-name mySubnet \
--nic-name myVNic
```

Ao usar sinalizadores da CLI para chamar os recursos existentes, instruímos o Azure a implantar a VM na rede existente.  Em outras palavras, depois que uma VNET e uma sub-rede forem implantadas, elas poderão ser mantidas como recursos estáticos ou permanentes na região do Azure.  

## <a name="next-steps"></a>Próximas etapas

* [Usar um modelo do Azure Resource Manager para criar uma implantação específica](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar seu próprio ambiente personalizado para uma VM do Linux usando os comandos da CLI do Azure diretamente](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM do Linux no Azure usando modelos](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Feb17_HO1-->


