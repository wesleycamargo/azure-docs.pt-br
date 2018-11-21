---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 050308e1c8de160f1671ded991e550087299ae2f
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285734"
---
# <a name="ultra-ssd-preview-managed-disks-for-azure-virtual-machine-workloads"></a>Discos Gerenciados SSD Ultra (versão prévia) para cargas de trabalho de Máquina Virtual do Azure

O SSD Ultra do Azure (versão prévia) proporciona alta taxa de transferência, alta IOPS e armazenamento em disco de baixa latência consistente para VMs de IaaS do Azure. Essa nova oferta fornece o melhor em desempenho de linha, nos mesmos níveis de disponibilidade que nossas ofertas de discos atuais. Os benefícios adicionais do SSD Ultra incluem a capacidade de alterar dinamicamente o desempenho do disco, juntamente com suas cargas de trabalho, sem a necessidade de reiniciar as máquinas virtuais. O SSD Ultra é adequado para cargas de trabalho de grande volume de dados, como SAP HANA, bancos de dados de camada superior e cargas de trabalho de transações pesadas.

## <a name="ultra-ssd-features"></a>Recursos do SSD Ultra

**Discos Gerenciados**: os SSDs Ultra estão disponíveis somente como Discos Gerenciados. Os SSDs Ultra não podem ser implantados como um Disco não gerenciado ou um Blob de páginas. Durante a criação de um Disco Gerenciado, você especificará o tipo de SKU do disco como UltraSSD_LRS e indicará o tamanho do disco, o IOPS e a taxa de transferência que você precisa e o Azure criará e gerenciará o disco para você.  

**Máquinas Virtuais**: os SSDs Ultra são projetados para funcionar com todas as SKUs de máquinas virtuais do Azure habilitadas para SSD Premium, no entanto, como está atualmente em versão prévia, as VMs são dimensionadas como ES/DS v3.

**Configuração dinâmica de desempenho**: os SSDs Ultra permitem que você altere dinamicamente o desempenho (IOPS e taxa de transferência) do disco, juntamente com suas necessidades de carga de trabalho, sem precisar reiniciar as máquinas virtuais.

## <a name="scalability-and-performance-targets"></a>Escalabilidade e metas de desempenho

Ao provisionar um SSD Ultra, você terá a opção de configurar a capacidade e o desempenho do disco de forma independente. Os SSDs Ultra vêm em vários tamanhos fixos, de 4 GiB até 64 TiB, e oferecem um modelo de configuração de desempenho flexível que permite que você configure IOPS e taxa de transferência de modo independente. Os SSDs Ultra só podem ser usados como discos de dados. É recomendável usar SSDs Premium como discos de sistema operacional.

Alguns recursos importantes do SSD Ultra são:

- Capacidade do disco: o SSD Ultra oferece uma variedade de tamanhos de disco diferentes, de 4 GiB até 64 TiB.
- IOPS de disco: os SSDs Ultra são compatíveis com limites de IOPS de 300 IOPS/GiB, até um máximo de 160 mil IOPS por disco. Para alcançar o IOPS que você provisionou, certifique-se de que o IOPS do disco selecionado seja menor que o IOPS da VM. O IOPS de disco mínimo é de 100 IOPS.
- Taxa de transferência de disco: com os SSDs Ultra, o limite de taxa de transferência de um único disco é de 256 KiB/s para cada IOPS provisionado, até um máximo de 2.000 MBps por disco (em que MBps = 10^6 Bytes por segundo). A taxa de transferência mínima de disco é de 1 MiB.

A tabela a seguir resume as diferentes configurações compatíveis com os diferentes tamanhos de disco:  

### <a name="ultra-ssd-managed-disk-offerings"></a>Ofertas de Disco gerenciado SSD Ultra

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

## <a name="pricing-and-billing"></a>Preços e cobrança

Ao usar os SSDs Ultra, as seguintes considerações de faturamento serão aplicadas:

- Tamanho do Disco Gerenciado
- IOPS do Disco Gerenciado provisionado
- Taxa de transferência do Disco Gerenciado Provisionado
- Taxa de reserva de VM de SSD Ultra

### <a name="managed-disk-size"></a>Tamanho do Disco Gerenciado

Os discos gerenciados são cobrados nos tamanhos de VM que você escolhe ao provisionar uma nova VM do Azure. O Azure mapeia o tamanho provisionado (arredondado) para a opção de tamanho de disco mais próxima. Para obter detalhes sobre os tamanhos de disco oferecidos, consulte a tabela na seção de Escalabilidade e Metas de Desempenho acima. Cada disco é mapeado para um tamanho de disco provisionado com suporte e será cobrado de acordo com uma hora por hora. Por exemplo, se você provisionasse um Disco SSD Ultra de 200 GiB e o excluísse após 20 horas, ele seria mapeado para a oferta de tamanho de disco de 256 GiB e você seria cobrado pelos 256 GiB por 20 horas. Essa cobrança foi baseada no consumo de horas de computação, independentemente do volume de dados realmente gravado no disco.

### <a name="managed-disk-provisioned-iops"></a>IOPS do Disco Gerenciado provisionado

IOPS é o número de solicitações que o aplicativo está enviando aos discos por segundo. Uma operação de entrada/saída pode ser leitura ou gravação sequencial ou leitura ou gravação aleatória. Com base no tamanho do disco ou no número de discos conectados à VM, o número médio de IOPS será cobrado por hora. Para obter detalhes sobre o IOPS de disco oferecido, consulte a tabela na seção de Escalabilidade e Metas de Desempenho acima.

### <a name="managed-disk-provisioned-throughput"></a>Taxa de transferência do Disco Gerenciado Provisionado

A Taxa de Transferência é o volume de dados que o aplicativo está enviando aos discos em um intervalo especificado, medida em bytes/segundo. Se o aplicativo estiver executando grandes operações de entrada/saída, ele exigirá taxa de transferência alta.  

Há uma relação entre a Taxa de transferência e o IOPS, conforme mostrado na seguinte fórmula: IOPS x tamanho de E/S = Taxa de transferência

Portanto, é importante determinar os valores ideais de Taxa de Transferência e IOPS que o aplicativo exige. Ao tentar otimizar um deles, o outro também é afetado. É recomendável iniciar com uma taxa de transferência correspondente ao tamanho de E/S de 16 KiB, ajustando se houver necessidade de mais taxa de transferência.

Para obter detalhes sobre a taxa de transferência de disco compatível com o SSD Ultra, consulte a tabela na seção Escalabilidade e Metas de Desempenho acima. Como o tamanho de disco e IOPS, a taxa de transferência provisionada é cobrada por hora por MBps provisionado.

### <a name="ultra-ssd-vm-reservation-fee"></a>Taxa de reserva de VM de SSD Ultra

Estamos introduzindo uma funcionalidade na VM que indica se sua VM é compatível com SSD Ultra. Uma VM compatível com SSD Ultra aloca a capacidade de largura de banda dedicada entre a instância de VM de computação e a unidade de escala de armazenamento de bloco para otimizar o desempenho e reduzir a latência. A adição dessa funcionalidade na VM resulta em uma cobrança de reserva que será imposta somente se você habilitar o recurso SSD Ultra na VM sem anexar um disco SSD Ultra a ela. Quando um disco SSD Ultra é anexado à VM compatível com SSD Ultra, essa cobrança não é aplicada. Essa cobrança é por vCPU provisionada na VM.

Consulte a [página de preços de Discos do Azure](https://azure.microsoft.com/pricing/details/managed-disks/) para saber os detalhes dos preços dos novos discos SSD Ultra disponíveis em versão prévia limitada.

### <a name="ultra-ssd-preview-scope-and-limitations"></a>Limitações e escopo da versão prévia do SSD Ultra

Na versão prévia, os discos SSD Ultra:

- Terão suporte inicialmente no Leste dos EUA 2 em uma única zona de disponibilidade  
- Só poderão ser usados com Zonas de Disponibilidade (conjuntos de disponibilidade e implantações de VM única fora das zonas não terão a possibilidade de anexar um disco SSD Ultra)
- Serão compatíveis somente com VMs ES/DS v3
- Só estarão disponíveis como discos de dados e serão compatíveis somente com tamanho de setor físico de 4k  
- Só poderão ser criados como discos vazios  
- Atualmente, só podem ser implantados usando o SDK do Python, a CLI e os modelos do Resource Manager.
- Não serão compatíveis (ainda) com instantâneos de disco, Imagens de VM, Conjuntos de disponibilidade, Conjuntos de Dimensionamento de Máquinas Virtuais nem com o Azure Disk Encryption.
- Não serão compatíveis (ainda) com a integração com o Backup do Azure nem com o Azure Site Recovery.
- Assim como acontece com a  [maioria das versões prévias](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), esse recurso não deve ser usado para cargas de trabalho de produção até a GA (disponibilidade geral).
