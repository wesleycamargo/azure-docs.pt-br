---
title: "Manutenção planejada para VMs Linux | Microsoft Docs"
description: "Compreenda o que é a manutenção planejada do Azure e como ela afeta suas máquinas virtuais Linux em execução no Azure"
services: virtual-machines-linux
documentationcenter: 
author: drewm
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: e65e614c-e969-40dc-a271-fe074069daf1
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/26/2016
ms.author: drewm
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: d0e7d00dd8e6ab53897340e13a3170519cbdb135


---
# <a name="planned-maintenance-for-linux-virtual-machines-in-azure"></a>Manutenção planejada para máquinas virtuais Linux no Azure
Compreenda o que é a manutenção planejada do Azure e como ela pode afetar a disponibilidade de suas máquinas virtuais Linux. Este artigo também está disponível para [máquinas virtuais do Windows](virtual-machines-windows-planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Este artigo fornece um panorama do processo de manutenção planejada do Azure. Se estiver tentando entender por que sua VM foi reinicializada, você poderá [ler esta postagem de blog detalhando a exibição de logs de reinicialização da VM](https://azure.microsoft.com/blog/viewing-vm-reboot-logs/).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="why-azure-performs-planned-maintenance"></a>Por que o Azure realiza manutenção planejada
O Microsoft Azure realiza atualizações periodicamente em todo o mundo para aumentar a confiabilidade, o desempenho e a segurança da infraestrutura de host subjacente a máquinas virtuais. Muitas dessas atualizações são realizadas sem nenhum impacto sobre as máquinas virtuais ou os Serviços de Nuvem, incluindo atualizações que preservam a memória.

Porém, algumas dessas atualizações exigem reinicialização das máquinas virtuais para que as atualizações necessárias à infraestrutura sejam aplicadas. As máquinas virtuais serão desligadas enquanto corrigimos a infraestrutura e, em seguida, elas serão reiniciadas.

Existem dois tipos de manutenção que podem afetar a disponibilidade das máquinas virtuais: planejada e não planejada. Esta página descreve como o Microsoft Azure realiza a manutenção planejada. Para obter mais informações sobre a manutenção não planejada, veja [Compreender manutenção planejada X manutenção não planejada](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]




<!--HONumber=Nov16_HO3-->


