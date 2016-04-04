<properties
	pageTitle="Farm do SharePoint Server 2013 Fase 2 | Microsoft Azure"
	description="Crie e configure os dois controladores de domínio de réplica do Active Directory na fase 2 do farm do SharePoint Server 2013 no Azure."
	documentationCenter=""
	services="virtual-machines-windows"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="josephd"/>

# Fase 2 da carga de trabalho do farm da intranet do SharePoint: configurar controladores de domínio

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

Nesta fase de implantação de um farm do SharePoint 2013 apenas para intranet com Grupos de disponibilidade AlwaysOn do SQL Server nos serviços de infraestrutura do Azure, você configura dois controladores de domínio na rede virtual do Azure. As solicitações da Web do cliente por recursos de farm do SharePoint podem ser autenticadas na rede virtual do Azure, em vez de enviar esse tráfego de autenticação pela conexão de VPN site a site ou Rota Expressa do Azure para sua rede local.

Conclua esta fase antes de passar para a [Fase 3](virtual-machines-windows-ps-sp-intranet-ph3.md). Consulte [Implantando o SharePoint com Grupos de Disponibilidade AlwaysOn do SQL Server no Azure](virtual-machines-windows-sp-intranet-overview.md) para conhecer todas as fases.

## Criar máquinas virtuais de controlador de domínio no Azure

Primeiro, você precisa preencher a coluna **Nome da máquina virtual** da Tabela M e modificar os tamanhos das máquinas virtuais conforme for necessário na coluna **Tamanho mínimo**.

Item | Nome da máquina virtual | Imagem da galeria | Tamanho mínimo
--- | --- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (primeiro controlador de domínio, por exemplo: DC1) | Windows Server 2012 R2 Datacenter | Standard\_A2
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (segundo controlador de domínio, por exemplo: DC2) | Windows Server 2012 R2 Datacenter | Standard\_A2
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (primeiro computador SQL Server, por exemplo: SQL1) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | Standard\_A5
4\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (segundo computador SQL Server, por exemplo: SQL2) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | Standard\_A5
5\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (nó principal para o cluster, por exemplo: MN1) | Windows Server 2012 R2 Datacenter | Standard\_A1
6\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (primeiro servidor de aplicativos do SharePoint, por exemplo: APP1) | Avaliação do Microsoft SharePoint Server 2013 – Windows Server 2012 R2 | Standard\_A4
7\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (segundo servidor de aplicativos do SharePoint, por exemplo: APP2) | Avaliação do Microsoft SharePoint Server 2013 – Windows Server 2012 R2 | Standard\_A4
8\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (primeiro servidor Web do SharePoint, por exemplo: WEB1) | Avaliação do Microsoft SharePoint Server 2013 – Windows Server 2012 R2 | Standard\_A4
9\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (segundo servidor Web do SharePoint, por exemplo: WEB2) | Avaliação do Microsoft SharePoint Server 2013 – Windows Server 2012 R2 | Standard\_A4

**Tabela M: Máquinas virtuais para o farm de intranet do SharePoint 2013 no Azure**

Para obter a lista completa de tamanhos de máquina virtual, consulte[Tamanhos das máquinas virtuais](virtual-machines-linux-sizes.md).

Use o seguinte bloco de comandos do Azure PowerShell para criar as máquinas virtuais para os dois controladores de domínio. Especifique os valores para as variáveis, removendo os caracteres < and >. Observe que esse conjunto de comandos do Azure PowerShell usa os valores das seguintes opções:

- Tabela M para as máquinas virtuais
- Tabela V para as configurações da rede virtual
- Tabela S para a sub-rede
- Tabela ST para suas contas de armazenamento
- Tabela A para os conjuntos de disponibilidade

Lembre-se de que você definiu as Tabelas V, S, ST e A na [Fase 1: configurar o Azure](virtual-machines-windows-ps-sp-intranet-ph1.md).

> [AZURE.NOTE] O comando a seguir define o uso do Azure PowerShell 1.0 e posterior. Para obter mais informações, consulte [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Quando você tiver fornecido a todos os valores adequados, execute o bloco resultante no prompt do Azure PowerShell.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$saName="<Table ST – Item 1 – Storage account name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	$avName="<Table A – Item 1 – Availability set name column>"
	
	# Create the first domain controller
	$vmName="<Table M – Item 1 - Virtual machine name column>"
	$vmSize="<Table M – Item 1 - Minimum size column>"
	$staticIP="<Table V – Item 6 - Value column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id -PrivateIpAddress $staticIP
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for AD DS data in GB>
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name "ADDSData" -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first domain controller." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second domain controller
	$vmName="<Table M – Item 2 - Virtual machine name column>"
	$vmSize="<Table M – Item 2 - Minimum size column>"
	$staticIP="<Table V – Item 7 - Value column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id -PrivateIpAddress $staticIP
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for AD DS data in GB>
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name "ADDSData" -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second domain controller." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE] Como essas máquinas virtuais são para um aplicativo de intranet, elas não recebem um endereço IP público ou um rótulo de nome de domínio DNS e não são expostas na Internet. No entanto, isso também significa que você não pode se conectar a eles no portal do Azure. O botão **Conectar** não ficará disponível quando você exibir as propriedades da máquina virtual. Use o acessório Conexão de Área de Trabalho Remota ou outra ferramenta da Área de Trabalho Remota para se conectar à máquina virtual usando o endereço IP privado ou o nome DNS da intranet.

## Configurar o primeiro controlador de domínio

Use o cliente de área de trabalho remota de sua escolha e crie uma conexão de área de trabalho remota para a primeira máquina virtual do controlador de domínio. Use seu nome DNS ou do computador da intranet e as credenciais da conta de administrador local.

Em seguida, você deverá adicionar o disco de dados adicional ao primeiro controlador de domínio.

### <a id="datadisk"></a>Para inicializar um disco vazio

1.	No painel esquerdo do Gerenciador do Servidor, clique em **Serviços de Arquivo e Armazenamento** e, em seguida, clique em **Discos**.
2.	No painel de conteúdo, no grupo **Discos**, clique em disco **2** (com a **Partição** definida como **Desconhecida**).
3.	Clique em **Tarefas**, e, em seguida, em **Novo Volume**.
4.	Na página Antes de começar do Assistente de Novo Volume, clique em **Avançar**.
5.	Na página Selecione o servidor e o disco, clique em **Disco 2** e, em seguida, em **Avançar**. Quando solicitado, clique em OK.
6.	Na página Especifique o tamanho do volume, clique em **Avançar**.
7.	Na página Atribuir a uma letra da unidade ou pasta, clique em **Avançar**.
8.	Na página Selecionar configurações do sistema de arquivos, clique em **Avançar**.
9.	Na página Confirmar seleções, clique em **Criar**.
10.	Ao concluir, clique em **Fechar**.

Em seguida, teste a conectividade do primeiro controlador de domínio para os locais na rede da sua organização.

### <a id="testconn"></a>Para testar a conectividade

1.	Na área de trabalho, abra um prompt do Windows PowerShell.
2.	Use o comando **ping** para executar ping em nomes e endereços IP de recursos na rede da organização.

Esse procedimento garante que a resolução de nome DNS esteja funcionando corretamente (que a máquina virtual esteja configurada corretamente com os servidores DNS locais) e que pacotes possam ser enviados para e da rede virtual entre locais. Se esse teste básico falhar, entre em contato com seu departamento de TI para solucionar problemas de resolução de nome DNS e entrega de pacotes.

Em seguida, no prompt de comando do Windows PowerShell do primeiro controlador de domínio, execute os seguintes comandos:

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

Você será solicitado a fornecer as credenciais de uma conta de administrador de domínio. O computador será reiniciado.

## Configurar o segundo controlador de domínio

Use o cliente de área de trabalho remota de sua escolha e crie uma conexão de área de trabalho remota para a segunda máquina virtual do controlador de domínio. Use seu nome DNS ou do computador da intranet e as credenciais da conta de administrador local.

Em seguida, você deverá adicionar o disco de dados adicional ao segundo controlador de domínio. Consulte [Procedimento para inicializar um disco vazio](#datadisk).

Em seguida, no prompt do Windows PowerShell do segundo controlador de domínio, execute os seguintes comandos:

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

Você será solicitado a fornecer as credenciais de uma conta de administrador de domínio. O computador será reiniciado.

Em seguida, você precisa atualizar os servidores DNS da sua rede virtual para que o Azure atribua às máquinas virtuais os endereços IP dos dois novos controladores de domínio para que eles sejam usados como seus servidores DNS. Observe que esse procedimento usa valores das Tabelas V (para as configurações de rede virtual) e M (para suas máquinas virtuais).

1.	No painel esquerdo do Portal do Azure, clique em **Redes virtuais** e, em seguida, clique no nome de sua rede virtual (Tabela V – Item 1 – coluna Valor).
2.	No painel de **Configurações**, clique em **Servidores DNS**.
3.	No painel **Servidores DNS**, digite o seguinte:
	- Para **Servidor DNS primário**: Tabela V - Item 6 - coluna Valor
	- Para **Servidor DNS secundário**: Tabela V - Item 7 - coluna Valor
4.	No Portal do Azure, no painel esquerdo, clique em **Máquinas virtuais**.
5.	No **Painel de máquinas virtuais**, clique no nome do seu primeiro controlador de domínio (Tabela M – Item 1 - coluna Nome da máquina virtual).
6.	No painel da máquina virtual, clique em **Reiniciar**.
7.	Quando o primeiro controlador de domínio for iniciado, clique no nome do segundo controlador de domínio no painel **Máquinas virtuais** (Tabela M – Item 2 - Coluna de nome de máquina virtual).
8.	No painel da máquina virtual, clique em **Reiniciar**. Aguarde até que o segundo controlador de domínio seja iniciado.

Observe que podemos reiniciar os dois controladores de domínio para que eles não sejam configurados com os servidores DNS locais como servidores DNS. Como ambos são servidores DNS, eles são configurados automaticamente com os servidores DNS locais como encaminhadores DNS quando são promovidos a controladores de domínio.

Em seguida, precisamos criar um site de replicação do Active Directory para garantir que servidores na rede virtual do Azure usem os controladores de domínio locais. Faça logon no controlador de domínio primário com uma conta de administrador do domínio e execute os seguintes comandos em um prompt de nível de administrador do Windows PowerShell:

	$vnet="<Table V – Item 1 – Value column>"
	$vnetSpace="<Table V – Item 5 – Value column>"
	New-ADReplicationSite -Name $vnet 
	New-ADReplicationSubnet –Name $vnetSpace –Site $vnet

## Configurar as permissões e contas do farm do SharePoint

O farm do SharePoint precisará das seguintes contas de usuário:

- sp\_farm: uma conta de usuário gerenciar os farms do SharePoint.
- sp\_farm\_db: uma conta de usuário com direitos sysadmin em instâncias do SQL Server.
- sp\_install: uma conta de usuário com os direitos de administração de domínio necessários para instalar funções e recursos.
- sqlservice: uma conta de usuário que pode ser executada como instâncias do SQL Server.

Em seguida, faça logon em qualquer computador com uma conta de administrador de domínio para o domínio do qual os controladores de domínio são membros, abra um prompt de comando de nível de administrador do Windows PowerShell e execute esses comandos *um por vez*:

	New-ADUser -SamAccountName sp_farm -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_farm" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

	New-ADUser -SamAccountName sp_farm_db -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_farm_db" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

	New-ADUser -SamAccountName sp_install -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_install" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

	New-ADUser -SamAccountName sqlservice -AccountPassword (read-host "Set user password" -assecurestring) -name "sqlservice" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

Para cada comando, será solicitado que você insira uma senha. Registre os nomes das contas e as senhas e armazene-as em um local seguro.

Em seguida, execute as seguintes etapas para adicionar outras propriedades de conta às novas contas de usuário.

1.	Na tela inicial, digite **Usuários do Active Directory** e, em seguida, clique em **Usuários e Computadores do Active Directory**.
2.	No painel de árvore, abra seu domínio e clique em **Usuários**.
3.	No painel de conteúdo, clique com botão direito em **sp\_install** e, em seguida, clique em **Adicionar a um grupo**.
4.	Na caixa de diálogo **Selecionar Grupos**, digite **administradores do domínio** e, em seguida, clique em **OK** duas vezes.
5.	Na caixa de diálogo, clique em **Exibição e em Recursos Avançados**. A opção permite que você veja todos os contêineres e guias ocultas nas janelas de propriedade para objetos do Active Directory.
6.	Clique no nome do domínio com o botão direito e clique em **Propriedades**.
7.	Na caixa de diálogo **Propriedades**, clique na guia **Segurança** e, em seguida, clique no botão **Avançado**.
8.	Na janela **Configurações de Segurança Avançadas para <YourDomain>**, clique em **Adicionar**.
9.	Na janela **Entrada de Permissão para <YourDomain>**, clique em **Selecionar uma entidade de segurança**.
10.	Na caixa de texto, digite **<YourDomain>\\sp\_install** e, em seguida, clique em **OK**.
11.	Selecione **Permitir** para **Criar objetos de computador** e clique em **OK** três vezes.

Isso mostra a configuração resultante da conclusão bem-sucedida desta fase com nomes de computador de espaço reservado.

![](./media/virtual-machines-windows-ps-sp-intranet-ph2/workload-spsqlao_02.png)

## Próxima etapa

- Use a [Fase 3](virtual-machines-windows-ps-sp-intranet-ph3.md) para continuar com a configuração desta carga de trabalho.

<!---HONumber=AcomDC_0323_2016-->