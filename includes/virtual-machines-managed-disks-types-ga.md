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
ms.openlocfilehash: 885bc1c627626ee7ba4f391be31131b18fa1ab39
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65211915"
---
## <a name="premium-ssd"></a>SSD Premium

Os SSDs Premium do Azure oferecem compatibilidade de disco de alto desempenho e baixa latência para VMs (máquinas virtuais) com cargas de trabalho com uso intensivo de E/S (entrada/saída). Para aproveitar a velocidade e o desempenho dos discos de armazenamento Premium, você pode migrar os discos de VM existentes para os SSDs Premium. Os SSDs Premium são adequados para aplicativos de produção críticos.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Quando você provisiona um disco de armazenamento premium, ao contrário do armazenamento padrão, a capacidade, IOPS e taxa de transferência de disco são garantidos. Por exemplo, se você criar um disco P50, o Azure provisionará uma capacidade de armazenamento de 4.095 GB, 7.500 IOPS e uma taxa de transferência de 250 MB/s para o disco. O aplicativo pode usar a capacidade e o desempenho no todo ou em parte. Os discos SSD Premium são projetados para oferecer desempenho 99,9% do tempo de destino.

## <a name="standard-ssd"></a>SSD Standard

Os SSDs Standard do Azure são uma opção econômica de armazenamento otimizado para cargas de trabalho que necessitam de desempenho consistente em níveis de IOPS mais baixos. SSD padrão oferece uma experiência de bom nível de entrada para aqueles que desejam migrar para a nuvem, especialmente se passar por problemas com a variação das cargas de trabalho em execução nas soluções de HD no local. Os SSDs Standard oferecem melhor disponibilidade, consistência, confiabilidade e latência em comparação aos discos HD. Os SSDs Standard são adequado para servidores Web, servidores de aplicativos de IOPS baixo, aplicativos empresariais pouco usados e cargas de trabalho de Desenvolvimento/Teste.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

SSDs padrão é projetado para fornecer latências de milissegundo de dígito único para a maioria das operações de e/s e entregar o IOPS e taxa de transferência até os limites descritos a tabela 99% do tempo anterior. IOPS e taxa de transferência reais podem variar, às vezes, dependendo dos padrões de tráfego. SSDs Padrão fornecerão um desempenho mais consistente do que os discos HD com a menor latência.

## <a name="standard-hdd"></a>HDD Standard

Os HDs Standard do Azure oferecem compatibilidade de disco confiável e de baixo custo para VMs que executam cargas de trabalho insensíveis a latência. Ele também oferece suporte a tabelas, blobs, filas e arquivos. Com o Armazenamento Standard, os dados são armazenados em HDs (unidades de disco rígido). Ao trabalhar com VMs, você pode usar discos SSD e HD Standard para cenários de Dev/Test e cargas de trabalho menos críticas. O Armazenamento Standard está disponível em todas as regiões do Azure.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

## <a name="billing"></a>Cobrança

Ao usar discos gerenciados, as seguintes considerações de cobrança se aplicam:

- Tipo de disco
- Tamanho do disco gerenciado
- Instantâneos
- Transferências de dados de saída
- Número de transações

**Tamanho do disco gerenciado**: discos gerenciados são cobrados no tamanho provisionado. O Azure mapeia o tamanho provisionado (arredondado) para o tamanho de disco oferecido mais próximo. Para obter detalhes sobre os tamanhos de disco oferecidos, confira as tabelas anteriores. Cada disco é mapeado para um tamanho de disco provisionado compatível e é cobrado de acordo. Por exemplo, se você provisionar uma SSD Standard de 200 GiB, ela será mapeada para a oferta de tamanho de disco E15 (256 GiB). A cobrança por qualquer disco provisionado é rateada por hora usando o preço mensal para a oferta de Armazenamento Premium. Por exemplo, se você provisionou um disco E10 e o excluiu após 20 horas, será cobrado pela a oferta E10 rateada em 20 horas. Isso é independente da quantidade de dados reais gravados no disco.

**Instantâneos**: Instantâneos são cobrados com base no tamanho usado. Por exemplo, se você criar um instantâneo de um disco gerenciado com capacidade provisionada de 64 GiB e tamanho real de dados usados de 10 GiB, o instantâneo será cobrado apenas pelo tamanho de dados usados de 10 GiB.