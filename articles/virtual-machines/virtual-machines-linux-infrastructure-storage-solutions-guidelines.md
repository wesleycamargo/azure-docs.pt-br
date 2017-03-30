---
title: "Soluções de armazenamento das VMs do Linux no Azure | Microsoft Docs"
description: "Saiba mais sobre as principais diretrizes de design e implementação referentes à implantação de soluções de armazenamento em serviços de infraestrutura do Azure."
documentationcenter: 
services: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3c368f07-189b-4520-bbe2-f4080253bbf2
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 6b36c479c987c801d34f90a4300b7221354b9615
ms.lasthandoff: 03/18/2017


---
# <a name="azure-storage-infrastructure-guidelines-for-linux-vms"></a>Diretrizes de infraestrutura de armazenamento do Azure para VMs Linux

[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Este artigo destaca as noções básicas sobre as necessidades de armazenamento e considerações de design para atingir o desempenho de VM (máquina virtual) ideal.

## <a name="implementation-guidelines-for-storage"></a>Diretrizes de implementação de armazenamento
Decisões:

* Você pretende usar o Azure Managed Disks ou discos não gerenciados?
* Você precisa usar o armazenamento Standard ou Premium para sua carga de trabalho?
* Você precisa da distribuição de discos para criar discos com mais de 1.023 GB?
* Você precisa da distribuição de discos para obter o desempenho de E/S ideal para sua carga de trabalho?
* De que conjunto de contas de armazenamento você precisa para hospedar a infraestrutura ou a carga de trabalho de TI?

Tarefas:

* Examinar as demandas de E/S dos aplicativos que serão implantados e planejar a quantidade e o tipo apropriados de contas de armazenamento.
* Crie o conjunto de contas de armazenamento usando a sua convenção de nomenclatura. Você pode usar a CLI do Azure ou o portal.

## <a name="storage"></a>Armazenamento
O Armazenamento do Azure é uma parte fundamental de implantação e gerenciamento de aplicativos e VMs (máquinas virtuais). O Armazenamento do Azure fornece serviços para armazenar dados de arquivo, dados não estruturados e mensagens, além de fazer parte da infraestrutura que dá suporte às VMs.

O [Azure Managed Disks](../storage/storage-managed-disks-overview.md) lida com o armazenamento para você nos bastidores. Com discos não gerenciados, você cria contas de armazenamento para armazenar os discos (arquivos VHD) para as VMs do Azure. Ao aumentar, é necessário verificar se você criou contas de armazenamento adicionais para não exceder o limite de IOPS de armazenamento com um dos discos. Com o Managed Disks lidando com o armazenamento, não há mais os limites de conta de armazenamento (como 20.000 IOPS/conta). Também não é mais necessário copiar as imagens personalizadas (arquivos VHD) em várias contas de armazenamento. Você pode gerenciá-las em um local central, uma conta de armazenamento por região do Azure, e usá-las para criar centenas de VMs em uma assinatura. Recomendamos o uso do Managed Disks para novas implantações.

Há dois tipos de conta de armazenamento disponíveis para dar suporte às VMs:

* Contas de armazenamento Standard fornece acesso ao armazenamento de blobs (usado para armazenar discos da VM do Azure), armazenamento de tabelas, armazenamento de filas e armazenamento de arquivos.
* [Armazenamento Premium](../storage/storage-premium-storage.md) dão suporte a discos de alto desempenho e baixa latência para cargas de trabalho de E/S intensiva, como cluster fragmentado do MongoDB. Um armazenamento premium dá suporte no momento somente a discos de VM do Azure.

O Azure cria VMs com um disco do sistema operacional, um disco temporário e zero ou mais discos de dados opcionais. O disco do sistema operacional e os discos de dados são blobs de página do Azure, enquanto o disco temporário é armazenado localmente no nó em que a máquina reside. Tome cuidado ao projetar aplicativos para usar somente este disco temporário para dados não persistentes, pois a VM poderá ser migrada entre hosts durante um evento de manutenção. Todos os dados armazenados no disco temporário seriam perdidos.

Durabilidade e alta disponibilidade são fornecidas pelo ambiente de Armazenamento do Azure subjacente para garantir que seus dados permaneçam protegidos contra falhas de hardware ou de manutenção não planejadas. Ao projetar seu ambiente de Armazenamento do Azure, você pode optar por replicar o armazenamento da VM:

* localmente em um datacenter do Azure fornecido
* entre os datacenters do Azure dentro de uma determinada região
* entre os datacenters do Azure em regiões diferentes.

Você pode ler [mais sobre as opções de replicação para alta disponibilidade](../storage/storage-introduction.md#replication-for-durability-and-high-availability).

Discos do sistema operacional e discos de dados têm um tamanho máximo de 1023 GB (gigabytes). O tamanho máximo de um blob é de 1024 GB e deve conter os metadados (rodapé) do arquivo VHD (um GB tem 1024<sup>3</sup> bytes). É possível usar o LVM (Gerenciador de Volume Lógico) para ultrapassar esse limite com o pool de discos de dados para apresentar volumes lógicos maiores que 1.023 GB à sua VM.

Há alguns limites de escalabilidade ao projetar suas implantações do Armazenamento do Azure. Para obter mais informações, consulte [Assinatura do Microsoft Azure e limite de serviços, cotas e restrições](../azure-subscription-service-limits.md#storage-limits). Consulte também [Metas de desempenho e escalabilidade do armazenamento do Azure](../storage/storage-scalability-targets.md).

Para armazenamento de aplicativos, é possível armazenar dados de objeto não estruturados, como documentos, imagens, backups, dados de configuração, logs etc. usando o armazenamento de blobs. Em vez de seu aplicativo gravar em um disco virtual anexado à VM, o aplicativo poderá gravar diretamente no armazenamento de blobs do Azure. O armazenamento de blobs também oferece a opção de [camadas de armazenamento quentes e frias](../storage/storage-blob-storage-tiers.md) dependendo de suas necessidades de disponibilidade e restrições de custo.

## <a name="striped-disks"></a>Discos distribuídos
Além de permitir que você crie discos com mais de 1023 GB, em muitos casos, o uso da distribuição para discos de dados pode melhorar o desempenho, permitindo que vários blobs façam o armazenamento de um único volume. Com a distribuição, a E/S necessária para gravar e ler dados de um único disco lógico continua em paralelo.

O Azure impõe limites no número de discos de dados e na quantidade de largura de banda disponíveis, dependendo do tamanho da VM. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Se você estiver usando a distribuição de disco para os discos de dados do Azure, considere as seguintes diretrizes:

* Os discos de dados devem ter sempre o tamanho máximo (1023 GB).
* Anexar o número máximo de discos de dados permitidos para o tamanho da VM.
* Use o LVM.
* Evite usar opções de cache de disco de dados do Azure (política de cache = Nenhuma).

Para obter mais informações, veja [Configuração de LVM em uma VM do Linux](virtual-machines-linux-configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="multiple-storage-accounts"></a>Várias contas de armazenamento
Esta seção não se aplica ao [Azure Managed Disks](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), pois você não cria contas de armazenamento separadas. 

Durante a criação do ambiente do Armazenamento do Azure para discos não gerenciados, você poderá usar várias contas de armazenamento conforme o número de VMs implantadas aumentar. Essa abordagem ajuda a distribuir a E/S em toda a infraestrutura subjacente do Armazenamento do Azure, para manter o desempenho ideal para suas VMs e aplicativos. Ao projetar os aplicativos que serão implantados, considere os requisitos de E/S que cada VM terá e faça um balanceamento dessas VMs entre as contas do Armazenamento do Azure. Tente evitar agrupar todas as VMs que exigem E/S alta em apenas uma ou duas contas de armazenamento.

Para saber mais sobre as funcionalidades de E/S das diferentes opções do Armazenamento do Azure e de alguns limites máximos recomendáveis, veja [Metas de desempenho e escalabilidade do armazenamento do Azure](../storage/storage-scalability-targets.md).

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]


