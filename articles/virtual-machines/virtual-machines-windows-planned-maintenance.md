---
title: "Manutenção planejada para VMs do Windows | Microsoft Docs"
description: "Compreenda o que é a manutenção planejada do Azure e como ela afeta suas máquinas virtuais do Windows em execução no Azure"
services: virtual-machines-windows
documentationcenter: 
author: drewm
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: eb4b92d8-be0f-44f6-a6c3-f8f7efab09fe
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: drewm
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: fecc7666f80f7a9a5b2f8daaa65fd1766616fc85
ms.lasthandoff: 03/03/2017


---
# <a name="planned-maintenance-for-virtual-machines-in-azure"></a>Manutenção planejada para máquinas virtuais no Azure
Compreenda o que é a manutenção planejada do Azure e como ela pode afetar a disponibilidade de suas máquinas virtuais do Windows. Este artigo também está disponível para [máquinas virtuais Linux](virtual-machines-linux-planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Este artigo fornece um panorama do processo de manutenção planejada do Azure. Se estiver tentando entender por que sua VM foi reinicializada, você poderá [ler esta postagem de blog detalhando a exibição de logs de reinicialização da VM](https://azure.microsoft.com/blog/viewing-vm-reboot-logs/).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="why-azure-performs-planned-maintenance"></a>Por que o Azure realiza manutenção planejada
O Microsoft Azure realiza atualizações periodicamente em todo o mundo para aumentar a confiabilidade, o desempenho e a segurança da infraestrutura de host subjacente a máquinas virtuais. Muitas dessas atualizações são realizadas sem nenhum impacto sobre as máquinas virtuais ou os Serviços de Nuvem, incluindo atualizações que preservam a memória.

Porém, algumas dessas atualizações exigem reinicialização das máquinas virtuais para que as atualizações necessárias à infraestrutura sejam aplicadas. As máquinas virtuais serão desligadas enquanto corrigimos a infraestrutura e, em seguida, elas serão reiniciadas.

Observe que dois tipos de manutenção podem afetar a disponibilidade das máquinas virtuais: planejada e não planejada. Esta página descreve como o Microsoft Azure realiza a manutenção planejada. Para obter mais informações sobre a manutenção não planejada, veja [Compreender manutenção planejada X manutenção não planejada](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]

