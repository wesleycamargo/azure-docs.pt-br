---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 91e9d3a99224c09ecfb5cc3b477a71a7f7bfed7a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65198794"
---
## <a name="benefits-of-managed-disks"></a>Benefícios dos discos gerenciados

Vamos falar sobre alguns benefícios que você ganha usando discos gerenciados.

### <a name="highly-durable-and-available"></a>Altamente durável e disponível

Os discos gerenciados foram criados para oferecer uma disponibilidade de 99,999%. Os discos gerenciados atingem isso fornecendo três réplicas dos seus dados, possibilitando alta durabilidade. Se uma ou duas réplicas apresentarem problemas, as réplicas restantes ajudarão a garantir a persistência dos seus dados e a alta tolerância contra falhas. Esta arquitetura ajudou o Azure a proporcionar consistentemente durabilidade de nível empresarial para discos IaaS (Infraestrutura como serviço), com uma taxa de falha anualizada líder do setor de ZERO POR CENTO.

### <a name="simple-and-scalable-vm-deployment"></a>Implantação simples e escalonável de VM

Usando discos gerenciados, é possível criar até 50 mil **discos** de VM de um tipo em uma assinatura por região, permitindo que você crie milhares de **VMs** em uma assinatura única. Esse recurso também aumenta a ainda mais escalabilidade dos [conjuntos de dimensionamento de máquinas virtuais](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md), permitindo que você crie até mil VMs em um conjunto de dimensionamento de máquinas virtuais usando uma imagem do Marketplace.

### <a name="integration-with-availability-sets"></a>Integração com conjuntos de disponibilidade

Os discos gerenciados são integrados a conjuntos de disponibilidade para garantir que os discos de [VMs em um conjunto de disponibilidade](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) estejam suficientemente isolados entre si para evitar pontos únicos de falha. Os discos são automaticamente colocados em unidades de escala de armazenamento diferentes (carimbos). Se um carimbo falhar devido a uma falha de hardware ou de software, somente as instâncias da VM com discos nesses carimbos falharão. Por exemplo, vamos supor que você tenha um aplicativo em execução em cinco VMs, e que as VMs estejam em um Conjunto de Disponibilidade. Os discos dessas VMs não serão armazenados no mesmo stamp, portanto, se um stamp ficar inativo, as outras instâncias do aplicativo continuarão em execução.

### <a name="integration-with-availability-zones"></a>Integração com as Zonas de Disponibilidade

Os discos gerenciados dão suporte às [Zonas de Disponibilidade](../articles/availability-zones/az-overview.md), que são uma oferta de alta disponibilidade que protege os aplicativos contra falhas do datacenter. As Zonas de Disponibilidade são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. Com Zonas de Disponibilidade, o Azure oferece o melhor SLA de tempo de atividade da VM de 99,99% do setor.

### <a name="azure-backup-support"></a>Suporte de Backup do Azure

Para proteger contra desastres regionais, o [Backup do Azure](../articles/backup/backup-introduction-to-azure-backup.md) pode ser usado para criar um trabalho de backup com backups baseados em tempo e políticas de retenção de backup. Isso permite que você execute restaurações de VM fáceis à vontade. No momento, o Backup do Azure dá suporte a tamanhos de disco de até quatro TiB (tebibytes). Para saber mais, confira [Using Azure Backup for VMs with managed disks](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup) (Usando o Backup do Azure para VMs com discos gerenciados).

### <a name="granular-access-control"></a>Controle de acesso granular

É possível usar o [RBAC (Controle de acesso baseado em função) do Azure](../articles/role-based-access-control/overview.md) para atribuir permissões específicas de um disco gerenciado a um ou mais usuários. Os discos gerenciados expõem uma variedade de operações, incluindo leitura, gravação (criar/atualizar), exclusão e recuperação de um [URI de SAS (assinatura de acesso compartilhado)](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) para o disco. Conceda acesso somente às operações que uma pessoa necessita para executar seu trabalho. Por exemplo, se não quiser que uma pessoa copie um disco gerenciado em uma conta de armazenamento, opte por não conceder acesso à ação de exportação para esse disco gerenciado. Da mesma forma, se não quiser que uma pessoa use um URI de SAS para copiar um disco gerenciado, opte por não conceder essa permissão ao disco gerenciado.

## <a name="disk-roles"></a>Funções do disco

Há três funções principais de disco no Azure: o disco de dados, o disco do SO e o disco temporário. Essas funções são mapeadas para discos anexados à sua máquina virtual.

![Funções de disco em ação](media/virtual-machines-managed-disks-overview/disk-types.png)

### <a name="data-disk"></a>Disco de dados

Um disco de dados é um disco gerenciado anexado a uma máquina virtual para armazenar dados de aplicativos ou outros dados que precisam ser mantidos. Discos de dados são registrados como unidades SCSI e rotulados com a letra que você escolher. Cada disco de dados tem uma capacidade máxima de 32.767 GiB (gibibytes). O tamanho da máquina virtual determina quantos discos de dados você pode anexar a ele e o tipo de armazenamento que pode usar para hospedar os discos.

### <a name="os-disk"></a>Disco do sistema operacional

Cada máquina virtual tem um disco de sistema operacional anexado. Esse disco do sistema operacional tem um SO pré-instalado, que foi selecionado quando a VM foi criada.

Esse disco tem uma capacidade máxima de 2.048 GiB.

### <a name="temporary-disk"></a>Disco temporário

Cada VM contém um disco temporário, que não é um disco gerenciado. O disco temporário fornece armazenamento de curto prazo para aplicativos e processos e destina-se apenas a armazenar dados, como arquivos de paginação ou de permuta. Os dados no disco temporário podem ser perdidos durante um [evento de manutenção](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) ou durante a [reimplantação de uma VM](../articles/virtual-machines/troubleshooting/redeploy-to-new-node-windows.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json). Em VMs do Linux do Azure, o disco temporário é /dev/sdb por padrão e, em VMs do Windows, o disco temporário é E: por padrão. Durante uma reinicialização padrão bem-sucedida da VM, os dados no disco temporário devem persistir.

## <a name="managed-disk-snapshots"></a>Instantâneos de disco gerenciado

Um instantâneo de disco gerenciado é uma cópia completa somente leitura de um disco gerenciado que, por padrão, é armazenada como um disco gerenciado padrão. Com os instantâneos, você pode fazer backup de seus discos gerenciados a qualquer momento. Esses instantâneos existem independentemente do disco de origem e podem ser usados para criar novos discos gerenciados. Eles são cobrados com base no tamanho usado. Por exemplo, se você criar um instantâneo de um disco gerenciado com capacidade provisionada de 64 GiB e tamanho real de dados usados de 10 GiB, esse instantâneo será cobrado apenas pelo tamanho de dados usados de 10 GiB.  

Para saber mais sobre como criar instantâneos com discos gerenciados, confira os seguintes recursos:

* [Criar cópia de VHD armazenada como um disco gerenciado usando instantâneos no Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Criar cópia de VHD armazenada como um disco gerenciado usando instantâneos no Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>Imagens

Os discos gerenciados também dão suporte à criação de uma imagem personalizada gerenciada. É possível criar uma imagem do seu VHD personalizado em uma conta de armazenamento ou diretamente de uma VM generalizada (por meio do sysprep). Esse processo captura uma única imagem. Essa imagem contém todos os discos gerenciados associados a uma VM, incluindo os discos do SO e de dados. Esta imagem personalizada gerenciada permite a criação de centenas de VMs usando sua imagem personalizada, sem a necessidade de copiar ou gerenciar contas de armazenamento.

Para saber mais sobre a criação de imagens, confira os artigos a seguir:

* [Como capturar uma imagem gerenciada de uma VM generalizada no Azure](../articles/virtual-machines/windows/capture-image-resource.md)
* [Como generalizar e capturar uma máquina virtual Linux usando a CLI do Azure](../articles/virtual-machines/linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>Imagens versus instantâneos

É importante compreender a diferença entre imagens e instantâneos. Com discos gerenciados é possível capturar uma imagem de uma VM generalizada que foi desalocada. Essa imagem inclui todos os discos anexados à VM. É possível usar essa imagem para criar uma VM e isso inclui todos os discos.

Um instantâneo é uma cópia de um disco no momento exato em que o instantâneo é tirado. Ele só se aplica a um disco. Se você tiver uma VM que tem um disco (o disco do SO), será possível capturar um instantâneo ou uma imagem dele e criar uma VM com base no instantâneo ou na imagem.

Um instantâneo não tem reconhecimento de nenhum disco, exceto o contido por ele. Isso torna problemático usá-lo em cenários que precisam da coordenação de vários discos, como distribuição. Os instantâneos precisariam ser capazes de coordenar uns com os outros e, no momento, não há suporte para isso.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os tipos de disco individuais oferecidos pelo Azure e qual tipo é uma boa opção para suas necessidades em nosso artigo sobre tipos de disco.
