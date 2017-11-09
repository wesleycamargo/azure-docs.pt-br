---
title: "Solucionar problemas de falhas de alocação de VM do Linux| Microsoft Docs"
description: "Solucionar problemas de falhas de alocação ao criar, reiniciar ou redimensionar uma VM do Linux no Azure"
services: virtual-machines-linux, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue,azure-resourece-manager,azure-service-management
ms.assetid: 1ef41144-6dd6-4a56-b180-9d8b3d05eae7
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2016
ms.author: cjiang
ms.openlocfilehash: 8cf421afe9b41af050152dc965d42e7809b5f2d9
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-linux-vms-in-azure"></a>Solucionar problemas de falhas de alocação ao criar, reiniciar ou redimensionar VMs do Linux no Azure
Quando você cria uma VM, reinicia VMs paradas (desalocadas) ou redimensiona uma VM, o Microsoft Azure aloca recursos de computação para sua assinatura. Eventualmente, você pode receber mensagens de erro durante a execução dessas operações, antes de alcançar os limites da assinatura do Azure. Este artigo explica as causas das falhas de alocação mais comuns e sugere possíveis correções. As informações também poderão ser úteis caso você pretenda implantar serviços. Também é possível [solucionar problemas de falhas de alocação ao criar, reiniciar ou redimensionar VMs do Windows no Azure](../windows/allocation-failure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-allocation-failure](../../../includes/virtual-machines-common-allocation-failure.md)]

