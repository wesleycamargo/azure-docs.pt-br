---
title: Implantar VMs do Linux em redes existentes - Portal do Azure | Microsoft Docs
description: Implante uma VM do Linux em uma Rede Virtual existente do Azure usando o portal.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 27704aa20a4514cfca200e70ffcc894059207281
ms.lasthandoff: 04/03/2017


---

# <a name="deploy-a-linux-vm-into-an-existing-vnet--nsg-using-the-portal"></a>Implantar uma VM do Linux em uma VNET e um NSG existentes usando o portal

Este artigo mostra como implantar uma VM em uma VNET (rede virtual) existente.  É recomendável que os ativos do Azure como VNETs e NSGs (Grupos de Segurança de Rede) sejam recursos estáticos e de longa duração que raramente são implantados.  Após a implantação de uma VNET, ela pode ser reutilizada por novas implantações constantes sem nenhum efeito negativo sobre a infraestrutura.  Ao considerar uma VNET como um comutador de rede de hardware tradicional, você não precisará configurar um novo comutador de hardware a cada implantação.  

Com uma VNET configurada corretamente, podemos continuar implantando novos servidores nela repetidamente e com pouca ou nenhuma alteração necessária durante a vida útil da VNET.

## <a name="create-the-resource-group"></a>Criar o Grupo de recursos

Primeiro, implantamos um Grupo de Recursos para organizar tudo o que criamos neste passo a passo.  Para obter mais informações sobre os Grupos de Recursos do Azure, consulte [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

![createResourceGroup](./media/deploy-linux-vm-into-existing-vnet-using-portal/createResourceGroup.png)


## <a name="create-the-vnet"></a>Criar a VNET

A primeira etapa é criar uma VNET na qual as VMs serão iniciadas.  A VNET contém uma sub-rede e associamos o NSG a essa sub-rede em uma etapa posterior.

![createVNet](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVNet.png)

## <a name="add-a-vnic-to-the-subnet"></a>Adicionar uma VNic à sub-rede

As VNics (placas de rede virtual) são importantes, pois você pode conectá-las a VMs diferentes, o que mantém a VNic como um recurso estático, enquanto as VMs podem ser temporárias. Crie uma VNic e associe-a à Sub-rede criada na etapa anterior.

![createVNic](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVNic.png)

## <a name="create-the-nsg"></a>Criar o NSG

Os NSGs do Azure são equivalentes a um firewall na camada de rede. Para obter mais informações sobre os NSGs do Azure, consulte [O que é um Grupo de Segurança de Rede?](../../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

![createNSG](./media/deploy-linux-vm-into-existing-vnet-using-portal/createNSG.png)

## <a name="add-an-inbound-ssh-allow-rule"></a>Adicionar uma regra de permissão de SSH de entrada

A VM do Linux precisa de acesso da Internet para que uma regra seja criada, permitindo que o tráfego da porta 22 de entrada passe pela rede para a porta 22 na VM do Linux.

![createInboundSSH](./media/deploy-linux-vm-into-existing-vnet-using-portal/createInboundSSH.png)

## <a name="associate-the-nsg-with-the-subnet"></a>Associar o NSG à sub-rede

Com a VNET e a sub-rede criada, associamos o NSG à sub-rede.  Os NSGs podem ser associados a uma sub-rede inteira ou a uma VNic individual.  Com o tráfego de filtragem do firewall no nível da sub-rede, todas as VNics e as VMs na sub-rede são protegidas pelo NSG comparado ao NSG associado a apenas uma única VNic e protegendo apenas uma VM.

![associateNSG](./media/deploy-linux-vm-into-existing-vnet-using-portal/associateNSG.png)


## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>Implantar a VM na VNET e no NSG

Usando o portal do Azure, a VM do Linux é implantada no Grupo de Recursos do Azure, na VNET, na Sub-rede e na VNic existentes.

![createVM](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVM.png)

Ao usar o portal para escolher os recursos existentes, instruímos o Azure a implantar a VM na rede existente.  Em outras palavras, depois que uma VNET e uma sub-rede forem implantadas, elas poderão ser mantidas como recursos estáticos ou permanentes na região do Azure.  

## <a name="next-steps"></a>Próximas etapas

* [Usar um modelo do Azure Resource Manager para criar uma implantação específica](../windows/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar seu próprio ambiente personalizado para uma VM do Linux usando os comandos da CLI do Azure diretamente](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM do Linux no Azure usando modelos](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

