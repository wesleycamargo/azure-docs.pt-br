<properties
	pageTitle="Armazenamento Premium: Armazenamento de Alto Desempenho para as Cargas de Trabalho da Máquina Virtual do Azure"
	description="O Armazenamento Premium dá suporte ao disco de alto desempenho e baixa latência para cargas de trabalho que usam muita E/S em execução em máquinas virtuais do Azure. As VMs das séries DS, DSv2 e GS do Azure são compatíveis com o Armazenamento Premium."
	services="storage"
	documentationCenter=""
	authors="ms-prkhad"
	manager=""
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="prkhad"/>


# Armazenamento Premium: armazenamento de alto desempenho para as cargas de trabalho da máquina virtual do Azure

## Visão geral

O Armazenamento Premium do Azure dá suporte de disco de alto desempenho e baixa latência para máquinas virtuais executando cargas de trabalho intensivas para entradas e saídas. Discos de VM (máquina virtual) que usam o armazenamento Premium armazenam dados em SSDs (unidades de estado sólido). Você pode migrar os discos de VM do seu aplicativo para o Armazenamento Premium do Azure para aproveitar a velocidade e o desempenho desses discos.

Uma VM do Azure dá suporte à anexação vários discos de Armazenamento Premium, para que seus aplicativos possam ter até 64 TB de armazenamento por VM. Com o Armazenamento Premium, seus aplicativos podem atingir 80.000 IOPS (operações de entrada/saída por segundo) por VM e taxa de transferência de disco de 2.000 MB por segundo por VM com latências extremamente baixas para operações de leitura.

Com o Armazenamento Premium, o Azure oferece a capacidade de realmente deslocar os exigentes aplicativos empresariais, por exemplo, Dynamics AX, Dynamics CRM, Exchange Server, Farms do SharePoint e SAP Business Suite, para a nuvem. Você pode executar várias cargas de trabalho de banco de dados com desempenho intenso, como o SQL Server, Oracle, MongoDB, MySQL, Redis, que exigem um desempenho alto e consistente e baixa latência no Armazenamento Premium.

>[AZURE.NOTE] É recomendável migrar qualquer disco de máquina virtual que exija IOPS alta para o Armazenamento Premium do Azure para obter o melhor desempenho para o seu aplicativo. Se o disco não requer IOPS alta, você pode limitar os custos mantendo-a no armazenamento padrão, que armazena dados de disco da máquina virtual em HDDs (unidades de disco rígido) em vez de SSDs.

Para começar com o Armazenamento Premium do Azure, visite a página [Introdução gratuita](https://azure.microsoft.com/pricing/free-trial/). Para saber mais sobre como migrar as máquinas virtuais existentes para o Armazenamento Premium, confira [Migração para o Armazenamento Premium do Azure](storage-migration-to-premium-storage.md).

>[AZURE.NOTE] O Armazenamento Premium tem suporte em algumas regiões. Você pode encontrar a lista de regiões disponíveis em [Serviços do Azure por Região](https://azure.microsoft.com/regions/#services).

## Recursos de Armazenamento Premium

**Discos de Armazenamento Premium**: o Armazenamento Premium do Azure é compatível com discos de VM que podem ser anexados a VMs do Azure das séries DS, DSv2 ou GS. Ao usar o Armazenamento Premium, você tem a opção de três tamanhos de disco, ou seja, P10 (128 GiB), P20 (512 GiB) e P30 (1024 GiB), cada um com suas próprias especificações de desempenho. Dependendo do requisito do aplicativo, você pode anexar um ou mais desses discos à VM das séries DS, DSv2 ou GS. Na seção a seguir sobre [Metas de desempenho e escalabilidade do Armazenamento Premium ](#premium-storage-scalability-and-performance-targets), descreveremos as especificações em mais detalhes.

**Blob de Páginas Premium**: o Armazenamento Premium dá suporte a Blobs de Páginas do Azure, que são usados para manter discos persistentes para VMs (Máquinas Virtuais) do Azure. Atualmente, o Armazenamento Premium não dá suporte a Blobs de Blocos do Azure, Blobs de Acréscimo do Azure, Arquivos do Azure, Tabelas do Azure nem Filas do Azure.

**Conta Premium\_LRS**: para começar a usar o Armazenamento Premium, você deve criar uma conta de Armazenamento Premium. Crie uma conta de armazenamento do tipo "Premium\_LRS" usando o [Portal do Azure](https://portal.azure.com) ou as seguintes bibliotecas do SDK: [API REST de Armazenamento](http://msdn.microsoft.com//library/azure/dd179355.aspx) versão 2014-02-14 ou posterior; [API REST do Serviço de Gerenciamento](http://msdn.microsoft.com/library/azure/ee460799.aspx) versão 2014-10-01 ou posterior (implantações clássicas;) [Referência de API REST do Provedor de Recursos de Armazenamento do Azure](http://msdn.microsoft.com/library/azure/mt163683.aspx) (implantações ARM); e [Azure PowerShell](../powershell-install-configure.md) versão 0.8.10 ou posterior. Saiba mais sobre limites de conta de armazenamento premium na seção a seguir sobre [Metas de desempenho e escalabilidade do Armazenamento Premium](#premium-storage-scalability-and-performance-targets).

**Armazenamento com Redundância Local premium**: uma conta de Armazenamento Premium tem redundância local (LRS) e mantém três cópias dos dados em uma única região. Para obter considerações sobre replicação geográfica ao usar o Armazenamento Premium, confira a seção [Instantâneos e cópia de Blob](#snapshots-and-copy-blob) neste artigo.

O Azure usa a conta de armazenamento como um contêiner para seu sistema operacional (SO) e discos de dados. Quando você cria uma VM DS, DSv2 ou GS do Azure e escolhe uma conta de Armazenamento Premium Azure, o sistema operacional e os discos de dados são armazenados nessa conta de armazenamento.

Você pode usar o Armazenamento Premium para discos de uma das seguintes maneiras:
- Primeiro, crie uma nova conta de armazenamento premium. Em seguida, ao criar uma nova VM DS, DSv2 ou GS, escolha a conta de armazenamento premium nas definições de configuração de Armazenamento. OU
- Ao criar uma nova VM DS, DSv2 ou GS, crie uma nova conta de armazenamento premium nas definições de configuração de Armazenamento ou permita que o Portal do Azure crie uma conta de armazenamento premium padrão.


Para obter instruções passo a passo, confira a seção [Início rápido](#quick-start), mais adiante neste artigo.

>[AZURE.NOTE] Uma conta de Armazenamento Premium não pode ser mapeada para um nome de domínio personalizado.

## VMs das séries DS, DSv2 e GS

O Armazenamento Premium é compatível com VMs (Máquinas Virtuais) do Azure das séries DS, DSv2 e GS. Você pode usar discos de armazenamento Standard e Premium com as séries DS, DSv2 ou GS das VMs. Mas você não pode usar discos de Armazenamento Premium com VMs que não pertençam à série DS ou à série GS. Para obter informações sobre tipos e tamanhos disponíveis de VM do Azure, veja [Tamanhos das máquinas virtuais](../virtual-machines/virtual-machines-linux-sizes.md). A seguir estão alguns dos recursos das VMs das séries Ds, DSv2 e GS.

**Serviço de nuvem**: VMs da série DS podem ser adicionadas a um serviço de nuvem que inclui apenas VMs da série DS. Não adicione VMs da série DS a um serviço de nuvem existente que inclua VMs que não são da série DS. Você pode migrar seus VHDs existentes para um novo serviço de nuvem que executa apenas VMs da série DS. Se quiser manter o mesmo VIP (endereço IP virtual) para o novo serviço de nuvem que hospeda as VMs da série DS, use [Endereços IP Reservados](../virtual-network/virtual-networks-instance-level-public-ip.md). As VMs da série GS podem ser adicionadas a um serviço de nuvem existente executando apenas VMs da série G.

**Disco do Sistema Operacional**: as máquinas virtuais do Azure das séries DS, DSv2 e GS podem ser configuradas para usar um disco do SO (sistema operacional) hospedado em uma conta de Armazenamento Standard ou em uma conta de Armazenamento Premium. Se você usar o disco do SO somente para a inicialização, considere o uso de um disco de SO baseado no Armazenamento Padrão. Com isso, você obterá custo-benefício e resultados de desempenho semelhantes aos do Armazenamento Premium após a inicialização. Se você executar qualquer outra tarefa no disco do SO que não seja de inicialização, use o Armazenamento Premium, pois ele fornece os melhores resultados de desempenho. Por exemplo, se o aplicativo ler ou gravar no/do disco do SO, o uso do disco do SO baseado no Armazenamento Premium oferece o melhor desempenho para sua VM.

**Discos de Dados**: você pode usar discos de armazenamento Premium ou Standard na mesma VM das séries DS, DSv2 ou GS. Com o Armazenamento Premium, você pode provisionar uma VM das séries DS, DSv2 ou GS e anexar vários discos de dados persistentes à VM. Se necessário, pode distribuir entre os discos para aumentar a capacidade e o desempenho do volume.

> [AZURE.NOTE] Se você distribuir discos de dados do Armazenamento Premium usando [Espaços de Armazenamento](http://technet.microsoft.com/library/hh831739.aspx), deverá configurá-lo com uma coluna para cada disco usado. Caso contrário, o desempenho geral do volume distribuído pode ser menor que o esperado devido a uma distribuição irregular de tráfego entre os discos. Por padrão, a interface do usuário do Gerenciador de servidores (UI) permite que você configure colunas para até 8 discos. Mas se você tiver mais de 8 discos, você precisa usar o PowerShell para criar o volume e também especificar manualmente o número de colunas. Caso contrário, a UI do Gerenciador de servidores continuará a usar 8 colunas, mesmo que haja mais discos. Por exemplo, se você tiver 32 discos em um conjunto único de distribuição, você deve especificar 32 colunas. Você pode usar o parâmetro *NumberOfColumns* do cmdlet do PowerShell [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) para especificar o número de colunas usadas pelo disco virtual. Para saber mais, consulte [Visão geral dos espaços de armazenamento](http://technet.microsoft.com/library/hh831739.aspx) e [Perguntas frequentes sobre os espaços de armazenamentos](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).

**Cache**: VMs das séries DS, DSv2 e GS têm um recurso exclusivo de cache com o qual você pode obter altos níveis de produtividade e latência, o que excede o desempenho do disco de Armazenamento Premium subjacente. Você pode configurar a política de cache de disco nos discos de Armazenamento Premium como ReadOnly, ReadWrite ou None. A política de cache de disco de padrão é ReadOnly para todos os discos de dados premium e ReadWrite para discos do sistema operacional. Use a configuração correta a fim de atingir o desempenho ideal para o aplicativo. Por exemplo, para discos de dados com necessidade de prontidão intensa ou somente leitura, como arquivos de dados do SQL Server, defina a política de cache de disco como "ReadOnly". Para discos de dados com gravação intensa ou somente gravação, como arquivos de log do SQL Server, defina a política de cache de disco como "None". Saiba mais sobre como otimizar seu design com o Armazenamento Premium em [Design para desempenho com o Armazenamento Premium](storage-premium-storage-performance.md).

**Análise**: para analisar o desempenho de VMs usando discos em contas de Armazenamento Premium, você pode habilitar o Diagnóstico de VM do Azure no Portal do Azure. Confira [Microsoft Azure Virtual Machine Monitoring with Azure Diagnostics Extension](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/) para obter detalhes. Para ver o desempenho de disco, use ferramentas baseados no sistema operacional, como o [Monitor de Desempenho do Windows](https://technet.microsoft.com/library/cc749249.aspx) para VMs do Windows e o [IOSTAT](http://linux.die.net/man/1/iostat) para VMs do Linux.

**Desempenho e limites de escala de VM**: cada tamanho de VM das séries DS, DSv2 e GS tem limites de escala e uma especificação de desempenho para IOPS, largura de banda e número de discos que podem ser anexados por VM. Ao usar discos de armazenamento premium com VMs das séries DS, DSv2 ou GS, verifique se há IOPS e largura de banda suficientes disponíveis na VM para direcionar o tráfego de disco. Por exemplo, uma VM STANDARD\_DS1 tem uma banda larga dedicada de 32 MB por segundo disponível para o tráfego de discos do Armazenamento Premium. Um disco de armazenamento premium P10 pode fornecer largura de banda de 100 MB por segundo. Se um disco P10 do Armazenamento Premium fosse anexado a essa VM poderia chegar apenas até 32 MB por segundo, mas não até os 100 MB por segundo que o disco P10 pode fornecer.

Atualmente, a maior VM na série DS é a STANDARD\_DS14, que pode fornecer até 512 MB por segundo em todos os discos. A maior VM na série GS é STANDARD\_GS5 e pode fornecer até 2.000 MB por segundo em todos os discos. Observe que esses limites são apenas para o tráfego de disco, não incluindo acertos de cache e tráfego de rede. Há uma largura de banda separada disponível para tráfego de rede de VM, que é diferente da largura de banda dedicada aos discos do Armazenamento Premium.

Para obter informações mais atualizadas sobre IOPS e produtividade (largura de banda) máximas para as VMs das séries DS, DSv2 e GS, confira [Sizes for Virtual Machines](../virtual-machines/virtual-machines-linux-sizes.md) (Tamanhos das máquinas virtuais). Para saber mais sobre os discos de armazenamento Premium e seus limites de IOPs e taxa de transferência, consulte a tabela na seção [Metas de desempenho e escalabilidade ao usar o Armazenamento Premium](#scalability-and-performance-targets-whpt-BRing-premium-storage) deste artigo.

## Metas de desempenho e escalabilidade do Armazenamento Premium

Nesta seção, descreveremos todas as metas de escalabilidade e desempenho que você deve considerar ao usar o Armazenamento Premium.

### Limites de conta de Armazenamento Premium

As contas de Armazenamento Premium têm as seguintes metas de escalabilidade:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Capacidade total da conta</strong></td>
	<td><strong>Largura de banda total para uma conta de armazenamento localmente redundante</strong></td>
</tr>
<tr>
	<td>
	<ul>
       <li type=round>Capacidade do disco: 35 TB</li>
       <li type=round>Capacidade de instantâneo: 10TB</li>
    </ul>
	</td>
	<td>Até 50 gigabits para Entrada + Saída</td>
</tr>
</tbody>
</table>

- Entrada refere-se a todos os dados (solicitações) que estão sendo enviados para uma conta de armazenamento.
- Saída refere-se a todos os dados (respostas) que está sendo recebidos de uma conta de armazenamento.

Para saber mais, consulte [Metas de desempenho e escalabilidade do Armazenamento do Azure](storage-scalability-targets.md).

Se o seu aplicativo precisa exceder as metas de escalabilidade de uma única conta de armazenamento, crie seu aplicativo para usar múltiplas contas de armazenamento e faça o particionamento dos seus dados através destas contas de armazenamento. Por exemplo, se você quiser anexar discos de 51 terabytes TB em várias VMs, separe-as entre duas contas de armazenamento, já que o limite para uma única conta de armazenamento Premium é de 35 TB. Certifique-se de que uma única conta de Armazenamento Premium nunca tenha mais de 35 TB de discos provisionados.

### Limites de discos de Armazenamento Premium

Quando você provisiona um disco em relação a uma conta de armazenamento Premium, quantas operações de entrada/saída por segundo (IOPS) e taxa de transferência (largura de banda) que podem ser obtidas depende do tamanho do disco. Atualmente, há três tipos de discos de Armazenamento Premium: P10, P20 e P30. Cada um tem limites específicos de IOPS e taxa de transferência conforme especificados na tabela a seguir:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Tipo de disco do Armazenamento Premium</strong></td>
	<td><strong>P10</strong></td>
	<td><strong>P20</strong></td>
	<td><strong>P30</strong></td>
</tr>
<tr>
	<td><strong>Tamanho do disco</strong></td>
	<td>128 GiB</td>
	<td>512 GiB</td>
	<td>1024 GiB (1 TB)</td>
</tr>
<tr>
	<td><strong>IOPS por disco</strong></td>
	<td>500</td>
	<td>2.300</td>
	<td>5.000</td>
</tr>
<tr>
	<td><strong>Taxa de transferência por disco</strong></td>
	<td>100 MB por segundo </td>
	<td>150 MB por segundo </td>
	<td>200 MB por segundo </td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] Verifique se há largura de banda suficiente disponível na VM para direcionar o tráfego de disco, conforme explicado na seção [VMs das séries DS, DSv2 e GS](#ds-dsv2-and-gs-series-vms) deste artigo. Caso contrário, a taxa de transferência e o IOPS do disco ficarão restritos a valores mais baixos com base nos limites da VM, e não nos limites de disco mencionados na tabela anterior.

Aqui estão alguns pontos importantes que você deve conhecer sobre metas de desempenho e escalabilidade do Armazenamento Premium:

- **Desempenho e capacidade provisionada**: ao provisionar um disco de armazenamento premium, diferentemente do armazenamento padrão, você tem a garantia de capacidade, IOPS e taxa de transferência para o disco. Por exemplo, se você criar um disco P30, o Azure provisionará uma capacidade de armazenamento de 1.024 GB, 5.000 IOPS e uma Taxa de transferência de 200 MB por segundo para o disco. O aplicativo pode usar a capacidade e o desempenho no todo ou em parte.

- **Tamanho de disco**: o Azure mapeia o tamanho do disco (arredondado) para a opção mais próxima do disco de Armazenamento Premium, conforme especificado na tabela. Por exemplo, um disco com 100 GiB é classificado como uma opção P10 e pode executar até 500 unidades de E/S por segundo, com uma taxa de transferência de até 100 MB por segundo. Da mesma forma, um disco com 400 GiB é classificado como uma opção P20 e pode executar até 2300 unidades de E/S por segundo, com uma taxa de transferência de até 150 MB por segundo.

	> [AZURE.NOTE] Você pode aumentar com facilidade o tamanho dos discos existentes. Por exemplo, se você quiser aumentar o tamanho de um disco de 30 GB para 128GB ou 1 TB. Ou, se você quiser converter seu disco P20 para P30 porque precisa de mais capacidade ou mais IOPS e taxa de transferência. Você pode expandir o disco usando o commandlet do PowerShell "Update-AzureDisk" com a propriedade "-ResizedSizeInGB". Para executar essa ação, o disco precisa ser desconectado da VM ou a VM precisa ser interrompida.

- **Tamanho de E/S**: o tamanho da unidade de E/S (entrada/saída) é 256 KB. Se os dados transferidos forem menores que 256 KB, ele será considerado uma única unidade de e/s. Tamanhos maiores de e/s são contados como várias entradas e saídas de tamanho de 256 KB. Por exemplo, taxa de transferência de e/s de KB 1100 é contada como cinco unidades de e/s.

- **Taxa de transferência**: o limite de taxa de transferência inclui gravações e leituras no disco que não são realizadas do cache. Por exemplo, um disco P10 tem taxa de transferência de 100 MB por segundo por disco. Alguns exemplos de taxas de transferência válidas para o disco P10 são:
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Taxa de transferência máxima por disco P10</strong></td>
	<td><strong>Leituras não armazenadas em cache do disco</strong></td>
	<td><strong>Gravações não armazenadas em cache do disco</strong></td>
</tr>
<tr>
	<td>100 MB por segundo</td>
	<td>100 MB por segundo</td>
	<td>0</td>
</tr>
<tr>
	<td>100 MB por segundo</td>
	<td>0</td>
	<td>100 MB por segundo</td>
</tr>
<tr>
	<td>100 MB por segundo </td>
	<td>60 MB por segundo </td>
	<td>40 MB por segundo </td>
</tr>
</tbody>
</table>

- **Acertos de cache**: os acertos de cache não são limitados pelo IOPS/Taxa de transferência alocada do disco. Por exemplo, quando você usa um disco de dados com a configuração de cache ReadOnly em uma VM das séries DS, DSv2 ou GS, as leituras realizadas no cache não estão sujeitas aos limites de disco do Armazenamento Premium. Assim, você pode obter uma taxa de transferência muito alta de um disco se a carga de trabalho for composta predominantemente de Leituras. Observe que o cache está sujeito a limites separados de IOPS/Taxa de transferência no nível da VM, com base no tamanho da VM. As VMs da Série DS têm aproximadamente 4000 IOPS e 33 MB/s por núcleo para IOs SSD em cache e local. As VMs da série DS têm um limite 5000 IOPS e 50 MB/s por núcleo para IOs SSD em cache e local.

## Limitação
Talvez você perceba uma limitação se o IOPS ou a taxa de transferência do aplicativo exceder os limites alocados para um disco de Armazenamento Premium ou se o tráfego total em todos os discos na VM exceder o limite de largura de banda em disco disponível para a VM. Para evitar a limitação, recomendamos a limitação do número de solicitações de E/S pendentes para discos com base nas metas de desempenho e escalabilidade do disco que você provisionou e com base na largura de banda de disco disponível na VM.

Seu aplicativo pode obter a latência menor quando ele é projetado para evitar a limitação. Por outro lado, se o número de solicitações de e/s pendentes para o disco for muito pequeno, seu aplicativo não poderá tirar proveito dos níveis máximos de IOPS e taxa de transferência que estão disponíveis no disco.

Os exemplos a seguir demonstram como calcular os níveis de limitação. Todos os cálculos baseiam-se no tamanho da unidade de E/S de 256 KB:

### Exemplo 1:
O aplicativo fez 495 unidades de E/S de tamanho de 16 KB em um segundo em um disco P10. Elas são contadas como 495 unidades de E/S por segundo (IOPS). Se você tentar uma e/s de 2 MB no mesmo segundo, o total de unidades de e/s é igual a 495 + 8. Isso ocorre porque a e/s de 2 MB resulta em unidades de e/s de 2048 KB/256 KB = 8 quando o tamanho da unidade de e/s é 256 KB. Como a soma de 495 + 8 excede o limite de 500 IOPS para o disco, a limitação ocorre.

### Exemplo 2:
O aplicativo fez 400 unidades de E/S de tamanho de 256 KB em um disco P10. A largura de banda total consumida é (400 * 256) / 1024 = 100 MB/s. Um disco P10 tem um limite de taxa de transferência de 100 MB por segundo. Se o seu aplicativo tentar realizar mais e/s nesse segundo, ele é limitado porque excede o limite alocado.

### Exemplo 3:
Você tem uma VM DS4 com dois discos P30 conectados. Cada disco P30 é capaz de uma taxa de transferência de 200 MB por segundo. No entanto, uma VM DS4 tem uma capacidade de largura de banda total do disco de 256 MB por segundo. Portanto, você não pode aplicar a taxa de transferência máxima aos os discos conectados nesta VM DS4 ao mesmo tempo. Para resolver esse problema, você pode sustentar o tráfego de 200 MB por segundo em um disco e 56 MB por segundo em outro disco. Se a soma do tráfego no disco for superior a 256 MB por segundo, o tráfego será limitado.

>[AZURE.NOTE] Se o tráfego de disco consiste principalmente de tamanhos de e/s pequenos, é muito provável que seu aplicativo atingirá o limite de IOPS antes do limite de taxa de transferência. Por outro lado, se o tráfego de disco consiste principalmente de tamanhos de e/s grandes, é muito provável que seu aplicativo atingirá o limite de taxa de transferência em vez do limite de IOPS. Você pode maximizar o IOPS do aplicativo e a capacidade da taxa de transferência usando tamanhos de E/S ideais e também limitando o número de solicitações de E/S pendentes para o disco.

Para saber mais sobre o design para alto desempenho usando o Armazenamento Premium, leia o artigo [Design para desempenho com o Armazenamento Premium](storage-premium-storage-performance.md).

## Instantâneos e Cópia de Blob
Você pode criar um instantâneo para armazenamento Premium da mesma maneira como você cria um instantâneo ao usar o armazenamento padrão. Como o armazenamento Premium é redundante localmente, é recomendável que você crie instantâneos e então copie esses instantâneos para uma conta de armazenamento com redundância geográfica padrão. Para saber mais, consulte [Opções de redundância do Armazenamento do Azure](storage-redundancy.md).

Se um disco estiver anexado a uma VM, determinadas operações de API não são permitidas no suporte da página de blob do disco. Por exemplo, você não poderá executar uma operação de [Cópia de Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) nesse blob enquanto o disco estiver anexado a uma VM. Em vez disso, primeiro crie um instantâneo desse blob usando o método de API REST do [Instantâneo de Blob ](http://msdn.microsoft.com/library/azure/ee691971.aspx) e depois execute a [Cópia de Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) do instantâneo para copiar o disco anexado. Como alternativa, você pode desanexar o disco e, em seguida, executar as operações necessárias no blob subjacente.

Os seguintes limites a se aplicam a instantâneos de blob de Armazenamento Premium:
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Limite de Armazenamento Premium</strong></td>
	<td><strong>Valor</strong></td>
</tr>
<tr>
	<td>Número máx. de instantâneos por blob</td>
	<td>100</td>
</tr>
<tr>
	<td>Capacidade de conta de armazenamento para instantâneos (inclui dados apenas em instantâneos e não inclui dados no blob de base)</td>
	<td>10 TB</td>
</tr>
<tr>
	<td>Tempo mín. entre instantâneos consecutivos</td>
	<td>10 minutos</td>
</tr>
</tbody>
</table>

Para manter cópias com redundância geográfica de seus instantâneos, você pode copiar instantâneos de uma conta de armazenamento premium para uma conta de armazenamento com redundância geográfica padrão usando AzCopy ou Copiar Blob. Para obter mais informações, confira [Transferir dados com o Utilitário de Linha de Comando AzCopy](storage-use-azcopy.md) e [Cópia de Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx).

Para obter informações detalhadas sobre como executar operações REST em blobs de página nas contas de Armazenamento Premium, consulte [Usando operações de Serviço de Blob com o Armazenamento Premium do Azure](http://go.microsoft.com/fwlink/?LinkId=521969) na biblioteca MSDN.

## Usando VMs do Linux com Armazenamento Premium
Consulte instruções importantes abaixo para configurar suas VMs do Linux no Armazenamento Premium:

- Para todos os discos de Armazenamento Premium com configuração de cache como "ReadOnly" ou "None", você deve desabilitar "barreiras" durante a montagem do sistema de arquivos a fim de atingir as metas de escalabilidade para o Armazenamento Premium. Você não precisa de barreiras para esse cenário, pois as gravações em discos de backup do Armazenamento Premium são duráveis para essas configurações de cache. Quando a solicitação de gravação for concluída, os dados terão sido gravados no armazenamento persistente. Use os métodos a seguir para desabilitar "barreiras", dependendo de seu sistema de arquivos:
	- Se você usar **reiserFS**, desabilite as barreiras usando a opção de montagem "barrier=none" (para habilitar as barreiras, use "barrier=flush")
	- Se você usar **ext3/ext4**, desabilite as barreiras usando a opção de montagem “barrier=0” (para habilitar as barreiras, use “barrier=1”)
	- Se você usar **XFS**, desabilite as barreiras usando a opção de montagem “nobarrier” (para habilitar as barreiras, use a opção “barrier”)

- Para discos de Armazenamento Premium com a configuração de cache "ReadWrite", as barreiras devem ser habilitadas para durabilidade de gravações.
- Para que os rótulos de volume persistam após a reinicialização da VM, atualize o /etc/fstab com as referências de UUID para os discos. Além disso, confira também [Como anexar um disco de dados a uma máquina virtual Linux](../virtual-machines/virtual-machines-linux-classic-attach-disk.md)

A seguir estão as distribuições do Linux que são validadas com o Armazenamento Premium. Recomendamos a atualização de suas VMs para pelo menos uma dessas versões (ou posterior) para obter melhor desempenho e estabilidade com o Armazenamento Premium. Além disso, algumas das versões exigem um LIS (Serviços de Integração do Linux v4.0 para Microsoft Azure) mais recente. Siga o link fornecido abaixo para download e instalação. Continuaremos a adicionar mais imagens à lista à medida que concluirmos validações adicionais. Perceba que nossas validações mostraram que o desempenho varia para essas imagens, e também depende de características da carga de trabalho e das configurações nas imagens. Imagens diferentes são ajustadas para tipos diferentes de carga de trabalho.
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Distribuição</strong></td>
	<td><strong>Versão</strong></td>
	<td><strong>Kernel com suporte</strong></td>
	<td><strong>Imagem com suporte</strong></td>
</tr>
<tr>
	<td rowspan="4"><strong>Ubuntu</strong></td>
	<td>12.04</td>
	<td>3.2.0-75.110</td>
	<td>Ubuntu-12_04_5-LTS-amd64-server-20150119-pt-BR-30GB</td>
</tr>
<tr>
	<td>14.04</td>
	<td>3.13.0-44.73</td>
	<td>Ubuntu-14_04_1-LTS-amd64-server-20150123-pt-BR-30GB</td>
</tr>
<tr>
	<td>14.10</td>
	<td>3.16.0-29.39</td>
	<td>Ubuntu-14_10-amd64-server-20150202-pt-BR-30GB</td>
</tr>
<tr>
	<td>15.04</td>
	<td>3.19.0-15</td>
	<td>Ubuntu-15_04-amd64-server-20150422-pt-BR-30GB</td>
</tr>
<tr>
	<td><strong>SUSE</strong></td>
	<td>SLES 12</td>
	<td>3.12.36-38.1</td>
	<td>suse-sles-12-priority-v20150213<br>suse-sles-12-v20150213</td>
</tr>
<tr>
	<td><strong>CoreOS</strong></td>
	<td>584.0.0</td>
	<td>3.18.4</td>
	<td>CoreOS 584.0.0</td>
</tr>
<tr>
	<td rowspan="2"><strong>CentOS</strong></td>
	<td>6.5, 6.6, 6.7, 7.0</td>
	<td></td>
	<td>
		<a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 obrigatório</a> </br>
		*Confira a observação abaixo
	</td>
</tr>
<tr>
	<td>7.1</td>
	<td>3.10.0-229.1.2.el7</td>
	<td>
		<a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 recomendado </a> <br/>
		*Confira a observação abaixo
	</td>
</tr>

<tr>
	<td rowspan="2"><strong>Oracle</strong></td>
	<td>6.4</td>
	<td></td>
	<td><a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 obrigatório</a></td>
</tr>
<tr>
	<td>7.0</td>
	<td></td>
	<td>Entre em contato com o suporte para obter detalhes</td>
</tr>
</tbody>
</table>


### Drivers LIS para Openlogic CentOS

Os clientes que executam VMs com OpenLogic CentOS devem executar o comando a seguir para instalar os drivers mais recentes:

	sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
	sudo yum install microsoft-hyper-v

Depois, uma reinicialização será necessária para ativar os novos drivers.

## Preços e cobrança
Ao usar o Armazenamento Premium, as seguintes considerações de cobrança se aplicam:
- Tamanho de disco do Armazenamento Premium
- Instantâneos de Armazenamento Premium
- Transferências de dados de saída

**Tamanho de disco de Armazenamento premium**: a cobrança para um disco de Armazenamento Premium depende do tamanho provisionado do disco. O Azure mapeia o tamanho do disco (arredondado) para a opção mais próxima de Disco de Armazenamento Premium, conforme especificado na tabela fornecida na seção [Escalabilidade e metas de desempenho ao usar o Armazenamento Premium](#scalability-and-performance-targets-whpt-BRing-premium-storage). A cobrança por qualquer disco provisionado é rateada por hora usando o preço mensal para a oferta de Armazenamento Premium. Por exemplo, se você provisionou um disco P10 e ele foi excluído após 20 horas, você será cobrado pela a oferta P10 rateada em 20 horas. Isto é independente da quantidade de dados reais gravados no disco ou do IOPS/taxa de transferência usados.

**Instantâneos de Armazenamento premium**: instantâneos no Armazenamento Premium são cobrados pela capacidade adicional usada pelos instantâneos. Para saber mais sobre instantâneos, consulte [Criando um instantâneo de um Blob](http://msdn.microsoft.com/library/azure/hh488361.aspx).

**Transferências de dados de saída**: [transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/) (dados saindo dos datacenters do Azure) acarretam a cobrança por uso de largura de banda.

Para obter informações detalhadas sobre os preços para Armazenamento Premium e VMs das séries DS, DSv2 e GS, confira:

- [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/)
- [Preços de Máquinas Virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/)

## Início rápido

## Criar e usar uma conta de Armazenamento Premium para um disco de dados da máquina virtual

Nesta seção, demonstraremos os cenários a seguir usando o Portal do Azure, o Azure PowerShell e a CLI do Azure:

- Como criar uma conta de Armazenamento Premium
- Como criar uma máquina virtual e anexar um disco de dados a ela ao usar o Armazenamento Premium.
- Como alterar a política de cache de um disco de dados anexado a uma máquina virtual.

### Criar uma máquina virtual do Azure usando o Armazenamento Premium pelo Portal do Azure

#### I. Criar uma conta de Armazenamento Premium no Portal do Azure

Esta seção mostra como criar uma conta de Armazenamento Premium usando o Portal do Azure.

1.	Entre no [Portal do Azure](https://portal.azure.com). Confira a oferta [Avaliação Gratuita](https://azure.microsoft.com/pricing/free-trial/) caso você ainda não tenha uma assinatura.

2.	No menu Ajuda, clique em **Novo**.

3.	Em **Novo**. Selecione **Dados + Armazenamento**. Lá, clique em **Conta de armazenamento**. Escolha o modelo de implantação. Use o Gerenciador de Recursos para novas implantações. Clique em **Criar**.

4.	Na folha da Conta de armazenamento, digite um nome para sua conta de armazenamento. Selecione o local desejado para a implantação. Confirme se o Armazenamento Premium está disponível no local selecionado conferindo os [Serviços do Azure por região](https://azure.microsoft.com/regions/#services).

5.	Clique em **Tipo**. Na folha **Escolha o tipo de conta de armazenamento**, escolha **Premium Localmente Redundante**. Clique em **Selecionar**. Depois que você clicar em **Selecionar**, o **Tipo** será exibido como **Premium-LRS**.

6.	Na folha **Conta de Armazenamento**, crie um novo **Grupo de recursos** ou selecione um grupo de recursos existente, se já tiver um. Clique em Criar.

	![Camada de Preços][Image1]

#### II. Criar uma máquina virtual do Azure por meio do Portal do Azure

Você deve criar uma VM das séries DS, DSv2 ou GS para poder usar o Armazenamento Premium. Siga as etapas em [Criar uma máquina virtual do Windows no Portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para criar uma nova máquina virtual DS, DSv2 ou GS.

#### III. Anexar um disco de dados de armazenamento premium por meio do Portal do Azure

1. Localize a VM DS, DSv2 ou GS nova ou existente no Portal do Azure.
2. Em **Todas as Configurações** da VM, vá para **Discos** e clique em **Anexar Novo**.
3. Insira o nome do disco de dados e selecione o **Tipo** como **Premium**. Selecione a configuração desejada de **Tamanho** e **Cache de host**.

	![Disco Premium][Image2]

Veja etapas mais detalhadas em [Como anexar um disco de dados no Portal do Azure](../virtual-machines/virtual-machines-windows-attach-disk-portal.md).

#### IV. Alterar a política de cache de disco por meio do Portal do Azure

1. Localize a VM DS, DSv2 ou GS nova ou existente no Portal do Azure.
2. Em Todas as Configurações da VM, vá para Discos e clique no disco que você deseja alterar.
3. Alterar a opção de cache de host para o valor desejado, None, ReadOnly ou ReadWrite

### Criar uma máquina virtual do Azure usando o Armazenamento Premium por meio do PowerShell do Azure

#### I. Criar uma conta de Armazenamento Premium no Portal do Azure PowerShell

Este exemplo de PowerShell mostra como criar uma nova conta de Armazenamento Premium e conectar um disco de dados que usa essa conta a uma nova máquina virtual do Azure.

1. Configure o ambiente do PowerShell seguindo as etapas fornecidas em [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).
2. Inicie o console do PowerShell, conecte-se a sua assinatura e execute o seguinte cmdlet do PowerShell na janela do console. Como visto nesta instrução do PowerShell, você precisa especificar o parâmetro **Type** como **Premium\_LRS** ao criar uma conta de armazenamento premium.

		New-AzureStorageAccount -StorageAccountName "yourpremiumaccount" -Location "West US" -Type "Premium_LRS"

#### II. Criar uma máquina virtual do Azure por meio do Azure PowerShell

Em seguida, crie uma nova VM da série DS e especifique que deseja o Armazenamento Premium, executando os cmdlets do PowerShell a seguir na janela do console. Você pode criar uma VM da série GS usando as mesmas etapas. Especifique o tamanho da VM apropriado nos comandos. Por exemplo, Standard\_GS2:

    	$storageAccount = "yourpremiumaccount"
    	$adminName = "youradmin"
    	$adminPassword = "yourpassword"
    	$vmName ="yourVM"
    	$location = "West US"
    	$imageName = "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201409.01-en.us-127GB.vhd"
    	$vmSize ="Standard_DS2"
    	$OSDiskPath = "https://" + $storageAccount + ".blob.core.windows.net/vhds/" + $vmName + "_OS_PIO.vhd"
    	$vm = New-AzureVMConfig -Name $vmName -ImageName $imageName -InstanceSize $vmSize -MediaLocation $OSDiskPath
    	Add-AzureProvisioningConfig -Windows -VM $vm -AdminUsername $adminName -Password $adminPassword
    	New-AzureVM -ServiceName $vmName -VMs $VM -Location $location

#### III. Anexar um disco de dados de armazenamento premium por meio do Azure PowerShell

Se você quiser mais espaço em disco para sua VM, anexe um novo disco de dados em uma VM das séries DS, DSv2 ou GS existente depois que ela for criada, executando os seguintes cmdlets do PowerShell na janela do console:

    	$storageAccount = "yourpremiumaccount"
    	$vmName ="yourVM"
    	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
    	$LunNo = 1
    	$path = "http://" + $storageAccount + ".blob.core.windows.net/vhds/" + "myDataDisk_" + $LunNo + "_PIO.vhd"
    	$label = "Disk " + $LunNo
    	Add-AzureDataDisk -CreateNew -MediaLocation $path -DiskSizeInGB 128 -DiskLabel $label -LUN $LunNo -HostCaching ReadOnly -VM $vm | Update-AzureVm

#### IV. Alterar a política de cache de disco por meio do Azure PowerShell

Para atualizar a política de cache de disco, anote o número de LUN do disco de dados anexado. Execute o comando a seguir para atualizar o disco de dados anexado na LUN número 2 para ReadOnly.

		Get-AzureVM "myservice" -name "MyVM" | Set-AzureDataDisk -LUN 2 -HostCaching ReadOnly | Update-AzureVM

### Criar uma máquina virtual do Azure usando o Armazenamento Premium por meio da Interface de Linha de Comando do Azure

A CLI ([Interface de Linha de Comando)](../xplat-cli-install.md) do Azure fornece um conjunto de comandos de software livre entre plataformas para o trabalho com a Plataforma Azure. Os exemplos a seguir mostram como usar o Azure CLI (versão 0.8.14 e posterior) para criar uma conta de armazenamento premium, uma nova máquina virtual e conectar um novo disco de dados de uma conta de Armazenamento Premium.

#### I. Criar uma conta de armazenamento premium por meio da CLI do Azure

````
azure storage account create "premiumtestaccount" -l "west us" --type PLRS
````

#### II. Criar uma máquina virtual da série DS por meio da CLI do Azure

	azure vm create -z "Standard_DS2" -l "west us" -e 22 "premium-test-vm"
		"b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-pt-BR-30GB" -u "myusername" -p "passwd@123"

Exibir informações sobre a máquina virtual

	azure vm show premium-test-vm

#### III. Anexar um novo disco de dados premium por meio da CLI do Azure

	azure vm disk attach-new premium-test-vm 20 https://premiumstorageaccount.blob.core.windows.net/vhd-store/data1.vhd

Exibir informações sobre o novo disco de dados

	azure vm disk show premium-test-vm-premium-test-vm-0-201502210429470316

#### IV. Alterar a política de cache de disco

Para alterar a política de cache em um dos seus discos usando o Azure CLI, execute o seguinte comando:

		$ azure vm disk attach -h ReadOnly <VM-Name> <Disk-Name>

Observe que as opções da política de cache podem ser ReadOnly, None ou ReadWrite. Para saber mais, consulte a ajuda executando o seguinte comando:

		azure vm disk attach --help

## Perguntas frequentes

1. **Posso anexar discos de dados premium e standard a uma VM das séries DS, DSv2 ou GS?**

	Sim. Você pode anexar discos de dados premium e standard a uma VM das séries DS, DSv2 ou GS.

2. **Posso anexar discos de dados premium e standard a uma VM das séries D, Dv2 ou G?**

	Não. Você só pode anexar um disco de dados standard a todas as VMs que não são das séries DS, DSv2 ou GS.

3. **Se criar um disco de dados premium com base em um VHD existente que tinha 80 GB de tamanho, quanto isso custará?**

	Um disco de dados premium criado com base no VHD de 80 GB será tratado como o próximo tamanho de disco premium disponível, um disco P10. Você será cobrado de acordo com os preços do disco P10.

4. **Existem custos de transação ao se usar o Armazenamento Premium?**

	Há um custo fixo para cada tamanho de disco que vem provisionado com determinado número de IOPS e Taxa de Transferência. Os únicos outros custos são largura de banda de saída e recurso de instantâneos, caso aplicável. Confira [Preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) para obter mais detalhes.

5. **Onde posso armazenar diagnósticos de inicialização da minha VM das séries DS, DSv2 ou GS?**

	Crie uma conta de armazenamento standard para armazenar os diagnósticos de inicialização de sua VM das séries DS, DSv2 ou GS.

6. **Que quantidade de IOPS e Taxa de Transferência posso obter do cache de disco?**

	Os limites combinados para cache e SSD local para um item da série DS são 4000 IOPS por núcleo e 33 MB por segundo por núcleo. A série GS oferece 5000 IOPS por núcleo e 50 MB por segundo por núcleo.

7. **O que é o SSD local em uma VM das séries DS, DSv2 ou GS?**

	O SSD local é um armazenamento temporário fornecido com uma VM das séries DS, DSv2 ou GS. Não há custo adicional para esse armazenamento temporário. É recomendável que você não use esse armazenamento temporário ou SSD local para armazenar os dados do aplicativo, pois eles não são persistidos no Armazenamento de Blobs do Azure.

8. **Posso converter minha conta de armazenamento padrão em uma conta de armazenamento premium?**

	Não. Não é possível converter uma conta de armazenamento padrão em uma conta de armazenamento premium ou vice-versa. Você deve criar uma nova conta de armazenamento com o tipo desejado e copiar dados para a nova conta de armazenamento, caso aplicável.

9. **Como converter minha VM da série D em uma VM da série DS**

	Confira o guia de migração, [Como migrar para o Armazenamento Premium do Azure](storage-migration-to-premium-storage.md), para mover a carga de trabalho de uma VM da série D usando uma conta de armazenamento padrão para uma VM da série DS usando uma conta de armazenamento premium.

## Próximas etapas

Para obter mais informações sobre o Armazenamento Premium do Azure, confira os artigos a seguir.

### Projetar e implementar com o Armazenamento Premium do Azure

- [Design para desempenho com o Armazenamento Premium](storage-premium-storage-performance.md)
- [Usando operações do serviço Blob com o Armazenamento Premium do Azure](http://go.microsoft.com/fwlink/?LinkId=521969)

### Diretrizes operacionais

- [Migrando para o Armazenamento do Azure Premium](storage-migration-to-premium-storage.md)


### Postagens de blog

- [Armazenamento Premium do Azure com disponibilidade geral](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)
- [Anúncio da série GS: como adicionar suporte ao Armazenamento Premium para as maiores VMs na nuvem pública](https://azure.microsoft.com/blog/azure-has-the-most-powerful-vms-in-the-public-cloud/)


[Image1]: ./media/storage-premium-storage/Azure_pricing_tier.png
[Image2]: ./media/storage-premium-storage/Azure_attach_premium_disk.png

<!-----------HONumber=AcomDC_0330_2016-->