---
title: "Diretrizes de máquinas virtuais do Azure | Microsoft Docs"
description: "Saiba mais sobre as principais diretrizes de design e implementação referentes à implantação de máquinas virtuais Windows no Azure"
documentationcenter: 
services: virtual-machines-windows
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f932e65a-437b-48b0-8d70-f61ded8ce1c6
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 1d8f2565fa50f8deeb2d139a9195129580bceab8
ms.lasthandoff: 03/31/2017


---
# <a name="azure-virtual-machines-guidelines-for-windows"></a>Diretrizes de máquinas virtuais do Azure para Windows
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

Este artigo destaca as noções básicas sobre as etapas de planejamento necessárias para criar e gerenciar VMs (máquinas virtuais) em seu ambiente do Azure.

## <a name="implementation-guidelines-for-vms"></a>Diretrizes de implementação de VMs
Decisões:

* Quantas VMs são necessárias para suas várias camadas de aplicativos e componentes de sua infraestrutura?
* De quais recursos da CPU e da memória cada VM precisa e quais são os requisitos de armazenamento?

Tarefas:

* Definir as cargas de trabalho para seu aplicativo e os recursos de que as VMs precisam.
* Alinhar as demandas de recursos para cada VM com os tipos de VM e de armazenamento apropriados.
* Definir os grupos de recursos para as diferentes camadas e componentes de sua infraestrutura.
* Definir sua convenção de nomenclatura de VM.
* Crie suas VMs usando o Azure PowerShell, o portal da Web ou os modelos do Resource Manager.

## <a name="virtual-machines"></a>Máquinas virtuais
Provavelmente, um dos principais recursos no ambiente do Azure são as VMs. Esse recurso é o local em que você executa seus aplicativos bancos de dados, serviços de autenticação, etc.

É importante entender os [diferentes tamanhos de VM](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para dimensionar corretamente seu ambiente de uma perspectiva de desempenho e custo. Se as VMs não tiverem núcleos de CPU ou memória suficiente, o desempenho do aplicativo será afetado, independentemente de como ele for projetado e desenvolvido. Examine as sugestões de cargas de trabalho para cada série de VM como ponto de partida ao decidir o tamanho da VM a ser usado para cada componente em sua infraestrutura. É possível [alterar o tamanho de uma VM](https://azure.microsoft.com/blog/resize-virtual-machines/) após a implantação.

O armazenamento desempenha um papel fundamental no desempenho da VM. Você pode usar o armazenamento Standard que use discos giratórios regulares ou o armazenamento Premium para altas cargas de trabalho de E/S e o desempenho de pico que usam os discos SSD. Assim como ocorre com o tamanho da VM, há considerações de custo para selecionar a mídia de armazenamento. Leia o [artigo sobre diretrizes de infraestrutura de armazenamento](infrastructure-storage-solutions-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para entender como projetar o armazenamento apropriado para o desempenho ideal de suas VMs.

## <a name="resource-groups"></a>Grupos de recursos
Componentes como VMs são agrupados logicamente para facilidade de gerenciamento e manutenção usando os [Grupos de Recursos do Azure](../../azure-resource-manager/resource-group-overview.md). Com os grupos de recursos, você pode criar, gerenciar e monitorar todos os recursos que compõem determinado aplicativo. Você também pode implementar [controles de acesso baseado em função](../../active-directory/role-based-access-control-what-is.md) para conceder acesso a outras pessoas de sua equipe apenas aos recursos necessários. Reserve tempo para planejar os grupos de recursos e as atribuições de função. Há diferentes abordagens para, de fato, criar e implementar grupos de recursos; portanto, não deixe de ler o [artigo sobre diretrizes de grupos de recursos](infrastructure-resource-groups-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para entender a melhor maneira de criar suas VMs.

## <a name="templates"></a>Modelos
É possível criar modelos, definidos por arquivos JSON declarativos, para criar suas VMs. Normalmente, os modelos também criam o armazenamento, a rede, as interfaces de rede, o endereçamento IP necessários etc., juntamente com as VMs em si. Você usa modelos para criar ambientes consistentes e reproduzíveis para fins de desenvolvimento e teste, a fim de replicar com facilidade os ambientes de produção e vice-versa. Leia mais sobre como [criar e usar modelos](../../azure-resource-manager/resource-group-overview.md#template-deployment) para entender como usá-los para criar e implantar suas VMs.

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]


