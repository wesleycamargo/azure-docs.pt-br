<properties
	pageTitle="Armazenamento Premium: Armazenamento de Alto Desempenho para as Cargas de Trabalho da Máquina Virtual do Azure"
	description="O Armazenamento Premium dá suporte ao disco de alto desempenho e baixa latência para cargas de trabalho que usam muita E/S em execução em máquinas virtuais do Azure. VMs série DS e GS do Azure dão suporte ao Armazenamento Premium."
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
	ms.date="11/04/2015"
	ms.author="tamram;selcint"/>


# Armazenamento Premium: armazenamento de alto desempenho para as cargas de trabalho da máquina virtual do Azure

## Visão geral

O Armazenamento Premium do Azure dá suporte de disco de alto desempenho e baixa latência para máquinas virtuais executando cargas de trabalho intensivas para entradas e saídas. Discos de VM (máquina virtual) que usam o armazenamento Premium armazenam dados em SSDs (unidades de estado sólido). Você pode migrar os discos de VM do seu aplicativo para o Armazenamento Premium do Azure para aproveitar a velocidade e o desempenho desses discos.

Uma VM do Azure dá suporte à anexação vários discos de Armazenamento Premium, para que seus aplicativos possam ter até 64 TB de armazenamento por VM. Com o Armazenamento Premium, seus aplicativos podem atingir 80.000 IOPS (operações de entrada/saída por segundo) por VM e taxa de transferência de disco de 2.000 MB por segundo por VM com latências extremamente baixas para operações de leitura.

>[AZURE.NOTE]É recomendável migrar qualquer disco de máquina virtual que exija IOPS alta para o Armazenamento Premium do Azure para obter o melhor desempenho para o seu aplicativo. Se o disco não requer IOPS alta, você pode limitar os custos mantendo-a no armazenamento padrão, que armazena dados de disco da máquina virtual em HDDs (unidades de disco rígido) em vez de SSDs.

Para começar com o Armazenamento Premium do Azure, visite a página [Introdução gratuita](http://azure.microsoft.com/pricing/free-trial/). Para obter informações sobre como migrar as máquinas virtuais existentes para o Armazenamento Premium, consulte [Migrando para o Armazenamento Premium do Azure](storage-migration-to-premium-storage.md).

## Coisas importantes a saber sobre o Armazenamento Premium

A seguir está uma lista de pontos importantes a considerar antes ou quando usar o Armazenamento Premium:

- Para usar o Armazenamento Premium, você precisa ter uma conta de armazenamento Premium. Para saber como criar uma conta de Armazenamento Premium, consulte [Criação e uso da conta de Armazenamento Premium para Discos](#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk).

- Atualmente, o Armazenamento Premium está disponível no [Portal de Visualização do Microsoft Azure](https://portal.azure.com/) e pode ser acessado por meio das bibliotecas SDK: [API REST de Armazenamento](http://msdn.microsoft.com//library/azure/dd179355.aspx) versão 2014-02-14 ou posterior; [API REST do Serviço de Gerenciamento](http://msdn.microsoft.com/library/azure/ee460799.aspx) versão 2014-10-01 ou posterior e [Azure PowerShell](../install-configure-powershell.md) versão 0.8.10 ou posterior.

- Para obter uma lista de regiões que atualmente dão suporte ao Armazenamento Premium, consulte [Serviços do Azure por região](http://azure.microsoft.com/regions/#services).

- O Armazenamento Premium oferece suporte a página de blobs somente do Azure, que são usados para manter discos persistentes para máquinas virtuais do Azure (VM). Para saber mais sobre os blobs da página do Azure, consulte [Compreendendo os blobs de blocos e os blobs de página](http://msdn.microsoft.com/library/azure/ee691964.aspx). O Armazenamento Premium não oferece suporte aos Blocos de Blobs do Azure, arquivos do Azure, tabelas do Azure ou filas do Azure.

- Uma conta de Armazenamento Premium é localmente redundante (LRS) e mantém três cópias dos dados dentro de uma única região. Para obter considerações sobre replicação geográfica ao usar o Armazenamento Premium, consulte a seção [Instantâneos e blob de cópia ao usar o Armazenamento Premium](#snapshots-and-copy-blob-whpt-BRing-premium-storage) neste artigo.

- Se quiser usar uma conta de Armazenamento Premium para os discos da VM, você precisa usar a série DS ou série GS das VMs. Você pode usar discos de armazenamento Standard e Premium com a série DS ou série GS das VMs. Mas você não pode usar discos de Armazenamento Premium com VMs que não pertençam à série DS ou à série GS. Para saber mais sobre os tamanhos e tipos de discos de VM do Azure disponíveis, consulte [Tamanhos de máquinas virtuais e de serviços em nuvem do Azure](../virtual-machines/virtual-machines-size-specs.md).

- O processo para configuração de discos de Armazenamento Premium para uma VM é semelhante para os discos de armazenamento padrão. Você precisa escolher a opção de Armazenamento Premium mais apropriada para os discos do Azure e a VM. O tamanho da VM deve ser adequado a sua carga de trabalho com base nas características de desempenho das ofertas Premium. Para saber mais, consulte [Escalabilidade e metas de desempenho ao usar o Armazenamento Premium](#scalability-and-performance-targets-whpt-BRing-premium-storage)

- Uma conta de Armazenamento Premium não pode ser mapeada para um nome de domínio personalizado.

- Não há suporte atualmente para a análise de armazenamento para Armazenamento Premium. Para analisar as métricas de desempenho de VMs usando discos em contas de Armazenamento Premium, use as ferramentas baseados no sistema operacional, por exemplo, [Monitor de Desempenho do Windows](https://technet.microsoft.com/library/cc749249.aspx) para VMs do Windows e [IOSTAT](http://linux.die.net/man/1/iostat) para VMs do Linux. Você também pode habilitar o diagnóstico de VM do Azure no Portal de visualização do Azure. Consulte o [Monitoramento de máquina virtual do Microsoft Azure com extensão de diagnóstico do Azure](http://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/) para obter detalhes.

## Usando o Armazenamento Premium para discos
Você pode usar o Armazenamento Premium para discos de uma das seguintes maneiras:

- Primeiro, criar uma nova conta de Armazenamento Premium e então usá-la ao criar a VM.
- Criar uma nova série DS ou série GS de VM. Ao criar a VM, selecione uma conta de Armazenamento Premium criada anteriormente, crie uma nova ou deixe o Portal criar uma conta premium padrão.

O Azure usa a conta de armazenamento como um contêiner para seu sistema operacional (SO) e discos de dados. Em outras palavras, se você cria uma VM do Azure da série DS ou série GS e seleciona uma conta de Armazenamento Premium do Azure, o sistema operacional e os discos de dados são armazenados nessa conta de armazenamento.

Para obter informações sobre como migrar as máquinas virtuais existentes para o Armazenamento Premium, consulte [Migrando para o Armazenamento Premium do Azure](storage-migration-to-premium-storage.md).

Para aproveitar os benefícios do Armazenamento Premium, crie uma conta de Armazenamento Premium usando primeiro um tipo de conta *Premium\_LRS*. Para fazer isso, você pode usar o [Portal de Visualização do Microsoft Azure](https://portal.azure.com/), o [PowerShell do Azure](../install-configure-powershell.md) ou a [ API REST do Serviço de Gerenciamento](http://msdn.microsoft.com/library/azure/ee460799.aspx). Para obter instruções detalhadas, consulte [Criação e uso da conta do Armazenamento Premium para discos](#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk).

### Observações importantes:

- Para obter detalhes sobre a capacidade e os limites de largura de banda para contas de Armazenamento Premium, consulte [Escalabilidade e metas de desempenho ao usar o Armazenamento Premium](#scalability-and-performance-targets-whpt-BRing-premium-storage). Se o seu aplicativo precisa exceder as metas de escalabilidade de uma única conta de armazenamento, crie seu aplicativo para usar múltiplas contas de armazenamento e faça o particionamento dos seus dados através destas contas de armazenamento. Por exemplo, se você quiser anexar discos de 51 terabytes TB em várias VMs, separe-as entre duas contas de armazenamento, já que o limite para uma única conta de armazenamento Premium é de 35 TB. Certifique-se de que uma única conta de Armazenamento Premium nunca tenha mais de 35 TB de discos provisionados.
- Por padrão, a política de cache de disco é "Somente leitura" para todos os discos de dados Premium e “Leitura e gravação" para o disco de sistema operacional Premium anexado à VM. Esta definição de configuração é recomendável para atingir o desempenho ideal de leitura para entrada e saída dos seus aplicativos. Para discos de dados de gravação intensa ou somente gravação (como arquivos de log do SQL Server), desabilite o cache de disco para que possa obter o melhor desempenho do aplicativo.
- Certifique-se de que há largura de banda suficiente disponível na sua VM para direcionar o tráfego de disco. Por exemplo, uma VM STANDARD\_DS1 tem uma banda larga dedicada de 32 MB por segundo disponível para o tráfego de discos do Armazenamento Premium. Ou seja, um disco de P10 do Armazenamento Premium conectado a essa VM pode chegar apenas atá 32 MB por segundo, mas não até os 100 MB por segundo que o disco P10 pode fornecer. Da mesma forma, uma VM STANDARD\_DS13 pode chegar a 256 MB por segundo em todos os discos. Atualmente, a maior VM na série DS é a STANDARD\_DS14, que pode fornecer até 512 MB por segundo em todos os discos. A maior VM na série GS é STANDARD\_GS5 e pode fornecer até 2.000 MB por segundo em todos os discos.

	Observe que esses limites são apenas para o tráfego de disco, não incluindo acertos de cache e tráfego de rede. Há uma largura de banda separada disponível para tráfego de rede de VM, que é diferente da largura de banda dedicada aos discos do Armazenamento Premium.
	
	Para ver informações mais atualizadas sobre o máximo de taxa de transferência (largura de banda) e IOPS para VMs da série DS e série GS, consulte [Tamanhos de serviço de nuvem e máquina virtual para Azure](../virtual-machines/virtual-machines-size-specs.md). Para saber mais sobre os discos de armazenamento Premium e seus limites de IOPs e taxa de transferência, consulte a tabela na seção [Metas de desempenho e escalabilidade ao usar o Armazenamento Premium](#scalability-and-performance-targets-whpt-BRing-premium-storage) deste artigo.

> [AZURE.NOTE]Os acertos de cache não são limitados pelo IOPS/Taxa de transferência alocada do disco. Ou seja, quando você usa um disco de dados com a configuração de cache ReadOnly em uma VM da série DS ou da série GS, as Leituras realizadas a partir do cache não estão sujeitas aos limites de disco do Armazenamento Premium. Assim, você pode obter uma taxa de transferência muito alta de um disco se a carga de trabalho for composta predominantemente de Leituras. Observe que o cache está sujeito a limites separados de IOPS/Taxa de transferência no nível da VM, com base no tamanho da VM. As VMs da Série DS têm aproximadamente 4000 IOPS e 33 MB/s por núcleo para IOs SSD em cache e local.

- Você pode usar tanto discos de armazenamento Premium quanto Standard na mesma VM da série DS ou da série GS.
- Com o armazenamento Premium, você pode provisionar uma máquina virtual da série DS e anexar vários discos de dados persistentes a uma máquina virtual. Se necessário, pode distribuir entre os discos para aumentar a capacidade e o desempenho do volume. Se você distribuir discos de dados do Armazenamento Premium usando [Espaços de Armazenamento](http://technet.microsoft.com/library/hh831739.aspx), deverá configurá-lo com uma coluna para cada disco usado. Caso contrário, o desempenho geral do volume distribuído pode ser menor que o esperado devido a uma distribuição irregular de tráfego entre os discos. Por padrão, a interface do usuário do Gerenciador de servidores (UI) permite que você configure colunas para até 8 discos. Mas se você tiver mais de 8 discos, você precisa usar o PowerShell para criar o volume e também especificar manualmente o número de colunas. Caso contrário, a UI do Gerenciador de servidores continuará a usar 8 colunas, mesmo que haja mais discos. Por exemplo, se você tiver 32 discos em um conjunto único de distribuição, você deve especificar 32 colunas. Você pode usar o parâmetro *NumberOfColumns* do cmdlet do PowerShell [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) para especificar o número de colunas usadas pelo disco virtual. Para saber mais, consulte [Visão geral dos espaços de armazenamento](http://technet.microsoft.com/library/jj822938.aspx) e [Perguntas frequentes sobre os espaços de armazenamentos](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).
- Evite adicionar VMs da série DS a um serviço de nuvem existente que inclua VMs que não são da série DS. Uma solução possível é migrar seus VHDs existentes para um novo serviço de nuvem que executa apenas VMs da série DS. Se você quiser manter o mesmo endereço IP virtual (VIP) para o novo serviço de nuvem que hospeda suas VMs da série DS, use o recurso [Endereços IP Reservados](virtual-networks-configure-vnet-to-vnet-connection.md). As VMs da série GS podem ser adicionadas a um serviço de nuvem existente executando apenas VMs da série G.
- As máquinas virtuais do Azure da série DS podem ser configuradas para usar um disco do sistema operacional (SO) hospedado em uma conta de Armazenamento Padrão ou em uma conta de Armazenamento Premium. Se você usar o disco do SO somente para a inicialização, considere o uso de um disco de SO baseado no Armazenamento Padrão. Com isso, você obterá custo-benefício e resultados de desempenho semelhantes aos do Armazenamento Premium após a inicialização. Se você executar qualquer outra tarefa no disco do SO que não seja de inicialização, use o Armazenamento Premium, pois ele fornece os melhores resultados de desempenho. Por exemplo, se o aplicativo ler ou gravar no/do disco do SO, o uso do disco do SO baseado no Armazenamento Premium oferece o melhor desempenho para sua VM.
- Você pode usar a [Interface de linha de comando do Azure (Azure CLI)](../xplat-cli-install.md) com Armazenamento Premium. Para alterar a política de cache em um dos seus discos usando o Azure CLI, execute o seguinte comando:

	`$ azure vm disk attach -h ReadOnly <VM-Name> <Disk-Name>`

	Observe que as opções da política de cache podem ser ReadOnly, None ou ReadWrite. Para saber mais, consulte a ajuda executando o seguinte comando:

	`azure vm disk attach --help`


## Escalabilidade e metas de desempenho ao usar o Armazenamento Premium

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
	<td>100 MB por segundo.*</td>
	<td>150 MB por segundo *</td>
	<td>200 MB por segundo *</td>
</tr>
</tbody>
</table>

> [AZURE.NOTE]Certifique-se de que exista largura de banda suficiente disponível em sua VM para direcionar o tráfego de disco, conforme explicado na seção [Usando o Armazenamento Premium para discos](#using-premium-storage-for-disks) anteriormente neste artigo. Caso contrário, a taxa de transferência e o IOPS do disco ficarão restritos a valores mais baixos com base nos limites da VM, e não nos limites de disco mencionados na tabela anterior.

O Azure mapeia o tamanho do disco (arredondado) para a opção mais próxima do disco de armazenamento Premium conforme especificado na tabela. Por exemplo, um disco com 100 GiB é classificado como uma opção P10 e pode executar até 500 unidades de E/S por segundo, com uma taxa de transferência de até 100 MB por segundo. Da mesma forma, um disco com 400 GiB é classificado como uma opção P20 e pode executar até 2300 unidades de E/S por segundo, com uma taxa de transferência de até 150 MB por segundo.

O tamanho da unidade de entrada/saída (e/s) é 256 KB. Se os dados transferidos forem menores que 256 KB, ele será considerado uma única unidade de e/s. Tamanhos maiores de e/s são contados como várias entradas e saídas de tamanho de 256 KB. Por exemplo, taxa de transferência de e/s de KB 1100 é contada como cinco unidades de e/s.

O limite de taxa de transferência inclui gravações e leituras no disco que não são realizadas a partir do cache. Por exemplo, a soma de leituras e gravações que não são feitas no cache deve ser menor ou igual a 100 MB por segundo para um disco P10. Da mesma forma, um único disco P10 pode ter 100 MB por segundo de leituras que não são realizadas no cache ou 100 MB por segundo de gravações, ou ainda 60 MB por segundo de leituras com 40 MB por segundo de gravações, por exemplo.

Ao criar seus discos no Azure, selecione a oferta de disco de Armazenamento Premium mais apropriada com base nas necessidades do seu aplicativo em termos de capacidade, desempenho, escalabilidade e picos de carga.

> [AZURE.NOTE]Você pode aumentar com facilidade o tamanho dos discos existentes. Por exemplo, se você quiser aumentar o tamanho de um disco de 30 GB para 128GB ou 1 TB. Ou, se você quiser converter seu disco P20 para P30 porque precisa de mais capacidade ou mais IOPS e taxa de transferência. Você pode expandir o disco usando o comando do PowerShell "Update-AzureDisk" com a propriedade "-ResizedSizeInGB". Para executar essa ação, o disco precisa ser desconectado da VM ou a VM precisa ser interrompida.

A tabela a seguir descreve as metas de escalabilidade para contas de armazenamento Premium:

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

Para saber mais, consulte [Metas de desempenho e escalabilidade do Armazenamento do Azure](http://msdn.microsoft.com/library/azure/dn249410.aspx).

## Limitação ao usar o Armazenamento Premium
Talvez você perceba uma limitação se o IOPS ou a taxa de transferência do aplicativo exceder os limites alocados para um disco de Armazenamento Premium, ou se o tráfego total em todos os discos na VM exceder o limite de largura de banda em disco disponível para a VM. Para evitar a limitação, recomendamos a limitação do número de solicitações de E/S pendentes para discos com base nas metas de desempenho e escalabilidade do disco que você provisionou e com base na largura de banda de disco disponível na VM.

Seu aplicativo pode obter a latência menor quando ele é projetado para evitar a limitação. Por outro lado, se o número de solicitações de e/s pendentes para o disco for muito pequeno, seu aplicativo não poderá tirar proveito dos níveis máximos de IOPS e taxa de transferência que estão disponíveis no disco.

Os exemplos a seguir demonstram como calcular os níveis de limitação:

### Exemplo 1:
O aplicativo fez 495 e/s de tamanho de disco de 16 KB (, que é igual a 495 unidades de e/s) em um segundo em um disco P10. Se você tentar uma e/s de 2 MB no mesmo segundo, o total de unidades de e/s é igual a 495 + 8. Isso ocorre porque a e/s de 2 MB resulta em unidades de e/s de 2048 KB/256 KB = 8 quando o tamanho da unidade de e/s é 256 KB. Como a soma de 495 + 8 excede o limite de 500 IOPS para o disco, a limitação ocorre.

### Observação:
Todos os cálculos baseiam-se no tamanho da unidade de e/s de 256 KB.

### Exemplo 2:
O aplicativo fez 400 entradas e saídas de tamanho de disco de 256 KB em um disco P10. A largura de banda total consumida é de (400 * 256) / 1024 = 100 MB/seg. Observe que 100 MB por segundo é o limite de taxa de transferência de um disco P10. Se o seu aplicativo tentar realizar mais e/s nesse segundo, ele é limitado porque excede o limite alocado.

### Exemplo 3:
Você tem uma VM DS4 com dois discos P30 conectados. Cada disco P30 é capaz de uma taxa de transferência de 200 MB por segundo. No entanto, uma VM DS4 tem uma capacidade de largura de banda total do disco de 256 MB por segundo. Portanto, você não pode aplicar a taxa de transferência máxima aos os discos conectados nesta VM DS4 ao mesmo tempo. Para resolver esse problema, você pode sustentar o tráfego de 200 MB por segundo em um disco e 56 MB por segundo em outro disco. Se a soma do tráfego no disco for superior a 256 MB por segundo, o tráfego será limitado.

### Observação:
Se o tráfego de disco consiste principalmente de tamanhos de e/s pequenos, é muito provável que seu aplicativo atingirá o limite de IOPS antes do limite de taxa de transferência. Por outro lado, se o tráfego de disco consiste principalmente de tamanhos de e/s grandes, é muito provável que seu aplicativo atingirá o limite de taxa de transferência em vez do limite de IOPS. Você pode maximizar o IOPS do seu aplicativo e a capacidade da taxa de transferência usando tamanhos de e/s ideais e também limitando o número de solicitações de e/s pendentes para o disco.

## Instantâneos e cópia de Blob ao usar o Armazenamento Premium
Você pode criar um instantâneo para armazenamento Premium da mesma maneira como você cria um instantâneo ao usar o armazenamento padrão. Como o armazenamento Premium é redundante localmente, é recomendável que você crie instantâneos e então copie esses instantâneos para uma conta de armazenamento com redundância geográfica padrão. Para saber mais, consulte [Opções de redundância do Armazenamento do Azure](http://msdn.microsoft.com/library/azure/dn727290.aspx).

Se um disco estiver anexado a uma VM, determinadas operações de API não são permitidas no suporte da página de blob do disco. Por exemplo, você não pode executar uma operação de [Cópia de Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) nesse blob enquanto o disco estiver conectado a uma VM. Em vez disso, primeiro crie um instantâneo desse blob usando o método de API REST do [Instantâneo de Blob ](http://msdn.microsoft.com/library/azure/ee691971.aspx) e depois execute a [Cópia de Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) do instantâneo para copiar o disco anexado. Como alternativa, você pode desanexar o disco e, em seguida, executar as operações necessárias no blob subjacente.

### Observações importantes:

- O número de instantâneos para um único blob é limitado a 100. Um instantâneo pode ser criado a cada 10 minutos no máximo.
- A capacidade máxima para instantâneos por conta de Armazenamento Premium é de 10 TB. Observe que a capacidade de instantâneo se refere apenas à quantidade total de dados em instantâneos, não inclui dados no blob base.
- Para manter cópias com redundância geográfica de seus instantâneos, você pode copiar instantâneos de uma conta de armazenamento premium para uma conta de armazenamento com redundância geográfica padrão usando AzCopy ou Copiar Blob. Para saber mais, consulte [Como usar o AzCopy com o Armazenamento do Microsoft Azure](storage-use-azcopy.md) e [Copiar Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx).
- Para obter informações detalhadas sobre como executar operações REST em blobs de página nas contas de Armazenamento Premium, consulte [Usando operações de Serviço de Blob com o Armazenamento Premium do Azure](http://go.microsoft.com/fwlink/?LinkId=521969) na biblioteca MSDN.

## Usando VMs do Linux com Armazenamento Premium
Consulte instruções importantes abaixo para configurar suas VMs do Linux no Armazenamento Premium:

- Para todos os discos de Armazenamento Premium com configuração de cache como "ReadOnly" ou "None", você deve desabilitar "barreiras" durante a montagem do sistema de arquivos a fim de atingir as metas de escalabilidade para o Armazenamento Premium. Você não precisa de barreiras para esse cenário, pois as gravações em discos de backup do Armazenamento Premium são duráveis para essas configurações de cache. Quando a solicitação de gravação for concluída, os dados terão sido gravados no armazenamento persistente. Use os métodos a seguir para desabilitar "barreiras", dependendo de seu sistema de arquivos:
	- Se você usar **reiserFS**, desabilite as barreiras usando a opção de montagem "barrier=none" (para habilitar as barreiras, use "barrier=flush")
	- Se você usar **ext3/ext4**, desabilite as barreiras usando a opção de montagem “barrier=0” (para habilitar as barreiras, use “barrier=1”)
	- Se você usar **XFS**, desabilite as barreiras usando a opção de montagem “nobarrier” (para habilitar as barreiras, use a opção “barrier”)

- Para discos de Armazenamento Premium com a configuração de cache "ReadWrite", as barreiras devem ser habilitadas para durabilidade de gravações.
- Para que os rótulos de volume persistam após a reinicialização da VM, atualize o /etc/fstab com as referências de UUID para os discos. Além disso, consulte também [Como anexar um disco de dados a uma máquina virtual Linux.](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-attach-disk)

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
	<td>Ubuntu-12\_04\_5-LTS-amd64-server-20150119-pt-BR-30GB</td>
</tr>
<tr>
	<td>14.04</td>
	<td>3.13.0-44.73</td>
	<td>Ubuntu-14\_04\_1-LTS-amd64-server-20150123-pt-BR-30GB</td>
</tr>
<tr>
	<td>14.10</td>
	<td>3.16.0-29.39</td>
	<td>Ubuntu-14\_10-amd64-server-20150202-pt-BR-30GB</td>
</tr>
<tr>
	<td>15.04</td>
	<td>3.19.0-15</td>
	<td>Ubuntu-15\_04-amd64-server-20150422-pt-BR-30GB</td>
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
		<a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 obrigatório </a></br> *Consulte a nota abaixo
	</td>
</tr>
<tr>
	<td>7.1</td>
	<td>3.10.0-229.1.2.el7</td>
	<td>
		<a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 recomendado </a> <br/>
		*Consulte a nota abaixo
	</td>
</tr>

<tr>
	<td rowspan="2"><strong>Oracle</strong></td>
	<td>6.4</td>
	<td></td>
	<td><a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 obrigatório </a></td>
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



## Preços e cobrança ao usar o Armazenamento Premium
Ao usar o Armazenamento Premium, as seguintes considerações de cobrança se aplicam:

- A cobrança para um disco de Armazenamento Premium depende do tamanho do disco provisionado. O Azure mapeia o tamanho do disco (arredondado) para a opção mais próxima de Disco de Armazenamento Premium, conforme especificado na tabela fornecida na seção [Escalabilidade e metas de desempenho ao usar o Armazenamento Premium](#scalability-and-performance-targets-whpt-BRing-premium-storage). A cobrança por qualquer disco provisionado é rateada por hora usando o preço mensal para a oferta de Armazenamento Premium. Por exemplo, se você provisionou um disco P10 e ele foi excluído após 20 horas, você será cobrado pela a oferta P10 rateada em 20 horas. Isto é independente da quantidade de dados reais gravados no disco ou do IOPS/taxa de transferência usados.
- Instantâneos no Armazenamento Premium serão cobrados pela capacidade adicional usada pelos instantâneos. Para saber mais sobre instantâneos, consulte [Criando um instantâneo de um Blob](http://msdn.microsoft.com/library/azure/hh488361.aspx).
- [Transferências de dados de saída](http://azure.microsoft.com/pricing/details/data-transfers/) (dados saindo dos data centers do Azure) acarretam a cobrança por uso de largura de banda.

Para obter informações detalhadas sobre os preços para Armazenamento Premium, VMs da série DS e VMs da série GS, consulte:

- [Preços do Armazenamento do Azure](http://azure.microsoft.com/pricing/details/storage/)
- [Preços de Máquinas Virtuais](http://azure.microsoft.com/pricing/details/virtual-machines/)

## Criar e usar uma conta de Armazenamento Premium para um disco de dados da máquina virtual

Esta seção demonstra como criar uma conta de Armazenamento Premium usando o Portal de Visualização do Azure , o PowerShell do Azure e a Interface de Linha de Comando do Azure (Azure CLI). Além disso, ela demonstra um exemplo de caso de uso para contas de armazenamento Premium: criação de uma máquina virtual e anexação de um disco de dados a uma máquina virtual usando o armazenamento Premium.

### Criar uma máquina virtual do Azure usando o Armazenamento Premium pelo Portal de Visualização do Azure

Esta seção mostra como criar uma conta de Armazenamento Premium usando o Portal de Visualização do Azure.

1.	Entre no [Portal de Visualização do Azure](https://portal.azure.com/). Confira a oferta [Avaliação Gratuita](http://azure.microsoft.com/pricing/free-trial/) caso você ainda não tenha uma assinatura.


    > [AZURE.NOTE]Se você efetuar logon no Portal de gerenciamento do Azure, clique em seu nome de conta de usuário no canto superior direito do portal. Em seguida, clique em **Alternar para o novo portal**.


2.	No menu Ajuda, clique em **Novo**.

3.	Em **Novo**, clique em **Tudo**. Selecione **Armazenamento, cache + backup**. A partir daí, clique em **Armazenamento** e clique em **Criar**.

4.	Na folha da Conta de armazenamento, digite um nome para sua conta de armazenamento. Clique em **Camada de Preços**. Na folha **Camadas de preços recomendadas**, clique em **Procurar Todas as Camadas de Preço**. Na folha **Escolha sua camada de preço**, escolha **Premium Localmente Redundante**. Clique em **Selecionar**. Observe que a folha**Conta de armazenamento** mostra **Padrão-GRS** como a **Camada de Preços** por padrão. Depois de clicar em **Selecionar**, a **Camada de Preços** é exibida como **Premium-LRS**.

	![Camada de Preços][Image1]


5.	Na folha **Conta de Armazenamento**, mante os valores padrão para **Grupo de Recursos**, **Assinatura**, **Local** e **Diagnósticos**. Clique em **Criar**.

Para obter uma explicação passo a passo completa em um ambiente do Azure, consulte [Criar de uma máquina virtual executando o Windows no Portal de Visualização do Azure](../virtual-machines-windows-tutorial-azure-preview.md).

### Criar uma máquina virtual do Azure usando o Armazenamento Premium por meio do PowerShell do Azure
Este exemplo de PowerShell mostra como criar uma nova conta de Armazenamento Premium e conectar um disco de dados que usa essa conta a uma nova máquina virtual do Azure.

1. Configure o ambiente do PowerShell seguindo as etapas fornecidas em [Como instalar e configurar o PowerShell do Azure](../install-configure-powershell.md).
2. Inicie o console do PowerShell, conecte-se a sua assinatura e execute o seguinte cmdlet do PowerShell na janela do console. Como visto nesta instrução do PowerShell, você precisa especificar o parâmetro **Type** como **Premium\_LRS** ao criar uma conta de armazenamento premium.

		New-AzureStorageAccount -StorageAccountName "yourpremiumaccount" -Location "West US" -Type "Premium_LRS"

3. Em seguida, crie uma nova VM da série DS e especifique que deseja o Armazenamento Premium, executando os cmdlets do PowerShell a seguir na janela do console. Você pode criar uma VM da série GS usando as mesmas etapas. Especifique o tamanho da VM apropriado nos comandos. Por exemplo, Standard\_GS2:

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

4. Se você quiser mais espaço em disco para sua VM, anexe um novo disco de dados em uma VM da série DS ou VM da série GS existente depois de criada, executando os seguintes cmdlets do PowerShell na janela do console:

    	$storageAccount = "yourpremiumaccount"
    	$vmName ="yourVM"
    	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
    	$LunNo = 1
    	$path = "http://" + $storageAccount + ".blob.core.windows.net/vhds/" + "myDataDisk_" + $LunNo + "_PIO.vhd"
    	$label = "Disk " + $LunNo
    	Add-AzureDataDisk -CreateNew -MediaLocation $path -DiskSizeInGB 128 -DiskLabel $label -LUN $LunNo -HostCaching ReadOnly -VM $vm | Update-AzureVm

### Criar uma máquina virtual do Azure usando o Armazenamento Premium por meio da Interface de Linha de Comando do Azure

A [Interface de Linha de Comando do Azure](../xplat-cli-install.md) (Azure CLI) fornece um conjunto de comandos entre plataformas, de software livre, para o trabalho com a plataforma do Azure. Os exemplos a seguir mostram como usar o Azure CLI (versão 0.8.14 e posterior) para criar uma conta de armazenamento premium, uma nova máquina virtual e conectar um novo disco de dados de uma conta de Armazenamento Premium.

#### Criar uma conta de Armazenamento Premium

````
azure storage account create "premiumtestaccount" -l "west us" --type PLRS
````

#### Criar uma máquina virtual da série DS

	azure vm create -z "Standard_DS2" -l "west us" -e 22 "premium-test-vm"
		"b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-pt-BR-30GB" -u "myusername" -p "passwd@123"

#### Exibir informações sobre a máquina virtual

	azure vm show premium-test-vm

#### Conectar um disco de dados

	azure vm disk attach-new premium-test-vm 20 https://premiumstorageaccount.blob.core.windows.net/vhd-store/data1.vhd

#### Exibir informações sobre o novo disco de dados

	azure vm disk show premium-test-vm-premium-test-vm-0-201502210429470316

## Próximas etapas

- [Usando operações do serviço Blob com o Armazenamento Premium do Azure](http://go.microsoft.com/fwlink/?LinkId=521969)
- [Migrando para o Armazenamento Premium do Azure](storage-migration-to-premium-storage.md).
- [Criar uma máquina virtual executando o Windows](../virtual-machines-windows-tutorial-azure-preview.md)
- [Tamanhos de máquinas virtuais e serviços de nuvem do Azure](../virtual-machines/virtual-machines-size-specs.md)
- [Documentação de armazenamento](http://azure.microsoft.com/documentation/services/storage/)
- [Referência do MSDN](http://msdn.microsoft.com/library/azure/gg433040.aspx)

[Image1]: ./media/storage-premium-storage-preview-portal/Azure_pricing_tier.png
 

<!---HONumber=Nov15_HO4-->