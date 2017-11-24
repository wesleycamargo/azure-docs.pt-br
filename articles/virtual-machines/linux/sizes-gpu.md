---
title: "Tamanhos de VM Linux do Azure — GPU | Microsoft Docs"
description: "Lista os diferentes tamanhos otimizados para GPU disponíveis para máquinas virtuais Linux no Azure."
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2017
ms.author: jonbeck
ms.openlocfilehash: 645f69e71c5a13bb70edabfd22f51ed5df619693
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2017
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Tamanhos de máquinas virtuais com GPU otimizadas

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

Para etapas de verificação e instalação do driver, consulte [Instalação do driver na série N para Linux](n-series-driver-setup.md).

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Não recomendamos instalar o X server nem outros sistemas que usem o `Nouveau` driver em VMs do Ubuntu NC. Antes de instalar os drivers de GPU NVIDIA, você precisa desabilitar o driver `Nouveau`.  

## <a name="other-sizes"></a>Outros tamanhos
- [Propósito geral](sizes-general.md)
- [Computação otimizada](sizes-compute.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [Computação de alto desempenho](sizes-hpc.md)

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.