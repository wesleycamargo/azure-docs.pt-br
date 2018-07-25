---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 07/06/2018
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 36902edd7b2df472960d19b8ef9a4ebd4cdfe695
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2018
ms.locfileid: "37906673"
---
Este artigo fornece informações sobre as gerações anteriores de tamanhos de máquina virtual. Esses tamanhos ainda podem ser usados, mas as gerações mais recentes estão disponíveis.


## <a name="ds-series"></a>Série DS

ACU: 160

Armazenamento Premium: com suporte

Cache de Armazenamento Premium: com suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache e armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3,5 |7 |4 |4.000 / 32 (43) |3.200 / 32 |2 / 500 |
| Standard_DS2 |2 |7 |14 |8 |8.000 / 64 (86) |6.400 / 64 |2 / 1000 |
| Standard_DS3 |4 |14 |28 |16 |16.000 / 128 (172) |12.800 / 128 |4 / 2000 |
| Standard_DS4 |8 |28 |56 |32 |32.000 / 256 (344) |25.600 / 256 |8 / 4000 |

<br>

## <a name="ds-series---memory-optimized"></a>Série DS - otimizado para memória

ACU: 160 <sup>1</sup>

Armazenamento Premium: com suporte

Cache de Armazenamento Premium: com suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache e armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8.000 / 64 (72) |6.400 / 64 |2 / 1000 |
| Standard_DS12 |4 |28 |56 |16 |16.000 / 128 (144) |12.800 / 128 |4 / 2000 |
| Standard_DS13 |8 |56 |112 |32 |32.000 / 256 (288) |25.600 / 256 |8 / 4000 |
| Standard_DS14 |16 |112 |224 |64 |64.000 / 512 (576) |51.200 / 512 |8 / 8000 |

<sup>1</sup> A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série DS pode ser limitada pelo número, tamanho e distribuição dos discos anexados.  Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho das máquinas virtuais do Azure](../articles/virtual-machines/windows/premium-storage.md).



## <a name="d-series"></a>Série D 

ACU: 160

Armazenamento Premium: sem suporte

Cache de Armazenamento Premium: sem suporte

| Tamanho         | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps de leitura / MBps de gravação | Discos de dados máximos / taxa de transferência: IOPS | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3,5         | 50             | 3000 / 46 / 23                                           | 4 / 4 x 500                         | 2 / 500                 |
| Standard_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1000                     |
| Standard_D3  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 2000                     |
| Standard_D4  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 4000                     |

<br>

## <a name="d-series---memory-optimized"></a>Série D - otimizado para memória

ACU: 160

Armazenamento Premium: sem suporte

Cache de Armazenamento Premium: sem suporte

| Tamanho         | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps de leitura / MBps de gravação | Discos de dados máximos / taxa de transferência: IOPS | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1000                     |
| Standard_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 2000                     |
| Standard_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 4000                     |
| Standard_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 8000                |

<br>

## <a name="a-series---compute-intensive-instances"></a>Série A – Instâncias de computação intensiva

ACU: 225

Armazenamento Premium: sem suporte

Cache de Armazenamento Premium: sem suporte

Os tamanhos A8-A11 e série H também são conhecidos como *instâncias de computação intensiva*. O hardware de datacenter do Azure que executa esses tamanhos é projetado e otimizado para aplicativos de uso intensivo de computação e rede, incluindo aplicativos, modelagem e simulações de cluster HPC (computação de alto desempenho). A série de A8-A11 usa Intel Xeon E5-2670 a 2,6 GHz e a série H usa Intel Xeon E5-2667 v3 a 3,2 GHz.  Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e largura de banda de rede para cada tamanho neste agrupamento. 

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (HDD): GiB | Discos de dados máximos | Taxa de transferência máxima do disco de dados: IOPS | Máximo de NICs|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 <sup>1</sup> |8 |56 |382 |32 |32 x 500 |2 |
| Standard_A9 <sup>1</sup> |16 |112 |382 |64 |64x500 |4 |
| Standard_A10 |8 |56 |382 |32 |32 x 500 |2  |
| Standard_A11 |16 |112 |382 |64 |64x500 |4 |

<sup>1</sup>Para os aplicativos MPI, a rede de back-end RDMA dedicada é habilitada pela rede InfiniBand FDR, que fornece latência ultrabaixa e largura de banda alta.

<br>

## <a name="a-series"></a>Séria A

ACU: 50-100

Armazenamento Premium: sem suporte

Cache de Armazenamento Premium: sem suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (HDD): GiB | Discos de dados máximos | Taxa de transferência máxima do disco de dados: IOPS | Máximo de NICs/Largura de banda de rede esperado (Mbps)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 <sup>1</sup> |1 |0,768 |20 |1 |1 x 500 |2 / 100 |
| Standard_A1 |1 |1,75 |70 |2 |2x500 |2 / 500  |
| Standard_A2 |2 |3,5 |135 |4 |4x500 |2 / 500 |
| Standard_A3 |4 |7 |285 |8 |8 x 500 |2 / 1000 |
| Standard_A4 |8 |14 |605 |16 |16 x 500 |4 / 2000 |
| Standard_A5 |2 |14 |135 |4 |4x500 |2 / 500 |
| Standard_A6 |4 |28 |285 |8 |8 x 500 |2 / 1000 |
| Standard_A7 |8 |56 |605 |16 |16 x 500 |4 / 2000 |
<br>

<sup>1</sup> O tamanho A0 está assinado em excesso no hardware físico. Para este tamanho específico somente, outras implantações de clientes podem afetar o desempenho da carga de trabalho em execução. O desempenho relativo é descrito a seguir como a linha de base esperada, sujeito a uma variação aproximada de 15%.

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0 - A4 usando CLI e PowerShell

No modelo de implantação clássica, alguns nomes de tamanhos de VM são ligeiramente diferentes na CLI e no PowerShell:

* Standard_A0 é ExtraSmall 
* Standard_A1 é pequeno
* Standard_A2 é médio
* Standard_A3 é grande
* Standard_A4 é ExtraLarge

## <a name="basic-a"></a>A Básico

Armazenamento Premium: sem suporte

Cache de Armazenamento Premium: sem suporte

Os tamanhos da camada básicos são principalmente para as cargas de trabalho de desenvolvimento e outros aplicativos que não requerem o balanceamento de carga, dimensionamento automático ou máquinas virtuais que consomem muita memória.

|Tamanho – Tamanho\Nome | vCPU |Memória|NICs (Máx.)|Tamanho máximo do disco temporário |Máx. de discos de dados (1023 GB cada)|Máx. IOPS (300 por disco)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2| 20 GB|1|1 x 300|
|A1\Basic_A1|1|1,75 GB|2| 40 GB |2|2 x 300|
|A2\Basic_A2|2|3,5 GB|2| 60 GB|4|4 x 300|
|A3\Basic_A3|4|7 GB|2| 120 GB |8|8 x 300|
|A4\Basic_A4|8|14 GB|2| 240 GB |16|16 x 300|
 


