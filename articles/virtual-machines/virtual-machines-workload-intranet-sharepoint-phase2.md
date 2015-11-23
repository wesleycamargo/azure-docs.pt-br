<properties
	pageTitle="Farm do SharePoint Server 2013 Fase 2 | Microsoft Azure"
	description="Crie e configure os dois controladores de domínio de réplica do Active Directory na fase 2 do farm do SharePoint Server 2013 no Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/20/2015"
	ms.author="josephd"/>

# Fase 2 da carga de trabalho do farm da intranet do SharePoint: configurar controladores de domínio

[AZURE.INCLUDE [learn-about-deployment-models-classic-include](../../includes/learn-about-deployment-models-classic-include.md)]Modelo de implantação do Gerenciador de Recursos.

Nesta fase de implantação de um farm do SharePoint 2013 apenas para intranet com Grupos de disponibilidade AlwaysOn do SQL Server nos serviços de infraestrutura do Azure, você configura dois controladores de domínio na rede virtual do Azure no Gerenciamento de Serviços. As solicitações da Web do cliente por recursos de farm do SharePoint podem ser autenticadas na rede virtual do Azure, em vez de enviar esse tráfego de autenticação pela conexão de VPN ou Rota Expressa do Azure à rede local.

Conclua esta fase antes de passar para a [Fase 3](virtual-machines-workload-intranet-sharepoint-phase3.md). Consulte [Implantando o SharePoint com Grupos de Disponibilidade AlwaysOn do SQL Server no Azure](virtual-machines-workload-intranet-sharepoint-overview.md) para conhecer todas as fases.

## Criar máquinas virtuais de controlador de domínio no Azure

Primeiro, você precisa preencher a coluna **Nome da máquina virtual** da Tabela M e modificar os tamanhos das máquinas virtuais conforme for necessário na coluna **Tamanho mínimo**.

Item | Nome da máquina virtual | Imagem da galeria | Tamanho mínimo
--- | --- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (primeiro controlador de domínio, por exemplo: DC1) | Windows Server 2012 R2 Datacenter | A2 (Médio)
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (segundo controlador de domínio, por exemplo: DC2) | Windows Server 2012 R2 Datacenter | A2 (Médio)
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (primeiro computador SQL Server, por exemplo: SQL1) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | A5
4\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (segundo computador SQL Server, por exemplo: SQL2) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | A5
5\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (nó principal para o cluster, por exemplo: MN1) | Windows Server 2012 R2 Datacenter | A1 (Pequeno)
6\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (primeiro servidor de aplicativos do SharePoint, por exemplo: APP1) | Avaliação do Microsoft SharePoint Server 2013 – Windows Server 2012 R2 | A4 (Extra grande)
7\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (segundo servidor de aplicativos do SharePoint, por exemplo: APP2) | Avaliação do Microsoft SharePoint Server 2013 – Windows Server 2012 R2 | A4 (Extra grande)
8\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (primeiro servidor Web do SharePoint, por exemplo: WEB1) | Avaliação do Microsoft SharePoint Server 2013 – Windows Server 2012 R2 | A4 (Extra grande)
9\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (segundo servidor Web do SharePoint, por exemplo: WEB2) | Avaliação do Microsoft SharePoint Server 2013 – Windows Server 2012 R2 | A4 (Extra grande)

**Tabela M: Máquinas virtuais para o farm de intranet do SharePoint 2013 no Azure**

Para obter a lista completa de tamanhos de máquina virtual, consulte[Tamanhos das máquinas virtuais](virtual-machines-size-specs.md).

Use o seguinte bloco de comandos do Azure PowerShell para criar as máquinas virtuais para os dois controladores de domínio. Especifique os valores para as variáveis, removendo os caracteres < and >. Observe que esse conjunto de comandos do Azure PowerShell usa os valores das seguintes opções:

- Tabela M para as máquinas virtuais
- Tabela V para as configurações da rede virtual
- Tabela S para a sub-rede
- Tabela A para os conjuntos de disponibilidade
- Tabela C para os serviços de nuvem

Lembre-se de que você definiu as Tabelas V, S, A e C na [Fase 1: Configurar o Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).

Quando você tiver fornecido a todos os valores adequados, execute o bloco resultante no prompt de comando do Azure PowerShell.

	# Create the first domain controller
	$vmName="<Table M – Item 1 - Virtual machine name column>"
	$vmSize="<Table M – Item 1 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 1 – Availability set name column>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred=Get-Credential –Message "Type the name and password of the local administrator account for the first domain controller."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None

	$subnetName="<Table S – Item 1 – Subnet name column>"
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	$vm1 | Set-AzureStaticVNetIP -IPAddress <Table V – Item 6 – Value column>

	$serviceName="<Table C – Item 1 – Cloud service name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the second domain controller
	$vmName="<Table M – Item 2 - Virtual machine name column>"
	$vmSize="<Table M – Item 2 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred=Get-Credential –Message "Type the name and password of the local administrator account for the second domain controller."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	$vm1 | Set-AzureStaticVNetIP -IPAddress <Table V – Item 7 – Value column>

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

## Configurar o primeiro controlador de domínio

Faça logon no primeiro computador do controlador de domínio usando as credenciais da conta de administrador local.

### <a id="logon"></a>Para fazer logon em uma máquina virtual usando uma conexão de Área de Trabalho Remota

1.	No Portal do Azure, no painel esquerdo, clique em **Máquinas Virtuais**.
2.	Para se conectar a uma VM, clique em **Em execução** na coluna **Status** ao lado do nome.
3.	Na barra de comandos localizada na parte inferior da página, clique em **Conectar**.
4.	O portal informa que o arquivo .rdp está sendo recuperado. Clique em **OK**.
5.	A caixa de diálogo do navegador será exibida com a pergunta: "Deseja abrir ou salvar ComputerName.rdp de manage.windowsazure.com?" Clique em **Abrir**.
6.	Na caixa de diálogo **Conexão de Área de Trabalho Remota**, clique em **Conectar**.
7.	Na caixa de diálogo **Segurança do Windows**, clique em **Usar outra conta**.
8.	Em **Nome de usuário**, digite o nome da VM e o nome de usuário da conta de administrador local criada com a máquina virtual (uma conta de máquina local). Use o seguinte formato: *NomeDoComputador**NomeDaContaDoAdministradorLocal*
9.	Em **Senha**, digite a senha da conta de administrador local.
10.	Clique em **OK**.
11.	Na caixa de diálogo **Conexão de Área de Trabalho Remota**, clique em **Sim**. A área de trabalho da nova máquina é exibida em uma janela de sessão de Área de Trabalho Remota.

Em seguida, você deverá adicionar o disco de dados adicional ao primeiro controlador de domínio.

### <a id="datadisk"></a>Para inicializar um disco vazio

1.	No painel esquerdo do Gerenciador do Servidor, clique em **Serviços de Arquivo e Armazenamento** e, em seguida, clique em **Discos**.
2.	No painel de conteúdo, no grupo **Discos**, clique em disco **2** (com a **Partição** definida como **Desconhecida**).
3.	Clique em **Tarefas**, e, em seguida, em **Novo Volume**.
4.	Na página **Antes de começar** do Assistente de Novo Volume, clique em **Avançar**.
5.	Na página **Selecione o servidor e o disco**, clique em **Disco 2** e, em seguida, em **Avançar**. Quando solicitado, clique em **OK**.
6.	Na página **Especifique o tamanho do volume**, clique em **Avançar**.
7.	Na página **Atribuir a uma letra da unidade ou pasta**, clique em **Avançar**.
8.	Na página **Selecionar configurações do sistema de arquivos**, clique em **Avançar**.
9.	Na página **Confirmar seleções**, clique em **Criar**.
10.	Quando a inicialização estiver concluída, clique em **Fechar**.

Em seguida, teste a conectividade do primeiro controlador de domínio para os locais na rede da sua organização.

### <a id="testconn"></a>Para testar a conectividade

1.	Na área de trabalho, abra um prompt de comando do Windows PowerShell.
2.	Use o comando **ping** para executar ping em nomes e endereços IP de recursos na rede da organização.

Esse procedimento garante que a resolução de nome DNS esteja funcionando corretamente (que a máquina virtual esteja configurada corretamente com os servidores DNS locais) e que pacotes possam ser enviados para e da rede virtual entre locais.

Em seguida, no prompt de comando do Windows PowerShell do primeiro controlador de domínio, execute os seguintes comandos:

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

O computador será reiniciado.

## Configurar o segundo controlador de domínio

Faça logon no segundo computador do controlador de domínio usando as credenciais da conta de administrador local. Para obter instruções, consulte [Fazendo logon em uma máquina virtual com procedimento de conexão de Área de Trabalho Remota](#logon).

Em seguida, você deverá adicionar o disco de dados adicional ao segundo controlador de domínio. Consulte [Procedimento para inicializar um disco vazio](#datadisk).

Em seguida, teste sua conectividade com os locais na rede da sua organização do segundo controlador de domínio. Consulte o [Procedimento para testar a conectividade](#testconn). Use esse procedimento para garantir que a resolução de nome DNS esteja funcionando corretamente (que a máquina virtual esteja configurada corretamente com os servidores DNS locais) e que pacotes possam ser enviados para e da rede virtual entre locais.

Em seguida, no prompt de comando do Windows PowerShell do segundo controlador de domínio, execute os seguintes comandos:

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

O computador será reiniciado.

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

Em seguida, atualize os servidores DNS da sua rede virtual para que o Azure atribua às máquinas virtuais os endereços IP dos dois novos controladores de domínio para que eles sejam usados como seus servidores DNS. Observe que esse procedimento usa os valores da Tabela V (para as configurações da rede virtual).

1.	No painel esquerdo do Portal do Azure, clique em **Redes** e, em seguida, clique no nome de sua rede virtual (Tabela V – Item 1 – coluna Valor).
2.	Clique em **Configurar**.
3.	Em **Servidores DNS**, remova as entradas correspondentes aos servidores DNS que estão na rede local.
4.	Em **Servidores DNS**, adicione duas entradas com nomes amigáveis e os endereços IP dos seguintes itens de tabela:
 - Tabela V – Item 6 – coluna Valor
 - Tabela V – Item 7 – coluna Valor
5.	Na barra de comandos na parte inferior, clique em **Salvar**.
6.	No painel esquerdo do Portal do Azure, clique em **Máquinas Virtuais** e clique na coluna **Status** ao lado do nome do seu primeiro controlador de domínio.
7.	Na barra de comandos, clique em **Reiniciar**.
8.	Quando o primeiro controlador de domínio for iniciado, clique na coluna **Status** ao lado do nome do seu segundo controlador de domínio.
9.	Na barra de comandos, clique em **Reiniciar**. Aguarde até que o segundo controlador de domínio seja iniciado.

Observe que você reinicia os dois controladores de domínio para que eles não sejam configurados com os servidores DNS locais como servidores DNS. Como ambos são servidores DNS, eles são configurados automaticamente com os servidores DNS locais como encaminhadores DNS quando são promovidos a controladores de domínio.

Em seguida, você precisa criar um site de replicação do Active Directory para garantir que servidores na rede virtual do Azure usem os controladores de domínio locais. Faça logon no controlador de domínio primário com a conta sp\_install e execute os seguintes comandos em um prompt de comando de nível de administrador do Windows PowerShell:

	$vnet="<Table V – Item 1 – Value column>"
	$vnetSpace="<Table V – Item 5 – Value column>"
	New-ADReplicationSite -Name $vnet
	New-ADReplicationSubnet –Name $vnetSpace –Site $vnet

Este diagrama mostra a configuração resultante da conclusão bem-sucedida desta fase, com nomes de computador de espaço reservado.

![](./media/virtual-machines-workload-intranet-sharepoint-phase2/workload-spsqlao_02.png)

## Próxima etapa

Para continuar a configuração dessa carga de trabalho, vá para a [Fase 3: configurar a infraestrutura do SQL Server](virtual-machines-workload-intranet-sharepoint-phase3.md).

## Recursos adicionais

[Implantação do SharePoint com Grupos de Disponibilidade AlwaysOn do SQL Server no Azure](virtual-machines-workload-intranet-sharepoint-overview.md)

[Farms do SharePoint hospedados nos serviços de infraestrutura do Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Infográfico do SharePoint com SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Arquiteturas do Microsoft Azure para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Diretrizes de implementação dos Serviços de Infraestrutura do Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Carga de trabalho dos Serviços de Infraestrutura do Azure: aplicativo de linha de negócios de alta disponibilidade](virtual-machines-workload-high-availability-lob-application.md)

<!---HONumber=Nov15_HO3-->