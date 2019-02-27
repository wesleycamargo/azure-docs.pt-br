---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8a067474fb172d4ff7a7fdf7eb6d24536bd2d017
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56443387"
---
# <a name="what-disk-types-are-available-in-azure"></a>Quais tipos de disco estão disponíveis no Azure?

Atualmente, os discos gerenciados do Azure oferecem quatro tipos de disco, três dos quais estão em GA (disponibilidade geral) e outro que está atualmente em versão prévia. Esses quatro tipos de disco têm seus próprios cenários de cliente de destino.

## <a name="disk-comparison"></a>Comparação de disco

A tabela a seguir oferece uma comparação entre discos ultra SSD (unidade de estado sólido) (versão prévia), SSD Premium, SSD Standard e HD (unidades de disco rígido) padrão de discos gerenciados, para ajudá-lo a decidir o que usar.

|   | SSD Ultra (versão prévia)   | SSD Premium   | SSD Standard   | HDD Standard   |
|---------|---------|---------|---------|---------|
|Tipo de disco   |SSD   |SSD   |SSD   |HDD   |
|Cenário   |Cargas de trabalho de E/S intensiva, como SAP HANA, bancos de dados de camada superior (por exemplo, SQL, Oracle) e outras cargas de trabalho de transações pesadas.   |Cargas de trabalho confidenciais produção e desempenho   |Servidores Web, aplicativos empresariais pouco usados e desenvolvimento/teste   |Backup, não crítico, acesso não frequente   |
|Tamanho do disco   |65.536 GiB (GibiByte) (versão prévia)   |4.095 GiB (GA), 32.767 GiB (versão prévia)    |4.095 (GA) GiB, 32.767 GiB (versão prévia)   |4.095 GiB (GA), 32.767 GiB (versão prévia)   |
|Taxa de transferência máxima   |2.000 MiB/s (versão prévia)   |250 MiB/s (GA), 750 MiB/s (versão prévia)   |60 MiB/s (GA), 500 MiB/s (versão prévia)   |60 MiB/s (GA), 500 MiB/s (versão prévia)   |
|IOPS Máxima   |160.000 (versão prévia)   |7.500 (GA), 20.000 (versão prévia)   |500 (GA), 2.000 (versão prévia)   |500 (GA), 2.000 (versão prévia)   |

## <a name="ultra-ssd-preview"></a>SSD Ultra (versão prévia)

O SSD Ultra do Azure (versão prévia) proporciona alta taxa de transferência, alta IOPS e armazenamento em disco de baixa latência consistente para VMs de IaaS do Azure. Alguns benefícios adicionais do SSD Ultra incluem a capacidade de alterar dinamicamente o desempenho do disco, juntamente com suas cargas de trabalho, sem a necessidade de reiniciar as máquinas virtuais. Os SSDs Ultra são adequados para cargas de trabalho de grande volume de dados, como SAP HANA, bancos de dados de camada superior e cargas de trabalho de transações pesadas. Os SSDs Ultra só podem ser usados como discos de dados. É recomendável usar SSDs Premium como discos de sistema operacional.

### <a name="performance"></a>Desempenho

Ao provisionar um disco Ultra, você pode configurar a capacidade e o desempenho do disco de forma independente. Os SSDs Ultra vêm em vários tamanhos fixos, de 4 GiB até 64 TiB e oferecem um modelo de configuração de desempenho flexível que permite que você configure IOPS e taxa de transferência de modo independente.

Alguns recursos importantes do SSD Ultra são:

- Capacidade do disco: A capacidade do SSD Ultra varia de 4 GiB até 64 TiB.
- IOPS do disco: O SSD Ultra é compatível com limites de IOPS de 300 IOPS/GiB, até um máximo de 160 mil IOPS por disco. Para alcançar o IOPS que você provisionou, certifique-se de que o IOPS do disco selecionado seja menor que o IOPS da VM. O IOPS de disco mínimo é de 100 IOPS.
- Taxa de transferência do disco: com o SSD Ultra, o limite de taxa de transferência de um único disco é de 256 KiB/s para cada IOPS provisionado, até um máximo de 2.000 MBps por disco (em que MBps = 10^6 Bytes por segundo). A taxa de transferência mínima de disco é de 1 MiB.

### <a name="disk-size"></a>Tamanho do disco

|Tamanho do Disco (GiB)  |Limites de IOPS  |Limite de taxa de transferência (MBps)  |
|---------|---------|---------|
|4     |1.200         |300         |
|8     |2.400         |600         |
|16     |4.800         |1.200         |
|32     |9.600         |2.000         |
|64     |19.200         |2.000         |
|128     |38.400         |2.000         |
|256     |76.800         |2.000         |
|512     |80.000         |2.000         |
|1.024 a 65.536 (os tamanhos nesse intervalo aumentam em incrementos de 1 TiB)     |160.000         |2.000         |

### <a name="preview-scope-and-limitations"></a>Limitações e escopo da versão prévia

Durante a versão prévia, SSDs Ultra:

- Terão suporte no Leste dos EUA 2 em uma única zona de disponibilidade  
- Só poderão ser usados com zonas de disponibilidade (conjuntos de disponibilidade e implantações de VM única fora das zonas não terão a possibilidade de anexar um disco Ultra)
- Serão compatíveis somente com VMs ES/DS v3
- Só estarão disponíveis como discos de dados e serão compatíveis somente com tamanho de setor físico de 4k  
- Só podem ser criados como discos vazios  
- Atualmente, só podem ser implantados usando os modelos do Azure Resource Manager, a CLI e o SDK do Python.
- Não são compatíveis ainda com instantâneos de disco, imagens de VM, conjuntos de disponibilidade, conjuntos de dimensionamento de máquinas virtuais nem com a criptografia de disco do Azure.
- Não são compatíveis ainda com a integração com o Backup do Azure nem com o Azure Site Recovery.
- Assim como acontece com a [maioria das versões prévias](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), esse recurso não deve ser usado em cargas de trabalho de produção até a GA (disponibilidade geral).

## <a name="premium-ssd"></a>SSD Premium

Os SSDs Premium do Azure oferecem compatibilidade de disco de alto desempenho e baixa latência para VMs (máquinas virtuais) com cargas de trabalho com uso intensivo de E/S (entrada/saída). Para aproveitar a velocidade e o desempenho dos discos de armazenamento Premium, você pode migrar os discos de VM existentes para os SSDs Premium. Os SSDs Premium são adequados para aplicativos de produção críticos.

### <a name="disk-size"></a>Tamanho do disco

Tamanhos marcados com um asterisco estão atualmente em versão prévia.

| Tamanhos de unidades SSD Premium  | P4               | P6               | P10             | P15 | P20              | P30              | P40              | P50              | P60*              | P70*              | P80*              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Tamanho do disco em GiB           | 32             | 64             | 128            | 256  | 512            | 1.024    | 2.048     | 4.095    | 8.192     | 16.384     | 32.767     |
| IOPS por disco       | Até 120 | Até 240              | Até 500              | Até 1.100 | Até 2.300              | Até 5.000              | Até 7.500             | Até 7.500              | Até 12.500              | Até 15.000              | Até 20.000              |
| Taxa de transferência por disco | Até 25 MiB/s | Até 50 MiB/s | Até 100 MiB/s | Até 125 MiB/s | Até 150 MiB/s | Até 200 MiB/s | Até 250 MiB/s | Até 250 MiB/s| Até 480 MiB/s | Até 750 MiB/s | Até 750 MiB/s |

## <a name="standard-ssd"></a>SSD Standard

Os SSDs Standard do Azure são uma opção econômica de armazenamento otimizado para cargas de trabalho que necessitam de desempenho consistente em níveis de IOPS mais baixos. SSD padrão oferece uma experiência de bom nível de entrada para aqueles que desejam migrar para a nuvem, especialmente se passar por problemas com a variação das cargas de trabalho em execução nas soluções de HD no local. Os SSDs Standard oferecem melhor disponibilidade, consistência, confiabilidade e latência em comparação aos discos HD. Os SSDs Standard são adequado para servidores Web, servidores de aplicativos de IOPS baixo, aplicativos empresariais pouco usados e cargas de trabalho de Desenvolvimento/Teste.

### <a name="disk-size"></a>Tamanho do disco

Tamanhos marcados com um asterisco estão atualmente em versão prévia.

| Tamanhos de SSD Standard  | E10               | E15               | E20             | E30 | E40              | E50              | E60*              | E70*              | E80*              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Tamanho do disco em GiB           | 128             | 256             | 512            | 1.024  | 2.048            | 4.095     | 8.192     | 16.384     | 32.767    |
| IOPS por disco       | Até 500              | Até 500              | Até 500              | Até 500 | Até 500              | Até 500              | Até 500             | Até 500              | Até 1.300              | Até 2.000              | Até 2.000              |
| Taxa de transferência por disco | Até 60 MiB/s | Até 60 MiB/s | Até 60 MiB/s | Até 60 MiB/s | Até 60 MiB/s | Até 60 MiB/s | Até 60 MiB/s | Até 60 MiB/s| Até 300 MiB/s |  Até 500 MiB/s | Até 500 MiB/s |

## <a name="standard-hdd"></a>HDD Standard

Os HDs Standard do Azure oferecem compatibilidade de disco confiável e de baixo custo para VMs que executam cargas de trabalho insensíveis a latência. Ele também oferece suporte a tabelas, blobs, filas e arquivos. Com o Armazenamento Standard, os dados são armazenados em HDs (unidades de disco rígido). Ao trabalhar com VMs, você pode usar discos SSD e HD Standard para cenários de Dev/Test e cargas de trabalho menos críticas. O Armazenamento Standard está disponível em todas as regiões do Azure.

### <a name="disk-size"></a>Tamanho do disco

Tamanhos marcados com um asterisco estão atualmente em versão prévia.

| Tipo de disco Standard  | S4               | S6               | S10             | S15 | S20              | S30              | S40              | S50              | S60*              | S70*              | S80*              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Tamanho do Disk em GiB          | 32             | 64             | 128            | 256  | 512            | 1.024    | 2.048     | 4.095    | 8.192     | 16.384     | 32.767     |
| IOPS por disco       | Até 500              | Até 500              | Até 500              | Até 500 | Até 500              | Até 500              | Até 500             | Até 500              | Até 1.300              | Até 2.000              | Até 2.000              |
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
