---
title: Exemplos de modelo do Azure Resource Manager para rede virtual | Microsoft Docs
description: Saiba mais sobre modelos diferentes do Azure Resource Manager disponíveis para implantação de redes virtuais do Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/13/2018
ms.author: jdial
ms.openlocfilehash: af0affc549afd8b63fe0d566fac198de054554c1
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
ms.locfileid: "31528980"
---
# <a name="azure-resource-manager-template-samples-for-virtual-network"></a>Exemplos de modelo do Azure Resource Manager para rede virtual

A tabela a seguir contém links para exemplos de modelos do Azure Resource Manager. Você pode implantar os modelos usando o [Portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), a [CLI](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure ou o Azure [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Para aprender a criar seus próprios modelos, consulte [Criar seu primeiro modelo](../azure-resource-manager/resource-manager-create-first-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Entender a estrutura e a sintaxe de modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-network%2ftoc.json).


| | |
|----|----|
|[Criar uma rede virtual com duas sub-redes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)| Crie uma rede virtual com duas sub-redes.|
|[Rotear o tráfego por meio de uma solução de virtualização de rede](https://github.com/Azure/azure-quickstart-templates/tree/master/201-userdefined-routes-appliance)| Crie uma rede virtual com três sub-redes. Implanta uma máquina virtual em cada uma das sub-redes. Cria uma tabela de rota que contém rotas para direcionar o tráfego de uma sub-rede para outra, por meio da máquina virtual na terceira sub-rede. Associe a tabela de rotas a uma das sub-redes.|
|[Criar um ponto de extremidade de serviço de rede virtual para o Armazenamento do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-2subnets-service-endpoints-storage-integration)|Crie uma nova rede virtual com duas sub-redes e uma interface de rede em cada sub-rede. Habilita um ponto de extremidade de serviço do Armazenamento do Azure para uma das sub-redes e protege uma nova conta de armazenamento para essa sub-rede.|
|[Conectar duas redes virtuais](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-to-vnet-peering)| Cria duas redes virtuais e um emparelhamento de rede virtual entre elas.|
|[Criar uma máquina virtual com vários endereços IP](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-ipconfig)| Crie uma VM Windows ou Linux com vários endereços IP.|
