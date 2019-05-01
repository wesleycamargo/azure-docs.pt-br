---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 04/26/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 4b7cda593bd4dd39a7220aa282529535c6a63bea
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64914530"
---
Série H máquinas virtuais (VMs) são projetadas para oferecer desempenho de classe de liderança, escalabilidade MPI e economia para uma variedade de cargas de trabalho HPC reais.

As VMs de série HB são otimizadas para aplicativos orientados por largura de banda de memória, tais como dinâmica de fluidos, análise explícita de elementos limitados e modelagem de clima. As VMs HB contam com 60 núcleos do processador AMD EPYC 7551, 4 GB de RAM por núcleo de CPU e nenhum hyperthreading. A plataforma AMD EPYC oferece mais de 260 GB/s de largura de banda de memória.

As VMs da série HC são otimizadas para aplicativos orientados pela computação densa, como análise de elemento finito implícita, molecular dynamics e química computacional. As VMs HC contam com 44 núcleos do processador Intel Xeon Platinum 8168, 8 GB de RAM por núcleo de CPU e nenhum hyperthreading. A plataforma Intel Xeon Platinum dá suporte a rico ecossistema de ferramentas de software, como o Intel Math Kernel Library da Intel.

100 Gb/s de recurso HB e VMs HC Mellanox EDR InfiniBand em uma fat sem bloqueio de configuração para um desempenho consistente RDMA árvore. HB e HC VMs oferece suporte a drivers de Mellanox/OFED padrão, de modo que todos os tipos MPI e versões, bem como verbos RDMA, também têm suporte.

VMs da série H são otimizadas para aplicativos orientados pela alta frequências de CPU ou memória grande por requisitos básicos. Série H VMs recurso 8 ou 16 2667 do Intel Xeon E5 v3 processador núcleos, 7 ou 14 GB de RAM por núcleo da CPU e nenhum hyperthreading. Recursos de série H 56 Gb/s Mellanox FDR InfiniBand em uma fat sem bloqueio de configuração para um desempenho consistente RDMA árvore. Suportam a VMs da série H Intel MPI 5.x e MS-MPI.

## <a name="hb-series"></a>HB-series

Armazenamento Premium: Suporte para o armazenamento Premium de cache: Com suporte

| Tamanho | vCPU | Processador | Memória (GB) | Largura de banda de memória GB/s | Frequência de base da CPU (GHz) | Frequência de todos os núcleos (GHz, horário de pico) | Frequência de núcleo único (GHz, horário de pico) | Desempenho de RDMA (GB/s) | Suporte do MPI | Armazenamento temporário (GB) | Discos de dados máximos | Placas de rede Ethernet máx |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2,0 | 2.55 | 2.55 | 100 | Todos | 700 | 4 | 1 |

<br>

## <a name="hc-series"></a>Série de HC

Armazenamento Premium: Suporte para o armazenamento Premium de cache: Com suporte


| Tamanho | vCPU | Processador | Memória (GB) | Largura de banda de memória GB/s | Frequência de base da CPU (GHz) | Frequência de todos os núcleos (GHz, horário de pico) | Frequência de núcleo único (GHz, horário de pico) | Desempenho de RDMA (GB/s) | Suporte do MPI | Armazenamento temporário (GB) | Discos de dados máximos | Placas de rede Ethernet máx |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Todos | 700 | 4 | 1 |


<br>

## <a name="h-series"></a>Série H

ACU: 290-300

Armazenamento Premium:  Sem suporte

Cache de Armazenamento Premium:  Sem suporte

| Tamanho | vCPU | Processador | Memória (GB) | Largura de banda de memória GB/s | Frequência de base da CPU (GHz) | Frequência de todos os núcleos (GHz, horário de pico) | Frequência de núcleo único (GHz, horário de pico) | Desempenho de RDMA (GB/s) | Suporte do MPI | Armazenamento temporário (GB) | Discos de dados máximos | Placas de rede Ethernet máx |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 | 8 | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16 | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 |  56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m | 8 | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | 2000 | Intel 5.x, MS-MPI | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | 2000 | Intel 5.x, MS-MPI | 64 | 4 |

<sup>1</sup> Para os aplicativos MPI, a rede de back-end RDMA dedicada é habilitada pela rede InfiniBand FDR, que fornece latência ultrabaixa e largura de banda alta.

<br>