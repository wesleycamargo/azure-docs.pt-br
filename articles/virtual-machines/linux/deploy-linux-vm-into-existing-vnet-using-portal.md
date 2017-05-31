---
title: Implantar VMs Linux em redes existentes com o Portal do Azure | Microsoft Docs
description: Implante uma VM do Linux em uma Rede Virtual existente do Azure usando o portal.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 964c0fc41773b50a9fbe476df47460484c2ada66
ms.contentlocale: pt-br
ms.lasthandoff: 05/11/2017


---

# <a name="how-to-deploy-a-linux-virtual-machine-into-an-existing-azure-virtual-network-with-the-azure-portal"></a>Como implantar uma máquina virtual Linux em uma Rede Virtual do Azure com o Portal do Azure

Este artigo mostra como implantar uma VM (máquina virtual) em uma VNet (rede virtual) existente. Os ativos do Azure como VNets e grupos de segurança de rede devem ser recursos estáticos e de longa duração que raramente são implantados. Após a implantação de uma VNET, ela pode ser reutilizada por novas implantações constantes sem nenhum efeito negativo sobre a infraestrutura. Ao considerar uma VNET como um comutador de rede de hardware tradicional, você não precisará configurar um novo comutador de hardware a cada implantação.  

Com uma VNET configurada corretamente, você pode continuar implantando novos servidores nela repetidamente e com pouca ou nenhuma alteração necessária durante a vida útil da VNET.

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Primeiro, crie um grupo de recursos para organizar tudo o que você criou neste passo a passo. Para saber mais sobre os grupos de recursos do Azure, confira [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)

![createResourceGroup](./media/deploy-linux-vm-into-existing-vnet-using-portal/createResourceGroup.png)


## <a name="create-the-vnet"></a>Criar a VNET

Em seguida, crie uma VNET na qual iniciar as VMs. A VNet contém uma sub-rede e é associada ao grupo de segurança de rede com essa sub-rede em uma etapa posterior.

![createVNet](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVNet.png)

## <a name="add-a-vnic-to-the-subnet"></a>Adicionar uma VNic à sub-rede

As VNics (placas de rede virtual) são importantes uma vez que você pode conectá-las a VMs diferentes. Essa abordagem mantém a VNic como um recurso estático, enquanto as VMs podem ser temporárias. Crie uma VNic e associe-a à sub-rede criada na etapa anterior.

![createVNic](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVNic.png)

## <a name="create-the-network-security-group"></a>Crie o grupo de segurança de rede

Os grupos de segurança de rede do Azure são equivalentes a um firewall na camada de rede. Para obter mais informações sobre grupos de segurança de rede do Azure, confira [O que é um grupo de segurança de rede](../../virtual-network/virtual-networks-nsg.md).

![createNSG](./media/deploy-linux-vm-into-existing-vnet-using-portal/createNSG.png)

## <a name="add-an-inbound-ssh-allow-rule"></a>Adicionar uma regra de permissão de SSH de entrada

A VM precisa de acesso da Internet e, portanto, é criada uma regra permitindo que o tráfego da porta 22 de entrada passe pela rede para a porta 22 na VM.

![createInboundSSH](./media/deploy-linux-vm-into-existing-vnet-using-portal/createInboundSSH.png)

## <a name="associate-the-nsg-with-the-subnet"></a>Associar o NSG à sub-rede

Com a VNet e a sub-rede criadas, associe o grupo de segurança de rede à sub-rede. Os grupos de segurança de rede podem ser associados a uma sub-rede inteira ou a uma VNic individual. Com o firewall filtrando o tráfego no nível da sub-rede, todas as VNics e as VMs na sub-rede estão protegidas pelo grupo de segurança de rede. Outra abordagem é o grupo de segurança de rede ser associado a apenas uma única VNic e proteger apenas uma VM.

![associateNSG](./media/deploy-linux-vm-into-existing-vnet-using-portal/associateNSG.png)


## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>Implantar a VM na VNET e no NSG

Usando o portal do Azure, a VM do Linux é implantada no Grupo de Recursos do Azure, na VNET, na Sub-rede e na VNic existentes.

![createVM](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVM.png)

Ao usar o portal para escolher os recursos existentes, você instrui o Azure a implantar a VM dentro da rede existente. Depois que uma VNET e uma sub-rede forem implantadas, elas poderão ser mantidas como recursos estáticos ou permanentes na região do Azure.  

## <a name="next-steps"></a>Próximas etapas

* [Usar um modelo do Azure Resource Manager para criar uma implantação específica](../windows/cli-deploy-templates.md)
* [Criar seu próprio ambiente personalizado para uma VM do Linux usando os comandos da CLI do Azure diretamente](create-cli-complete.md)
* [Criar uma VM do Linux no Azure usando modelos](create-ssh-secured-vm-from-template.md)

