---
title: Armazenamento Premium de alto desempenho e discos de VM do Azure | Microsoft Docs
description: "Discute o Armazenamento Premium de alto desempenho e discos de VM do Azure gerenciados e não gerenciados. As VMs das séries DS, DSv2 e GS do Azure são compatíveis com o Armazenamento Premium."
services: storage
documentationcenter: 
author: ramankumarlive
manager: aungoo-msft
editor: tysonn
ms.assetid: e2a20625-6224-4187-8401-abadc8f1de91
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: ramankum
translationtype: Human Translation
ms.sourcegitcommit: 3a353bc874c1827f8a0fc85352894ad96cff16b5
ms.openlocfilehash: c9e43df37784999036c6cf250f27a808f79ebe2f
ms.lasthandoff: 02/10/2017


---
# <a name="high-performance-premium-storage-and-unmanaged-and-managed-azure-vm-disks"></a>Armazenamento Premium de alto desempenho e discos de VM do Azure gerenciados e não gerenciados
O Armazenamento Premium do Microsoft Azure dá suporte a disco de alto desempenho e baixa latência para VMs (máquinas virtuais) que estão executando cargas de trabalho intensivas de entrada/saída. Os discos de VM que usam Armazenamento Premium armazenam dados em SSDs (unidades de estado sólido). Você pode migrar os discos de VM do seu aplicativo para o Armazenamento Premium do Azure para aproveitar a velocidade e o desempenho desses discos.

Uma VM do Azure dá suporte à anexação vários discos de Armazenamento Premium, para que seus aplicativos possam ter até 64 TB de armazenamento por VM. Com o Armazenamento Premium, seus aplicativos podem atingir 80.000 IOPS (operações de entrada/saída por segundo) por VM e taxa de transferência de disco de 2000 MB por segundo por VM com latências extremamente baixas para operações de leitura.

Com o Armazenamento Premium, o Azure oferece a capacidade de realmente deslocar (lift-and-shift) os exigentes aplicativos empresariais, por exemplo, Dynamics AX, Dynamics CRM, Exchange Server, Farms do SharePoint e SAP Business Suite, para a nuvem. Você pode executar várias cargas de trabalho de banco de dados com desempenho intenso, como o SQL Server, Oracle, MongoDB, MySQL e Redis, que exigem um desempenho alto e consistente e baixa latência no Armazenamento Premium.

> [!NOTE]
> É recomendável migrar qualquer disco de máquina virtual que exija IOPS alta para o Armazenamento Premium para obter o melhor desempenho para o seu aplicativo. Se o disco não requer IOPS alta, você pode limitar os custos mantendo-a no armazenamento padrão, que armazena dados de disco da máquina virtual em HDDs (unidades de disco rígido) em vez de SSDs.
> 

Há duas maneiras de criar discos Premium para VMs do Azure:

**Discos não gerenciados**: esse é o método original onde você gerencia as contas de armazenamento usadas para armazenar os arquivos VHD que correspondem aos discos de VM. Os arquivos VHD são armazenados como blobs de páginas nas contas de armazenamento. 

**[Azure Managed Disks](storage-managed-disks-overview.md)**: esse recurso gerencia as contas de armazenamento usadas para os discos de VM para você. Você só precisa especificar o tipo (Premium ou Standard) e o tamanho do disco que você precisa, e o Azure criará e gerenciará o disco para você. Você não precisa se preocupar sobre onde colocar os discos em várias contas de armazenamento para garantir que permaneça dentro dos limites de escalabilidade para contas de armazenamento – o Azure cuida disso para você.

Embora os dois tipos de discos estejam disponíveis, é recomendável usar o Managed Disks para aproveitar seus muitos recursos.

Para começar com o Armazenamento Premium do Azure, visite [Introdução gratuita](https://azure.microsoft.com/pricing/free-trial/) . 

Para saber mais sobre como migrar suas VMs existentes para o Armazenamento Premium, consulte [Migrar VM com Windows do Azure para o Managed Disks](../virtual-machines/virtual-machines-windows-convert-unmanaged-to-managed-disks.md) ou [Migrar VM com Linux do Azure para o Managed Disks](../virtual-machines/virtual-machines-linux-convert-unmanaged-to-managed-disks.md).

> [!NOTE]
> O Armazenamento Premium tem suporte na maioria das regiões. Encontre a lista de regiões disponíveis em [Serviços do Azure por região](https://azure.microsoft.com/regions/#services), examinando as regiões nas quais as VMs da série de tamanho (DS, DSV2, Fs e GS) têm suporte.
> 

## <a name="premium-storage-features"></a>Recursos do Armazenamento Premium

Vamos analisar alguns recursos do Armazenamento Premium.

**Discos de Armazenamento Premium**: o Armazenamento Premium do Azure é compatível com discos de VM que podem ser anexados a VMs de série de tamanho específicas, incluindo as séries DS, DSv2, GS e Fs. Você tem a opção de três tamanhos de disco: P10 (128 GiB), P20 (512 GiB) e P30 (1024 GiB), cada um com suas próprias especificações de desempenho. Dependendo dos requisitos do aplicativo, você pode anexar um ou mais desses discos à VM. Na seção a seguir sobre [Metas de desempenho e escalabilidade do Armazenamento Premium ](#premium-storage-scalability-and-performance-targets) , descreveremos as especificações em mais detalhes.

**Blob de páginas Premium**: o Armazenamento Premium dá suporte a blobs de páginas, que são usados para armazenar discos não gerenciados persistentes para VMs. Ao contrário do Armazenamento Standard, o Armazenamento Premium não oferece suporte a blobs de bloco, blobs de acréscimo, arquivos, tabelas ou filas.
Qualquer objeto colocado em uma conta de Armazenamento Premium será um blob de páginas, que será ajustado para um dos tamanhos de provisionados com suporte. É por isso que a conta de Armazenamento Premium não se destina ao armazenamento de blobs pequenos.

**Conta de Armazenamento Premium**: para começar a usar o Armazenamento Premium, crie uma conta de Armazenamento Premium para discos não gerenciados. Se você preferir usar o [Portal do Azure](https://portal.azure.com), você poderá criar uma conta de Armazenamento Premium, especificando o nível de desempenho "Premium" e "LRS (Armazenamento com redundância Local)" como a opção de replicação. Também é possível cria uma conta de Armazenamento Premium ao especificar o do tipo "Premium_LRS" usando a [API REST de Armazenamento](/rest/api/storageservices/fileservices/Azure-Storage-Services-REST-API-Reference) versão 2014-02-14 ou posterior; a [API REST do Gerenciamento de Serviço](http://msdn.microsoft.com/library/azure/ee460799.aspx) versão 2014-10-01 ou posterior (implantações Clássicas); a [Referência de API REST do Provedor de Recursos de Armazenamento do Azure](/rest/api/storagerp) (implantações do Resource Manager); e [Azure PowerShell](../powershell-install-configure.md) versão 0.8.10 ou posterior. Saiba mais sobre limites de conta de armazenamento premium na seção a seguir sobre [Metas de Desempenho e Escalabilidade do Armazenamento Premium](#premium-storage-scalability-and-performance-targets.md).

**Armazenamento com Redundância Local Premium**: uma conta de Armazenamento Premium só dá suporte ao LRS (Armazenamento com Redundância Local) como a opção de replicação; ou seja, mantém três cópias dos dados em uma única região. Para obter considerações sobre replicação geográfica ao usar o Armazenamento Premium, confira a seção [Instantâneos e cópia de Blob](#snapshots-and-copy-blob) neste artigo.

O Azure usa a conta de armazenamento como contêiner para seus discos não gerenciados. Quando você cria uma VM DS, DSv2, GS ou Fs do Azure com discos não gerenciados e seleciona uma conta de Armazenamento Premium, o sistema operacional e os discos de dados são armazenados nessa conta de armazenamento.

## <a name="premium-storage-supported-vms"></a>VMs com suporte do Armazenamento Premium
O Armazenamento Premium oferece suporte às VMs das séries DS, DSv2, GS e Fs. Você pode usar os discos do Armazenamento Standard e Premium com essas VMS. Não é possível usar discos de Armazenamento Premium com séries de VM que não são compatíveis com o Armazenamento Premium.

Para obter informações sobre os tipos e tamanhos disponíveis de VM do Azure para VMs Windows, veja [Tamanhos de VMs Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Para obter informações sobre os tipos e tamanhos de VM para VMs Linux, veja [Tamanhos de VMs Linux](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Veja a seguir algumas das funcionalidades das VMs das séries DS, DSv2, GS e Fs:

**Serviço de nuvem**: VMs da série DS podem ser adicionadas a um serviço de nuvem que inclua apenas VMs da série DS. Não adicione VMs da série DS a um serviço de nuvem existente que inclua VMs que não são da série DS. Você pode migrar seus VHDs existentes para um novo serviço de nuvem que executa apenas VMs da série DS. Se quiser manter o mesmo VIP (endereço IP virtual) para o novo serviço de nuvem que hospeda as VMs da série DS, use [Endereços IP Reservados](../virtual-network/virtual-networks-instance-level-public-ip.md). As VMs da série GS podem ser adicionadas a um serviço de nuvem existente executando apenas VMs da série Gs.

**Disco do sistema operacional**: as VMs que podem ser executadas no Armazenamento Premium podem ser configuradas para usar um disco de OS (sistema operacional) Premium ou Standard. Recomendamos o uso do disco de OS baseado no Armazenamento Premium para ter a melhor experiência.

**Discos de dados**: você pode usar discos Premium ou Standard na mesma VM em execução no Armazenamento Premium. Com o Armazenamento Premium, você pode provisionar uma VM e anexar vários discos de dados persistentes a ela. Se necessário, pode distribuir entre os discos para aumentar a capacidade e o desempenho do volume.

> [!NOTE]
> Se você distribuir discos de dados do Armazenamento Premium usando [Espaços de Armazenamento](http://technet.microsoft.com/library/hh831739.aspx), deverá configurá-lo com uma coluna para cada disco usado. Caso contrário, o desempenho geral do volume distribuído pode ser menor que o esperado devido a uma distribuição irregular de tráfego entre os discos. Por padrão, a interface do usuário do Gerenciador de servidores (UI) permite que você configure colunas para até 8 discos. Se você tiver mais de oito discos, você precisa usar o PowerShell para criar o volume e também especificar manualmente o número de colunas. Caso contrário, a UI do Gerenciador de servidores continuará a usar 8 colunas, mesmo que haja mais discos. Por exemplo, se você tiver 32 discos em um conjunto único de distribuição, você deve especificar 32 colunas. Você pode usar o parâmetro *NumberOfColumns* do cmdlet do PowerShell [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) para especificar o número de colunas usadas pelo disco virtual. Para saber mais, confira [Visão geral de Espaços de Armazenamento](http://technet.microsoft.com/library/hh831739.aspx) e [Storage Spaces Frequently Asked Questions](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx) (Perguntas frequentes sobre os espaços de armazenamento).
> 

**Cache**: as VMs da série de tamanho com suporte para Armazenamento Premium têm uma funcionalidade de caching exclusiva com a qual você pode obter altos níveis de produtividade e latência, o que excede o desempenho do disco de Armazenamento Premium subjacente. Você pode configurar a política de caching de disco nos discos de Armazenamento Premium como ReadOnly, ReadWrite ou None. A política de cache de disco de padrão é ReadOnly para todos os discos de dados premium e ReadWrite para discos do sistema operacional. Use a configuração correta a fim de atingir o desempenho ideal para o aplicativo. Por exemplo, para discos de dados com necessidade de prontidão intensa ou somente leitura, como arquivos de dados do SQL Server, defina a política de cache de disco como "ReadOnly". Para discos de dados com gravação intensa ou somente gravação, como arquivos de log do SQL Server, defina a política de cache de disco como "None". Saiba mais sobre como otimizar seu design com o Armazenamento Premium em [Design para desempenho com o Armazenamento Premium](storage-premium-storage-performance.md).

**Análise**: para analisar o desempenho de VMs usando discos no Armazenamento Premium, você pode habilitar o Diagnóstico de VM do Azure no [Portal do Azure](https://portal.azure.com). Confira [Microsoft Azure Virtual Machine Monitoring with Azure Diagnostics Extension](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/) (Monitoramento de máquina virtual do Microsoft Azure com a Extensão de Diagnóstico do Azure) para obter detalhes. Para ver o desempenho de disco, use ferramentas baseadas no sistema operacional, como o [Monitor de Desempenho do Windows](https://technet.microsoft.com/library/cc749249.aspx) para VMs Windows e o [IOSTAT](http://linux.die.net/man/1/iostat) para VMs Linux.

**Desempenho e limites de escala de VM**: cada tamanho de VM com suporte do Armazenamento Premium tem limites de escala e especificações de desempenho para IOPS, largura de banda e número de discos que podem ser anexados por VM. Ao usar discos de Armazenamento Premium com VMs, verifique se há IOPS e largura de banda suficientes disponíveis na VM para direcionar o tráfego de disco.
Por exemplo, uma VM STANDARD_DS1 tem uma largura de banda dedicada de 32 MB/s disponível para o tráfego de discos do Armazenamento Premium. Um disco de Armazenamento Premium P10 pode fornecer largura de banda de 100 MB/s. Se um disco de Armazenamento Premium P10 fosse anexado a essa VM, poderia chegar apenas até 32 MB/s, mas não até os 100 MB/s que o disco P10 pode fornecer.

Atualmente, a maior VM na série DS é a Standard_DS15_v2, que pode fornecer até 960 MB/s em todos os discos. A maior VM na série GS é Standard_GS5 e pode fornecer até 2000 MB/s em todos os discos.
Observe que esses limites são apenas para o tráfego de disco, não incluindo acertos de cache e tráfego de rede. Há uma largura de banda separada disponível para tráfego de rede de VM, que é diferente da largura de banda dedicada aos discos de Armazenamento Premium.

Para obter informações mais atualizadas sobre IOPS e produtividade (largura de banda) máximos para as VMs com suporte do Armazenamento Premium, confira [Tamanhos de VMs Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [Tamanhos de VMs Linux](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para saber mais sobre os discos de Armazenamento Premium e seus limites de IOPs e taxa de transferência, confira a tabela na seção [Metas de desempenho e escalabilidade do Armazenamento Premium](#premium-storage-scalability-and-performance-targets) deste artigo.

## <a name="premium-storage-scalability-and-performance-targets"></a>Metas de desempenho e escalabilidade do Armazenamento Premium
Nesta seção, descreveremos todas as metas de escalabilidade e desempenho que você deve considerar ao usar o Armazenamento Premium.

As contas de Armazenamento Premium têm as seguintes metas de escalabilidade:

| Capacidade total da conta | Largura de banda total para uma conta de armazenamento com redundância local |
| --- | --- | 
| Capacidade do Disco: 35 TB <br>Capacidade de instantâneo: 10TB | Até 50 gigabits para Entrada + Saída |

* Entrada refere-se a todos os dados (solicitações) que estão sendo enviados para uma conta de armazenamento.
* Saída refere-se a todos os dados (respostas) que está sendo recebidos de uma conta de armazenamento.

Para saber mais, consulte [Metas de desempenho e escalabilidade do Armazenamento do Azure](storage-scalability-targets.md).

Se você estiver usando contas de Armazenamento Premium para discos não gerenciados, e seu aplicativo ultrapassar as metas de escalabilidade de uma única conta de armazenamento, leve em considere uma migração para o Managed Disks. Se você não quiser migrar para o Managed Disks, crie seu aplicativo para que ele use várias contas de armazenamento, e particione os dados entre essas contas de armazenamento. Por exemplo, se você quiser anexar discos de 51 terabytes TB em várias VMs, separe-as entre duas contas de armazenamento, já que o limite para uma única conta de Armazenamento Premium é de 35 TB. Certifique-se de que uma única conta de Armazenamento Premium nunca tenha mais do que 35 TB de discos provisionados.

### <a name="premium-storage-disk-limits"></a>Limites do disco de Armazenamento Premium
Quando você provisiona um disco de Armazenamento Premium, o tamanho do disco determina os valores máximos de IOPS e taxa de transferência (largura de banda). Há três tipos de discos de Armazenamento Premium: P10, P20 e P30. Cada um tem limites específicos de IOPS e taxa de transferência conforme especificados na tabela a seguir:

|Tipo de disco de armazenamento Premium | P10 | P20 | P30 |
| --- | --- | --- | --- |
| Tamanho do disco | 128 GiB | 512 GiB | 1024 GiB (1 TB) |
| IOPS por disco | 500 | 2.300 | 5.000 |
Taxa de transferência por disco | 100 MB/s | 150 MB/s | 200 MB/s |

> [!NOTE]
> Certifique-se de que exista largura de banda suficiente disponível em sua VM para direcionar o tráfego de disco, conforme explicado na seção [VMs com suporte do Armazenamento Premium](#premium-storage-supported-vms) anteriormente neste artigo. Caso contrário, a taxa de transferência e o IOPS do disco ficarão restritos a valores mais baixos com base nos limites da VM, e não nos limites de disco mencionados na tabela anterior.  
> 
> 

Aqui estão alguns pontos importantes que você deve conhecer sobre metas de desempenho e escalabilidade do Armazenamento Premium:

* **Desempenho e capacidade provisionada**: ao provisionar um disco de Armazenamento Premium, diferentemente do Armazenamento Standard, você tem a garantia de capacidade, IOPS e taxa de transferência para o disco. Por exemplo, se você criar um disco P30, o Azure provisionará uma capacidade de armazenamento de 1024 GB, 5000 IOPS e uma Taxa de transferência de 200 MB/s para o disco. O aplicativo pode usar a capacidade e o desempenho no todo ou em parte.

* **Tamanho de disco**: o Azure mapeia o tamanho do disco (arredondado) para a opção mais próxima do Disco de Armazenamento Premium, conforme especificado na tabela. Por exemplo, um disco com 100 GiB é classificado como uma opção P10 e pode executar até 500 IOPS, com uma taxa de transferência de até 100 MB/s. Da mesma forma, um disco com 400 GiB é classificado como P20 e pode executar até 2300 IOPS e taxa de transferência de 150 MB/s.
  
> [!NOTE]
> Você pode aumentar com facilidade o tamanho dos discos existentes. Por exemplo, talvez você queira aumentar o tamanho de um disco de 30 GB para 128GB ou até mesmo 1 TB. Ou, pode querer converter seu disco P20 para P30 porque precisa de mais capacidade ou mais IOPS e taxa de transferência. 
> 
 
* **Tamanho de E/S**: o tamanho da unidade de E/S (entrada/saída) é 256 KB. Se os dados transferidos forem menores que 256 KB, ele será considerado uma única unidade de e/s. Tamanhos maiores de e/s são contados como várias entradas e saídas de tamanho de 256 KB. Por exemplo, taxa de transferência de e/s de KB 1100 é contada como cinco unidades de e/s.

* **Taxa de transferência**: o limite de taxa de transferência inclui gravações e leituras do disco que não são realizadas do cache. Por exemplo, um disco P10 tem taxa de transferência de 100 MB/s por disco. Alguns exemplos de taxas de transferência válidas para o disco P10 são:

| Taxa de transferência máxima por disco P10 | Leituras não armazenadas em cache do disco | Gravações não armazenadas em cache do disco |
| --- | --- | --- |
| 100 MB por segundo | 100 MB por segundo | 0 |
| 100 MB por segundo | 0 | 100 MB por segundo |
| 100 MB por segundo | 60 MB por segundo | 40 MB por segundo |

* **Acertos de cache**: os acertos de cache não são limitados pelo IOPS ou Taxa de transferência alocada do disco. Por exemplo, quando você usa um disco de dados com a configuração de cache ReadOnly em uma VM com suporte do Armazenamento Premium, as Leituras realizadas do cache não estão sujeitas aos limites de taxa de transferência e IOPS do disco. Portanto, você pode obter uma taxa de transferência muito alta de um disco se a carga de trabalho for composta predominantemente de leituras. Observe que o cache está sujeito a limites separados de IOPS e taxa de transferência no nível da VM, com base no tamanho da VM. As VMs da Série DS têm aproximadamente 4000 IOPS e 33 MB/s por núcleo para IOs SSD em cache e local. As VMs da série DS têm um limite 5000 IOPS e 50 MB/s por núcleo para IOs SSD em cache e local. 

## <a name="throttling"></a>Limitação
Talvez você perceba uma limitação se o IOPS ou a taxa de transferência do aplicativo exceder os limites alocados para um disco de Armazenamento Premium ou se o tráfego total em todos os discos na VM exceder o limite de largura de banda em disco disponível para a VM. Para evitar a limitação, recomendamos a limitação do número de solicitações de E/S pendentes para discos com base nas metas de desempenho e escalabilidade do disco que você provisionou e com base na largura de banda de disco disponível na VM.  

Seu aplicativo pode obter a latência menor quando ele é projetado para evitar a limitação. Por outro lado, se o número de solicitações de e/s pendentes para o disco for muito pequeno, seu aplicativo não poderá tirar proveito dos níveis máximos de IOPS e taxa de transferência que estão disponíveis no disco.

Os exemplos a seguir demonstram como calcular os níveis de limitação. Todos os cálculos baseiam-se no tamanho da unidade de E/S de 256 KB:

### <a name="example-1"></a>Exemplo 1:
O aplicativo fez 495 unidades de E/S de tamanho de 16 KB em um segundo em um disco P10. Elas são contadas como 495 unidades de E/S por segundo (IOPS). Se você tentar uma e/s de 2 MB no mesmo segundo, o total de unidades de e/s é igual a 495 + 8. Isso ocorre porque a e/s de 2 MB resulta em unidades de e/s de 2048 KB/256 KB = 8 quando o tamanho da unidade de e/s é 256 KB. Como a soma de 495 + 8 excede o limite de 500 IOPS para o disco, a limitação ocorre.

### <a name="example-2"></a>Exemplo 2:
O aplicativo fez 400 unidades de E/S de tamanho de 256 KB em um disco P10. A largura de banda total consumida é (400 * 256) / 1024 = 100 MB/s. Um disco P10 tem um limite de taxa de transferência de 100 MB por segundo. Se o seu aplicativo tentar realizar mais e/s nesse segundo, ele é limitado porque excede o limite alocado.

### <a name="example-3"></a>Exemplo 3:
Você tem uma VM DS4 com dois discos P30 conectados. Cada disco P30 é capaz de uma taxa de transferência de 200 MB por segundo. No entanto, uma VM DS4 tem uma capacidade de largura de banda total do disco de 256 MB por segundo. Portanto, você não pode aplicar a taxa de transferência máxima aos os discos conectados nesta VM DS4 ao mesmo tempo. Para resolver esse problema, você pode sustentar o tráfego de 200 MB por segundo em um disco e 56 MB por segundo em outro disco. Se a soma do tráfego no disco for superior a 256 MB por segundo, o tráfego será limitado.

> [!NOTE]
> Se o tráfego de disco consiste principalmente de tamanhos de e/s pequenos, é muito provável que seu aplicativo atingirá o limite de IOPS antes do limite de taxa de transferência. Por outro lado, se o tráfego de disco consiste principalmente de tamanhos de e/s grandes, é muito provável que seu aplicativo atingirá o limite de taxa de transferência em vez do limite de IOPS. Você pode maximizar o IOPS do aplicativo e a capacidade da taxa de transferência usando tamanhos de E/S ideais e também limitando o número de solicitações de E/S pendentes para o disco.
> 

Para saber mais sobre o design para alto desempenho usando o Armazenamento Premium, leia o artigo [Design para desempenho com o Armazenamento Premium](storage-premium-storage-performance.md).

## <a name="snapshots-and-copy-blob"></a>Instantâneos e blob de cópia

Para o serviço de Armazenamento, o arquivo VHD é um blob de páginas. Você pode tirar instantâneos dos blobs de páginas e copiá-los para outro local, como uma conta de armazenamento diferente.

### <a name="unmanaged-disks"></a>Discos não gerenciados

Você pode criar [instantâneos incrementais](storage-incremental-snapshots.md) para discos não gerenciados premium da mesma maneira que usa os instantâneos com o Armazenamento Standard. Como o Armazenamento Premium só dá suporte ao LRS (Armazenamento com Redundância Local) como opção de replicação, recomendamos que você crie instantâneos e, então, copie esses instantâneos para uma conta de armazenamento com redundância geográfica padrão. Para saber mais, consulte [Opções de redundância do Armazenamento do Azure](storage-redundancy.md).

Se um disco estiver anexado a uma VM, determinadas operações de API não são permitidas nos discos. Por exemplo, você não pode executar uma operação de [Cópia de Blob](/rest/api/storageservices/fileservices/Copy-Blob) nesse blob enquanto o disco estiver anexado a uma VM. Em vez disso, primeiro crie um instantâneo desse blob usando o método de API REST [Instantâneo de Blob](/rest/api/storageservices/fileservices/Snapshot-Blob) e depois execute a [Cópia de Blob](/rest/api/storageservices/fileservices/Copy-Blob) do instantâneo para copiar o disco anexado. Como alternativa, você pode desanexar o disco e, em seguida, executar as operações necessárias.

Os seguintes limites a se aplicam a instantâneos de blob de Armazenamento Premium:

| Limite de Armazenamento Premium | Valor |
| --- | --- |
| Número máx. de instantâneos por blob | 100 |
| Capacidade de conta de armazenamento para instantâneos (inclui dados apenas em instantâneos e não inclui dados no blob de base) | 10 TB |
| Tempo mín. entre instantâneos consecutivos | 10 minutos |

Para manter cópias com redundância geográfica de seus instantâneos, você pode copiar instantâneos de uma conta de armazenamento premium para uma conta de armazenamento com redundância geográfica padrão usando AzCopy ou Copiar Blob. Para obter mais informações, confira [Transferir dados com o Utilitário de Linha de Comando AzCopy](storage-use-azcopy.md) e [Copy Blob](/rest/api/storageservices/fileservices/Copy-Blob) (Cópia de Blob).

Para obter informações detalhadas sobre como executar operações REST em blobs de página nas contas de Armazenamento Premium, consulte [Usar operações do serviço Blob com o Armazenamento Premium do Azure](http://go.microsoft.com/fwlink/?LinkId=521969).

### <a name="managed-disks"></a>Discos gerenciados

Um instantâneo de um disco gerenciado é uma cópia somente leitura do disco gerenciado que é armazenado como um disco gerenciado padrão. [Instantâneos incrementais](storage-incremental-snapshots.md) não têm suporte no momento para o Managed Disks, mas terão suporte no futuro. Para aprender a tirar um instantâneo de um disco gerenciado, consulte [Criar uma cópia de um VHD armazenado como um Managed Disk do Azure usando instantâneos gerenciado no Windows](../virtual-machines/virtual-machines-windows-snapshot-copy-managed-disk.md) ou [Criar uma cópia de um VHD armazenado como um Managed Disk do Azure usando instantâneos gerenciados no Linux](../virtual-machines/linux/virtual-machines-linux-snapshot-copy-managed-disk.md)

Se um disco gerenciado estiver anexado a uma VM, determinadas operações de API não serão permitidas nos discos. Por exemplo, você não pode gerar uma assinatura de acesso compartilhado (SAS) para executar uma operação de cópia enquanto o disco estiver anexado a uma VM. Em vez disso, primeiro crie um instantâneo do disco e, em seguida, execute a cópia do instantâneo. Como alternativa, você pode desanexar o disco e, em seguida, gerar uma assinatura de acesso compartilhado (SAS) para executar a operação de cópia.


## <a name="using-linux-vms-with-premium-storage"></a>Usando VMs do Linux com Armazenamento Premium
Consulte instruções importantes abaixo para configurar suas VMs do Linux no Armazenamento Premium:

* Para todos os discos de Armazenamento Premium com configuração de cache como "ReadOnly" ou "None", você deve desabilitar "barreiras" durante a montagem do sistema de arquivos a fim de atingir as metas de escalabilidade para o Armazenamento Premium. Você não precisa de barreiras para esse cenário, pois as gravações em discos de Armazenamento Premium são duráveis para essas configurações de cache. Quando a solicitação de gravação for concluída, os dados terão sido gravados no armazenamento persistente. Use os métodos a seguir para desabilitar "barreiras", escolhendo o certo para o seu sistema de arquivos:
  
* Se você usar **reiserFS**, desabilite as barreiras usando a opção de montagem "barrier=none" (para habilitar as barreiras, use "barrier=flush")
* Se você usar **ext3/ext4**, desabilite as barreiras usando a opção de montagem “barrier=0” (para habilitar as barreiras, use “barrier=1”)
* Se você usar **XFS**, desabilite as barreiras usando a opção de montagem “nobarrier” (para habilitar as barreiras, use a opção “barrier”)
* Para discos de Armazenamento Premium com a configuração de cache "ReadWrite", as barreiras devem ser habilitadas para durabilidade de gravações.
* Para que os rótulos de volume persistam após a reinicialização da VM, atualize o /etc/fstab com as referências de UUID para os discos. Confira também, [Adicionar um disco gerenciado a uma VM Linux](../virtual-machines/virtual-machines-linux-add-disk.md).

A seguir estão as distribuições do Linux que são validadas com o Armazenamento Premium. Recomendamos a atualização de suas VMs para pelo menos uma dessas versões (ou posterior) para obter melhor desempenho e estabilidade com o Armazenamento Premium. Além disso, algumas das versões exigem um LIS (Serviços de Integração do Linux) v4.0 para Microsoft Azure mais recente. Siga o link fornecido abaixo para download e instalação. Continuaremos a adicionar mais imagens à lista à medida que concluirmos validações adicionais. Perceba que nossas validações mostraram que o desempenho varia para essas imagens, e também depende de características da carga de trabalho e das configurações nas imagens. Imagens diferentes são ajustadas para tipos diferentes de carga de trabalho.

| Distribuição | Versão | Kernel com suporte | Detalhes |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x, 8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5, 6.6, 6.7, 7.0 | &nbsp; | [LIS4 obrigatório](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Veja a observação abaixo* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [LIS4 recomendado](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Veja a observação abaixo* |
| RHEL | 6.8+, 7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+ | &nbsp; | UEK4 ou RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 ou RHCK c/[LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 ou RHCK c/[LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |


### <a name="lis-drivers-for-openlogic-centos"></a>Drivers LIS para Openlogic CentOS

Os clientes que executam VMs com OpenLogic CentOS devem executar o comando a seguir para instalar os drivers mais recentes:

```
sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
sudo yum install microsoft-hyper-v
```

Depois, uma reinicialização será necessária para ativar os novos drivers.

## <a name="pricing-and-billing"></a>Preços e cobrança

Ao usar o Armazenamento Premium, as seguintes considerações de cobrança se aplicam:

* Tamanho do blob/disco do Armazenamento Premium
* Instantâneos de Armazenamento Premium
* Transferências de dados de saída

**Tamanho de blob/disco de Armazenamento Premium**: a cobrança para um blob/disco de Armazenamento Premium depende do tamanho provisionado do blob/disco. O Azure mapeia o tamanho provisionado (arredondado) para a opção mais próxima de Disco de Armazenamento Premium, conforme especificado na tabela fornecida na seção [Escalabilidade e metas de desempenho ao usar o Armazenamento Premium](#premium-storage-scalability-and-performance-targets) . Cada disco será mapeado para um dos tamanhos provisionados com suporte e será cobrado de acordo. A cobrança por qualquer disco provisionado é rateada por hora usando o preço mensal para a oferta de Armazenamento Premium. Por exemplo, se você provisionou um disco P10 e ele foi excluído após 20 horas, você será cobrado pela a oferta P10 rateada em 20 horas. Isto é independente da quantidade de dados reais gravados no disco ou do IOPS/taxa de transferência usados.

**Instantâneos de discos não gerenciados Premium**: os instantâneos de discos não gerenciados Premium são cobrados pela capacidade adicional usada pelos instantâneos. Para saber mais sobre instantâneos, consulte [Criando um instantâneo de um Blob](/rest/api/storageservices/fileservices/Snapshot-Blob).

**Instantâneos de discos não gerenciados Premium**: um instantâneo de um disco gerenciado é uma cópia somente leitura do disco que é armazenado como um disco gerenciado padrão. O custo de um instantâneo será o mesmo de um disco gerenciado padrão. Por exemplo, se você tirar um instantâneo de um disco gerenciado premium de 128 GB, o custo de um instantâneo será equivalente ao de um disco gerenciado padrão de 128 GB.  

**Transferências de dados de saída**: as [transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/) (dados saindo dos datacenters do Azure) incorrem em cobrança por uso de largura de banda.

Para obter detalhes dos preços do Armazenamento Premium, VMs com suporte do Armazenamento Premium e Managed Disks confira:

* [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/)
* [Preços de Máquinas Virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Preço do Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-service-support"></a>Suporte de serviço do Backup do Azure 

O backup das máquinas virtuais com discos não gerenciados pode ser feito usando o Backup do Azure. [Mais detalhes](../backup/backup-azure-vms-first-look-arm.md).

Você também pode usar o serviço de Backup do Azure com o Managed Disks para criar um trabalho de backup com backups baseados em tempo, fácil restauração de VM e políticas de retenção de backup. Você pode ler mais sobre isso em [Usando o serviço de backup do Azure para VMs com Managed Disks](../backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). 

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o Armazenamento Premium do Azure, confira os artigos a seguir.

### <a name="design-and-implement-with-azure-premium-storage"></a>Projetar e implementar com o Armazenamento Premium do Azure
* [Design para desempenho com o Armazenamento Premium](storage-premium-storage-performance.md)
* [Usando operações do serviço Blob com o Armazenamento Premium do Azure](http://go.microsoft.com/fwlink/?LinkId=521969)

### <a name="operational-guidance"></a>Diretrizes operacionais
* [Migrando para o Armazenamento do Azure Premium](storage-migration-to-premium-storage.md)

### <a name="blog-posts"></a>Postagens de blog
* [Armazenamento Premium do Azure com disponibilidade geral](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)
* [Anúncio da série GS: como adicionar suporte ao Armazenamento Premium para as maiores VMs na nuvem pública](https://azure.microsoft.com/blog/azure-has-the-most-powerful-vms-in-the-public-cloud/)
