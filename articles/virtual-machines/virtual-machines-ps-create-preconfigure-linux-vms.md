<properties
	pageTitle="Usar o Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas em Linux"
	description="Aprenda a usar o PowerShell do Azure para criar e pré-configurar máquinas virtuais do Azure baseadas em Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="cynthn"/>

# Usar o Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas em Linux

> [AZURE.SELECTOR]
- [Azure CLI](virtual-machines-linux-tutorial.md)
- [PowerShell](virtual-machines-ps-create-preconfigure-linux-vms.md)

Estas etapas mostram como personalizar um conjunto de comandos do Azure PowerShell que criam e pré-configuram uma máquina virtual do Azure baseada em Linux no Gerenciamento de Serviços usando uma abordagem de bloco de construção. Você pode usar este processo para criar rapidamente um conjunto de comandos para uma nova máquina virtual baseada em Linux e expandir uma implantação existente ou criar vários conjuntos de comandos que criam rapidamente um ambiente personalizado para teste/desenvolvimento ou profissionais de TI.

Estas etapas seguem uma abordagem de preencher lacunas para criar conjuntos de comandos do Azure PowerShell. Esta abordagem poderá ser útil se você for novo no Azure PowerShell ou apenas quiser saber quais valores especificar para uma configuração bem-sucedida. Os usuários avançados do Azure PowerShell podem pegar os comandos e substituí-los por seus próprios valores de variáveis (as linhas que começam com "$").

Para o tópico complementar sobre como configurar máquinas virtuais baseadas em Windows, confira [Usar o Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas em Windows](virtual-machines-ps-create-preconfigure-windows-vms.md).

## Etapa 1: instalar o Azure PowerShell

Se você ainda não fez isso, use as instruções em [Como instalar e configurar o PowerShell do Azure](../install-configure-powershell.md) para instalar o PowerShell do Azure no computador local. Em seguida, abra um prompt de comando do PowerShell do Azure.

## Etapa 2: definir a assinatura e a conta de armazenamento

Defina a assinatura e a conta de armazenamento do Azure executando os comandos a seguir no prompt de comando do Azure PowerShell. Substitua tudo que estiver entre aspas, inclusive os caracteres < and >, pelos nomes corretos.

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Você pode obter o nome de assinatura correto na propriedade **SubscriptionName** na saída do comando **Get-AzureSubscription**. Você pode obter o nome de conta de armazenamento correto na propriedade **Label** na saída do comando **Get-AzureStorageAccount**, após emitir o comando **Select-AzureSubscription**. Você também pode armazenar esses comandos em um arquivo de texto para uso futuro.

## Etapa 3: determinar a ImageFamily

Em seguida, você precisa determinar o valor de ImageFamily para a imagem específica correspondente à máquina virtual do Azure que deseja criar. Você pode obter a lista de valores de ImageFamily disponíveis com o comando a seguir.

	Get-AzureVMImage | select ImageFamily -Unique

Aqui estão alguns exemplos de valores de ImageFamily para computadores baseados em Linux:

- Ubuntu Server 12.10
- CoreOS Alpha
- SUSE Linux Enterprise Server 12

Abra uma nova instância do editor de texto de sua escolha ou uma instância do ISE (ambiente de script integrado) do PowerShell. Copie o seguinte para o novo arquivo de texto ou o ISE do PowerShell, substituindo o valor de ImageFamily.

	$family="<ImageFamily value>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## Etapa 4: criar o conjunto de comandos

Crie o restante do seu conjunto de comandos, copiando um dos seguintes conjuntos de blocos de comandos para o novo arquivo de texto ou o ISE do PowerShell e, em seguida, preencha os valores das variáveis e remova os caracteres < and >. Veja os dois [exemplos](#examples) no final deste artigo para ter uma ideia do resultado final.

Comece seu conjunto de comandos escolhendo um destes dois blocos de comandos (obrigatório).

Opção 1: especifique um nome e um tamanho de máquina virtual.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Opção 2: especifique um nome, o tamanho e o nome do conjunto de disponibilidade.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availset="<set name>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

Para os valores de InstanceSize para máquinas virtuais da série D, DS ou G, confira [Tamanhos de máquina virtual e serviços de nuvem no Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Use os comandos a seguir para especificar o nome de usuário inicial do Linux e a senha (obrigatório). Escolha uma senha forte. Para verificar a força da senha, consulte [Verificador de senha: usando senhas fortes](https://www.microsoft.com/security/pc-security/password-checker.aspx).

	$cred=Get-Credential -Message "Type the name and password of the initial Linux account."
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

Opcionalmente, especifique um conjunto de pares de chaves SSH que já tenham sido implantadas na assinatura.

	$vm1 | Add-AzureProvisioningConfig -Linux -SSHKeyPairs "<SSH key pairs>"

Para obter mais informações, consulte [Como usar SSH com o Linux no Azure](virtual-machines-linux-use-ssh-key.md).

Opcionalmente, especifique uma lista de pares de chaves SSH que já tenham sido implantadas na assinatura.

	$vm1 | Add-AzureProvisioningConfig -Linux - SSHPublicKeys "<SSH public keys>"

Para opções adicionais de pré-configuração para máquinas virtuais baseadas em Linux, confira a sintaxe para o conjunto de parâmetros **Linux** em [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx).

Opcionalmente, atribua um endereço IP específico, conhecido como um DIP estático, à máquina virtual.

	$vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

Você pode verificar se um endereço IP específico está disponível com o comando a seguir.

	Test-AzureStaticVNetIP –VNetName <VNet name> –IPAddress <IP address>

Opcionalmente, atribua a máquina virtual a uma sub-rede específica de uma rede virtual do Azure.

	$vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

Opcionalmente, adicione um disco de dados único à máquina virtual.

	$disksize=<size of the disk in GB>
	$disklabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$hcaching="<Specify one: ReadOnly, ReadWrite, None>"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

Opcionalmente, adicione a máquina virtual a um conjunto existente de balanceamento de carga para tráfego externo.

	$prot="<Specify one: tcp, udp>"
	$localport=<port number of the internal port>
	$pubport=<port number of the external port>
	$endpointname="<name of the endpoint>"
	$lbsetname="<name of the existing load-balanced set>"
	$probeprotocol="<Specify one: tcp, udp>"
	$probeport=<TCP or UDP port number of probe traffic>
	$probepath="<URL path for probe traffic>"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

Finalmente, inicie o processo de criação de máquina virtual escolhendo um dos blocos de comandos a seguir (obrigatório).

Opção 1: criar a máquina virtual em um serviço de nuvem existente.

	New-AzureVM –ServiceName "<short name of the cloud service>" -VMs $vm1

O nome curto do serviço de nuvem é o nome que aparece na lista de Serviços de Nuvem no Portal do Azure ou na lista de grupos de recursos no Portal de Visualização do Azure.

Opção 2: criar a máquina virtual em um serviço de nuvem e em uma rede virtual existentes.

	$svcname="<short name of the cloud service>"
	$vnetname="<name of the virtual network>"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## Etapa 5: executar o conjunto de comandos

Examine o conjunto de comandos do PowerShell do Azure criado em seu editor de texto ou o ISE do PowerShell, formado por vários blocos de comandos, da Etapa 4. Certifique-se de que você especificou todas as variáveis necessárias e que elas tenham os valores corretos. Verifique também se você removeu todos os caracteres < and >.

Se você estiver usando um editor de texto, copie o conjunto de comandos para a área de transferência e clique com o botão direito no prompt de comando aberto do PowerShell do Azure. Isto emitirá o conjunto de comandos como uma série de comandos do PowerShell e criará sua máquina virtual do Azure. Como alternativa, execute o comando definido no ISE do PowerShell.

Se você criar a máquina virtual na assinatura, conta de armazenamento, serviço de nuvem, conjunto de disponibilidade, rede virtual ou sub-rede incorreta, exclua a máquina virtual, corrija a sintaxe do bloco de comandos e, em seguida, execute o conjunto de comandos corrigido.

Após a criação da máquina virtual, confira [Como fazer logon em uma máquina virtual que executa Linux](virtual-machines-linux-how-to-log-on.md).

Se pretender criar novamente essa máquina virtual ou uma semelhante, você poderá:

- Salve esse conjunto de comandos como um arquivo de script do PowerShell (*.ps1)
- Salvar este conjunto de comandos como um runbook de automação do Azure, na seção **Automação** do Portal do Azure

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

[Documentação de máquinas virtuais](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Perguntas frequentes sobre máquinas virtuais do Azure](http://msdn.microsoft.com/library/azure/dn683781.aspx)

[Visão geral das máquinas virtuais do Azure](http://msdn.microsoft.com/library/azure/jj156143.aspx)

[Como instalar e configurar o PowerShell do Azure](../install-configure-powershell.md)

[Como fazer logon em uma máquina virtual que executa o Linux](virtual-machines-linux-how-to-log-on.md)

[Usar o Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas em Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)

<!---HONumber=Sept15_HO3-->