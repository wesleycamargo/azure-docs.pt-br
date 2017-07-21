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
ms.translationtype: Human Translation
ms.sourcegitcommit: d9ae8e8948d82b9695d7d144d458fe8180294084
ms.openlocfilehash: 09fc9021e8dfb910d1a81178434ca2e27c0bacf7
ms.contentlocale: pt-br
ms.lasthandoff: 05/23/2017


---



# <a name="vm-preserving-maintenance-in-place-vm-migration"></a>Manutenção para preservar VMs (migração de VMs no local)

Embora a maioria das atualizações não cause impacto nas VMs hospedadas, há casos em que atualizações de componentes ou serviços resultam em uma interferência mínima nas VMs em execução (sem uma reinicialização completa da máquina virtual).

Essas atualizações são realizadas com tecnologia que permite a migração ao vivo in-loco, também nomeada de "atualização que preservam a memória". Durante a atualização do host, a máquina virtual é colocada em um estado em “pausa”, preservando a memória em RAM, enquanto o ambiente de hospedagem (por exemplo, o sistema operacional subjacente) aplica as atualizações e os patches necessários.
Em seguida, a máquina virtual continua após 30 segundos no estado em pausa.
Após ser reiniciada, o relógio da máquina virtual será sincronizado automaticamente.

Nem todas as atualizações podem ser implantadas usando esse mecanismo, mas, dado o breve período de pausa, a implantação de atualizações dessa maneira reduz muito o impacto nas máquinas virtuais.

Atualizações de múltiplas instâncias (VMs em um conjunto de disponibilidade) são aplicadas em um domínio de atualização por vez.

Alguns aplicativos podem ser afetados por essas atualizações mais do que outros. Aplicativos que executam processamento de eventos em tempo real, transmissão de mídia ou transcodificação, ou cenários de rede de alta produtividade, por exemplo, podem não ser projetados para tolerar uma pausa de 30 segundos. Aplicativos executando em uma máquina virtual podem ter conhecimentos das atualizações futuras chamando a [API de Eventos Agendados](../virtual-machines-scheduled-events.md) do [Serviço de Metadados do Azure](../virtual-machines-instancemetadataservice-overview.md).
