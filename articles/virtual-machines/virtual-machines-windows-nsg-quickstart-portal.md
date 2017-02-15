---
title: Abrir portas para uma VM usando o Portal do Azure | Microsoft Azure
description: "Saiba como abrir uma porta/criar um ponto de extremidade para sua VM do Windows usando o modelo de implantação do Resource Manager no Portal do Azure"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 979260cdb1ce7f3eaa4b7e05be4c34fce06d0399


---
# <a name="opening-ports-to-a-vm-in-azure-using-the-azure-portal"></a>Abrir portas para uma VM no Azure usando o Portal do Azure
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Comandos rápidos
Você também pode [executar essas etapas usando o Azure PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Primeiro, crie o seu Grupo de Segurança de Rede. Selecione um grupo de recursos no portal, clique em **Adicionar** e, em seguida, pesquise por um 'Grupo de segurança de rede' e selecione-o:

![Adicionar um Grupo de Segurança de Rede](./media/virtual-machines-windows-nsg-quickstart-portal/add-nsg.png)

Insira um nome para o seu Grupo de Segurança de Rede, selecione ou crie um grupo de recursos e selecione uma localização. Clique em **Criar** quando terminar:

![Criar um grupo de segurança de rede](./media/virtual-machines-windows-nsg-quickstart-portal/create-nsg.png)

Selecione o novo Grupo de Segurança de Rede. Selecione 'Regras de segurança de entrada' e clique no botão **Adicionar** para criar uma regra:

![Adicionar uma regra de entrada](./media/virtual-machines-windows-nsg-quickstart-portal/add-inbound-rule.png)

Forneça um nome para sua nova regra. A porta 80 já está inserida por padrão. Essa folha é o local no qual você alteraria a origem, o protocolo e o destino ao adicionar regras extras ao seu Grupo de Segurança de Rede. Clique em **OK** para criar a regra:

![Criar uma regra de entrada](./media/virtual-machines-windows-nsg-quickstart-portal/create-inbound-rule.png)

A etapa final é associar o Grupo de Segurança de Rede com uma sub-rede ou uma interface de rede específica. Vamos associar o Grupo de Segurança de Rede com uma sub-rede. Selecione 'Sub-redes' e então clique em **Associar**:

![Associar um Grupo de Segurança de Rede a uma sub-rede](./media/virtual-machines-windows-nsg-quickstart-portal/associate-subnet.png)

Selecione sua rede virtual e, então, selecione a sub-rede apropriada:

![Associar um Grupo de Segurança de Rede à rede virtual](./media/virtual-machines-windows-nsg-quickstart-portal/select-vnet-subnet.png)

Você criou um Grupo de Segurança de Rede, uma regra de entrada que permite o tráfego na porta 80 e o associou a uma sub-rede. Todas as VMs que você conectar a essa sub-rede estarão acessíveis na porta 80.

## <a name="more-information-on-network-security-groups"></a>Mais informações sobre os Grupos de Segurança de Rede
Os comandos rápidos aqui permitem que você coloque tudo em funcionamento com o tráfego que flui para sua VM. Os Grupos de Segurança de Rede fornecem muitos recursos excelentes e granularidade para controlar o acesso aos recursos. Você pode ler mais sobre a [criação de um Grupo de Segurança de Rede e as regras ACL aqui](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Você pode definir Grupos de Segurança de Rede e regras de ACL como parte dos modelos do Azure Resource Manager. Leia mais sobre a [criação de Grupos de Segurança de Rede com modelos](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Se precisar usar o encaminhamento de porta para mapear uma porta externa exclusiva para uma porta interna em sua VM, use um balanceador de carga e regras de NAT (Conversão de Endereços de Rede). Por exemplo, talvez você queira expor a porta TCP 8080 externamente e direcionar o tráfego para a porta TCP 80 em uma VM. Você pode aprender sobre a [criação de um balanceador de carga para a Internet](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Próximas etapas
Neste exemplo, você criou uma regra simples para permitir o tráfego HTTP. Você pode encontrar informações sobre a criação de ambientes mais detalhados nos seguintes artigos:

* [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [O que é um NSG (grupo de segurança de rede)?](../virtual-network/virtual-networks-nsg.md)
* [Visão Geral do Azure Resource Manager para Balanceadores de Carga](../load-balancer/load-balancer-arm.md)




<!--HONumber=Nov16_HO3-->


