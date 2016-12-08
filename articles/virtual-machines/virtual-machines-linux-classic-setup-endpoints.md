---
title: "Configurar pontos de extremidade em uma VM do Linux clássica | Microsoft Docs"
description: "Saiba como configurar pontos de extremidade para uma VM do Linux no Portal Clássico do Azure para permitir a comunicação com uma máquina virtual do Linux no Azure"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: f3749738-1109-4a1d-8635-40e4bd220e91
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 372fc76485720f34e7c5b85edf83a01aa5263022


---
# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>Como configurar pontos de extremidade em uma máquina virtual clássica do Linux no Azure
Todas as máquinas virtuais do Linux criadas no Azure usando o modelo de implantação clássico podem se comunicar automaticamente com outras máquinas virtuais no mesmo serviço de nuvem ou rede virtual por um canal de rede privada. No entanto, os computadores na Internet ou outras redes virtuais requerem pontos de extremidade para direcionar o tráfego de rede de entrada para uma máquina virtual. Este artigo também está disponível para [máquinas virtuais do Windows](virtual-machines-windows-classic-setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

No modelo de implantação **Resource Manager**, os pontos de extremidade são configurados usando **NSGs (Grupos de Segurança de Rede)**. Para saber mais, consulte [Abrir portas e pontos de extremidade](virtual-machines-linux-nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Ao criar uma máquina virtual Linux no portal clássico do Azure, em geral, um ponto de extremidade para o SSH (Secure Shell) é criado para você automaticamente. Você pode configurar pontos de extremidade adicionais criando a máquina virtual ou posteriormente, conforme a necessidade.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Próximas etapas
* Também é possível criar um ponto de extremidade de VM usando a [Interface de Linha de Comando do Azure](../virtual-machines-command-line-tools.md). Execute o comando **azure vm endpoint create** .
* Se você criou uma máquina virtual no modelo de implantação do Gerenciador de Recursos, é possível usar a CLI do Azure no modo do Gerenciador de Recursos para [criar grupos de segurança de rede](../virtual-network/virtual-networks-create-nsg-arm-cli.md) a fim de controlar o tráfego para a VM.




<!--HONumber=Nov16_HO3-->


