---
title: "Manutenção para preservação de VMs para VMs Windows no Azure | Microsoft Docs"
description: "Migração de VMs no local para atualizações para preservar a memória."
services: virtual-machines-windows
documentationcenter: 
author: 
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: bc903f7523295da704ea8f0128dd553e3fdd96a9
ms.lasthandoff: 03/31/2017


---



# <a name="vm-preserving-maintenance-with-in-place-vm-migration"></a>Manutenção para preservar VM com a migração de VM in-loco

Embora a maioria das atualizações não cause impacto nas VMs hospedadas, há casos em que atualizações de componentes ou serviços resultam em uma interferência mínima nas VMs em execução (sem uma reinicialização completa da máquina virtual).

Essas atualizações são realizadas com uma tecnologia que permite a migração ao vivo no local, também chamada de “atualização para preservar a memória”. Durante a atualização do host, a máquina virtual é colocada em um estado em “pausa”, preservando a memória em RAM, enquanto o ambiente de hospedagem (por exemplo, o sistema operacional subjacente) aplica as atualizações e os patches necessários.
Em seguida, a máquina virtual continua após 30 segundos no estado em pausa.
Após ser reiniciada, o relógio da máquina virtual será sincronizado automaticamente.

Nem todas as atualizações podem ser implantadas usando esse mecanismo, mas, dado o breve período de pausa, a implantação de atualizações dessa maneira reduz muito o impacto nas máquinas virtuais.

Atualizações de múltiplas instâncias (VMs em um conjunto de disponibilidade) são aplicadas em um domínio de atualização por vez.

Os aplicativos executados em uma máquina virtual podem saber sobre as atualizações futuras chamando os eventos agendados do serviço de metadados. Para obter mais informações sobre eventos agendados, consulte [Serviço de Metadados do Azure – eventos agendados](../virtual-machines-scheduled-events.md).
