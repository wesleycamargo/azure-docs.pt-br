---
title: "Visão geral do Azure Managed Disks Standard e Premium | Microsoft Docs"
description: "Visão geral do Azure Managed Disks, que lida com as contas de armazenamento para você ao usar VMs do Azure"
services: storage
documentationcenter: na
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 272250b3-fd4e-41d2-8e34-fd8cc341ec87
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 6ec77968a0f264b8bf1fa56a23e4cc7faef614da
ms.lasthandoff: 03/17/2017


---

# <a name="azure-managed-disks-overview"></a>Visão geral do Azure Managed Disks

O Azure Managed Disks simplifica o gerenciamento de discos para VMs IaaS do Azure por meio do gerenciamento de [contas de armazenamento](storage-introduction.md) associadas aos discos de VM. Você só precisa especificar o tipo ([Premium](storage-premium-storage.md) ou [Standard](storage-standard-storage.md)) e o tamanho do disco que você precisa, e o Azure criará e gerenciará o disco para você.

>[!NOTE]
>As VMs com Managed Disks exigem tráfego de saída na porta 8443 para reportar o status das [extensões de VM](../virtual-machines/virtual-machines-windows-extensions-features.md) instaladas na plataforma Azure. O provisionamento de uma VM com extensões falhará sem a disponibilidade dessa porta. Além disso, o status da implantação de uma extensão será desconhecido se ela estiver instalada em uma VM em execução. Se não for possível desbloquear a porta 8443, você deverá usar discos não gerenciados. Estamos trabalhando ativamente para corrigir esse problema. Confira [Perguntas frequentes sobre discos de VM IaaS](storage-faq-for-disks.md#managed-disks-and-port-8443) para obter mais detalhes. 
>
>

## <a name="benefits-of-managed-disks"></a>Benefícios dos discos gerenciados

Vamos analisar alguns dos benefícios obtidos com os discos gerenciados.

### <a name="simple-and-scalable-vm-deployment"></a>Implantação simples e escalonável de VM

O Managed Disks lida com o armazenamento para você nos bastidores. Anteriormente, era necessário criar contas de armazenamento para armazenar os discos (arquivos VHD) para suas VMs do Azure. Ao escalar verticalmente, era necessário certificar-se de que você criou contas de armazenamento adicionais para não exceder o limite de IOPS do armazenamento com qualquer um de seus discos. Com o Managed Disks lidando com o armazenamento, não há mais os limites de conta de armazenamento (como 20.000 IOPS/conta). Também não é mais necessário copiar as imagens personalizadas (arquivos VHD) em várias contas de armazenamento. Você pode gerenciá-las em um local central, uma conta de armazenamento por região do Azure, e usá-las para criar centenas de VMs em uma assinatura.

O Managed Disks permitirá que você crie até 10.000 **discos** de VM em uma assinatura, o que lhe permitirá criar milhares de **VMs** em uma única assinatura. Esse recurso também aumenta a escalabilidade dos [Conjuntos de dimensionamento de máquinas virtuais (VMSS)](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md), permitindo que você crie até mil VMs em um VMSS usando uma imagem do Marketplace.

### <a name="better-reliability-for-availability-sets"></a>Maior confiabilidade para Conjuntos de disponibilidade

O Managed Disks fornece mais confiabilidade para Conjuntos de disponibilidade, assegurando que os discos de VMs em um Conjunto de disponibilidade sejam suficientemente isolados entre si para evitar pontos de falha. Ele faz isso colocando automaticamente os discos em unidades de escala (carimbos) de armazenamentos diferentes. Se um carimbo falhar devido a uma falha de hardware ou de software, somente as instâncias da VM com discos nesses carimbos falharão. Por exemplo, vamos supor que você tenha um aplicativo em execução em cinco VMs, e as VMs estejam em um Conjunto de disponibilidade. Os discos dessas VMs não serão armazenados no mesmo carimbo. Portanto, se um carimbo ficar inativo, as outras instâncias do aplicativo continuarão executando.

### <a name="granular-access-control"></a>Controle de acesso granular

Use o [RBAC (Controle de acesso baseado em função do Azure)](../active-directory/role-based-access-control-what-is.md) para atribuir permissões específicas a um disco gerenciado a um ou mais usuários. O Managed Disks expõe uma variedade de operações, incluindo leitura, gravação (criar/atualizar), exclusão e recuperação de um [URI de assinatura de acesso compartilhado (SAS)](storage-dotnet-shared-access-signature-part-1.md) para o disco. Conceda acesso somente às operações que uma pessoa necessita para executar seu trabalho. Por exemplo, se não quiser que uma pessoa copie um disco gerenciado em uma conta de armazenamento, opte por não conceder acesso à ação de exportação para esse disco gerenciado. Da mesma forma, se não quiser que uma pessoa use um URI de SAS para copiar um disco gerenciado, opte por não conceder essa permissão ao disco gerenciado.

### <a name="azure-backup-service-support"></a>Suporte de serviço do Backup do Azure 
Use o serviço de Backup do Azure com o Managed Disks para criar um trabalho de backup com backups baseados em tempo, fácil restauração de VM e políticas de retenção de backup. O Managed Disks dá suporte apenas ao LRS (Armazenamento com Redundância Local) como a opção de replicação; isso significa que ele mantém três cópias dos dados em uma única região. Para a recuperação de desastre regional, é necessário fazer backup dos discos da VM em outra região usando o [serviço de Backup do Azure](../backup/backup-introduction-to-azure-backup.md) e uma conta de armazenamento GRS como o cofre de backup. Leia mais sobre isso em [Usando o serviço de Backup do Azure para VMs com o Managed Disks](../backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). 

## <a name="pricing-and-billing"></a>Preços e cobrança 

Ao usar o Managed Disks, as seguintes considerações de cobrança se aplicam:

* Tipo de armazenamento

* Tamanho do disco

* Número de transações

* Transferências de dados de saída

* Instantâneos do disco gerenciado (cópia completa do disco)

Vamos examinar esses itens com mais detalhes.

**Tipo de armazenamento:** o Managed Disks oferece dois níveis de desempenho: [Premium](storage-premium-storage.md) (baseado em SSD) e [Standard](storage-standard-storage.md) (baseado em disco rígido). A cobrança de um disco gerenciado depende de qual tipo de armazenamento você selecionou para o disco.


**Tamanho do disco**: a cobrança do disco gerenciado depende do tamanho do disco provisionado. O Azure mapeia o tamanho provisionado (arredondado) para a opção mais próxima do Managed Disks, conforme especificado na tabela abaixo. Cada disco gerenciado será mapeado para um dos tamanhos provisionados com suporte e será cobrado adequadamente. Por exemplo, se você criar um disco gerenciado padrão e especificar um tamanho provisionado de 200 GB, será cobrado de acordo com o preço do tipo de disco S20.

Estes são os tamanhos de disco disponíveis para um disco gerenciado premium:

| **Tipo de Premium Managed <br>Disk**  | **P10** | **P20** | **P30**        |
|------------------|---------|---------|----------------|
| Tamanho do disco        | 128 GB  | 512 GB  | 1024 GB (1 TB) |

Estes são os tamanhos de disco disponíveis para um disco gerenciado padrão: 

| **Tipo de Standard Managed <br>Disk** | **S4**  | **S6**  | **S10**        | **S20** | **S30**        |
|------------------|---------|---------|----------------|---------|----------------|
| Tamanho do disco        | 32 GB   | 64 GB   | 128 GB  | 512 GB  | 1024 GB (1 TB) |

**Número de transações**: você será cobrado pelo número de transações executadas em um disco gerenciado padrão. Não há custo para transações de um disco gerenciado premium.

**Transferências de dados de saída**: as [transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/) (dados saindo dos datacenters do Azure) incorrem em cobrança por uso de largura de banda.

**Instantâneos de discos gerenciados (cópia completa de disco)**: um Instantâneo gerenciado é uma cópia somente leitura de um disco gerenciado que é armazenado como um disco gerenciado padrão. Com os instantâneos, você pode fazer backup de seus discos gerenciados a qualquer momento. Esses instantâneos existem independentemente do disco de origem e podem ser usados para criar novos Managed Disks. O custo de um instantâneo gerenciado é o mesmo de um disco gerenciado padrão. Por exemplo, se você tirar um instantâneo de um disco gerenciado premium de 128 GB, o custo de um instantâneo gerenciado será equivalente ao de um disco gerenciado padrão de 128 GB.

[Instantâneos incrementais](storage-incremental-snapshots.md) não têm suporte no momento para o Managed Disks, mas terão suporte no futuro.

Para saber mais sobre como criar instantâneos com o Managed Disks, consulte estes recursos:

* [Criar cópia de VHD armazenado como um Disco Gerenciado usando instantâneos no Windows](../virtual-machines/virtual-machines-windows-snapshot-copy-managed-disk.md)
* [Criar cópia de VHD armazenado como um Disco Gerenciado usando instantâneos no Linux](../virtual-machines/linux/virtual-machines-linux-snapshot-copy-managed-disk.md)


Para obter informações detalhadas sobre os preços para Managed Disks, confira [Preços do Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks).

## <a name="images"></a>Imagens

O Managed Disks também oferece suporte à criação de uma imagem personalizada gerenciada. Crie uma imagem de seu VHD personalizado em uma conta de armazenamento ou diretamente de uma VM generalizada (por meio do Sysprep). Isso captura em uma única imagem todos os discos gerenciados associados a uma VM, incluindo o sistema operacional e os discos de dados. Isso permite a criação de centenas de VMs usando sua imagem personalizada, sem a necessidade de copiar ou gerenciar contas de armazenamento.

Para saber mais sobre a criação de imagens, confira os artigos a seguir:
* [Como capturar uma imagem gerenciada de uma VM generalizada no Azure](../virtual-machines/virtual-machines-windows-capture-image-resource.md)
* [Como generalizar e capturar uma máquina virtual Linux usando a CLI 2.0 do Azure](../virtual-machines/virtual-machines-linux-capture-image.md)

## <a name="images-versus-snapshots"></a>Imagens versus instantâneos

É comum ver a palavra "imagem" usada com VMs e, agora, você vê também a palavra "instantâneos". É importante compreender a diferença entre elas. Com o Managed Disks, você pode capturar uma imagem de uma VM generalizada que foi desalocada. Esta imagem incluirá todos os discos anexados à VM. Use essa imagem para criar uma nova VM e ela incluirá todos os discos.

Um instantâneo é uma cópia de um disco no momento exato em que é tirado. Ele só se aplica a um disco. Se você tiver uma VM com apenas um disco (o SO), será possível tirar um instantâneo ou uma imagem dele e criar uma VM a partir do instantâneo ou da imagem.

E se uma VM tiver cinco discos e eles estiverem distribuídos? Você pode tirar um instantâneo de cada um dos discos, mas não há reconhecimento dentro da VM do estado dos discos – os instantâneos dizem respeito apenas àquele disco. Nesse caso, os instantâneos precisariam ser coordenados entre si, e não há suporte para isso no momento.

## <a name="managed-disks-and-encryption"></a>Managed Disks e criptografia

Há dois tipos de criptografia a serem abordados em referência a discos gerenciados. O primeiro é o SSE (Criptografia do Serviço de Armazenamento), que é executado pelo serviço de armazenamento. O segundo é o Azure Disk Encryption, que pode ser habilitado nos discos do sistema operacional e de dados das VMs. 

### <a name="storage-service-encryption-sse"></a>SSE (Criptografia do Serviço de Armazenamento)

O Armazenamento do Azure dá suporte à criptografia automática dos dados gravados em uma conta de armazenamento. Para obter mais detalhes, confira [Criptografia do Serviço de Armazenamento do Azure para dados em repouso](storage-service-encryption.md). E quanto aos dados nos discos gerenciados? No momento, não é possível habilitar a Criptografia do Serviço de Armazenamento para o Managed Disks. No entanto, ela será liberada no futuro. Enquanto isso, você precisa estar ciente de como usar um arquivo VHD que reside em uma conta de armazenamento criptografada e que também foi criptografado. 

A SSE criptografa os dados conforme eles são gravados na conta de armazenamento. Se você tiver um arquivo VHD que já foi criptografado com SSE, não será possível usar esse arquivo VHD para criar uma VM que utiliza Managed Disks. Também não é possível converter um disco não gerenciado criptografado em um disco gerenciado. E, por fim, se você desabilitar a criptografia nessa conta de armazenamento, ela não descriptografará o arquivo VHD. 

Para usar um disco que foi criptografado, primeiro copie o arquivo VHD em uma conta de armazenamento que nunca foi criptografada. Em seguida, crie uma VM com o Managed Disks e especifique esse arquivo VHD durante a criação, ou anexe o arquivo VHD copiado a uma VM em execução com o Managed Disks. 

### <a name="azure-disk-encryption-ade"></a>ADE (Azure Disk Encryption)

O Azure Disk Encryption permite criptografar os discos do sistema operacional e os discos de dados usados por uma Máquina Virtual IaaS. Isso inclui discos gerenciados. No Windows, as unidades são criptografadas usando a tecnologia de criptografia BitLocker padrão do setor. No Linux, os discos são criptografados usando a tecnologia DM-Crypt. Esse recurso é integrado ao Cofre de Chaves do Azure para permitir que você controle e gerencie as chaves de criptografia de disco. Para obter mais informações, consulte [Azure Disk Encryption para VMs IaaS Windows e Linux](../security/azure-security-disk-encryption.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Managed Disks, confira os artigos a seguir.

### <a name="get-started-with-managed-disks"></a>Introdução aos Discos Gerenciados 

* [Criar uma VM do Windows usando o Gerenciador de Recursos e o PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md)

* [Criar uma VM Linux usando a CLI 2.0 do Azure](../virtual-machines/virtual-machines-linux-quick-create-cli.md)

* [Anexar um disco de dados gerenciado a uma VM do Windows usando o PowerShell](../virtual-machines/virtual-machines-windows-attach-disk-ps.md)

* [Adicionar um disco gerenciado a uma VM Linux](../virtual-machines/virtual-machines-linux-quick-create-cli.md)

### <a name="compare-managed-disks-storage-options"></a>Comparar as opções de armazenamento de Managed Disks 

* [Discos e armazenamento Premium](storage-premium-storage.md)

* [Discos e armazenamento Standard](storage-standard-storage.md)

### <a name="operational-guidance"></a>Diretrizes operacionais

* [Migração do AWS e outras plataformas para os Azure Managed Disks](../virtual-machines/virtual-machines-windows-on-prem-to-azure.md)

* [Converter VMs do Azure para discos gerenciados no Azure](../virtual-machines/virtual-machines-windows-migrate-to-managed-disks.md)

