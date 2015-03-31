<properties 
	pageTitle="Usar o PowerShell do Azure para criar e pré-configurar máquinas virtuais baseadas em Windows" 
	description="Aprenda a usar o PowerShell do Azure para criar e pré-configurar máquinas virtuais do Azure baseadas em Windows." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="josephd"/>

#Usar o PowerShell do Azure para criar e pré-configurar máquinas virtuais baseadas em Windows

Estas etapas mostram como personalizar um conjunto de comandos do PowerShell do Azure que criam e pré-configuram uma máquina virtual do Azure baseada em Windows usando uma abordagem de bloco de construção. Você pode usar este processo para criar rapidamente um conjunto de comandos para uma nova máquina virtual baseada em Windows e expandir uma implantação existente ou criar vários conjuntos de comandos que criam rapidamente um ambiente personalizado para teste/desenvolvimento ou profissionais de TI.

Estas etapas seguem uma abordagem de preencher lacunas para criar conjuntos de comandos do PowerShell do Azure. Esta abordagem poderá ser útil se você for novo no PowerShell ou apenas quiser saber quais valores especificar para uma configuração bem-sucedida. Os usuários avançados do PowerShell podem pegar os comandos e substituí-los por seus próprios valores de variáveis (as linhas que começam com "$").

Para o tópico complementar sobre como configurar máquinas virtuais baseadas em Linux, consulte [Usar o PowerShell do Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas em Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-ps-create-preconfigure-linux-vms/).

##Etapa 1: Instale o Azure PowerShell

Se ainda não fez isso, use as instruções em [Como instalar e configurar o PowerShell do Azure](../install-configure-powershell/) para instalar o PowerShell Azure em seu computador local. Em seguida, abra um prompt de comando do PowerShell do Azure.

##Etapa 2: Definir a assinatura e a conta de armazenamento

Defina a assinatura e a conta de armazenamento do Azure executando estes comandos no prompt de comando do PowerShell do Azure. Substitua tudo que estiver entre aspas, inclusive os caracteres < e >, pelos nomes corretos.

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr -Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Você pode obter o nome de assinatura correto na propriedade SubscriptionName na saída do comando **Get-AzureSubscription**. Você pode obter o nome de conta de armazenamento correto na propriedade Label na saída do comando **Get-AzureStorageAccount**, após emitir o comando **Select-AzureSubscription**. Você também pode armazenar esses comandos em um arquivo de texto para uso futuro.

##Etapa 3: Determinar a ImageFamily

Em seguida, você precisa determinar o valor de ImageFamily ou Label para a imagem específica correspondente à máquina virtual do Azure que deseja criar. Aqui estão alguns exemplos da Galeria no Portal de Gerenciamento do Azure.

![](./media/virtual-machines-use-PS-create-preconfigure-windows-vms/PSPreconfigWindowsVMs_1.png)
 
Você pode obter a lista de valores de ImageFamily disponíveis com este comando.

	Get-AzureVMImage | select ImageFamily -Unique

Aqui estão alguns exemplos de valores de ImageFamily para computadores baseados em Windows:

- Windows Server 2012 R2 Datacenter 
- Windows Server 2008 R2 SP1 
- Visualização técnica do Windows Server 
- SQL Server 2012 SP1 Enterprise em Windows Server 2012 

Se você encontrar a imagem que você está procurando, abra uma nova instância do editor de texto de sua escolha e copie o seguinte para o novo arquivo de texto, substituindo o valor de ImageFamily. 

	$family="<ImageFamily value>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

Em alguns casos, o nome da imagem está na propriedade Label, e não no valor de ImageFamily. Se você não encontrar a imagem que você está procurando usando a propriedade ImageFamily, liste as imagens por sua propriedade Label com este comando.

	Get-AzureVMImage | select Label -Unique

Se você encontrar a imagem certa com este comando, abra uma nova instância do editor de texto de sua escolha e copie o seguinte para o novo arquivo de texto, substituindo o valor de Label. 

	$label="<Label value>"
	$image = Get-AzureVMImage | where { $_.Label -eq $label } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

##Etapa 4: Criar o conjunto de comandos

Crie o restante do seu conjunto de comandos, copiando o conjunto apropriado de blocos abaixo para o novo arquivo de texto e, em seguida, preencha os valores das variáveis e remova os caracteres < e >. Consulte os dois [exemplos](#examples) no final deste artigo para ter uma ideia do resultado final.

Comece seu conjunto de comandos escolhendo um destes dois blocos de comandos (obrigatório).


Opção 1: Especifique um nome e um tamanho de máquina virtual.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Opção 2: Especifique um nome, o tamanho e o nome do conjunto de disponibilidade.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availset="<set name>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

Para os valores de InstanceSize para máquinas virtuais da série D, DS ou G, consulte [Tamanhos de máquina virtual e serviços de nuvem no Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Opcionalmente, para um computador Windows autônomo, especifique a conta de administrador local e a senha.

	$localadminusername="<local administrator account name>"
	$localadminpassword="<local administrator account password>"
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $localadminusername -Password $localadminpassword

Opcionalmente, para adicionar o computador Windows a um domínio do Active Directory existente, especifique a conta e a senha de administrador local, o domínio e o nome e a senha de uma conta de domínio.

	$localadminusername="<local administrator account name>"
	$localadminpassword="<local administrator account password>"
	$domaindns="<FQDN of the domain that the machine is joining>"
	$domacctdomain="<domain of the account that has permission to add the machine to the domain>"
	$domacctname="<domain account name that has permission to add the machine to the domain>"
	$domacctpassword="<password of the domain account that has permission to add the machine to the domain>"
	$vm1 | Add-AzureProvisioningConfig -AdminUserName $localadminusername -Password $localadminpassword -WindowsDomain -Domain $domacctdomain -DomainUserName $domacctname -DomainPassword $domacctpassword -JoinDomain $domaindns

Observe que as duas opções exigem que você especifique senhas e nomes de conta. Se você estiver salvando o conjunto de comandos em um arquivo, armazene-o em um local seguro para protegê-los.

Para opções adicionais de pré-configuração para máquinas virtuais baseadas em Windows, consulte a sintaxe para os conjuntos de parâmetros **Windows** e **WindowsDomain** em [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx).

Opcionalmente, atribua um endereço IP específico, conhecido como um DIP estático, à máquina virtual.

	$vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

Você pode verificar se um endereço IP específico está disponível com:

	Test-AzureStaticVNetIP -VNetName <VNet name> -IPAddress <IP address>

Opcionalmente, atribua a máquina virtual a uma sub-rede específica de uma rede virtual do Azure.

	$vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

Opcionalmente, adicione um disco de dados único à máquina virtual. 

	$disksize=<size of the disk in GB>
	$disklabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$hcaching="<Specify one: ReadOnly, ReadWrite, None>"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

Para um controlador de domínio Active Directory, defina $hcaching como "None".

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

Finalmente, inicie o processo de criação de máquina virtual escolhendo um destes blocos de comandos (obrigatório).

Opção 1: Criar a máquina virtual em um novo serviço de nuvem. 

	New-AzureVM -Location "<An Azure location, such as US West>" -VMs $vm1

Opção 2: Criar a máquina virtual em um serviço de nuvem existente. 

	New-AzureVM -ServiceName "<short name of the cloud service>" -VMs $vm1

O nome curto do serviço de nuvem é o nome que aparece na lista de Serviços de Nuvem no Portal de Gerenciamento do Azure ou na lista de Grupos de Recursos no Portal de Visualização do Azure. 

Opção 3: Criar a máquina virtual em um serviço de nuvem e em uma rede virtual existentes.

	$svcname="<short name of the cloud service>"
	$vnetname="<name of the virtual network>"
	New-AzureVM -ServiceName $svcname -VMs $vm1 -VNetName $vnetname

##Etapa 5: Executar o conjunto de comandos

Revise o conjunto de comandos do PowerShell do Azure criado em seu editor de texto, que consiste em vários blocos de comandos da etapa 4. Certifique-se de que você especificou todas as variáveis necessárias e que elas tenham os valores corretos. Verifique também se você removeu todos os caracteres < e >.

Copie o conjunto de comandos para a área de transferência e clique com o botão direito no prompt de comando aberto do PowerShell do Azure. Isto emitirá o conjunto de comandos como uma série de comandos do PowerShell e criará sua máquina virtual do Azure.

Se pretender criar novamente essa máquina virtual ou uma semelhante, você poderá: 

- Salvar este conjunto de comandos como um arquivo de texto ou como um arquivo de script do PowerShell (*. ps1)
- Salvar este conjunto de comandos como um runbook de automação do Azure, na seção **Automação** do Portal de Gerenciamento do Azure 

##<a id="examples"></a>Exemplos

Aqui estão dois exemplos de uso das etapas acima para criar conjuntos de comandos do PowerShell do Azure que criam máquinas virtuais do Azure baseadas em Windows.

###Exemplo 1

Preciso de um conjunto de comandos do PowerShell para criar a máquina virtual inicial para um controlador de domínio Active Directory que:

- Usa a imagem do Windows Server 2012 R2 Datacenter
- Tem o nome de AZDC1 
- É um computador autônomo
- Tem um disco de dados adicional de 20 GB
- Tem o endereço IP estático 192.168.244.4
- Está na sub-rede de Back-end da rede virtual AZDatacenter
- Está no serviço de nuvem Azure-TailspinToys

Aqui está o conjunto de comandos do PowerShell do Azure correspondente para criar essa máquina virtual, com linhas em branco entre cada bloco para facilitar a leitura.

	$family="Windows Server 2012 R2 Datacenter"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vmname="AZDC1"
	$vmsize="Medium"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$localadminusername="DCLocalAdmin"
	$localadminpassword="DCeq7294*"
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $localadminusername -Password $localadminpassword

	$vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

	$disksize=20
	$disklabel="DCData"
	$lun=0
	$hcaching="None"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM -ServiceName $svcname -VMs $vm1 -VNetName $vnetname

###Exemplo 2

Preciso de um conjunto de comandos do PowerShell para criar uma máquina virtual para um servidor de linha de negócios que:

- Usa a imagem do Windows Server 2012 R2 Datacenter
- Tem o nome de LOB1
- É um membro do domínio corp.contoso.com
- Tem um disco de dados adicional de 200 GB 
- Está na sub-rede de Front-end da rede virtual AZDatacenter
- Está no serviço de nuvem Azure-TailspinToys

Aqui está o conjunto de comandos do PowerShell do Azure correspondente para criar essa máquina virtual.

	$family="Windows Server 2012 R2 Datacenter"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vmname="LOB1"
	$vmsize="Large"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$localadminusername="LOBLocalAdmin"
	$localadminpassword="LOBmx7137*"
	$domacctdomain="CORP"
	$domacctname="admin7"
	$domacctpassword="frank0987&"
	$domaindns="corp.contoso.com"
	$vm1 | Add-AzureProvisioningConfig -AdminUserName $localadminusername -Password $ localadminpassword -WindowsDomain -Domain $domacctdomain -DomainUserName $domacctname -DomainPassword $domacctpassword -JoinDomain $domaindns

	$vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

	$disksize=200
	$disklabel="LOBData"
	$lun=0
	$hcaching="ReadWrite"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM -ServiceName $svcname -VMs $vm1 -VNetName $vnetname


##Recursos adicionais

[Documentação de máquinas virtuais](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Perguntas frequentes sobre máquinas virtuais do Azure](http://msdn.microsoft.com/library/azure/dn683781.aspx)

[Visão geral das máquinas virtuais do Azure](http://msdn.microsoft.com/library/azure/jj156143.aspx)

[Como instalar e configurar o PowerShell do Azure](../install-configure-powershell/)

[Usar o PowerShell do Azure para criar e pré-configurar máquinas virtuais baseadas em Linux](../virtual-machines-ps-create-preconfigure-linux-vms/)

<!--HONumber=47-->
