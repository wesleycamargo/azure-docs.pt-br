---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: ac8686d0ea5704492bfc2e08972a2f70c9b34c43
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37906812"
---
As máquinas virtuais da série H do Azure são as mais recentes em VMs de computação de alto desempenho destinadas a necessidades computacionais de alto nível, como modelagem molecular e dinâmica de fluido computacional. Essas VMs de 8 e 16 vCPUs baseiam-se na tecnologia do processador Intel Haswell E5-2667 V3 apresentando memória DDR4 e armazenamento temporário baseado em SSD. 

Além de potência de CPU considerável, a série H oferece diversas opções para RDMA e rede de baixa latência usando FDR InfiniBand e várias configurações de memória para dar suporte a requisitos computacionais com uso intensivo de memória.



## <a name="h-series"></a>Série H

ACU: 290-300

Armazenamento Premium: sem suporte

Cache de Armazenamento Premium: sem suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima do disco: IOPS | Máximo de NICs |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |32 |32 x 500 |2  |
| Standard_H16 |16 |112 |2000 |64 |64 x 500 |4 |
| Standard_H8m |8 |112 |1000 |32 |32 x 500 |2  |
| Standard_H16m |16 |224 |2000 |64 |64 x 500 |4  |
| Standard_H16r <sup>1</sup> |16 |112 |2000 |64 |64 x 500 |4  |
| Standard_H16mr <sup>1</sup> |16 |224 |2000 |64 |64 x 500 |4 |

<sup>1</sup> Para os aplicativos MPI, a rede de back-end RDMA dedicada é habilitada pela rede InfiniBand FDR, que fornece latência ultrabaixa e largura de banda alta.

<br>






