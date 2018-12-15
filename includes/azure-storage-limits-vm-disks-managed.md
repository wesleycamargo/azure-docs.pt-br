---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 12/12/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: bbb338d2b1d359d8e141b18a2beacd8b7faafe9c
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53326456"
---
**HDDs de máquina virtual gerenciados Standard**

| Tipo de disco Standard  | S4               | S6               | S10             | S15 | S20              | S30              | S40              | S50              | S60 *             | S70 *             | S80 *             |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Tamanho do Disk em GiB          | 32             | 64             | 128            | 256  | 512            | 1.024    | 2.048     | 4.095    | 8.192     | 16.384     | 32.767     |
| IOPS por disco       | Até 500              | Até 500              | Até 500              | Até 500 | Até 500              | Até 500              | Até 500             | Até 500              | Até 1.300              | Até 2.000              | Até 2.000              |
| Taxa de transferência por disco | Até 60 MiB/s | Até 60 MiB/s | Até 60 MiB/s | Até 60 MiB/s | Até 60 MiB/s | Até 60 MiB/s | Até 60 MiB/s | Até 60 MiB/s| Até 300 MiB/s | Até 500 MiB/s | Até 500 MiB/s |

**SSDs de máquina virtual gerenciados Standard**

| Tipo de Disco SSD Padrão  | E10               | E15               | E20             | E30 | E40              | E50              | E60 *             | E70 *             | E80 *             |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Tamanho do disco em GiB           | 128             | 256             | 512            | 1.024  | 2.048            | 4.095     | 8.192     | 16.384     | 32.767    |
| IOPS por disco       | Até 500              | Até 500              | Até 500              | Até 500 | Até 500              | Até 500              | Até 500             | Até 500              | Até 1.300              | Até 2.000              | Até 2.000              |
| Taxa de transferência por disco | Até 60 MB/s | Até 60 MB/s | Até 60 MB/s | Até 60 MB/s | Até 60 MB/s | Até 60 MB/s | Até 60 MB/s | Até 60 MB/s| Até 300 MiB/s |  Até 500 MiB/s | Até 500 MiB/s |

**Discos de máquina virtual gerenciados Premium: de acordo com os limites de disco**

| Tipo de disco Premium  | P4               | P6               | P10             | P15 | P20              | P30              | P40              | P50              | P60 *             | P70 *             | P80 *             |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Tamanho do disco em GiB           | 32             | 64             | 128            | 256  | 512            | 1.024    | 2.048     | 4.095    | 8.192     | 16.384     | 32.767     |
| IOPS por disco       | Até 120 | Até 240              | Até 500              | Até 1.100 | Até 2.300              | Até 5.000              | Até 7.500             | Até 7.500              | Até 12.500              | Até 15.000              | Até 20.000              |
| Taxa de transferência por disco | Até 25 MiB/s | Até 50 MiB/s | Até 100 MiB/s | Até 125 MiB/s | Até 150 MiB/s | Até 200 MiB/s | Até 250 MiB/s | Até 250 MiB/s| Até 480 MiB/s | Até 750 MiB/s | Até 750 MiB/s |

**Discos de máquina virtual gerenciados Premium: de acordo com os limites de VM**

| Recurso | Limite padrão |
| --- | --- |
| IOPS máxima por VM |80.000 IOPS com VM GS5 |
| Taxa de transferência máxima por VM |2.000 MB/s com VM GS5 |
