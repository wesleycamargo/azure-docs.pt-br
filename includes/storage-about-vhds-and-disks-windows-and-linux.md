---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7f36f297e4d167954f9fdc600357d84efb73296d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263314"
---
## <a name="about-vhds"></a>Sobre VHDs

Os VHDs usados no Azure são arquivos .vhd armazenados como blobs de página em uma conta de armazenamento padrão ou premium no Azure. Para conhecer mais detalhes sobre os blobs, consulte [Noções Gerais sobre blobs de blocos e blobs de páginas](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/). Para obter detalhes sobre o armazenamento premium, veja [Armazenamento premium de alto desempenho e máquinas virtuais do Azure](../articles/virtual-machines/windows/premium-storage.md).

O Azure dá suporte aos discos fixos no formato VHD. O formato fixo dispõe o disco lógico linearmente dentro do arquivo, de forma que o deslocamento X do disco é armazenado no deslocamento X do blob. Um pequeno rodapé ao final do blob descreve as propriedades do VHD. Frequentemente, o formato fixo desperdiça espaço, porque muitos discos têm em si grandes intervalos não usados. No entanto, o Azure armazena arquivos .vhd em um formato livre, assim você obtém os benefícios de discos fixos e dinâmicos ao mesmo tempo. Para saber mais, confira [Introdução aos discos rígidos virtuais](https://technet.microsoft.com/library/dd979539.aspx).

Todos os arquivos VHD no Azure que você deseja usar como uma fonte para criar discos ou imagens são somente para leitura, exceto os arquivos. vhd carregados ou copiados para o Armazenamento do Azure pelo usuário (que pode ser leitura/gravação ou somente leitura). Quando você cria um disco ou uma imagem, o Azure faz cópias dos arquivos .vhd da fonte. Essas cópias podem ser somente leitura ou de leitura e gravação, dependendo de como você usar o VHD.

Quando você cria uma máquina virtual por meio de uma imagem, o Azure cria um disco para a máquina virtual que é uma cópia do arquivo .vhd de origem. Para proteger contra exclusão acidental, o Azure faz uma concessão de qualquer arquivo .vhd de origem que é usado para criar uma imagem, um disco de sistema operacional ou um disco de dados.

Antes de excluir um arquivo .vhd de origem, você precisará remover a concessão excluindo o disco ou a imagem. Para excluir um arquivo .vhd que está sendo usado por uma máquina virtual como disco do sistema operacional, você pode excluir a máquina virtual, o disco do sistema operacional e o arquivo .vhd de origem de uma só vez excluindo a máquina virtual e todos os discos associados. No entanto, a exclusão de um arquivo .vhd que é uma origem de um disco de dados requer várias etapas em uma ordem definida. Primeiro, desanexar o disco da máquina virtual; em seguida, excluir o disco e, por fim, excluir o arquivo .vhd.

> [!WARNING]
> Se você excluir um arquivo .vhd de origem do armazenamento ou excluir sua conta de armazenamento, a Microsoft não poderá recuperar esses dados para você.

## <a name="types-of-disks"></a>Tipos de discos

Os Discos do Azure foram projetados para oferecer uma disponibilidade de 99,999%. Os Discos do Azure forneceram consistentemente a durabilidade no nível empresarial, com uma Taxa de Falha Anual de ZERO% líder do setor.

Há três níveis de desempenho a escolher para o armazenamento na hora de criar seus discos: discos SSD Premium, SSD Standard e Armazenamento HDD Standard. Além disso, há dois tipos de discos: não gerenciados e gerenciados.

### <a name="standard-hdd-disks"></a>Discos HDD Standard

Os discos HDD Standard tem o apoio de HDDs e apresentam armazenamento com bom custo-benefício. O armazenamento HDD padrão pode ser replicado localmente em um datacenter ou ser redundante geograficamente com centros de dados primários e secundários. Para saber mais sobre a replicação de armazenamento, confira [Replicação do Armazenamento do Azure](../articles/storage/common/storage-redundancy.md).

Para saber mais sobre como usar discos HDD Standard, confira [Armazenamento e discos Standard](../articles/virtual-machines/windows/standard-storage.md).

### <a name="standard-ssd-disks"></a>Discos SSD Standard

Discos SSD Standard são projetados para o mesmo tipo de cargas de trabalho dos discos HDD Standard, mas oferecem desempenho e confiabilidade mais consistentes do que o HDD. Os discos SSD Standard combinam elementos dos discos SSD Premium e HDD Standard para formar uma solução com bom custo-benefício mais adequada a usos como servidores Web que não precisam de IOPS alto nos discos. Quando disponíveis, os discos SSD Standard são a opção de implantação recomendada para a maioria das cargas de trabalho. Os discos SSD Standard estão disponíveis como discos gerenciados em todas as regiões, mas atualmente só estão disponíveis com o tipo de resiliência de armazenamento localmente redundante (LRS).

### <a name="premium-ssd-disks"></a>Discos SSD Premium

O Armazenamento Premium tem o suporte de SSDs e dá suporte de disco de alto desempenho e baixa latência a VMs executando cargas de trabalho intensivas para entradas e saídas. Normalmente você pode usar os discos SSD Premium com tamanhos que incluem um "s" no nome da série. Por exemplo, há a série de Dv3 e a série Dsv3; a série Dsv3 pode ser usada com discos SSD Premium.  Para saber mais, veja [Armazenamento Premium](../articles/virtual-machines/windows/premium-storage.md).

### <a name="unmanaged-disks"></a>Discos não gerenciados

Discos não gerenciados são do tipo tradicional de discos que foram usados por máquinas virtuais. Com esses discos, você cria sua própria conta de armazenamento e especifica essa conta de armazenamento ao criar o disco. Não coloque muitos discos na mesma conta de armazenamento, pois você pode exceder as [metas de escalabilidade](../articles/storage/common/storage-scalability-targets.md) da conta de armazenamento (20.000 IOPS, por exemplo), resultando na limitação das máquinas virtuais. Com discos não gerenciados, você precisa descobrir como maximizar o uso de uma ou mais contas de armazenamento para obter o melhor desempenho do suas VMs.

### <a name="managed-disks"></a>Discos gerenciados

Gerenciado identificadores de discos de armazenamento de criação/gerenciamento em segundo plano de contas para você e garante que você não precisa se preocupar sobre os limites de escalabilidade da conta de armazenamento. Você simplesmente especifica o tamanho do disco e o nível de desempenho (Standard/Premium) e o Azure cria e gerencia o disco para você. À medida que você adiciona discos ou dimensiona a VM para cima e para baixo, não é preciso se preocupar com o armazenamento que está sendo usado.

Você também pode gerenciar suas imagens personalizadas em uma conta de armazenamento por região do Azure e usá-las para criar centenas de VMs na mesma assinatura. Para saber mais sobre os Managed Disks, confira a [Visão geral dos Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

É recomendável que você use Azure Managed Disks para novas VMs e converta os discos não gerenciados anteriores para discos gerenciados, para tirar proveito dos muitos recursos disponíveis nos Managed Disks.

### <a name="disk-comparison"></a>Comparação de disco

A tabela a seguir fornece uma comparação do HDD Standard, do SSD Standard e do SSD Premium para discos gerenciados e não gerenciados, para que você possa decidir o que usar. Tamanhos demarcados com um asterisco estão atualmente em visualização.

|    | Disco Premium do Azure |Disco SSD Standard do Azure | Disco HDD Standard do Azure
|--- | ------------------ | ------------------------------- | -----------------------
| Tipo de disco | Unidades de Estado Sólido (SSD) | Unidades de Estado Sólido (SSD) | Unidades de Disco Rígido (HDD)  
| Visão geral  | Suporte a disco com base em SSD de alto desempenho e baixa latência, para máquinas virtuais executando cargas de trabalho com uso intensivo de E/S ou hospedando ambiente de produção de missão crítica |Desempenho e confiabilidade mais consistentes do que com HDD. Otimizado para cargas de trabalho com baixo IOPS| Disco econômico baseado em HDD para acesso não frequente
| Cenário  | Cargas de trabalho confidenciais produção e desempenho |Servidores Web, aplicativos corporativos pouco utilizados e Desenvolvimento/Teste| Backup, não crítico, acesso não frequente
| Tamanho do disco | P4: 32 GB (somente Managed Disks)<br>P6: 64 GB (somente Managed Disks)<br>P10: 128 GiB<br>P15: 256 GB (somente Managed Disks)<br>P20: 512 GiB<br>P30: 1024 GiB<br>P40: 2048 GiB<br>P50: 4,095 GiB<br>P60: 8,192 GiB * (8 TiB)<br>P70: 16,384 GiB * (16 TiB)<br>P80: 32,767 GiB * (32 TiB) |Somente Managed Disks:<br>E10: 128 GiB<br>E15: 256 GiB<br>E20: 512 GiB<br>E30: 1024 GiB<br>E40: 2048 GiB<br>E50: 4095 GiB<br>E60: 8,192 GiB * (8 TiB)<br>E70: 16,384 GiB * (16 TiB)<br> E80: 32,767 GiB * (32 TiB) | Discos não gerenciados: 1 GiB – 4 TiB (4095 GiB) <br><br>Managed Disks:<br> S4: 32 GiB <br>S6: 64 GiB <br>S10: 128 GiB <br>S15: 256 GiB <br>S20: 512 GiB <br>S30: 1024 GiB <br>S40: 2048 GiB<br>S50: 4095 GiB<br>S60: 8,192 GiB * (8 TiB)<br>S70: 16,384 GiB * (16 TiB)<br>S80: 32,384 GiB * (32 TiB)
| Taxa de Transferência Máxima por Disco | P4: 25 MiB/s<br> P6: 50 MiB/s<br> P10: 100 MiB/s<br> P15: 200 MiB/s<br> P20: 150 MiB/s<br> P30: 200 MiB/s<br> P40-P50: 250 MiB/s<br> P60: 480 MiB/s *<br> P70-P80: 750 MiB/s * | E10-E50: Até 60 MiB/s<br> E60: Até 300 MiB/s *<br> E70-E80: 500 MiB/s *| S4 - S50: Até 60 MiB/s<br> E60: Até 300 MiB/s *<br> S70-S80: Até 500 MiB/s *
| IOPS Máxima por Disco | P4: 120 IOPS<br> P6: 240 IOPS<br> P10: 500 IOPS<br> P15: 1100 IOPS<br> P20: 2300 IOPS<br> P30: 5000 IOPS<br> P40-P50: 7500 IOPS<br> P60: 12,500 IOPS *<br> P70: 15,000 IOPS *<br> P80: 20,000 IOPS * | E10-E50: Até 500 IOPS<br> E60: Até 1300 IOPS *<br> E70-E80: Até 2000 IOPS * | S4-S50: Até 500 IOPS<br> S60: Até 1300 IOPS *<br> S70-S80: Até 2000 IOPS *
