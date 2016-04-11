<properties
	pageTitle="Usar instantâneos incrementais para backup e recuperação de máquinas virtuais do Azure | Microsoft Azure"
	description="Crie uma solução personalizada para backup e recuperação de seus discos de máquina virtual do Azure usando instantâneos incrementais."
	services="storage"
	documentationCenter="na"
	authors="ms-prkhad"
	manager=""
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/21/2016"
	ms.author="tamram;prkhad"/>


# Fazer backup de discos de máquina virtual do Azure com instantâneos virtuais

## Visão geral 

O Armazenamento do Azure oferece a capacidade de fazer instantâneos dos blobs. Os instantâneos capturam o estado do blob no momento em questão. Neste artigo, descrevemos um cenário de como você pode manter backups dos discos de máquinas virtuais usando instantâneos. Você pode usar essa metodologia quando optar por não usar o Serviço de Backup e Recuperação do Azure e desejar criar uma estratégia de backup personalizada para seus discos da máquina virtual.

Os discos de máquinas virtuais do Azure são armazenados como blobs de página no Armazenamento do Azure. Como estamos falando de uma estratégia de backup para discos de máquina virtual neste artigo, faremos referência aos instantâneos no contexto dos blobs de página. Para saber mais sobre instantâneos, consulte [Criando um instantâneo de um Blob](https://msdn.microsoft.com/library/azure/hh488361.aspx).

## O que é um instantâneo?

Um instantâneo de blob é uma versão somente leitura de um blob capturada em um dado momento. Quando um instantâneo tiver sido criado, ele pode ser lido, copiado ou excluído, mas não modificado. Os instantâneos fornecem uma maneira de fazer backup de um blob da maneira como ele aparece em um momento específico. Até a versão XX do REST você podia copiar instantâneos completos. Com a versão YY do REST e as versões superiores, você pode copiar instantâneos incrementais.

## Cópia de instantâneo completo

Instantâneos podem ser copiados para outra conta de armazenamento como um blob para manter backups do blob de base. Você também pode copiar um instantâneo sobre seu blob de base, que é semelhante a restaurar o blob para uma versão anterior. Quando um instantâneo é copiado de uma conta de armazenamento para outra, ele ocupa o mesmo espaço que o blob de páginas de base na conta de armazenamento de destino. Portanto, copiar instantâneos inteiros de uma conta de armazenamento para outra é um processo lentos que consumirá muito espaço na conta de armazenamento de destino.

>[AZURE.NOTE] Se você copiar o blob de base para outro destino, os instantâneos do blob não serão copiados com ele. Da mesma forma, se você substituir um blob de base por uma cópia, os instantâneos associados ao blob de base não serão afetados e permanecerão intactos com o nome do blob de base.

### Fazer backup de discos usando instantâneos

Como estratégia de backup para seus discos de máquina virtual, você pode fazer instantâneos periódicos do blob de páginas ou de disco e copiá-los para outra conta de armazenamento usando ferramentas como a operação [Copiar Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) ou [AzCopy](storage-use-azcopy.md). Você pode copiar um instantâneo para um blob de páginas de destino com um nome diferente. O blob de páginas de destino resultante é um blob de páginas gravável, e não um instantâneo. Mais adiante neste artigo, descreveremos as etapas para fazer backups dos discos de máquinas virtuais usando instantâneos.

### Restaurar discos usando instantâneos

Quando for o momento de restaurar o disco para uma versão anterior estável capturada em um dos instantâneos de backup, você pode copiar um instantâneo sobre o blob de páginas de base. Após ser promovido a blob de páginas de base, o instantâneo permanece, mas sua origem é substituída por uma cópia que pode ser lida e gravada. Mais adiante neste artigo, descreveremos as etapas para restaurar uma versão anterior do disco por meio do instantâneo.

### Implementando uma cópia completa do instantâneo

Você pode implementar uma cópia completa do instantâneo fazendo o seguinte

-   Primeiro, faça um instantâneo do blob de base usando a operação de [Snapshot Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx).

-   Em seguida, copie o instantâneo para uma conta de armazenamento de destino usando [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx).

&lt; Trechos de código mostrando create snapshot e copy blob &gt;

## Cópia de instantâneo incremental

A nova API Diff Page Ranges é uma maneira muito melhor para fazer backup de instantâneos de seus blobs de página ou discos. A API retorna a lista de alterações entre o blob de base e os instantâneos. Isso reduz a quantidade de espaço de armazenamento usado na conta de backup. A API dá suporte a blobs de página no Armazenamento Premium e no Armazenamento Standard. Com essa API, você pode criar soluções de backup mais rápidas e eficientes para VMs do Azure. Isso será disponibilizado com o REST versão xx ou superior.

A Cópia de instantâneo incremental permite copiar de uma conta de armazenamento para outra a diferença entre:

-   o blob de base e seu instantâneo OU

-   quaisquer dois instantâneos do blob de base

**Observação**: este recurso está disponível para os Blobs de Página do Azure Premium e Standard.

Quando você tem uma estratégia de backup personalizada que usa instantâneos, copiar os instantâneos de uma conta de armazenamento para outra pode ser um processo muito lento que consome muito espaço de armazenamento. Em vez de copiar todo o instantâneo para uma conta de armazenamento de backup, você pode gravar a diferença em um blob de páginas de backup. Dessa forma, o tempo para copiar e o espaço para armazenar backups são reduzidos substancialmente.

### Implementando a Cópia de instantâneo incremental

Você pode implementar a cópia de instantâneo incremental fazendo o seguinte

-   Faça um instantâneo do blob de base usando [Snapshot Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx).

-   Copie o blob de base para a conta de armazenamento de destino usando [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx).

-   Obtenha a diferença entre o blob de base e instantâneo usando [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) para o blob de base. Use o novo parâmetro **prevsnapshot** para especificar o valor de DateTime do instantâneo do qual você deseja obter a diferença. Quando esse parâmetro estiver presente, a resposta REST incluirá apenas as páginas que foram alteradas entre o blob de destino e o instantâneo anterior, incluindo páginas em branco.

-   Por fim, use [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) para aplicar essas alterações ao blob de páginas de backup.

&lt; Trechos de código mostrando get page ranges e put page &gt;

Na próxima seção, descreveremos com mais detalhes como você pode manter backups de discos usando a Cópia de instantâneo incremental

## Cenário

Nesta seção, descreveremos um cenário que envolve uma estratégia de backup personalizada para discos de máquina virtual usando instantâneos.

Considere uma VM do Azure da série DS com um disco P30 de armazenamento premium anexado. O disco P30 chamado mypremiumdisk é armazenado em uma conta de armazenamento premium chamada mypremiumaccount. Uma conta de armazenamento standard chamada mybackupstdaccount será usada para armazenar o backup do mypremiumdisk. Gostaríamos de manter um instantâneo de mypremiumdisk a cada 12 horas.

Para saber mais sobre a criação discos e contas de armazenamento, consulte [Sobre as contas de armazenamento do Azure](storage-create-storage-account.md).

Para saber mais sobre como fazer backup de VMs do Azure, consulte [Planejar backups de VMs do Azure](../backup/backup-azure-vms-introduction.md).

## Etapas para manter backups de um disco usando instantâneos incrementais

As etapas descritas abaixo farão instantâneos de mypremiumdisk e manterão os backups em mybackupstdaccount. O backup será um blob de páginas padrão chamado mybackupstdpageblob, que sempre refletirá o mesmo estado que o último instantâneo de mypremiumdisk.

1.  Fazer uma cópia do disco de armazenamento premium na conta de armazenamento padrão de backup. Copie mypremiumdisk de mypremiumaccount para mybackupstdaccount como um novo blob de páginas chamado mybackupstdpageblob.

2.  Faça um instantâneo de mybackupstdpageblob chamado mybackupstdpageblob\_ss1 usando Snapshot Blob e armazene-o em mybackupstdaccount.

3.  Durante a janela de backup, crie um instantâneo de mypremiumdisk, digamos mypremiumdisk\_ss1, e armazene-o em mypremiumaccount.

4.  Obtenha as alterações incrementais entre mypremiumdisk e seu primeiro instantâneo mypremiumdisk\_ss1 usando GetPageRanges. Grave essas alterações incrementais do blob de páginas de backup mybackupstdpageblob em mybackupstdaccount. Se houver intervalos excluídos nas alterações incrementais, eles deverão ser excluídos do blob de páginas de backup. Use Put Page para gravar as alterações incrementais no blob de páginas de backup.

5.  Faça um instantâneo de do blob de páginas de backup mybackupstdpageblob chamado mybackupstdpageblob\_ss2 e armazene-o em mybackupstdaccount. Exclua o instantâneo da conta de armazenamento premium mypremiumdisk\_ss1.

6.  Repita as etapas 3 a 5 em cada janela de backup. Dessa forma, você pode manter backups de mypremiumdisk em uma conta de armazenamento padrão.

![Fazer backup de disco usando instantâneos incrementais](./media/storage-incremental-snapshots/storage-incremental-snapshots-1.png)

&lt; Trecho de código mostrando Copy Blob, Get Page Ranges, Put Page &gt;

## Etapas para restaurar um disco por meio de instantâneos

As etapas descritas abaixo vão restaurar o disco premium mypremiumdisk para um instantâneo anterior da conta de armazenamento de backup mybackupstdaccount.

1.  Identifique o ponto para o qual você deseja restaurar o disco premium. Digamos que seja o instantâneo mybackupstdpageblob\_ss1, que está é armazenado na conta de armazenamento de backup mybackupstdaccount.

2.  Em mybackupstdaccount, promova o instantâneo mybackupstdpageblob\_ss1 como o novo blob de páginas de base de backup mybackupstdpageblobrestored.

3.  Copie o blob de páginas de base de backup mybackupstdpageblobrestored de mybackupstdaccount para mypremiumaccount como o novo disco premium mypremiumdiskrestored.

4.  Aponte a VM da série DS para o disco restaurado mypremiumdiskrestored e desanexe o antigo mypremiumdisk da VM.

5.  Inicie o processo de backup descrito na seção anterior para o disco restaurado mypremiumdiskrestored usando mybackupstdpageblobrestored como o blob de páginas de backup.

![Restaurar disco por meio de instantâneos](./media/storage-incremental-snapshots/storage-incremental-snapshots-2.png)

&lt; Trecho de código mostrando Copy Blob, Attach disk e detach disk &gt;

## Consulte também

- [Criando um instantâneo de um Blob](https://msdn.microsoft.com/library/azure/hh488361.aspx)
- [Planejar sua Infraestrutura de backup de VM](../backup/backup-azure-vms-introduction.md)

<!---HONumber=AcomDC_0330_2016-->