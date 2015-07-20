<properties 
	pageTitle="Fase 4 da carga de trabalho do farm da intranet do SharePoint: Configurar servidores do SharePoint" 
	description="Na quarta fase da implantação de um farm do SharePoint 2013 somente intranet com Grupos de Disponibilidade AlwaysOn do SQL Server nos serviços de infraestrutura do Azure, você deve criar as máquinas virtuais do servidor do SharePoint e um novo farm do SharePoint." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="josephd"/>

# Fase 4 da carga de trabalho do farm da intranet do SharePoint: Configurar servidores do SharePoint

Nesta fase da implantação de um farm do SharePoint 2013 somente intranet com Grupos de Disponibilidade AlwaysOn do SQL Server nos serviços de infraestrutura do Azure, você deve criar as camadas de aplicativo e da Web do farm do SharePoint e o próprio farm com o Assistente de Configuração do SharePoint.

Conclua esta fase antes de passar para a [Fase 5](virtual-machines-workload-intranet-sharepoint-phase5.md). Consulte [Implantando o SharePoint com Grupos de Disponibilidade AlwaysOn do SQL Server no Azure](virtual-machines-workload-intranet-sharepoint-overview.md) para conhecer todas as fases.

## Criar as máquinas virtuais do servidor do SharePoint no Azure

Existem quatro máquinas virtuais do servidor do SharePoint. Duas máquinas virtuais do servidor do SharePoint destinam-se aos servidores Web front-end e duas para administração e hospedagem de aplicativos do SharePoint. Dois servidores do SharePoint em cada camada oferecem alta disponibilidade.

Use o seguinte bloco de comandos do PowerShell para criar as máquinas virtuais para os quatro servidores do SharePoint. Especifique os valores para as variáveis, removendo os caracteres < and >. Observe que esse conjunto de comandos do PowerShell usa os valores das seguintes tabelas:

- Tabela M para as máquinas virtuais
- Tabela V para as configurações da rede virtual
- Tabela S para a sub-rede
- Tabela A para os conjuntos de disponibilidade
- Tabela C para os serviços de nuvem

Lembre-se de que você definiu a tabela na [Fase 2: Configurar controladores de domínio](virtual-machines-workload-intranet-sharepoint-phase2.md) e as Tabelas V, S, A e C na [Fase 1: Configurar o Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).

Quando você tiver fornecido a todos os valores adequados, execute o bloco resultante no prompt de comando do PowerShell do Azure.

	# Create the first SharePoint application server
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 3 – Availability set name column>"
	$image= Get-AzureVMImage | where { $_.Label -eq "SharePoint Server 2013 Trial" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet
	
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SharePoint application server."
	$cred2=Get-Credential –Message "Now type the name and password of an account that has permissions to add this virtual machine to the domain."
	$ADDomainName="<name of the AD domain that the server is joining (example CORP)>"
	$domainDNS="<FQDN of the AD domain that the server is joining (example corp.contoso.com)>"
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS
	
	$subnetName="<Table 6 – Item 1 – Subnet name column>"
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	$serviceName="<Table C – Item 3 – Cloud service name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName
	
	# Create the second SharePoint application server
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet
	
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SharePoint application server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS
	
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName
	
	# Create the first SharePoint web server
	$vmName="<Table M – Item 8 - Virtual machine name column>"
	$vmSize="<Table M – Item 8 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 4 – Availability set name column>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet
	
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SharePoint web server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS
	
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName
	
	# Create the second SharePoint web server
	$vmName="<Table M – Item 9 - Virtual machine name column>"
	$vmSize="<Table M – Item 9 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet
	
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SharePoint web server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS
	
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

Use o [procedimento para fazer logon em uma máquina virtual com uma Conexão de Área de Trabalho Remota](virtual-machines-workload-intranet-sharepoint-phase2.md#logon) uma vez para cada um dos quatro servidores do SharePoint para fazer logon usando as credenciais da conta [Domínio]\\sp_farm_db criadas na [Fase 2: Configurar controladores de domínio](virtual-machines-workload-intranet-sharepoint-phase2.md).

Use o [procedimento para testar a conectividade](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn) uma vez para cada um dos quatro servidores do SharePoint para testar a conectividade para os locais na rede da sua organização.

## Configurar o farm do SharePoint

Use estas etapas para configurar o primeiro servidor do SharePoint no farm.

1.	Na área de trabalho do primeiro servidor de aplicativos do SharePoint, clique duas vezes em **Assistente de Configuração de Produtos do SharePoint 2013**. Quando solicitado a permitir que o programa faça alterações no computador, clique em **Sim**.
2.	Na página Bem-vindo aos Produtos do SharePoint, clique em **Avançar**.
3.	A caixa de diálogo **Assistente de Configuração de Produtos do SharePoint** será exibida, avisando que os serviços (como o IIS) serão reiniciados ou redefinidos. Clique em **Sim**.
4.	Na página Conectar a um farm de servidores, selecione **Criar um novo farm de servidores** e clique em **Avançar**.
5.	Na página Especificar as Configurações do Banco de Dados de Configurações:
- Em **Servidor de banco de dados**, digite o nome do SQL Server primário. 
- Em **Nome de usuário**, digite [Domínio]* * \\sp_farm_db** (criado na [Fase 2: Configurar controladores de domínio](virtual-machines-workload-intranet-sharepoint-phase2.md)). Lembre-se de que a conta sp_farm_db tem privilégios sysadmin no SQL Server. 
- Em **Senha**, digite a senha da conta sp_farm_db.
6.	Clique em **Próximo**.
7.	Na página Especificar as Configurações de Segurança do Farm, digite uma senha duas vezes. Registre a senha e armazene-a em um local seguro para referência futura. Clique em **Próximo**.
8.	Na página Configurar Aplicativo Web da Administração Central do SharePoint, clique em **Avançar**.
9.	A página Concluindo o Assistente de Configuração de Produtos do SharePoint é exibida. Clique em **Próximo**.
10.	A página Configurando Produtos do SharePoint é exibida. Aguarde até que o processo de configuração seja concluído, o que pode levar aproximadamente 8 minutos.
11.	Depois que o farm for configurado com êxito, clique em **Concluir**. O novo site de administração é iniciado.
12.	Para começar a configurar o farm do SharePoint, clique em **Iniciar o Assistente**.

Execute o procedimento a seguir no segundo servidor de aplicativos do SharePoint e nos dois servidores Web front-end.

1.	Na área de trabalho, clique duas vezes em **Assistente de Configuração de Produtos do SharePoint 2013**. Quando solicitado a permitir que o programa faça alterações no computador, clique em **Sim**.
2.	Na página Bem-vindo aos Produtos do SharePoint, clique em **Avançar**.
3.	A caixa de diálogo Assistente de Configuração de Produtos do SharePoint será exibida, avisando que os serviços (como o IIS) serão reiniciados ou redefinidos. Clique em **Sim**.
4.	Na página Conectar a um farm de servidores, clique em **Conectar-se a um farm de servidores existente** e clique em **Avançar**.
5.	Na página Especificar as Configurações do Banco de Dados de Configurações, digite o nome do SQL Server primário em **Servidor de banco de dados** e, em seguida, clique em **Recuperar Nomes de Bancos de Dados**. 
6.	Clique em **SharePoint_Config** na lista de nomes no banco de dados e, em seguida, clique em **Avançar**. 
7.	Na página Especificar as Configurações de Segurança do Farm, digite a senha do procedimento anterior. Clique em **Próximo**.
8.	A página Concluindo o Assistente de Configuração de Produtos do SharePoint é exibida. Clique em **Próximo**.
9.	Na página Configuração Bem-sucedida, clique em **Concluir**. 

Quando o SharePoint cria o farm, ele configura um conjunto de logons de servidor na máquina virtual primária do SQL Server. O SQL Server 2012 introduz o conceito de usuários com senhas para bancos de dados independentes. O próprio banco de dados armazena todos os metadados do banco de dados e as informações dos usuários. Dessa forma, um usuário definido nesse banco de dados não precisa ter um logon correspondente. As informações incluídas nesse banco de dados são replicadas pelo grupo de disponibilidade e ficam disponíveis após um failover. Para saber mais, consulte [Bancos de Dados Independentes](http://go.microsoft.com/fwlink/p/?LinkId=262794).

No entanto, por padrão, bancos de dados do SharePoint não são bancos de dados independentes. Portanto, você precisará configurar manualmente o SQL Server secundário para que ele tenha o mesmo conjunto de logons de contas do farm do SharePoint que o SQL Server principal. Você pode executar essa sincronização pelo SQL Server Management Studio conectando-se a ambos os servidores ao mesmo tempo.

Depois de concluir a configuração inicial, mais opções de configuração para os recursos do farm do SharePoint serão disponibilizadas. Para saber mais, consulte [Planejando o SharePoint 2013 nos Serviços de Infraestrutura do Azure](http://msdn.microsoft.com/library/dn275958.aspx).

## Configurar o balanceamento de carga interna

Você deve configurar o balanceamento de carga interna para que o tráfego do cliente para o farm do SharePoint seja distribuído uniformemente para os dois servidores Web front-end. Isso requer que você especifique uma instância de balanceamento de carga interna que consiste em um nome e o seu próprio endereço IP, atribuído a partir do espaço de endereço da sub-rede. Para determinar se um endereço IP escolhido para o balanceador de carga interna está disponível, use os seguintes comandos do PowerShell do Azure:

	$vnet="<Table V – Item 1 – Value column>"
	$testIP="<a chosen IP address from the subnet address space, Table S - Item 1 – Subnet address space column>"
	Test-AzureStaticVNetIP –VNetName $vnet –IPAddress $testIP

Se o campo **IsAvailable** na exibição do comando **Test-AzureStaticVNetIP** for **True**, você poderá usar o endereço IP.

Execute os comandos a seguir no prompt de comando do PowerShell do Azure em seu computador local:

	$serviceName="<Table C – Item 3 – Cloud service name column>"
	$ilb="<name of your internal load balancer instance>"
	$subnet="<Table S – Item 1 – Subnet name column>"
	$IP="<an available IP address for your ILB instance>"
	Add-AzureInternalLoadBalancer –ServiceName $serviceName -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP
	
	$prot="tcp"
	$locport=80
	$pubport=80
	# This example assumes unsecured HTTP traffic to the SharePoint farm.
	
	$epname="SPWeb1"
	$vmname="<Table M – Item 8 – Virtual machine name column>"
	Get-AzureVM –ServiceName $serviceName –Name $vmname | Add-AzureEndpoint -Name $epname -LBSetName $ilb -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
	
	$epname="SPWeb2"
	$vmname="<Table M – Item 9 – Virtual machine name column>"
	Get-AzureVM –ServiceName $serviceName –Name $vmname | Add-AzureEndpoint -Name $epname -LBSetName $ilb -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

Em seguida, adicione um registro de endereço DNS na infraestrutura de DNS da sua organização que resolva o nome de domínio totalmente qualificado do farm do SharePoint (como sp.corp.contoso.com) para o endereço IP atribuído à instância do balanceador de carga interna (o valor de **$IP** no bloco de comando anterior do PowerShell do Azure).

Esta é a configuração resultante da conclusão bem-sucedida desta fase.

![](./media/virtual-machines-workload-intranet-sharepoint-phase4/workload-spsqlao_04.png)

## Próxima etapa

Para continuar com a configuração dessa carga de trabalho, vá para a [Fase 5: Criar o Grupo de Disponibilidade e adicionar os bancos de dados do SharePoint](virtual-machines-workload-intranet-sharepoint-phase5.md).

## Recursos adicionais

[Implantação do SharePoint com Grupos de Disponibilidade AlwaysOn do SQL Server no Azure](virtual-machines-workload-intranet-sharepoint-overview.md)

[Farms do SharePoint hospedados nos serviços de infraestrutura do Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Infográfico do SharePoint com SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Arquiteturas do Microsoft Azure para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Diretrizes de implementação dos Serviços de Infraestrutura do Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)
 

<!---HONumber=July15_HO2-->