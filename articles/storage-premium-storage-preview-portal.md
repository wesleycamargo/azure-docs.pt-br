<properties 
	pageTitle="Armazenamento Premium: Armazenamento de alto desempenho para cargas de trabalho de máquina virtual do Azure" 
	description="Saiba mais sobre o armazenamento Premium do Azure para discos. Saiba como criar uma conta de armazenamento Premium." 
	services="storage" 
	documentationCenter="" 
	authors="Selcin" 
	manager="adinah" 
	editor="tysonn"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2014" 
	ms.author="selcint"/>


# Armazenamento Premium: Armazenamento de alto desempenho para cargas de trabalho de máquina virtual do Azure

Bem-vindo à demonstração pública do **armazenamento de discos Premium do Azure**!

Com a introdução do novo Armazenamento Premium, o Microsoft Azure agora oferece dois tipos de armazenamento durável: **Armazenamento Premium** e **armazenamento padrão**. O Armazenamento Premium armazena dados utilizando a mais recente tecnologia de unidades de estado sólido (SSD), enquanto o armazenamento padrão armazena dados em unidades de disco rígido (HDD). 

O Armazenamento Premium oferece suporte de disco de alto desempenho e baixa latência para cargas de trabalho intensivas para entradas e saídas em execução nas máquinas virtuais do Azure. Você pode anexar vários discos de Armazenamento Premium a uma máquina virtual (VM). Com o armazenamento Premium, seus aplicativos podem ter até 32 TB de armazenamento por VM e obter 50.000 IOPS (operações de entrada/saída por segundo) por VM com muito menos latências por operações de leitura. O Armazenamento Premium está disponível apenas para armazenamento de dados em discos usados por máquinas virtuais do Azure. 

Para inscrever-se para a visualização do Armazenamento Premium do Azure, visite a [página de ](http://azure.microsoft.com/services/preview/)visualização do Azure.

Este artigo fornece uma visão geral detalhada do Armazenamento Premium do Azure.

**Sumário:**


* [Coisas importantes a saber sobre o Armazenamento Premium](#important)

* [Usando o Armazenamento Premium para discos](#using)

* [Escalabilidade e metas de desempenho ao usar o Armazenamento Premium](#scale)	

* [Limitação ao usar o Armazenamento Premium](#throttling)	

* [Instantâneos e cópia de Blob ao usar o Armazenamento Premium](#restapi)	

* [Preços e cobrança ao usar o Armazenamento Premium](#pricing)	

* [Criação e uso da conta de Armazenamento Premium para discos](#howto1)	

* [Recursos adicionais](#see)

###<a id="important">Coisas importantes a saber sobre o Armazenamento Premium</a>


A seguir está uma lista de pontos importantes a considerar antes ou quando usar o Armazenamento Premium:

- Para usar o Armazenamento Premium, você precisa ter uma conta de armazenamento Premium. Para saber como criar uma conta de Armazenamento Premium, confira em [Criação e uso da conta de Armazenamento Premium para discos](#howto1).

- O Armazenamento Premium está disponível no momento no [Portal de visualização do Microsoft Azure](https://portal.azure.com/) e acessível através das seguintes bibliotecas SDK: [API de REST do armazenamento](http://msdn.microsoft.com//library/azure/dd179355.aspx) versão 2014-02-14 ou posterior; [API REST do serviço de gerenciamento](http://msdn.microsoft.com/library/azure/ee460799.aspx) versão 2014-10-01 ou posterior; e [PowerShell do Azure](http://azure.microsoft.com/documentation/articles/install-configure-powershell/) versão 0.8.10 ou posterior. 

- O Armazenamento Premium está disponível para visualização limitada nas seguintes regiões: Oeste dos EUA, Leste dos EUA 2 e Europa Ocidental.

- O Armazenamento Premium oferece suporte a página de blobs somente do Azure, que são usados para manter discos persistentes para máquinas virtuais do Azure (VM). Para obter informações sobre a página de blobs do Azure, confira em [Compreendendo blocos e páginas de Blobs](http://msdn.microsoft.com/library/azure/ee691964.aspx). O Armazenamento Premium não oferece suporte aos Blocos de Blobs do Azure, arquivos do Azure, tabelas do Azure ou filas do Azure.

- Uma conta de Armazenamento Premium é localmente redundante (LRS) e mantém três cópias dos dados dentro de uma única região.  Para obter considerações sobre replicação geográfica ao usar o Armazenamento Premium, confira em a seção [Instantâneos e cópia de Blob ao usar o Armazenamento Premium](#restapi) neste artigo.

- Se você quiser usar uma conta de Armazenamento Premium para os discos VM, você precisa usar a série DS de Máquinas virtuais. Você pode usar discos de armazenamento Standard e Premium com a série DS de VMs. Mas você não pode usar discos de Armazenamento Premium com máquinas virtuais que não pertençam as séries DS. Para obter informações sobre tamanhos e tipos de disco de VM do Azure disponíveis, confira em [Máquina virtual e tamanhos de serviços em nuvem do Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). 

- O processo para configuração de discos de Armazenamento Premium para uma VM é semelhante para os discos de armazenamento padrão. Você precisa escolher a opção de Armazenamento Premium mais apropriada para os discos do Azure e a VM. O tamanho da VM deve ser adequado a sua carga de trabalho com base nas características de desempenho das ofertas Premium. Para obter mais informações, confira em [Escalabilidade e metas de desempenho ao usar o armazenamento Premium](#scale).
 
- Uma conta de Armazenamento Premium não pode ser mapeada para um nome de domínio personalizado.

- Não há suporte atualmente para a análise de armazenamento para Armazenamento Premium.

###<a id="using">Usando o Armazenamento Premium para discos</a>
Você pode usar o Armazenamento Premium para discos de uma das seguintes maneiras:

- Primeiro, criar uma nova conta de Armazenamento Premium e então usá-la ao criar a VM.
- Criar uma nova série DS de Máquinas virtuais. Ao criar a VM, selecione uma conta de Armazenamento Premium criada anteriormente, crie uma nova ou deixe o Portal criar uma conta premium padrão.

O Azure usa a conta de armazenamento como um contêiner para seu sistema operacional e discos de dados. Em outras palavras, se você cria uma VM do Azure da série DS e seleciona uma conta de Armazenamento Premium Azure, o sistema operacional e discos de dados são armazenados na conta de armazenamento.

Para aproveitar os benefícios do armazenamento Premium, criar primeiro uma conta de armazenamento Premium usando um tipo de conta do  *Premium_LRS*. Para fazer isso, você pode usar o [Portal de visualização do Microsoft Azure](https://portal.azure.com/), [PowerShell do Azure](http://azure.microsoft.com/documentation/articles/install-configure-powershell/), ou [API REST do serviço de gerenciamento](http://msdn.microsoft.com/library/azure/ee460799.aspx). Para obter instruções detalhadas, confira em [Criação e uso da conta de Armazenamento Premium para discos](#howto1).

<h4>Observações importantes:</h4>

- Para saber a capacidade e limites de visualização de largura de banda para contas de Armazenamento Premium, confira em [Escalabilidade e metas de desempenho ao usar o Armazenamento Premium](#scale) . Se o seu aplicativo precisa exceder as metas de escalabilidade de uma única conta de armazenamento, crie seu aplicativo para usar múltiplas contas de armazenamento e faça o particionamento dos seus dados através destas contas de armazenamento. Por exemplo, se você deseja anexar discos de 51 TB em uma quantidade de VMs, separe-as entre duas contas de armazenamento, já que o limite para uma única conta de armazenamento Premium é de 32 TB.
- Por padrão, a política de cache de disco é "Somente leitura" para todos os discos de dados Premium e "Leitura e gravação" para o disco de sistema operacional Premium anexado à VM. Esta definição de configuração é recomendável para atingir o desempenho ideal de leitura para entrada e saída dos seus aplicativos. Para discos de dados de gravação intensa ou somente gravação (como arquivos de log do SQL Server), desabilite o cache de disco para que possa obter o melhor desempenho do aplicativo.
- Certifique-se de que há largura de banda suficiente disponível na sua VM para direcionar o tráfego de disco. Para saber a largura de banda de disco disponível para cada tamanho de VM, confira em [Máquina Virtual e tamanhos de serviços em nuvem do Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx).
- Você pode usar tanto discos de armazenamento Premium quanto Standard na mesma VM da série DS.
- Com o armazenamento Premium, você pode provisionar uma máquina virtual da série DS e anexar vários discos de dados persistentes a uma máquina virtual. Se necessário, pode distribuir entre os discos para aumentar a capacidade e o desempenho do volume. Se você distribuir os discos de dados do armazenamento Premium usando [Espaços de armazenamento](http://technet.microsoft.com/library/hh831739.aspx), você deve configurá-lo com uma coluna para cada disco que for usado. Caso contrário, o desempenho geral do volume distribuído pode ser menor que o esperado devido a uma distribuição irregular de tráfego entre os discos. Por padrão, a interface do usuário do Gerenciador de servidores (UI) permite que você configure colunas para até 8 discos. Mas se você tiver mais de 8 discos, você precisa usar o PowerShell para criar o volume e também especificar manualmente o número de colunas. Caso contrário, a UI do Gerenciador de servidores continuará a usar 8 colunas, mesmo que haja mais discos. Por exemplo, se você tiver 32 discos em um conjunto único de distribuição, você deve especificar 32 colunas. Você pode usar o *NumberOfColumns* parâmetro [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) do cmdlet do PowerShell para especificar o número de colunas usadas pelo disco virtual. Para obter mais informações, consulte [Visão geral dos Espaços de armazenamento](http://technet.microsoft.com/library/jj822938.aspx) e [Perguntas frequentes sobre Espaços de armazenamentos](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).


###<a id="scale">Escalabilidade e metas de desempenho ao usar o Armazenamento Premium</a>
Quando você provisiona um disco em relação a uma conta de armazenamento Premium, quantas operações de entrada/saída por segundo (IOPS) e taxa de transferência (largura de banda) que podem ser obtidas depende do tamanho do disco. Atualmente, há três tipos de discos de armazenamento Premium: P10, P20 e P30. Cada um tem limites específicos de IOPS e taxa de transferência conforme especificados na tabela a seguir:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Tipo de disco de armazenamento Premium</strong></td>
	<td><strong>P10</strong></td>
	<td><strong>P20</strong></td>
	<td><strong>P30</strong></td>
</tr>
<tr>
	<td><strong>Tamanho do disco</strong></td>
	<td>128 GB</td>
	<td>512 GB</td>
	<td>1024 GB (1 TB)</td>
</tr>
<tr>
	<td><strong>IOPS por disco</strong></td>
	<td>500</td>
	<td>2300</td>
	<td>5000</td>
</tr>
<tr>
	<td><strong>Taxa de transferência por disco</strong></td>
	<td>100 MB por segundo</td>
	<td>150 MB por segundo</td>
	<td>200 MB por segundo</td>
</tr>
</tbody>
</table>

O Azure mapeia o tamanho do disco (arredondado) para a opção mais próxima do disco de armazenamento Premium conforme especificado na tabela. Por exemplo, um disco de tamanho 100 GB é classificado como uma opção P10 e pode executar até 500 unidades de entrada e saída por segundo e com taxa de transferência de até 100 MB por segunda. Da mesma forma, um disco de tamanho 400 GB é classificado como uma opção P20 e pode executar até 2300 unidades de e/s por segundo e taxa de transferência de até 150 MB por segunda.

O tamanho da unidade de entrada/saída (e/s) é 256 KB. Se os dados transferidos forem menores que 256 KB, ele será considerado uma única unidade de e/s. Tamanhos maiores de e/s são contados como várias entradas e saídas de tamanho de 256 KB. Por exemplo, taxa de transferência de e/s de KB 1100 é contada como cinco unidades de e/s.

O limite de taxa de transferência inclui tanto leituras quanto gravações no disco. Por exemplo, a soma de leituras e gravações deve ser menor ou igual a 100 MB por segundo para um disco P10.  Da mesma forma, um único disco P10 pode ter 100 MB por segundo de leituras ou 100 MB por segundo de gravações ou 60 MB por segundo de leituras com 40 MB por segundo de gravações, por exemplo.
 
Ao criar seus discos no Azure, selecione a oferta de disco de Armazenamento Premium mais apropriada com base nas necessidades do seu aplicativo em termos de capacidade, desempenho, escalabilidade e picos de carga.

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
       <li type=round>Capacidade do disco: 32 TB</li>
       <li type=round>Capacidade de instantâneo: 10 TB</li>
    </ul>
	</td>
	<td>Até 50 gigabits para Entrada + Saída</td>
</tr>
</tbody>
</table>

- Entrada refere-se a todos os dados (solicitações) que estão sendo enviados para uma conta de armazenamento.
- Saída refere-se a todos os dados (respostas) que está sendo recebidos de uma conta de armazenamento.

Para obter mais informações, confira em [Metas de desempenho e escalabilidade de armazenamento do Azure](http://msdn.microsoft.com/library/dn249410.aspx).


###<a id="throttling">Limitação ao usar o Armazenamento Premium</a>
Você pode ver a limitação se o IOPS ou a taxa de transferência de seu aplicativo exceder os limites alocados para um disco de armazenamento Premium. Para evitar limitações, é recomendável que você limite o número de solicitações de e/s pendentes baseadas em disco com base nas metas de desempenho e escalabilidade para o disco que você provisionou.  

Seu aplicativo pode obter a latência menor quando ele é projetado para evitar a limitação. Por outro lado, se o número de solicitações de e/s pendentes para o disco for muito pequeno, seu aplicativo não poderá tirar proveito dos níveis máximos de IOPS e taxa de transferência que estão disponíveis no disco.

Os exemplos a seguir demonstram como calcular os níveis de limitação:

<h4>Exemplo 1:</h4>
O aplicativo fez 495 e/s de tamanho de disco de 16 KB (, que é igual a 495 unidades de e/s) em um segundo em um disco P10. Se você tentar uma e/s de 2 MB no mesmo segundo, o total de unidades de e/s é igual a 495 + 8. Isso ocorre porque a e/s de 2 MB resulta em unidades de e/s de 2048 KB/256 KB = 8 quando o tamanho da unidade de e/s é 256 KB. Como a soma de 495 + 8 excede o limite de 500 IOPS para o disco, a limitação ocorre. 

<h4>Observação:</h4> 
Todos os cálculos baseiam-se no tamanho da unidade de e/s de 256 KB.

<h4>Exemplo 2:</h4>
O aplicativo fez 400 entradas e saídas de tamanho de disco de 256 KB em um disco P10. A largura de banda total consumida é (400 * 256) / 1024 = 100 MB/seg. Observe que 100 MB por segundo é o limite de taxa de transferência de um disco P10. Se o seu aplicativo tentar realizar mais e/s nesse segundo, ele é limitado porque excede o limite alocado.

<h4>Observação:</h4>
Se o tráfego de disco consiste principalmente de tamanhos de e/s pequenos, é muito provável que seu aplicativo atingirá o limite de IOPS antes do limite de taxa de transferência. Por outro lado, se o tráfego de disco consiste principalmente de tamanhos de e/s grandes, é muito provável que seu aplicativo atingirá o limite de taxa de transferência em vez do limite de IOPS. Você pode maximizar o IOPS do seu aplicativo e a capacidade da taxa de transferência usando tamanhos de e/s ideais e também limitando o número de solicitações de e/s pendentes para o disco.


###<a id="restapi">Instantâneos e cópia de Blob ao usar o Armazenamento Premium</a>
Você pode criar um instantâneo para armazenamento Premium da mesma maneira como você cria um instantâneo ao usar o armazenamento padrão. Como o armazenamento Premium é redundante localmente, é recomendável que você crie instantâneos e então copie esses instantâneos para uma conta de armazenamento com redundância geográfica padrão. Para obter mais informações, confira em [Opções de redundância de armazenamento do Azure](http://msdn.microsoft.com/library/azure/dn727290.aspx).

Se um disco estiver anexado a uma VM, determinadas operações de API não são permitidas no suporte da página de blob do disco. Por exemplo, você não pode executar uma operação de [Cópia de Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) nesse blob enquanto o disco estiver anexado a uma máquina virtual. Em vez disso, primeiro crie um instantâneo desse blob usando o método de API REST do [Instantâneo de Blob](http://msdn.microsoft.com/library/azure/ee691971.aspx) e depois execute a [Cópia de Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) do instantâneo para copiar o disco anexado. Como alternativa, você pode desanexar o disco e, em seguida, executar as operações necessárias no blob subjacente.

<h4>Observações importantes:</h4>

- Se a operação de cópia de blob no armazenamento Premium substituir um blob existente no destino, o blob que está sendo substituído não deve ter todos os instantâneos.  Uma cópia em ou entre contas de armazenamento Premium exigem que o blob de destino não tenha instantâneos ao iniciar a cópia.
- O número de instantâneos para um único blob é limitado a 100. Um instantâneo pode ser criado a cada 10 minutos no máximo.
- 10 TB é a capacidade máxima para instantâneos por conta de Armazenamento Premium. Observe que a capacidade do instantâneo são os únicos dados que existem nos instantâneos. Em outras palavras, a capacidade do instantâneo não inclui o tamanho do blob de base.
- Você pode copiar instantâneos de uma conta de armazenamento Premium para uma conta de armazenamento com redundância geográfica padrão usando AzCopy ou Copiar Blob. Para obter mais informações, confira em [Como usar o AzCopy com o Armazenamento do Microsoft Azure](http://azure.microsoft.com/documentation/articles/storage-use-azcopy/) e [Copiar Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx).
- Para obter informações detalhadas sobre como executar operações REST em relação aos blobs de página nas contas de Armazenamento Premium, confira em [Uso de operações de serviço de Blob com o Armazenamento Premium do Azure](http://go.microsoft.com/fwlink/?LinkId=521969) na biblioteca MSDN.


###<a id="pricing">Preços e cobrança ao usar o Armazenamento Premium</a>
Ao usar o Armazenamento Premium, as seguintes considerações de cobrança se aplicam:

- A cobrança para um disco de Armazenamento Premium depende do tamanho do disco provisionado. O Azure mapeia o tamanho do disco (arredondado) para a opção mais próxima do disco de Armazenamento Premium conforme especificado na tabela fornecida na seção [Escalabilidade e metas de desempenho ao usar o Armazenamento Premium](#scale) . A cobrança por qualquer disco provisionado é rateada por hora usando o preço mensal para a oferta de Armazenamento Premium. Por exemplo, se você provisionou um disco P10 e ele foi excluído após 20 horas, você será cobrado pela a oferta P10 rateada em 20 horas. Isto é independente da quantidade de dados reais gravados no disco ou do IOPS/taxa de transferência usados.
- Instantâneos no Armazenamento Premium serão cobrados pela capacidade adicional usada pelos instantâneos. Para obter informações sobre instantâneos, confira em [Criação de um instantâneo de um Blob](http://msdn.microsoft.com/library/azure/hh488361.aspx).
- [Transferências de dados de saída](http://azure.microsoft.com/pricing/details/data-transfers/) (dados fora dos data center do Azure) acarretam a cobrança por uso de largura de banda. 

Para obter informações detalhadas sobre os preços para Armazenamento Premium e Máquinas virtuais da série DS, confira em:

- [Preços de armazenamento do Azure](http://azure.microsoft.com/pricing/details/storage/)
- [Preços de máquinas virtuais](http://azure.microsoft.com/pricing/details/virtual-machines/)

###<a id="howto1">Criação e uso da conta de Armazenamento Premium para discos</a>
Esta seção demonstra como criar uma conta de Armazenamento Premium usando o Portal de visualização do Azure e o Azure PowerShell. Além disso, ela demonstra um exemplo de caso de uso para contas de armazenamento Premium: criação de uma máquina virtual e anexação de um disco de dados a uma máquina virtual usando o armazenamento Premium.

Nesta seção:

* [Portal de visualização do Azure: Criar uma conta de Armazenamento Premium](#howto3)	

* [PowerShell do Azure: Criar uma conta de Armazenamento Premium e usá-la para operações básicas de VM](#howto2)	
 
###<a id="howto3">Portal de visualização do Azure: Criar uma conta de Armazenamento Premium</a>
Esta seção mostra como criar uma conta de Armazenamento Premium usando o Portal de visualização do Azure.

1.	Entre no [Portal de visualização do Azure](https://portal.azure.com/). Confira a oferta [Avaliação Gratuita](http://azure.microsoft.com/pricing/free-trial/) caso você ainda não tenha uma assinatura. 


    >[AZURE.NOTE] Se você efetuar logon no Portal de gerenciamento do Azure, clique em seu nome de conta de usuário no canto superior direito do portal. Em seguida, clique em **Alternar para o novo portal**.
        

2.	No menu de Hub, clique em **Novo**.

3.	Em **Novo**, clique em **Tudo**. Selecione **Armazenamento, cache, + backup**. A partir daí, clique em **Armazenamento** e, em seguida, clique em **Criar**.

4.	Na folha da Conta de armazenamento, digite um nome para sua conta de armazenamento. Clique em **Camada de preço**. Na folha de **Camadas de preços recomendadas**, clique em **Procurar todas as camadas de preços**. Na folha **Escolha sua camada de preço**, escolha **Premium localmente redundante**. Clique em **Selecionar**. Observe que a folha da **Conta de armazenamento** mostra **GRS padrão** como a **Camada de preço** por padrão. Depois de clicar em **Selecionar**, a **Camada de preço** é mostrada como **LRS Premium**.
	
	![Pricing Tier][Image1]

	
5.	Na folha da **Conta de armazenamento**, mantenha os valores padrão para **Grupo de recursos**, **assinatura**, **Local** e **Diagnósticos**. Clique em **Criar**.

Para obter uma explicação passo a passo completa em um ambiente do Azure, confira em [Criação de uma Máquina Virtual executando o Windows no Portal de visualização do Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial-azure-preview/).

###<a id="howto2">PowerShell do Azure: Criar uma conta de Armazenamento Premium e usá-la para operações básicas de VM</a>
Esta seção mostra como criar uma conta de Armazenamento Premium e como usá-la durante a criação de uma máquina virtual e anexação de um disco de dados a uma máquina virtual usando o PowerShell do Azure.

1. Configuração do ambiente do seu PowerShell, seguindo as etapas fornecidas em [Como instalar e configurar o PowerShell do Azure](http://azure.microsoft.com/documentation/articles/install-configure-powershell/).
2. Inicie o console do PowerShell, conecte-se a sua assinatura e execute o seguinte cmdlet do PowerShell na janela do console. Conforme visto nesta declaração de PowerShell, você precisa especificar o parâmetro **Tipo** como **LRS Premium** ao criar uma conta de armazenamento Premium.

		New-AzureStorageAccount -StorageAccountName "yourpremiumaccount" -Location "West US" -Type "Premium_LRS"

3. Em seguida, crie uma nova VM da série DS e especifique que deseja o Armazenamento Premium, executando os seguintes cmdlets do PowerShell na janela do console:

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

4. Se você quiser mais espaço em disco para sua VM, anexe um novo disco de dados em uma VM da série DS existente depois de criada, executando os seguintes cmdlets do PowerShell na janela do console:

    	$storageAccount = "yourpremiumaccount"
    	$vmName ="yourVM"
    	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
    	$LunNo = 1
    	$path = "http://" + $storageAccount + ".blob.core.windows.net/vhds/" + "myDataDisk_" + $LunNo + "_PIO.vhd"
    	$label = "Disk " + $LunNo
    	Add-AzureDataDisk -CreateNew -MediaLocation $path -DiskSizeInGB 128 -DiskLabel $label -LUN $LunNo -HostCaching ReadOnly -VM $vm | Update-AzureVm

###<a id="see">Recursos adicionais</a>
[Uso de Operações de serviço de blob com o Armazenamento Premium do Azure](http://go.microsoft.com/fwlink/?LinkId=521969)

[Criação de uma Máquina virtual executando o Windows](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial-azure-preview/)

[Máquinas virtuais e tamanhos de serviço de nuvem para o Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx)

[Documentação de armazenamento](http://azure.microsoft.com/documentation/services/storage/)

[Referência do MSDN](http://msdn.microsoft.com/library/azure/gg433040.aspx)

[Image1]: ./media/storage-premium-storage-preview-portal/Azure_pricing_tier.png

<!--HONumber=42-->
