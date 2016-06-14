<properties 
	pageTitle="Ambiente de teste de farm do SharePoint 2013 | Microsoft Azure" 
	description="Aprenda a criar um farm de intranet do SharePoint 2013 de duas camadas em um ambiente de nuvem híbrida para teste de desenvolvimento ou teste profissional de TI." 
	services="virtual-machines-windows" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/01/2016" 
	ms.author="josephd"/>

# Configurar um farm de intranet do SharePoint em uma nuvem híbrida para teste

Este tópico orienta a criação de um ambiente de nuvem híbrida para testar um farm de intranet do SharePoint hospedado no Microsoft Azure. Veja abaixo a configuração resultante.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sp/virtual-machines-windows-ps-hybrid-cloud-test-env-sp-ph3.png)
 
Esta configuração simula um ambiente de produção do SharePoint no Azure em seu local na Internet. Ele consiste em:

- Uma rede local simplificada (a sub-rede Corpnet).
- Uma rede virtual entre locais hospedada no Microsoft Azure (TestVNET).
- Uma conexão de VPN site a site.
- Um farm do SharePoint de duas camadas e um controlador de domínio secundário na rede virtual TestVNET.

Esta configuração proporciona uma base e um ponto de partida comum com os quais é possível:

- Desenvolver e testar aplicativos em um farm de intranet do SharePoint em um ambiente de nuvem híbrida.
- Realizar testes dessa carga de trabalho de TI baseada em nuvem híbrida.

Há três fases principais para configurar esse ambiente de teste de nuvem híbrida:

1.	Configurar um ambiente de nuvem híbrida para testes.
2.	Configurar o computador do servidor SQL (SQL1).
3.	Configurar o servidor do SharePoint (SP1).

Essa carga de trabalho requer uma assinatura do Azure. Se você tiver uma assinatura do MSDN ou do Visual Studio, confira [Crédito Azure mensal para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Fase 1: Configurar o ambiente de nuvem híbrida

Use as instruções no tópico [Configurar um ambiente de nuvem híbrida para testes](virtual-machines-windows-ps-hybrid-cloud-test-env-base.md). Como esse ambiente de teste não exige a presença do servidor APP1 na sub-rede Corpnet, fique à vontade para desligá-lo por enquanto.

Esta é a configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sp/virtual-machines-windows-ps-hybrid-cloud-test-env-sp-ph1.png)

> [AZURE.NOTE] Para a Fase 1, você também pode configurar o [ambiente de teste de nuvem híbrida simulada](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md).
 
## Fase 2: Configurar o computador do servidor SQL (SQL1)

No portal do Azure, inicie o computador DC2, se necessário.

Do portal do Azure, conecte-se ao DC2 usando as credenciais de CORP\\User1.

Em seguida, crie uma conta de administrador de farm do SharePoint. Abra um prompt do Windows PowerShell com nível de administrador no DC2 e execute esse comando.

	New-ADUser -SamAccountName SPFarmAdmin -AccountPassword (read-host "Set user password" -assecurestring) -name "SPFarmAdmin" -enabled $true -ChangePasswordAtLogon $false

Quando solicitado a fornecer a senha da conta SPFarmAdmin, digite uma senha forte e registre-a em um local seguro.

Em seguida, crie uma máquina virtual do Azure para o SQL1 com estes comandos no prompt de comando do Azure PowerShell em seu computador local. Antes de executar estes comandos, preencha os valores variáveis e remova os caracteres < and >.

	$rgName="<your resource group name>"
	$locName="<the Azure location of your resource group>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
	$vm=New-AzureRMVMConfig -VMName SQL1 -VMSize Standard_A4
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-SQLDataDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name "Data" -DiskSizeInGB 100 -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the SQL Server computer." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName SQL1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Standard -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Quando concluído, use o portal do Azure para conectar-se ao SQL1 usando a conta de administrador local.

Em seguida, configure regras de Firewall do Windows para permitir o tráfego para testar a conectividade básica e o SQL Server. Em um prompt de comando com nível de administrador do Windows PowerShell no SQL1, execute estes comandos.

	New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc2.corp.contoso.com

O comando ping deve resultar em quatro respostas bem-sucedidas do endereço IP 192.168.0.4.

Em seguida, adicione o disco de dados extra como um novo volume com a letra da unidade F:.

1.	No painel esquerdo do Gerenciador do Servidor, clique em **Serviços de Arquivo e Armazenamento** e, em seguida, clique em **Discos**.
2.	No painel de conteúdo, no grupo **Discos**, clique em **disco 2** (com a **Partição** definida como **Desconhecida**).
3.	Clique em **Tarefas**, e, em seguida, em **Novo Volume**.
4.	Na página Antes de começar do Assistente de Novo Volume, clique em **Avançar**.
5.	Na página Selecione o servidor e o disco, clique em **Disco 2** e, em seguida, em **Avançar**. Quando solicitado, clique em **OK**.
6.	Na página Especifique o tamanho do volume, clique em **Avançar**.
7.	Na página Atribuir a uma letra da unidade ou pasta, clique em **Avançar**.
8.	Na página Selecionar configurações do sistema de arquivos, clique em **Avançar**.
9.	Na página Confirmar seleções, clique em **Criar**.
10.	Ao concluir, clique em **Fechar**.

Execute estes comandos no prompt de comando do Windows PowerShell no SQL1:

	md f:\Data
	md f:\Log
	md f:\Backup

Em seguida, integre a SQL1 ao domínio CORP do Active Directory com estes comandos no prompt do Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Use a conta CORP\\User1 quando for solicitado a fornecer as credenciais de conta de domínio para o comando **Add-Computer**.

Após a reinicialização, use o Portal do Azure para se conectar ao SQL1 usando a *conta de administrador local*.

Em seguida, configure o SQL Server 2014 para usar a unidade F: para novos bancos de dados e permissões de conta de usuário.

1.	Na tela inicial, digite **Gerenciamento do SQL Server** e, em seguida, clique em **SQL Server 2014 Management Studio**.
2.	Em **Conectar ao Servidor**, clique em **Conectar**.
3.	No painel de árvore do Pesquisador de Objetos, clique com o botão direito do mouse em **SQL1** e clique em **Propriedades**.
4.	Na janela **Propriedades do Servidor**, clique em **Configurações de Banco de Dados**.
5.	Localize os **Locais padrão de banco de dados** e defina estes valores: 
	- Para **Dados**, digite o caminho **f:\\Data**.
	- Para **Log**, digite o caminho **f:\\Log**.
	- Para **Backup**, digite o caminho **f:\\Backup**.
	- Observe que apenas os novos bancos de dados usam esses locais.
6.	Clique em **OK** para fechar a janela.
7.	No painel de árvore do **Pesquisador de Objetos**, abra **Segurança**.
8.	Clique com o botão direito em **Logons** e clique em **Novo logon**.
9.	Em **Nome de Logon**, digite **CORP\\User1**.
10.	Na página **Funções de Servidor**, clique em **sysadmin** e, em seguida, clique em **OK**.
11.	No painel de árvore **Pesquisador de Objetos**, clique com o botão direito do mouse em **Logons** e clique em **Novo Logon**.
12.	Na página **Geral**, em **Nome de Logon**, digite **CORP\\SPFarmAdmin**.
13.	Na página **Funções de Servidor**, selecione **dbcreator** e clique em **OK**.
14.	Feche o Microsoft SQL Server Management Studio.

Esta é a configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sp/virtual-machines-windows-ps-hybrid-cloud-test-env-sp-ph2.png)

## Fase 3: Configurar os servidores do SharePoint (SP1)

Primeiro, crie uma máquina virtual do Azure para o SP1 com estes comandos no prompt de comando do Azure PowerShell em seu computador local.

	$rgName="<your resource group name>"
	$locName="<the Azure location of your resource group>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name SP1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name SP1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
	$vm=New-AzureRMVMConfig -VMName SP1 -VMSize Standard_A3
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the SharePoint 2013 server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName SP1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SP1-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Em seguida, use o portal do Azure para se conectar à máquina virtual SP1 com as credenciais da conta de administrador local.

Em seguida, configure uma regra do Firewall do Windows para permitir o tráfego e testar a conectividade básica. De um prompt de comando do Windows PowerShell no SP1, execute estes comandos.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc2.corp.contoso.com

O comando ping deve resultar em quatro respostas bem-sucedidas do endereço IP 192.168.0.4.

Em seguida, integre a SP1 ao domínio CORP do Active Directory com estes comandos no prompt do Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Use a conta CORP\\User1 quando for solicitado a fornecer as credenciais de conta de domínio para o comando **Add-Computer**.

Após a reinicialização, use o portal do Azure para conectar-se ao SP1 com a conta e senha de CORP\\User1.

Em seguida, configure o SP1 para um novo farm do SharePoint e um site de equipe padrão.

1.	Na tela inicial, digite **Produtos do SharePoint 2013** e clique em **Assistente de Configuração de Produtos do SharePoint 2013**. Quando solicitado a permitir que o programa faça alterações no computador, clique em **Sim**.
2.	Na página Bem-vindo aos Produtos do SharePoint, clique em **Avançar**. 
3.	Na caixa de diálogo que notifica que alguns serviços talvez precisem ser reiniciados durante a configuração, clique em **Sim**.
4.	Na página Conectar a um farm de servidores, clique em **Criar um novo farm de servidores** e clique em **Avançar**.
5.	Na página Especificar Definições do Banco de Dados de Configuração, digite **sql1.corp.contoso.com** em **Servidor de Banco de Dados**, digite **CORP\\SPFarmAdmin** em **Nome de usuário**, digite a senha da conta SPFarmAdmin em **Senha** e clique em **Avançar**.
6.	Na página Especificar Configurações de Segurança do Farm, digite **P@ssphrase** tanto em **Senha** quanto em **Confirmar senha** e clique em **Avançar**.
7.	Na página Configurar Aplicativo Web da Administração Central do SharePoint, clique em **Avançar**.
8.	Na página Concluindo o Assistente de Configuração de Produtos do SharePoint, clique em **Avançar**. O Assistente de Configuração de Produtos do SharePoint pode levar alguns minutos para ser concluído.
9.	Na página Configuração Bem-sucedida, clique em **Concluir**. Após a conclusão, o Internet Explorer é iniciado com uma guia chamada Assistente de Configuração Inicial do Farm.
10.	Na caixa de diálogo **Ajude a Melhorar o SharePoint**, clique em **Não, não desejo participar** e, em seguida, clique em **OK**.
11.	Para **Como você deseja configurar o farm do SharePoint?**, clique em **Iniciar o Assistente**.
12.	Na página Configurar o farm do SharePoint, em **Conta de serviço**, clique em **Usar conta gerenciada existente**.
13.	Em **Serviços**, desmarque todas as caixas de seleção exceto a caixa ao lado de **Serviço de Controle de Sessão** e clique em **Avançar**. A página Processando ou Trabalhando nisso pode ser exibida por alguns instantes até ser concluída.
14.	Na página Criar Coleção de Sites, em **Título e descrição**, digite **Contoso Corporation** em **Título**, especifique a URL **http://sp1**/ e clique em **OK**. A página Processando ou Trabalhando nisso pode ser exibida por alguns instantes até ser concluída. Esta etapa cria um site de equipe na URL http://sp1.
15.	Na página Isso conclui o Assistente de Configuração de Farm, clique em **Concluir**. A guia do Internet Explorer mostra o site de Administração Central do SharePoint 2013.
16.	Faça logon no computador CLIENT1 com as credenciais da conta CORP\\User1 e inicie o Internet Explorer.
17.	Na barra de endereços, digite **http://sp1/** e pressione ENTER. Você deve ver o site de equipe do SharePoint da Contoso Corporation. O site pode levar algum tempo para renderizar.

Esta é a configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sp/virtual-machines-windows-ps-hybrid-cloud-test-env-sp-ph3.png)
 
Seu farm de intranet do SharePoint em um ambiente de nuvem híbrida agora está pronto para teste.

## Próxima etapa

- [Configure](https://technet.microsoft.com/library/ee836142.aspx) o farm do SharePoint.

<!---HONumber=AcomDC_0601_2016-->