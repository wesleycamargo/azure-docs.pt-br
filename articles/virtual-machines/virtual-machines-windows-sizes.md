
---
title: Tamanhos das VMs do Windows no Azure | Microsoft Docs
description: "Lista os tamanhos diferentes disponíveis de máquinas virtuais do Windows no Azure."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: aabf0d30-04eb-4d34-b44a-69f8bfb84f22
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 365d6ad9ec0e0a7ad8d9742d863540646257e298
ms.lasthandoff: 03/27/2017

---

# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Tamanhos das máquinas virtuais do Windows no Azure

Este artigo descreve os tamanhos e as opções disponíveis de máquinas virtuais do Azure que você pode usar para executar seus aplicativos Windows e cargas de trabalho. Ele também fornece considerações de implantação a serem observadas ao planejar o uso desses recursos.  Este artigo também está disponível para [máquinas virtuais Linux](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!IMPORTANT]
>* Para obter informações sobre os preços dos vários tamanhos, consulte [Preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). 
>* Para ver os limites gerais em VMs do Azure, consulte [Limites de assinatura e serviços do Azure, cotas e restrições](../azure-subscription-service-limits.md).
>* Os custos de armazenamento são calculados separadamente com base nas páginas usadas na conta de armazenamento. Para obter detalhes, [Preço de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).
> * Saiba mais sobre como as [ACUs (unidade de computação do Azure)](virtual-machines-windows-acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
>
>
<br>    




| Tipo                     | Tamanhos           |    Descrição       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Propósito geral](virtual-machines-windows-sizes-general.md)          | DSv2, Dv2, DS, D, Av2, A0-7 | Relação equilibrada de CPU/memória. Ideal para teste e desenvolvimento, bancos de dados pequenos a médios e servidores Web de tráfego baixo a médio. |
| [Computação otimizada](virtual-machines-windows-sizes-compute.md)        | Fs, F             | Alta relação de CPU/memória. Boa para servidores web de tráfego médio, dispositivos de rede, processos de lote e servidores de aplicativo.        |
| [Memória otimizada](virtual-machines-windows-sizes-memory.md)         | GS, G, DSv2, DS   | Alta relação de memória/núcleo. Ótima para servidores de banco de dados relacionais, caches médios a grandes e análises na memória.                 |
| [Armazenamento otimizado](virtual-machines-windows-sizes-storage.md)        | Ls                | Alta taxa de transferência de disco e de E/S. Ideal para Big Data, SQL e bancos de dados NoSQL.                                                         |
| [GPU](virtual-machines-windows-sizes-gpu.md)            | NV, NC            | Máquinas virtuais especializadas, destinadas para renderização gráfica e edição de vídeo pesadas. Disponível com uma ou várias GPUs.       |
| [Computação de alto desempenho](virtual-machines-windows-sizes-hpc.md) | H, A8-11          | Nossas máquinas virtuais de CPU mais rápidas e potentes com adaptadores de rede de alta taxa de transferência (RDMA) opcionais. 

<br>

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](virtual-machines-windows-acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.

Saiba mais sobre os diferentes tamanhos de VM que estão disponíveis:
- [Propósito geral](virtual-machines-windows-sizes-general.md)
- [Computação otimizada](virtual-machines-windows-sizes-compute.md)
- [Memória otimizada](virtual-machines-windows-sizes-memory.md)
- [Armazenamento otimizado](virtual-machines-windows-sizes-storage.md)
- [GPU otimizada](virtual-machines-windows-sizes-gpu.md)
- [Computação de alto desempenho](virtual-machines-windows-sizes-hpc.md)




