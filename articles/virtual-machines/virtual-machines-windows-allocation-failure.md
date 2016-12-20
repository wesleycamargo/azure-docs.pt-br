---
title: "Solução de problemas de falhas de alocação de VM do Windows | Microsoft Docs"
description: "Solucionar problemas de falhas de alocação ao criar, reiniciar ou redimensionar uma VM do Windows no Azure"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/02/2016
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 7c83071775cd4c237dfcdb77dc89a6d5c4df4aac


---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-windows-vms-in-azure"></a>Solucionar problemas de falhas de alocação ao criar, reiniciar ou redimensionar VMs do Windows no Azure
Quando você cria uma VM, reinicia VMs paradas (desalocadas) ou redimensiona uma VM, o Microsoft Azure aloca recursos de computação para sua assinatura. Eventualmente, você pode receber mensagens de erro durante a execução dessas operações, antes de alcançar os limites da assinatura do Azure. Este artigo explica as causas das falhas de alocação mais comuns e sugere possíveis correções. As informações também poderão ser úteis caso você pretenda implantar serviços. Também é possível [solucionar problemas de falhas de alocação ao criar, reiniciar ou redimensionar VMs do Linux no Azure](virtual-machines-linux-allocation-failure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-allocation-failure](../../includes/virtual-machines-common-allocation-failure.md)]




<!--HONumber=Nov16_HO3-->


