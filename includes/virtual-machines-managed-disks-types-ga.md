---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/13/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0201776914610ddaca50a670fc500156a65cd734
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2019
ms.locfileid: "58115137"
---
## <a name="premium-ssd"></a>SSD Premium

Os SSDs Premium do Azure oferecem compatibilidade de disco de alto desempenho e baixa latência para VMs (máquinas virtuais) com cargas de trabalho com uso intensivo de E/S (entrada/saída). Para aproveitar a velocidade e o desempenho dos discos de armazenamento Premium, você pode migrar os discos de VM existentes para os SSDs Premium. Os SSDs Premium são adequados para aplicativos de produção críticos.

### <a name="disk-size"></a>Tamanho do disco

Tamanhos marcados com um asterisco estão atualmente em versão prévia.

| Tamanhos de unidades SSD Premium | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60* | P70* | P80* |
|-------------------|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Tamanho do disco em GiB | 32 | 64 | 128 | 256 | 512 | 1.024 | 2.048 | 4.095 | 8.192 | 16.384 | 32.767 |
| IOPS por disco | Até 120 | Até 240 | Até 500 | Até 1.100 | Até 2.300 | Até 5.000 | Até 7.500 | Até 7.500 | Até 12.500 | Até 15.000 | Até 20.000 |
| Taxa de transferência por disco | Até 25 MiB/s | Até 50 MiB/s | Até 100 MiB/s | Até 125 MiB/s | Até 150 MiB/s | Até 200 MiB/s | Até 250 MiB/s | Até 250 MiB/s| Até 480 MiB/s | Até 750 MiB/s | Até 750 MiB/s |

## <a name="standard-ssd"></a>SSD Standard

Os SSDs Standard do Azure são uma opção econômica de armazenamento otimizado para cargas de trabalho que necessitam de desempenho consistente em níveis de IOPS mais baixos. SSD padrão oferece uma experiência de bom nível de entrada para aqueles que desejam migrar para a nuvem, especialmente se passar por problemas com a variação das cargas de trabalho em execução nas soluções de HD no local. Os SSDs Standard oferecem melhor disponibilidade, consistência, confiabilidade e latência em comparação aos discos HD. Os SSDs Standard são adequado para servidores Web, servidores de aplicativos de IOPS baixo, aplicativos empresariais pouco usados e cargas de trabalho de Desenvolvimento/Teste.

### <a name="disk-size"></a>Tamanho do disco

Tamanhos marcados com um asterisco estão atualmente em versão prévia.

| Tamanhos de SSD Standard | E4 | E6 | E10 | E15 | E20 | E30 | E40 | E50 | E60* | E70* | E80* |
|--------------------|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Tamanho do disco em GiB | 32 | 64 | 128 | 256 | 512 | 1.024 | 2.048 | 4.095 | 8.192 | 16.384 | 32.767 |
| IOPS por disco | Até 120 | Até 240 | Até 500 | Até 500 | Até 500 | Até 500 | Até 500 | Até 500 | Até 1.300 | Até 2.000 | Até 2.000 |
| Taxa de transferência por disco |  Até 25 MiB/s |  Até 50 MiB/s  |  Até 60 MiB/s | Até 60 MiB/s | Até 60 MiB/s | Até 60 MiB/s | Até 60 MiB/s | Até 60 MiB/s| Até 300 MiB/s |  Até 500 MiB/s | Até 500 MiB/s |

## <a name="standard-hdd"></a>HDD Standard

Os HDs Standard do Azure oferecem compatibilidade de disco confiável e de baixo custo para VMs que executam cargas de trabalho insensíveis a latência. Ele também oferece suporte a tabelas, blobs, filas e arquivos. Com o Armazenamento Standard, os dados são armazenados em HDs (unidades de disco rígido). Ao trabalhar com VMs, você pode usar discos SSD e HD Standard para cenários de Dev/Test e cargas de trabalho menos críticas. O Armazenamento Standard está disponível em todas as regiões do Azure.

### <a name="disk-size"></a>Tamanho do disco

Tamanhos marcados com um asterisco estão atualmente em versão prévia.

| Tipo de disco Standard | S4 | S6 | S10 | S15 | S20 | S30 | S40 | S50 | S60* | S70* | S80* |
|--------------------|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Tamanho do Disk em GiB | 32 | 64 | 128 | 256 | 512 | 1.024 | 2.048 | 4.095 | 8.192 | 16.384 | 32.767 |
| IOPS por disco | Até 500 | Até 500 | Até 500 | Até 500 | Até 500 | Até 500 | Até 500 | Até 500 | Até 1.300 | Até 2.000 | Até 2.000 |
| Taxa de transferência por disco | Até 60 MiB/s | Até 60 MiB/s | Até 60 MiB/s | Até 60 MiB/s | Até 60 MiB/s | Até 60 MiB/s | Até 60 MiB/s | Até 60 MiB/s| Até 300 MiB/s | Até 500 MiB/s | Até 500 MiB/s |

## <a name="billing"></a>Cobrança

Ao usar discos gerenciados, as seguintes considerações de cobrança se aplicam:

- Tipo de disco
- Tamanho do disco gerenciado
- Instantâneos
- Transferências de dados de saída
- Número de transações

**Tamanho do disco gerenciado**: discos gerenciados são cobrados no tamanho provisionado. O Azure mapeia o tamanho provisionado (arredondado) para o tamanho de disco oferecido mais próximo. Para obter detalhes sobre os tamanhos de disco oferecidos, confira as tabelas anteriores. Cada disco é mapeado para um tamanho de disco provisionado compatível e é cobrado de acordo. Por exemplo, se você provisionar uma SSD Standard de 200 GiB, ela será mapeada para a oferta de tamanho de disco E15 (256 GiB). A cobrança por qualquer disco provisionado é rateada por hora usando o preço mensal para a oferta de Armazenamento Premium. Por exemplo, se você provisionou um disco E10 e o excluiu após 20 horas, será cobrado pela a oferta E10 rateada em 20 horas. Isso é independente da quantidade de dados reais gravados no disco.

**Instantâneos**: Instantâneos são cobrados com base no tamanho usado. Por exemplo, se você criar um instantâneo de um disco gerenciado com capacidade provisionada de 64 GiB e tamanho real de dados usados de 10 GiB, o instantâneo será cobrado apenas pelo tamanho de dados usados de 10 GiB.