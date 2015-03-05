<properties 
	pageTitle="Configurar um farm de intranet do SharePoint em uma nuvem híbrida para teste" 
	description="Aprenda a criar um farm de intranet do SharePoint em um ambiente de nuvem híbrida para teste de desenvolvimento ou teste profissional de TI." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2015" 
	ms.author="josephd"/>


#Configurar um farm de intranet do SharePoint em uma nuvem híbrida para teste

Este tópico orienta a criação de um ambiente de nuvem híbrida para testar um farm de intranet do SharePoint hospedado no Microsoft Azure. Veja abaixo a configuração resultante.

![](./media/virtual-networks-set-up-SharePoint-hybrid-cloud-for-testing/CreateSPFarmHybridCloud_3.png)
 
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

Se ainda não tiver uma assinatura do Azure, você poderá se inscrever para uma avaliação gratuita em [Teste o Azure](http://www.windowsazure.com/pricing/free-trial/). Se você tiver uma assinatura do MSDN, consulte [Benefício do Azure para assinantes do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

##Fase 1: Configurar o ambiente de nuvem híbrida

Use as instruções no tópico [Configurar um ambiente de nuvem híbrida para testes](../virtual-networks-setup-hybrid-cloud-environment-testing/). Como esse ambiente de teste não exige a presença do servidor APP1 na sub-rede Corpnet, fique à vontade para desligá-lo por enquanto.

Esta é a configuração atual.

![](./media/virtual-networks-set-up-SharePoint-hybrid-cloud-for-testing/CreateSPFarmHybridCloud_1.png)
 
##Fase 2: Configurar o computador do servidor SQL (SQL1)

No Portal de Gerenciamento do Azure, inicie o computador DC2 se necessário.

Primeiro, crie uma conexão de área de trabalho remota para o DC2 usando as credenciais de CORP\User1.

Em seguida, crie uma conta de administrador de farm do SharePoint. Abra um prompt do Windows PowerShell com nível de administrador no DC2 e execute esse comando.

	New-ADUser -SamAccountName SPFarmAdmin -AccountPassword (read-host "Set user password" -assecurestring) -name "SPFarmAdmin" -enabled $true -ChangePasswordAtLogon $false

Quando solicitado a fornecer a senha da conta SPFarmAdmin, digite uma senha forte e registre-a em um local seguro.

Em seguida, crie uma máquina virtual do Azure para o SQL1 com estes comandos no prompt de comando do PowerShell do Azure em seu computador local.

	$storageacct="<Name of the storage account for your TestVNET virtual network>"
	Set-AzureStorageAccount -StorageAccountName $storageacct
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Standard on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$LocalAdminName="<A local administrator account name>" 
	$LocalAdminPW="<A password for the local administrator account>"
	$User1Password="<The password for the CORP\User1 account>"
	$vm1=New-AzureVMConfig -Name SQL1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUserName $LocalAdminName -Password $LocalAdminPW -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $User1Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 100 -DiskLabel SQLFiles -LUN 0 -HostCaching None
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Em seguida, conecte-se à nova máquina virtual SQL1 *using the local administrator account*.

1.	No painel esquerdo do Portal de Gerenciamento do Azure, clique em **Máquinas Virtuais** e, em seguida, em **Executando** na coluna Status do SQL1.
2.	Na barra de tarefas, clique em **Conectar**. 
3.	Quando solicitado a abrir SQL1.rdp, clique em **Abrir**.
4.	Quando receber uma caixa de mensagem Conexão de Área de Trabalho Remota, clique em **Conectar**.
5.	Quando solicitado a fornecer credenciais, use estas:
	- Nome: **SLQ1\\**[Nome da conta de administrador local]
	- Senha: [Senha da conta de administrador local]
6.	Quando receber uma caixa de mensagem Conexão de Área de Trabalho Remota referente a certificados, clique em **Sim**.

Em seguida, configure regras de Firewall do Windows para permitir o tráfego para testar a conectividade básica e o SQL Server. Em um prompt de comando com nível de administrador do Windows PowerShell no SQL1, execute estes comandos.

	New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

O comando ping deve resultar em quatro respostas bem-sucedidas do endereço IP 10.0.0.1.

Em seguida, adicione o disco de dados extra como um novo volume com a letra da unidade F:.

1.	No painel esquerdo do Gerenciador do Servidor, clique em **Serviços de Arquivo e Armazenamento** e, em seguida, clique em **Discos**.
2.	No painel de conteúdo, no grupo **Discos**, clique em **disco 2** (com a **Partição** definida como **Desconhecido**).
3.	Clique em **Tarefas** e, em seguida, em **Novo Volume**.
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

Em seguida, configure o SQL Server 2014 para usar a unidade F: para novos bancos de dados e permissões de conta de usuário.

1.	Na tela inicial, digite **Gerenciamento do SQL Server** e, em seguida, clique em **SQL Server 2014 Management Studio**.
2.	Em **Conectar ao Servidor**, clique em **Conectar**.
3.	No painel de árvore do Pesquisador de Objetos, clique em **SQL1** e, em seguida, em **Propriedades**.
4.	Na janela **Propriedades do Servidor**, clique em **Configurações de Banco de Dados**.
5.	Localize os **Locais padrão de banco de dados** e defina estes valores: 
	- Para **Dados**, digite o caminho **f:\Data**.
	- Para **Log**, digite o caminho **f:\Log**.
	- Para **Backup**, digite o caminho **f:\Backup**.
	- Observe que apenas os novos bancos de dados usam esses locais.
6.	Clique em **OK** para fechar a janela.
7.	No painel de árvore do **Pesquisador de Objetos**, abra **Segurança**.
8.	Clique com o botão direito em **Logons** e clique em **Novo Logon**.
9.	Em **Nome de Logon**, digite **CORP\User1**.
10.	Na página **Funções de Servidor**, clique em **sysadmin** e, em seguida, clique em **OK**.
11.	No painel de árvore do **Pesquisador de Objetos**, clique com o botão direito em **Logons** e clique em **Novo Logon**.
12.	Na página **Geral**, em **Nome de Logon**, digite **CORP\SPFarmAdmin**.
13.	Na página **Funções de Servidor**, selecione **dbcreator** e clique em **OK**.
14.	Feche o Microsoft SQL Server Management Studio.

Esta é a configuração atual.

![](./media/virtual-networks-set-up-SharePoint-hybrid-cloud-for-testing/CreateSPFarmHybridCloud_2.png)

 
##Fase 3: Configurar o servidor do SharePoint (SP1)

Primeiro, crie uma máquina virtual do Azure para o SP1 com estes comandos no prompt de comando do PowerShell do Azure em seu computador local.

	$image= Get-AzureVMImage | where { $_.Label -eq "SharePoint Server 2013 Trial" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$LocalAdminName="<A local administrator account name>" 
	$LocalAdminPW="<A password for the local administrator account>"
	$User1Password="<The password for the CORP\User1 account>"
	$vm1=New-AzureVMConfig -Name SP1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUserName $LocalAdminName -Password $LocalAdminPW -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $User1Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Depois, conecte-se à máquina virtual SP1 com as credenciais de CORP\User1.

Em seguida, configure uma regra do Firewall do Windows para permitir o tráfego e testar a conectividade básica. Em um prompt de comando com nível de administrador do Windows PowerShell no SP1, execute estes comandos.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

O comando ping deve resultar em quatro respostas bem-sucedidas do endereço IP 10.0.0.1.

Em seguida, configure o SP1 para um novo farm do SharePoint e um site de equipe padrão.

1.	Na tela inicial, digite **Produtos do SharePoint 2013** e clique em **Assistente de Configuração de Produtos do SharePoint 2013**. Quando solicitado a permitir que o programa faça alterações no computador, clique em **Sim**.
2.	Na página Bem-vindo aos Produtos do SharePoint, clique em **Avançar**. 
3.	Na caixa de diálogo que notifica que alguns serviços talvez precisem ser reiniciados durante a configuração, clique em **Sim**.
4.	Na página Conectar a um farm de servidores, clique em **Criar um novo farm de servidores** e clique em **Avançar**.
5.	Na página Especificar Definições do Banco de Dados de Configuração, digite **sql1.corp.contoso.com** em **Servidor de Banco de Dados**, digite **CORP\SPFarmAdmin** em **Nome de usuário**, digite a senha da conta SPFarmAdmin em **Senha** e clique em **Avançar**.
6.	Na página Especificar Configurações de Segurança do Farm, digite **P@ssphrase** tanto em **Senha** quanto em **Confirmar senha** e clique em **Avançar**.
7.	Na página Configurar Aplicativo Web da Administração Central do SharePoint, clique em **Avançar**.
8.	Na página Concluindo o Assistente de Configuração de Produtos do SharePoint, clique em **Avançar**. O Assistente de Configuração de Produtos do SharePoint pode levar alguns minutos para ser concluído.
9.	Na página Configuração Bem-sucedida, clique em **Concluir**. Após a conclusão, o Internet Explorer é iniciado com uma guia chamada Assistente de Configuração Inicial do Farm.
10.	Na caixa de diálogo **Ajude a Melhorar o SharePoint**, clique em **Não, não desejo participar** e, em seguida, clique em **OK**.
11.	Para **Como você deseja configurar o farm do SharePoint?**, clique em **Iniciar o Assistente**.
12.	Na página Configurar o farm do SharePoint, em **Conta de serviço**, clique em **Usar conta gerenciada existente**.
13.	Em **Serviços**, desmarque todas as caixas de seleção exceto a caixa ao lado de **Serviço de Controle de Sessão** e clique em **Avançar**. A página Processando ou Trabalhando nisso pode ser exibida por alguns instantes até ser concluída.
14.	Na página Criar Coleção de Sites, em **Título e descrição**, digite **Contoso Corporation** em **Título**, especifique a URL **http://sp1** e clique em **OK**. A página Processando ou Trabalhando nisso pode ser exibida por alguns instantes até ser concluída. Esta etapa cria um site de equipe na URL http://sp1.
15.	Na página Isso conclui o Assistente de Configuração de Farm, clique em **Concluir**. A guia do Internet Explorer mostra o site de Administração Central do SharePoint 2013.
16.	Faça logon no computador CLIENT1 com as credenciais da conta CORP\User1 e inicie o Internet Explorer.
17.	Na barra de endereços, digite **http://sp1/** e pressione ENTER. Você deve ver o site de equipe do SharePoint da Contoso Corporation. O site pode levar algum tempo para renderizar.

Esta é a configuração atual.

![](./media/virtual-networks-set-up-SharePoint-hybrid-cloud-for-testing/CreateSPFarmHybridCloud_3.png)
 
Seu farm de intranet do SharePoint em um ambiente de nuvem híbrida agora está pronto para teste.

##Recursos adicionais

[SharePoint nos serviços de infraestrutura do Azure](http://msdn.microsoft.com/library/azure/dn275955.aspx)

[Farm do SharePoint Server](../virtual-machines-sharepoint-farm-azure-preview/)

[Configurar um ambiente de nuvem híbrida para teste](../virtual-networks-setup-hybrid-cloud-environment-testing/)

[Configurar um aplicativo LOB baseado na Web em uma nuvem híbrida para teste](../virtual-networks-setup-lobapp-hybrid-cloud-testing/)

[Configurar a Sincronização de Diretórios (DirSync) do Office 365 em uma nuvem híbrida para teste](../virtual-networks-setup-dirsync-hybrid-cloud-testing/)

<!--HONumber=45--> 
