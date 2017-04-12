---
title: Diferentes maneiras de criar uma VM Windows no Azure | Microsoft Docs
description: "Lista as diferentes maneiras de criar uma máquina virtual do Windows com o Gerenciador de Recursos."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 809ba8f4-b54e-43c5-bbe3-8e710c49971f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/02/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 5e51c49aac01a22d86c7c1a12b2f2ca7ddc056bc
ms.lasthandoff: 03/31/2017


---
# <a name="different-ways-to-create-a-windows-virtual-machine"></a>Diferentes maneiras de criar uma máquina virtual Windows

O Azure oferece diferentes maneiras de criar uma máquina virtual, pois máquinas virtuais são adequadas para diferentes usuários e finalidades. Isso significa que você precisa tomar algumas decisões sobre a máquina virtual e como criá-la. Este artigo fornece um resumo dessas opções e links para instruções.

## <a name="azure-portal"></a>Portal do Azure
Usar o portal do Azure é uma maneira fácil de testar uma máquina virtual, especialmente, se estiver se familiarizando com o Azure. 

[Criar uma máquina virtual que executa o Windows usando o portal](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="template"></a>Modelo
As máquinas virtuais exigem uma combinação de recursos (como conjuntos de disponibilidade e contas de armazenamento). Em vez de implantar e gerenciar cada recurso separadamente, é possível criar um modelo do Azure Resource Manager que implanta e provisiona todos os recursos em uma única operação coordenada.

* [Criar uma máquina virtual do Windows com um modelo do Gerenciador de Recursos](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="azure-powershell"></a>Azure PowerShell
Se preferir trabalhar em um shell de comando, será possível usar o Azure PowerShell.

* [Criar uma VM do Windows usando o PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="visual-studio"></a>Visual Studio
Use o Visual Studio para criar, gerenciar e implantar VMs com as Ferramentas do Azure para Visual Studio e o SDK do Azure.

[Ferramentas do Azure para Visual Studio](https://www.visualstudio.com/features/azure-tools-vs)


