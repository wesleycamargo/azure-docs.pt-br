---
title: Diretrizes de conjuntos de disponibilidade do Azure | Microsoft Docs
description: "Saiba mais sobre as principais diretrizes de design e implementação para a implantação de conjuntos de disponibilidade em serviços de infraestrutura do Azure."
documentationcenter: 
services: virtual-machines-windows
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f9449f58-664b-4d5d-82f6-84c5d083047f
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 2e981b0bebbc88fdad23ce8279762fe717e48a4c
ms.openlocfilehash: 59ef4be4854c4625bed5f9f1f32e7771ccb6979d


---
# <a name="azure-availability-sets-guidelines"></a>Diretrizes de conjuntos de disponibilidade do Azure
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

Este artigo concentra-se em compreender as etapas de planejamento necessárias para conjuntos de disponibilidade para garantir que seus aplicativos permaneçam acessíveis durante eventos planejados ou não planejados.

## <a name="implementation-guidelines-for-availability-sets"></a>Diretrizes de implementação para conjuntos de disponibilidade
Decisões:

* Quantos conjuntos de disponibilidade são necessários para as várias funções e camadas da infraestrutura do seu aplicativo?

Tarefas:

* Defina o número de VMs em cada camada de aplicativo que você precisa.
* Determine se você precisa ajustar o número de falhas ou atualizar domínios a serem usados para seu aplicativo.
* Definir os conjuntos de disponibilidade necessários usando sua convenção de nomenclatura e quais VMs residem neles. Uma VM pode residir apenas em um conjunto de disponibilidade. 

## <a name="availability-sets"></a>Conjuntos de disponibilidade
No Azure, VMs (máquinas virtuais) podem ser depositadas em um agrupamento lógico denominado um conjunto de disponibilidade. Quando você cria VMs em um conjunto de disponibilidade, a plataforma do Azure distribui o posicionamento dessas VMs na infraestrutura subjacente. Caso ocorra um evento de manutenção planejada para a plataforma do Azure ou uma falha de infraestrutura/hardware subjacente, o uso de conjuntos de disponibilidade garante que pelo menos uma VM permaneça em execução.

Como prática recomendada, os aplicativos não devem residir em uma única VM. Um conjunto de disponibilidade que contém uma única VM não obtém nenhuma proteção contra eventos planejados ou não planejados na plataforma Azure. O [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines) requer duas ou mais VMs em um conjunto de disponibilidade para permitir a distribuição de VMs na infraestrutura subjacente.

A infraestrutura subjacente no Azure é dividida em atualizar domínios e domínios de falha. Esses domínios são definidos pelos hosts que compartilham um ciclo de atualização comum ou uma infraestrutura física semelhante, como energia e rede. O Azure distribui automaticamente as VMs em um conjunto de disponibilidade entre domínios para manter a disponibilidade e a tolerância a falhas. Dependendo do tamanho do seu aplicativo e o número de VMs em um conjunto de disponibilidade, você pode ajustar o número de domínios que deseja usar. Você pode ler mais sobre [gerenciamento de disponibilidade e o uso dos domínios de atualização e de falha](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Ao projetar sua infraestrutura de aplicativos, planeje as camadas de aplicativo que serão usadas. Agrupe VMs que têm a mesma finalidade em conjuntos de disponibilidade, como um conjunto de disponibilidade para suas VMs de front-end executando IIS. Crie um conjunto de disponibilidade separado para suas VMs de back-end executando o SQL Server. O objetivo é garantir que cada componente do aplicativo seja protegido por um conjunto de disponibilidade e pelo menos uma instância sempre permaneça em execução.

Balanceadores de carga podem ser utilizados antes de cada camada de aplicativo para trabalhar junto com um conjunto de disponibilidade e certificar-se de que o tráfego sempre possa ser roteado para uma instância em execução. Sem um balanceador de carga, suas VMs podem continuar em execução durante manutenções planejadas e não planejadas, mas os usuários finais não poderá resolvê-los se a VM primária não estiver disponível.

Projete o aplicativo para alta disponibilidade na camada de armazenamento. A prática recomendada é usar uma conta de armazenamento separada para cada VM em um Conjunto de Disponibilidade. Mantenha todos os discos (sistema operacional e dados) associados a uma VM na mesma conta de armazenamento. Considere os [limites](../storage/storage-scalability-targets.md) da conta de armazenamento ao adicionar mais VHDs a uma conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]




<!--HONumber=Dec16_HO3-->


