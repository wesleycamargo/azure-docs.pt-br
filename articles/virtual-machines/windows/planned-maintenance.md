---
title: "Manutenção planejada para VMs Windows no Azure | Microsoft Docs"
description: "Entenda o que é a manutenção planejada do Azure e como ela afeta as máquinas virtuais Windows em execução no Azure."
services: virtual-machines-windows
documentationcenter: 
author: 
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: eb4b92d8-be0f-44f6-a6c3-f8f7efab09fe
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 198c1804b342b8faf406a08eff7bc42994b9dd0d
ms.lasthandoff: 03/31/2017


---
# <a name="planned-maintenance-for-windows-virtual-machines"></a>Manutenção planejada para máquinas virtuais Windows 

O Microsoft Azure realiza atualizações periodicamente em todo o mundo para aumentar a confiabilidade, o desempenho e a segurança da infraestrutura de host subjacente a máquinas virtuais. Essas atualizações vão desde a aplicação de patch de componentes de software no ambiente de hospedagem (sistema operacional, hipervisor e vários agentes implantados no host), a atualização de componentes de rede até o encerramento de hardware.

A maioria dessas atualizações é realizada sem nenhum impacto às máquinas virtuais hospedadas ou aos serviços de nuvem.

No entanto, há casos em que as atualizações afetam as máquinas virtuais hospedadas:

-   A manutenção para preservar a VM com o uso da migração de VM in-loco descreve uma classe de atualizações em que as máquinas virtuais não são reinicializadas durante a manutenção.

-   A manutenção de reinicialização de VM que exige uma reinicialização ou reimplantação em máquinas virtuais hospedadas.

Observe que esta página descreve como o Microsoft Azure realiza a manutenção planejada. Para obter mais informações sobre eventos não planejados (interrupções), consulte [Gerenciar a disponibilidade das máquinas virtuais](manage-availability.md).
