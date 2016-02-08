<properties
	pageTitle="Criar uma VM do Linux usando o Azure PowerShell | Microsoft Azure"
	description="Saiba como criar e pré-configurar uma VM do Linux usando o Azure PowerShell."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="cynthn"/>

# Criar e pré-configurar uma máquina virtual do Linux usando o Azure PowerShell

> [AZURE.SELECTOR]
- [Azure CLI](virtual-machines-linux-tutorial.md)
- [PowerShell](virtual-machines-ps-create-preconfigure-linux-vms.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.
 
Estas etapas mostram como criar uma máquina virtual do Linux usando uma abordagem de preenchimento de lacunas para a criação de conjuntos de comandos do Azure PowerShell. Esta abordagem poderá ser útil se você for novo no Azure PowerShell ou apenas quiser saber quais valores especificar para uma configuração bem-sucedida.

Para criar o conjunto de comandos, copie os conjuntos de blocos de comando em um arquivo de texto ou no ISE do Windows PowerShell; em seguida, preencha os valores das variáveis e remova os caracteres < and >. Veja os dois [exemplos](#examples) no final deste artigo para ter uma ideia do resultado final.

Para ver o tópico complementar sobre máquinas virtuais baseadas em Windows, confira [Usar o Azure PowerShell para criar máquinas virtuais baseadas em Windows](virtual-machines-ps-create-preconfigure-windows-vms.md).

## Instale o Azure PowerShell

Se você ainda não fez isso, veja como [instalar e configurar o Azure PowerShell](../install-configure-powershell.md). Em seguida, abra um prompt de comando do PowerShell do Azure.

## Definir a assinatura e a conta de armazenamento

Defina a assinatura e a conta de armazenamento do Azure executando os comandos a seguir no prompt de comando do Azure PowerShell.

Você pode obter o nome de assinatura correto na propriedade **SubscriptionName** da saída do comando **Get-AzureSubscription**.

Você pode obter o nome da conta de armazenamento correto na propriedade **Label** da saída do comando **Get-AzureStorageAccount**, após emitir o comando Select-AzureSubscription.

Substitua tudo que estiver entre aspas, inclusive os caracteres < and >, pelos nomes corretos.

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount


## Localizar a imagem que deseja usar

Em seguida, especifique o valor ImageFamily para a imagem que deseja usar. Você pode obter a lista de valores de ImageFamily disponíveis com o comando a seguir.

	Get-AzureVMImage | select ImageFamily -Unique

Aqui estão alguns exemplos de valores de ImageFamily para computadores baseados em Linux:

- Ubuntu Server 12.10
- CoreOS Alpha
- SUSE Linux Enterprise Server 12

Abra uma nova instância do editor de texto de sua escolha ou uma instância do ISE (ambiente de script integrado) do PowerShell. Copie o seguinte para o novo arquivo de texto ou o ISE do PowerShell, substituindo o valor de ImageFamily.

	$family="<ImageFamily value>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## Especificar o nome, o tamanho e, opcionalmente, o conjunto de disponibilidade

Comece seu conjunto de comandos escolhendo um destes dois blocos de comandos (obrigatório).

**Opção 1**: especifique um nome e um tamanho de máquina virtual.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

**Opção 2**: especifique um nome, o tamanho e o nome do conjunto de disponibilidade.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availset="<set name>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

Para os valores de InstanceSize para máquinas virtuais da série D, DS ou G, confira [Tamanhos de máquina virtual e serviços de nuvem no Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).


## Configurar as opções de segurança de acesso do usuário

**Opção 1**: especifique o nome e a senha do usuário inicial do Linux (obrigatório). Escolha uma senha forte. Para verificar a força da senha, consulte [Verificador de senha: usando senhas fortes](https://www.microsoft.com/security/pc-security/password-checker.aspx).

	$cred=Get-Credential -Message "Type the name and password of the initial Linux account."
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

**Opção 2**: especifique um conjunto de pares de chaves SSH que já foram implantadas na assinatura.

	$vm1 | Add-AzureProvisioningConfig -Linux -SSHKeyPairs "<SSH key pairs>"

Para saber mais, confira [Como usar o SSH com o Linux no Azure](virtual-machines-linux-use-ssh-key.md).

**Opção 3**: especifique uma lista de chaves SSH públicas que já foram implantadas na assinatura.

	$vm1 | Add-AzureProvisioningConfig -Linux - SSHPublicKeys "<SSH public keys>"

Para ver outras opções de pré-configuração para máquinas virtuais baseadas em Linux, confira a sintaxe para o conjunto de parâmetros do **Linux** em [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx).


## Opcional: atribuir um DIP estático

Opcionalmente, atribua um endereço IP específico, conhecido como um DIP estático, à máquina virtual.

	$vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

Você pode verificar se um endereço IP específico está disponível com o comando a seguir.

	Test-AzureStaticVNetIP –VNetName <VNet name> –IPAddress <IP address>

## Opcional: atribuir a máquina virtual a uma sub-rede específica 

Atribua a máquina virtual a uma sub-rede específica de uma Rede Virtual do Azure.

	$vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

	
## Opcional: adicionar um disco de dados
	
Adicione o seguinte conteúdo ao conjunto de comandos para adicionar um disco de dados à máquina virtual.

	$disksize=<size of the disk in GB>
	$disklabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$hcaching="<Specify one: ReadOnly, ReadWrite, None>"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

## Opcional: adicionar a máquina virtual a um balanceamento de carga existente 

Adicione o seguinte conteúdo ao conjunto de comandos para adicionar a máquina virtual a um conjunto de balanceamento de carga de tráfego externo.

	$prot="<Specify one: tcp, udp>"
	$localport=<port number of the internal port>
	$pubport=<port number of the external port>
	$endpointname="<name of the endpoint>"
	$lbsetname="<name of the existing load-balanced set>"
	$probeprotocol="<Specify one: tcp, http>"
	$probeport=<TCP or HTTP port number of probe traffic>
	$probepath="<URL path for probe traffic>"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

## Decidir como iniciar o processo de criação de máquina virtual 

Adicione um bloco ao conjunto de comandos para iniciar o processo de criação da máquina virtual, escolhendo um dos seguintes blocos de comando.

**Opção 1**: crie a máquina virtual em um serviço de nuvem existente.

	New-AzureVM –ServiceName "<short name of the cloud service>" -VMs $vm1

O nome curto do serviço de nuvem é o nome que aparece na lista de Serviços de Nuvem do Azure no portal clássico do Azure ou na lista de grupos de recursos no Portal do Azure.

**Opção 2**: crie a máquina virtual em um serviço de nuvem e em uma rede virtual existentes.

	$svcname="<short name of the cloud service>"
	$vnetname="<name of the virtual network>"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## Executar o conjunto de comandos

Revise o conjunto de comandos do Azure PowerShell criado no editor de texto ou no ISE do Windows PowerShell, verifique se você especificou todas as variáveis e se elas têm os valores corretos. Verifique também se você removeu todos os caracteres < and >.

Copie o conjunto de comandos para a área de transferência e clique com o botão direito no prompt de comando aberto do PowerShell do Azure. Isto emitirá o conjunto de comandos como uma série de comandos do PowerShell e criará sua máquina virtual do Azure.

Após a criação da máquina virtual, confira [Como fazer logon em uma máquina virtual com Linux](virtual-machines-linux-how-to-log-on.md).

Caso pretenda usar novamente o conjunto de comandos:

- Salve esse conjunto de comandos como um arquivo de script do PowerShell (*.ps1)
- Salve este conjunto de comandos como um runbook de automação do Azure na seção **automação** do portal clássico do Azure

## <a id="examples"></a>Exemplos

Aqui estão dois exemplos de como usar as etapas anteriores para criar conjuntos de comandos do Azure PowerShell que criam máquinas virtuais do Azure baseadas em Linux.

### Exemplo 1

Preciso de um conjunto de comandos do PowerShell para criar a máquina virtual Linux inicial para um servidor MySQL que:

- Usa a imagem do Ubuntu Server 12.10.
- Tem o nome de AZMYSQL1.
- Tem um disco de dados adicional de 500 GB.
- Tem o endereço IP estático 192.168.244.4.
- Está na sub-rede de Back-end da rede virtual AZDatacenter.
- Está no serviço de nuvem Azure-TailspinToys.

Aqui está o conjunto de comandos do PowerShell do Azure correspondente para criar essa máquina virtual, com linhas em branco entre cada bloco para facilitar a leitura.

	$family="Ubuntu Server 12.10"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vmname="AZMYSQL1"
	$vmsize="Large"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$cred=Get-Credential -Message "Type the name and password of the initial Linux account."
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

	$disksize=500
	$disklabel="MySQLData"
	$lun=0
	$hcaching="None"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

### Exemplo 2

Preciso de um conjunto de comandos do PowerShell para criar uma máquina virtual Linux para um servidor Apache que:

- Usa a imagem do SUSE Linux Enterprise Server 12.
- Tem o nome de LOB1.
- Tem um disco de dados adicional de 50 GB.
- É membro do conjunto do balanceador de carga LOBServers de tráfego da Web padrão.
- Está na sub-rede de Front-end da rede virtual AZDatacenter.
- Está no serviço de nuvem Azure-TailspinToys.

Aqui está o conjunto de comandos do PowerShell do Azure correspondente para criar essa máquina virtual.

	$family="SUSE Linux Enterprise Server 12"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vmname="LOB1"
	$vmsize="Medium"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$cred=Get-Credential -Message "Type the name and password of the initial Linux account."
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

	$disksize=50
	$disklabel="LOBApp"
	$lun=0
	$hcaching="ReadWrite"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$prot="tcp"
	$localport=80
	$pubport=80
	$endpointname="LOB1"
	$lbsetname="LOBServers"
	$probeprotocol="tcp"
	$probeport=80
	$probepath="/"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## Recursos adicionais

[Documentação de máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/)

[Perguntas frequentes sobre máquinas virtuais do Azure](http://msdn.microsoft.com/library/azure/dn683781.aspx)

[Visão geral das máquinas virtuais do Azure](http://msdn.microsoft.com/library/azure/jj156143.aspx)

[Como instalar e configurar o PowerShell do Azure](../install-configure-powershell.md)

[Como fazer logon em uma máquina virtual que executa o Linux](virtual-machines-linux-how-to-log-on.md)

[Usar o Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas em Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)

<!---HONumber=AcomDC_0128_2016-->