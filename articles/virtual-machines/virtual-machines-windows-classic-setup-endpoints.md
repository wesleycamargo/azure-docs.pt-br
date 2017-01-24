---
title: "Configurar pontos de extremidade em uma VM do Windows clássica | Microsoft Docs"
description: "Saiba como configurar pontos de extremidade para uma VM do Windows no portal clássico do Azure para permitir a comunicação com uma máquina virtual do Windows no Azure."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 8afc21c2-d3fb-43a3-acce-aa06be448bb6
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: f022bd4d430fbb94ca9e52f03fd7e5bf69e336ad


---
# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>Como configurar pontos de extremidade em uma máquina virtual clássica do Windows no Azure
Todas as máquinas virtuais do Windows criadas no Azure usando o modelo de implantação clássico podem se comunicar automaticamente com outras máquinas virtuais no mesmo serviço de nuvem ou rede virtual por um canal de rede privada. No entanto, os computadores na Internet ou outras redes virtuais requerem pontos de extremidade para direcionar o tráfego de rede de entrada para uma máquina virtual. Este artigo também está disponível para [máquinas virtuais Linux](virtual-machines-linux-classic-setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos.

No modelo de implantação **Resource Manager**, os pontos de extremidade são configurados usando **NSGs (Grupos de Segurança de Rede)**. Para obter mais informações, consulte [Permitir o acesso externo à sua VM usando o Portal do Azure](virtual-machines-windows-nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Quando você cria uma máquina virtual do Windows no portal clássico do Azure, pontos de extremidade comuns como aqueles para a Área de Trabalho Remota e a Comunicação Remota do Windows PowerShell normalmente são criados para você automaticamente. Você pode configurar pontos de extremidade adicionais criando a máquina virtual ou posteriormente, conforme a necessidade.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Próximas etapas
* Para usar um cmdlet do Azure PowerShell para configurar um ponto de extremidade de VM, consulte [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).
* Para usar um cmdlet do Azure PowerShell para gerenciar uma ACL em um ponto de extremidade, consulte [Como gerenciar ACLs (listas de controle de acesso) para pontos de extremidade usando o PowerShell](../virtual-network/virtual-networks-acl-powershell.md).
* Se você criou uma máquina virtual no modelo de implantação do Resource Manager, é possível usar o Azure PowerShell para [criar grupos de segurança de rede](../virtual-network/virtual-networks-create-nsg-arm-ps.md) a fim de controlar o tráfego para a VM.




<!--HONumber=Dec16_HO1-->


