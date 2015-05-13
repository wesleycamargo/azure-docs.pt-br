<properties 
    pageTitle="Migrando para o Armazenamento Premium do Azure | Microsoft Azure" 
    description="Migre para o Armazenamento Premium do Azure para ter um alto desempenho, suporte a disco de baixa latência para cargas de trabalho com uso intenso de E/S em execução nas máquinas virtuais do Azure." 
    services="storage" 
    documentationCenter="na" 
    authors="tamram" 
    manager="adinah" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/14/2015" 
    ms.author="tamram"/>


# Migrando para o Armazenamento do Azure Premium

## Visão geral

O Armazenamento Premium armazena dados nas unidades de estado sólido \(SSDs\) de tecnologia mais recente, oferece um alto desempenho e suporte de disco de baixa latência para intensas cargas de trabalho de E/S em execução nas Máquinas Virtuais do Azure. Com o armazenamento Premium, seus aplicativos podem ter até 32 TB de armazenamento por VM e obter 50.000 IOPS \(operações de entrada/saída por segundo\) por VM com muito menos latências por operações de leitura. Este artigo fornece orientação sobre como migrar seus discos, Máquinas Virtuais \(VMs\) do local, do Armazenamento Padrão ou de uma plataforma de nuvem diferente para o Armazenamento do Azure Premium. Para obter uma visão geral detalhada da oferta de Armazenamento do Azure Premium, verifique o Armazenamento Premium: [Armazenamento de Alto Desempenho para Cargas de Trabalho da Máquina Virtual do Azure](storage-premium-storage-preview-portal.md).

## Antes de começar 

### Pré-requisitos
- Você também precisará de uma assinatura do Azure. Se não tiver, poderá criar uma assinatura de [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/) de um mês ou visitar [Preços do Azure](http://azure.microsoft.com/pricing/) para obter mais opções. 
- Para executar os cmdlets PowerShell, você precisará do módulo PowerShell do Microsoft Azure. Consulte [Downloads do Microsoft Azure](http://azure.microsoft.com/downloads/) para baixar o módulo.
- Quando você planejar usar as máquinas virtuais do Azure em execução no Armazenamento Premium, precisará usar as VMs da série DS. Você pode usar os discos de Armazenamento Standard e Premium com as VMS da série DS. Os discos de armazenamento Premium estarão disponíveis com mais tipos de VM no futuro. Para obter informações sobre todos os tamanhos e tipos de discos da VM do Azure disponíveis, consulte [Máquina Virtual e Tamanhos do Serviço em Nuvem do Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). 

### Considerações 

#### Tamanhos de VM 
Resumidos abaixo estão os tamanhos de VM da série DS e suas características. Revise as características de desempenho dessas ofertas de Armazenamento Premium e escolha a opção mais adequada para os discos do Azure e a VM que melhor atenda à sua carga de trabalho. Certifique-se de que há largura de banda suficiente disponível na sua VM para direcionar o tráfego de disco.

|Tamanho da VM|Núcleos de CPU|Máx. IOPS|Máx. Largura de Banda do Disco|
|:---:|:---:|:---:|:---:|
|**STANDARD\_DS1**|1|3\.200|32 MB por segundo|
|**STANDARD\_DS2**|2|6\.400|64 MB por segundo|
|**STANDARD\_DS3**|4|12\.800|128 MB por segundo|
|**STANDARD\_DS4**|8|25\.600|256 MB por segundo|
|**STANDARD\_DS11**|2|6\.400|64 MB por segundo|
|**STANDARD\_DS12**|4|12\.800|128 MB por segundo|
|**STANDARD\_DS13**|8|25\.600|256 MB por segundo|
|**STANDARD\_DS14**|16|50\.000|512 MB por segundo|

#### Tamanhos do disco 
Há três tipos de discos que podem ser usados com a VM e cada um tem IOPs específicos e limites completos. Leve em consideração esses limites ao escolher o tipo de disco para sua VM com base nas necessidades de seu aplicativo em termos de capacidade, desempenho, escalabilidade e cargas de pico.

|Tipo de disco de armazenamento Premium|P10|P20|P30|
|:---:|:---:|:---:|:---:|
|Tamanho do disco|128 GB|512 GB|1024 GB \(1 TB\)|
|IOPS por disco|500|2\.300|5\.000|
|Taxa de transferência por disco|100 MB por segundo|150 MB por segundo|200 MB por segundo|

#### Metas de Escalabilidade da Conta de Armazenamento

As contas de Armazenamento Premium têm as seguintes metas de escalabilidade, além das [Metas de Desempenho e Escalabilidade do Armazenamento do Azure](http://msdn.microsoft.com/library/azure/dn249410.aspx). Se as exigências de seu aplicativo exceder as metas de escalabilidade de uma única conta de armazenamento, crie seu aplicativo para usar múltiplas contas de armazenamento e particione seus dados nessas contas de armazenamento.

|Capacidade total da conta|Largura de banda total para uma conta de armazenamento localmente redundante|
|:--|:---|
|Capacidade de disco: 35 TB<br />Capacidade do instantâneo: 10 TB|Até 50 gigabits para Entrada + Saída|

Para obter mais informações sobre as especificações do Armazenamento Premium, verifique as [Metas de Escalabilidade e Desempenho ao usar o Armazenamento Premium](storage-premium-storage-preview-portal.md#scalability-and-performance-targets-whpt-bring-premium-storage)

#### Discos de Dados Adicionais 
Dependendo da carga de trabalho, determine se discos de dados adicionais são necessários para sua VM. Você pode anexar diversos discos de dados persistentes à sua VM. Se necessário, pode distribuir entre os discos para aumentar a capacidade e o desempenho do volume. Se você distribuir discos de dados do Armazenamento Premium usando [Espaços de Armazenamento](http://technet.microsoft.com/library/hh831739.aspx), deverá configurá-lo com uma coluna para cada disco usado. Caso contrário, o desempenho geral do volume distribuído pode ser menor que o esperado devido a uma distribuição irregular de tráfego entre os discos. Para as VMs do Linux, você pode usar o utilitário mdadm para alcançar o mesmo. Consulte o artigo [Configurar o Software RAID no Linux](virtual-machines-linux-configure-raid.md) para obter detalhes.

#### Política de Cache do Disco 
Por padrão, a política de cache de disco é "Somente leitura" para todos os discos de dados Premium e “Leitura e gravação" para o disco de sistema operacional Premium anexado à VM. Esta definição de configuração é recomendável para atingir o desempenho ideal de leituras de entrada e saída dos seus aplicativos. Para discos de dados de gravação intensa ou somente gravação \(como arquivos de log do SQL Server\), desabilite o cache de disco para que possa obter o melhor desempenho do aplicativo. As configurações de cache existentes para os discos de dados podem ser atualizadas usando o Portal do Azure ou o parâmetro *-HostCaching* do cmdlet *Set-AzureDataDisk*.

#### Local 
Escolha um local onde o Armazenamento do Azure Premium está disponível. Consulte [Coisas Importantes a saber sobre o Armazenamento Premium](storage-premium-storage-preview-portal.md#important-things-to-know-about-premium-storage) para obter informações atualizadas sobre os locais disponíveis. As máquinas virtuais na mesma região da conta de armazenamento que armazena os discos da VM fornecerão um desempenho superior em relação a estarem em regiões separadas.

#### Outras definições de configuração da VM do Azure 

Ao criar uma VM do Azure, você será solicitado a definir certas configurações da máquina virtual. Lembre-se de que há algumas configurações que são fixas durante a vida útil da VM, ao passo que você pode modificar ou adicionar outras posteriormente. Revise essas definições de configuração da máquina virtual do Azure e verifique se elas estão definidas corretamente para atender aos seus requisitos de carga de trabalho. Consulte [Definições de configuração da VM](https://msdn.microsoft.com/library/azure/dn763935.aspx) para obter mais detalhes.

## Preparar VHDs para a Migração 

A seção a seguir fornece diretrizes para preparar os VHDs de sua VM para estarem prontos para migrar. O VHD pode ser:
 
- Uma imagem do sistema operacional generalizada que pode ser usada para criar várias VMs do Azure.  
- Um disco do sistema operacional que pode ser usado com uma instância da máquina virtual do Azure.  
- Um disco de dados que pode ser anexado a uma VM do Azure para o armazenamento persistente.

### Pré-requisitos

- Uma assinatura do Azure, uma conta de armazenamento e um contêiner para o qual a conta de armazenamento copiará o VHD. Observe que a conta de armazenamento de destino pode ser uma conta de Armazenamento Standard ou Premium, dependendo do requisito. 
- Uma ferramenta para generalizar o VHD, caso você planeje criar várias instâncias da VM a partir dele. Por exemplo, o sysprep para Windows ou o virt-sysprep para Ubuntu. Uma ferramenta para carregar o arquivo VHD para a conta de armazenamento. Por exemplo, o [AzCopy](storage-use-azcopy.md) ou o [Gerenciador de armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Este guia descreve as etapas usando a ferramenta AzCopy. Para uma grande quantidade de dados com largura de banda limitada, você pode considerar o uso do [serviço de Importação/Exportação do Microsoft Azure](storage-import-export-service.md) para transferir os dados enviando as unidades de disco rígido para um datacenter do Azure. Usando o serviço de Importação/Exportação, você pode copiar os dados apenas para uma conta de armazenamento padrão. Quando os dados estiverem em sua conta de armazenamento padrão, você usará a [cópia da API do blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) ou o AzCopy para transferir os dados para sua conta de armazenamento premium.
- Uma ferramenta para copiar o VHD pode ser executada em uma VM do Azure na mesma região dos VHDs.
- O Microsoft Azure suporta apenas arquivos VHD de tamanho fixo. Os arquivos VHDX ou VHDs dinâmicos não têm suporte. Se você tiver um VHD dinâmico, poderá convertê-lo para ter um tamanho fixo usando o cmdlet [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx).

### Preparando os VHDs

|Cenário|Etapas|
|:---|:---|
|VHD do Sistema Operacional Generalizado para criar várias instâncias de VM|<p>Se você estiver carregando um **VHD que será usado para criar várias instâncias de VM do Azure genéricas**, primeiro deverá generalizar o VHD usando um utilitário sysprep. Isso se aplica a um VHD que é local ou está na nuvem. O sysprep remove quaisquer informações específicas da máquina do VHD.</p><p>\*\*Importante:\*\* tire um instantâneo ou faça backup de sua VM antes de generalizar. Executar o sysprep irá excluir a instância da VM.</p>Siga as etapas abaixo para usar o sysprep em um VHD do sistema operacional Windows:<br />note que executar o comando Sysprep exigirá que você finalize a máquina virtual. Para obter mais informações sobre o Sysprep, consulte [Visão Geral do Sysprep](http://technet.microsoft.com/library/hh825209.aspx) ou [Referência Técnica do Sysprep]\(http://technet.microsoft.com/library/cc766049(v=ws.10).aspx). <ul><li>Abra uma janela do Prompt de Comando como administrador.</li><li>Digite o seguinte comando para abrir o Sysprep:<br />\*\*%windir%\\system32\\sysprep\\sysprep.exe\*\*</li><li>Em Ferramenta de Preparação do Sistema, selecione Entrar no Sistema OOBE \(Configuração Inicial pelo Usuário\), marque a caixa de seleção Generalizar, selecione **Finalizar**, em seguida, clique em **OK**.</li><li>Isso irá generalizar o sistema operacional e finalizará o sistema.![][1]</li></ul>Para uma VM Ubuntu, use virt-sysprep para fazer o mesmo. Consulte [virt sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) para obter mais detalhes. Consulte também um [software de Provisionamento do Servidor Linux](http://www.cyberciti.biz/tips/server-provisioning-software.html) de fonte aberta para ver outros sistemas operacionais Linux.|
|VHD de Sistema Operacional Exclusivo para criar uma única instância de VM|Se você tiver um aplicativo em execução na máquina virtual que exige dados específicos da máquina, não generalize o VHD. **Um VHD não generalizado pode ser usado para criar uma instância exclusiva da VM do Azure.** Por exemplo, se você tiver um Controlador de Domínio em seu VHD, executar o sysprep irá torná-lo inútil como um Controlador de Domínio. Examine os aplicativos em execução em sua VM e o impacto do sysprep neles antes de generalizar o VHD.|
|VHDs do disco de dados a serem anexados à\(s\) instância\(s\) de VM\(s\)|Se você tiver discos de dados em um armazenamento na nuvem a serem migrados, deverá verificar se as máquinas virtuais que usam esses discos devem ser finalizadas. Para os discos de dados que estão no local, crie um VHD consistente.|
Agora que o VHD está pronto, siga as etapas descritas na próxima seção para carregar o VHD no Armazenamento do Azure e registrá-lo como uma imagem do sistema operacional, disco provisionado do sistema operacional ou disco de dados provisionado.

## Copiar os VHDs para o Armazenamento do Azure

### Fonte

|Cenário|Etapas|
|:---|:---|
|Copiando o VHD do Armazenamento do Azure|Se você estiver migrando o VHD de uma conta de armazenamento Standard do Azure para uma conta de armazenamento Premium, deverá copiar o caminho de origem do contêiner do VHD, o nome de arquivo do VHD e a chave da conta de armazenamento da conta de armazenamento de origem.<ul><li>Vá para o Portal do Azure \> Máquinas Virtuais \> Discos</li><li>Copie e salve a URL do contêiner do VHD na coluna Local, https://*AccountName*.blob.core.windows.net/*ContainerName*/</li></ul>|
|Copiando o VHD da Nuvem não do Azure|Se você estiver migrando o VHD do Armazenamento em Nuvem não do Azure, primeiro deverá exportar o VHD para um diretório local. Copie o caminho de origem completo do diretório local onde o VHD está armazenado. <ul><li>Se você estiver usando o AWS, exporte a instância do EC2 para um VHD em um depósito do Amazon S3. Siga as etapas descritas em [Exportando instâncias do Amazon EC2](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ExportingEC2Instances.html) para instalar a ferramenta de interface da linha de comando \(CLI\) do Amazon EC2 e execute o comando para exportar a instância do EC2 para um arquivo VHD. Use **VHD** para a variável DISK\_IMAGE\_FORMAT ao executar o comando. O arquivo VHD exportado é salvo no depósito do Amazon S3 designado durante esse processo.</li></ul>![][2]<ul><li>Baixe o arquivo VHD do depósito S3. Selecione o arquivo VHD \> **Ações** \> **Baixar**.</li></ul>![][3]|
|Copiando um VHD do Local|Se você estiver migrando o VHD de um ambiente local, será necessário o caminho de origem completo onde o VHD está armazenado. Isso pode ser um local do servidor ou um compartilhamento de arquivos.

### Destino 

Crie uma conta de armazenamento para manter seus VHDs. Leve em consideração os seguintes pontos ao planejar onde armazenar seus VHDs.

- A conta de armazenamento de destino pode ser o armazenamento standard ou premium, dependendo do requisito do aplicativo. 
- O local da conta de armazenamento deve ser igual às VMs do Azure da série DS que você criará na fase final. Você pode copiar para uma nova conta de armazenamento ou planejar usar a mesma conta de armazenamento com base em suas necessidades.
- Copie e salve a chave da conta de armazenamento da conta de armazenamento de destino para a próxima fase.
- Para os discos de dados, você pode optar por manter alguns discos de dados em uma conta de armazenamento standard \(por exemplo, os discos que têm um armazenamento mais legal\) e alguns com IOPs pesados na conta de armazenamento premium.

### Copiando um VHD com o AzCopy

Usando o AzCopy, é possível carregar o VHD facilmente na Internet. Dependendo do tamanho dos VHDs, isso pode levar tempo. Lembre-se de verificar os limites de entrada/saída da conta de armazenamento ao usar essa opção. Você pode encontrar os Limites de Armazenamento do Azure [aqui](azure-subscription-service-limits#storage-limits).

1. Baixe e instale o AzCopy aqui: [Versão mais recente do AzCopy](http://aka.ms/downloadazcopy)  
2. Abra o PowerShell do Azure e vá para a pasta onde o AzCopy está instalado.  
3. Use o seguinte comando para copiar o arquivo VHD de "Origem" para "Destino". **AzCopy /Origem:** *&lt;Source&gt;* **/SourceKey:** *&lt;Source-Storage-Key&gt;* **/Destino:** *&lt;Destination&gt;* **/DestKey:** *&lt;Dest-Storage-Key&gt;* **/BlobType:page /Padrão:** *&lt;File-Name&gt;*  
  - *&lt;Source&gt;:* Local da pasta ou URL do contêiner de armazenamento que contém o VHD.    
 - *&lt;Source-Storage-Key&gt;:* Chave da conta de armazenamento da conta de armazenamento de origem.  
 - *&lt;Destination&gt;:* O URL do contêiner de armazenamento para o qual copiar o VHD.
 - *&lt;Dest-Storage-Key&gt;:* Chave da conta de armazenamento da conta de armazenamento de destino.
 - /BlobType:page: Especifica que o destino é um blob de páginas.
 - *&lt;File-Name&gt;*: O nome do arquivo VHD que você deseja copiar.
 - /Padrão:\*&lt;File-Name&gt;:\* Especifique o nome de arquivo do VHD que você está copiando.
   
Esse comando copiará todos os arquivos em *&lt;Source&gt;* para o contêiner *&lt;Destination&gt;*. Para obter detalhes sobre como usar a ferramenta AzCopy, consulte Introdução ao [Utilitário da Linha de Comando do AzCopy](storage-use-azcopy.md).
### Outras opções para carregar um VHD 

- [API do Blob da Cópia de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx) 
- [Serviço de Importação/Exportação do Azure](https://msdn.microsoft.com/library/dn529096.aspx) 

>[AZURE.NOTE]A Importação/Exportação pode ser usada para copiar apenas para a conta de armazenamento standard. Você precisará copiar do armazenamento standard para a conta de armazenamento premium usando uma ferramenta como o AzCopy.

## Criar máquinas virtuais do Azure usando o Armazenamento Premium

Depois do VHD ser carregado para a conta de armazenamento desejada, siga as instruções nesta seção para registrar o VHD como uma imagem do sistema operacional ou disco do sistema operacional de acordo com seu cenário, em seguida, crie uma instância de VM a partir dele. O VHD do disco de dados pode ser anexado à VM assim que criado.

### Registrar o VHD
  
Para criar uma máquina virtual a partir do VHD do sistema operacional ou anexar o VHD de Dados a uma nova VM, primeiro você deve registrá-los. Siga as etapas abaixo, dependendo do cenário.

<table>
<tr>
<th>Cenário</th>
<th>Etapas para registrar o VHD</th>
</tr>
<tr>
<td>VHD do Sistema Operacional Generalizado para criar várias instâncias de VM do Azure</td>
<td><p>Depois do VHD de imagem do sistema operacional generalizado ser carregado para a conta de armazenamento, registre-o como uma <b>Imagem da VM do Azure</b> para que você possa criar uma ou mais instâncias de VM a partir dele. </p><p>Use os seguintes cmdlets do PowerShell para registrar seu VHD como uma imagem do sistema operacional da VM do Azure. Forneça a URL completa do contêiner para onde o VHD foi copiado.</p><p><code>Add-AzureVMImage -ImageName "OSImageName" -MediaLocation “https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd” -OS Windows</code></p>Copie e salve o nome dessa nova Imagem da VM do Azure. No exemplo acima, é "OSImageName".</td>
</tr>
</tr>
<td>VHD do sistema operacional exclusivo para criar uma instância de VM do Azure</td>
<td><p>Depois do VHD exclusivo do sistema operacional ser carregado para a conta de armazenamento, registre-o como um <b>Disco do Sistema Operacional do Azure</b> para que você possa criar uma instância de VM a partir dele.</p><p>Use estes cmdlets do PowerShell para registrar seu VHD como um Disco do sistema operacional do Azure. Forneça a URL completa do contêiner para onde o VHD foi copiado.</p><code>"https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "OS Disk" -OS "Windows"</code><p>Copie e salve o nome desse novo Disco do sistema operacional do Azure. No exemplo acima, é “OSDisk”.</p></td>
</tr>
</tr>
<td>VHD do Disco de Dados a ser anexado à(s) instância(s) de VM(s) do Azure</td>
<td><p>Depois do VHD do disco de dados ser carregado para a conta de armazenamento, registre-o como um Disco de Dados do Azure para que ele possa ser anexado à sua nova instância de VM do Azure da Série DS.</p><p>Use estes cmdlets do PowerShell para registrar seu VHD como um Disco de Dados do Azure. Forneça a URL completa do contêiner para onde o VHD foi copiado.</p><code>Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "Data Disk"</code><p>Copie e salve o nome desse novo Disco de Dados do Azure. No exemplo acima, é “DataDisk”.</p></td>
</tr>
</table>

### Criar a VM do Azure da série DS  

Assim que a imagem do sistema operacional ou o disco do sistema operacional for registrado, uma nova instância da máquina virtual do Azure da série DS poderá ser criada. Você usará a imagem do sistema operacional ou o nome de disco do sistema operacional registrado. Selecione o tipo de VM na camada de Armazenamento Premium. No exemplo abaixo, estamos usando o tamanho de VM "Standard\_DS2".

>[AZURE.NOTE]Atualize o tamanho do disco para assegurar que ele corresponda à sua capacidade, ao requisito de desempenho e aos tamanhos de disco disponíveis do Azure.

Execute os cmdlets do PowerShell passo a passo abaixo para criar a nova VM,

Primeiro, defina os parâmetros comuns:

	$vmSize ="Standard_DS2"
	$adminName = "youradmin"
	$adminPassword = "yourpassword"
	$vmName ="yourVM"
	$location = "East US 2"  

Em segundo lugar, dependendo do cenário, crie a instância de máquina virtual do Azure a partir da imagem do sistema operacional ou do disco do sistema operacional registrado.

<table>
<tr>
<th>Cenário</th>
<th>Etapas</th>
</tr>
<tr>
<td>VHD do Sistema Operacional Generalizado para criar várias instâncias de VM do Azure</td>
<td>Crie uma ou mais instâncias de VM do Azure novas da Série DS usando a <b>Imagem do sistema operacional do Azure</b> registrada. Especifique o nome da Imagem do sistema operacional na configuração da máquina virtual ao criar uma nova máquina, conforme mostrado abaixo,</br></br>
<code>$OSImage = Get-AzureVMImage –ImageName “OSImageName” $vm = New-AzureVMConfig -Name "NewVM" -InstanceSize $vmSize -ImageName $OSImage.ImageName</code>
</br></br>
<code>Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm New-AzureVM -ServiceName "NewVM"</code></td></tr>  
<tr>
<td>VHD do sistema operacional exclusivo para criar uma instância de VM do Azure</td>
<td>Crie uma nova instância de VM do Azure da série DS usando o <b>Disco do sistema operacional do Azure</b> registrado. Especifique o nome do Disco do sistema operacional na configuração da máquina virtual ao criar uma nova máquina, conforme mostrado abaixo,</br></br>
<code>$OSDisk = Get-AzureDisk –DiskName “OSDisk” $vm = New-AzureVMConfig -Name “NewVM” -InstanceSize $vmSize -DiskName $OSDisk.DiskName</code>
</br></br>  
<code>Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm New-AzureVM -ServiceName "NewVM"</code>
</td>
</tr>
</table>

Especifique outras informações da VM do Azure, como um serviço de nuvem, região, conta de armazenamento, conjunto de disponibilidade e política de cache. Observe que a instância da máquina virtual deve ser localizada junto com o sistema operacional ou discos de dados associados, portanto, o serviço de nuvem selecionado, a região e a conta de armazenamento devem estar no mesmo local dos VHDs subjacentes desses discos.

### Anexar o Disco de Dados  

Por fim, se você registrou os VHDs do disco de dados, anexe-os à nova VM do Azure da série DS.

Use o seguinte cmdlet do PowerShell para anexar um disco de dados à nova VM e especifique a política de cache. No exemplo abaixo, a política de cache é definida para Somente Leitura.

	$vmName ="yourVM"
	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
	Add-AzureDataDisk -ImportFrom -DiskName “DataDisk” -LUN 0 –HostCaching ReadOnly –VM $vm | Update-AzureVM  

>[AZURE.NOTE]Pode haver etapas específicas necessárias para dar suporte aos aplicativos que podem não estar cobertos por este guia.


## Próximas etapas  

Verifique os seguintes recursos para os cenários específicos de migração das máquinas virtual

- [Migrar Máquinas Virtuais do Azure entre as Contas de Armazenamento](http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)  
- [Crie e carregue um VHD do Windows Server no Azure.](virtual-machines-create-upload-vhd-windows-server.md)  
- [Criando e Carregando um Disco Rígido Virtual que Contém o Sistema Operacional Linux](virtual-machines-linux-create-upload-vhd.md)  
- [Migrando Máquinas Virtuais do Amazon AWS para o Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)  

Verifique também os seguintes recursos para saber mais sobre o Armazenamento do Azure e as Máquinas Virtuais do Azure,

- [Armazenamento do Azure](http://azure.microsoft.com/documentation/services/storage/)   
- [Máquinas Virtuais do Azure](http://azure.microsoft.com/documentation/services/virtual-machines/)  
- [Armazenamento Premium: Armazenamento de Alto Desempenho para as Cargas de Trabalho da Máquina Virtual do Azure](storage-premium-storage-preview-portal.md)  



[1]: ./media/storage-migration-to-premium-storage/storage-migration-to-premium-storage-image1.png
[2]: ./media/storage-migration-to-premium-storage/storage-migration-to-premium-storage-image2.png
[3]: ./media/storage-migration-to-premium-storage/storage-migration-to-premium-storage-image3.png
<!--HONumber=52-->
