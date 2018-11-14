---
title: Configurar pontos de extremidade em uma VM do Windows clássica | Microsoft Docs
description: Saiba como configurar pontos de extremidade para uma VM clássica do Windows no portal do Azure para permitir a comunicação com uma máquina virtual do Windows no Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 8afc21c2-d3fb-43a3-acce-aa06be448bb6
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/23/2018
ms.author: cynthn
ms.openlocfilehash: 373003eb8be0482ed96d7d11ecd879237f69b47a
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957158"
---
# <a name="set-up-endpoints-on-a-windows-virtual-machine-by-using-the-classic-deployment-model"></a>Configurar pontos de extremidade em uma máquina virtual do Windows usando o modelo de implantação clássico
As máquinas virtuais do Windows (VMs) que você cria no Azure usando o modelo de implantação clássico podem se comunicar automaticamente por um canal de rede privada com outras VMs no mesmo serviço de nuvem ou rede virtual. No entanto, os computadores na Internet ou outras redes virtuais exigem pontos de extremidade para direcionar o tráfego de rede de entrada para uma VM. 

Você também pode configurar pontos de extremidade na [máquinas virtuais Linux](../../linux/classic/setup-endpoints.md).

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../../../resource-manager-deployment-model.md). Este artigo aborda o modelo de implantação clássico. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos.  
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

No modelo de implantação do **Resource Manager**, os pontos de extremidade são configurados usando **Network Security Groups (NSGs)**. Para obter mais informações, consulte [Permitir acesso externo à sua VM usando o portal do Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Quando você cria uma VM do Windows no portal do Azure, os pontos de extremidade comuns, como pontos de extremidade da Área de Trabalho Remota e do Windows PowerShell, geralmente são criados automaticamente para você. Você pode configurar pontos de extremidade adicionais posteriormente conforme necessário.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Próximas etapas
* Para usar um cmdlet do Azure PowerShell para configurar um ponto de extremidade de VM, consulte [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).
* Para usar um cmdlet do Azure PowerShell para gerenciar uma ACL em um ponto de extremidade, consulte [Como gerenciar ACLs (listas de controle de acesso) para pontos de extremidade usando o PowerShell](../../../virtual-network/virtual-networks-acl-powershell.md).
* Se você criou uma máquina virtual no modelo de implantação do Resource Manager, é possível usar o Azure PowerShell para [criar grupos de segurança de rede](../../../virtual-network/tutorial-filter-network-traffic.md) a fim de controlar o tráfego para a VM.
