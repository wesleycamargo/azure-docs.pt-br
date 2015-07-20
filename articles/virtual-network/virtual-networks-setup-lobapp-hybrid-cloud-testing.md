<properties 
	pageTitle="Configurar um aplicativo LOB baseado na Web em uma nuvem híbrida para teste" 
	description="Aprenda a criar um aplicativo de linha de negócios baseado na Web em um ambiente de nuvem híbrida para testes profissionais de TI ou testes de desenvolvimento." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="josephd"/>

# Configurar um aplicativo LOB baseado na Web em uma nuvem híbrida para teste

Este tópico orienta a criação de um ambiente de nuvem híbrida para testar um aplicativo de linha de negócios (LOB) da intranet, hospedado no Microsoft Azure. Veja abaixo a configuração resultante.

![](./media/virtual-networks-setup-lobapp-hybrid-cloud-testing/CreateLOBAppHybridCloud_3.png)

Para obter um exemplo de um aplicativo LOB de produção hospedado no Azure, consulte o plano gráfico da arquitetura de **Aplicativos de linha de negócios** em [Diagramas e planos gráficos de arquitetura de software Microsoft](http://msdn.microsoft.com/dn630664).

Esta configuração simula um aplicativo LOB no ambiente de produção do Azure em seu local na Internet. Ele consiste em:

- Uma rede local simplificada (a sub-rede Corpnet).
- Uma rede virtual entre locais hospedada no Azure (TestVNET).
- Uma conexão de VPN site a site.
- Um servidor de linha de negócios, um SQL Server e um controlador de domínio secundário na rede virtual TestVNET.

Esta configuração proporciona uma base e um ponto de partida comum com os quais é possível:

- Desenvolver e testar aplicativos LOB hospedados no IIS (Serviços de Informações da Internet) com um back-end de banco de dados do SQL Server 2014 no Azure.
- Realizar testes dessa carga de trabalho de TI baseada em nuvem híbrida.

Há três fases principais para configurar esse ambiente de teste de nuvem híbrida:

1.	Configurar um ambiente de nuvem híbrida para testes.
2.	Configurar o computador do servidor SQL (SQL1).
3.	Configurar o servidor LOB (LOB1).

Se você não tiver uma conta do Azure, será possível se inscrever em uma conta gratuita no [Experimente o Azure](http://azure.microsoft.com/pricing/free-trial/). Se tiver uma assinatura do MSDN, consulte [Benefício do Azure para assinantes do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Fase 1: Configurar o ambiente de nuvem híbrida

Use as instruções no tópico [Configurar um ambiente de nuvem híbrida para testes](virtual-networks-setup-hybrid-cloud-environment-testing.md). Como esse ambiente de teste não exige a presença do servidor APP1 na sub-rede Corpnet, fique à vontade para desligá-lo por enquanto.

Esta é a configuração atual.

![](./media/virtual-networks-setup-lobapp-hybrid-cloud-testing/CreateLOBAppHybridCloud_1.png)

> [AZURE.NOTE]Para a Fase 1, você também pode configurar o ambiente de teste de nuvem híbrida simulada. Consulte [Configurar um ambiente de nuvem híbrida simulada para testes](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md) para ver as instruções.
 
## Fase 2: Configurar o computador do servidor SQL (SQL1)

No Portal de Gerenciamento do Azure, inicie o computador DC2 se necessário.

Em seguida, crie uma máquina virtual do Azure para o SQL1 com estes comandos no prompt de comando do PowerShell do Azure em seu computador local. Antes de executar estes comandos, preencha os valores variáveis e remova os caracteres < and >.

	$storageacct="<Name of the storage account for your TestVNET virtual network>"
	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for SQL1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	Set-AzureStorageAccount –StorageAccountName $storageacct
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Standard on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name SQL1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 100 -DiskLabel SQLFiles –LUN 0 -HostCaching None
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Em seguida, conecte-se à nova máquina virtual SQL1* usando a conta de administrador local*.

1.	No painel esquerdo do Portal de Gerenciamento do Azure, clique em **Máquinas Virtuais** e, em seguida, em **Executando** na coluna Status do SQL1.
2.	Na barra de tarefas, clique em **Conectar**. 
3.	Quando solicitado a abrir SQL1.rdp, clique em **Abrir**.
4.	Quando receber uma caixa de mensagem de Conexão de Área de Trabalho Remota, clique em **Conectar**.
5.	Quando solicitado a fornecer credenciais, use estas:
	- Nome: **SQL1\**[Nome da conta de administrador local]
	- Senha: [senha da conta de administrador local]
6.	Quando receber uma caixa de mensagem de Conexão de Área de Trabalho Remota referindo-se aos certificados, clique em **Sim**.

Em seguida, configure regras de Firewall do Windows para permitir o tráfego para testar a conectividade básica e o SQL Server. Em um prompt de comando com nível de administrador do Windows PowerShell no SQL1, execute estes comandos.

	New-NetFirewallRule -DisplayName “SQL Server” -Direction Inbound –Protocol TCP –LocalPort 1433,1434,5022 -Action allow 
	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

O comando ping deve resultar em quatro respostas bem-sucedidas do endereço IP 10.0.0.1.

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

Em seguida, configure o SQL Server 2014 para usar a unidade F: para novos bancos de dados e permissões de conta de usuário.

1.	Na tela inicial, digite **Gerenciamento do SQL Server** e, em seguida, clique em **SQL Server 2014 Management Studio**.
2.	Em **Conectar ao Servidor**, clique em **Conectar**.
3.	No painel de árvore do Pesquisador de Objetos, clique com o botão direito do mouse em **SQL1** e, em seguida, em **Propriedades**.
4.	Na janela **Propriedades do Servidor**, clique em **Configurações de Banco de Dados**.
5.	Localize os **Locais padrão de banco de dados** e defina estes valores: 
	- Para **Dados**, digite o caminho **f:\\Data**.
	- Para **Log**, digite o caminho **f:\\Log**.
	- Para **Backup**, digite o caminho **f:\\Backup**.
	- Aviso: somente novos bancos de dados usam esses locais.
6.	Clique em **OK** para fechar a janela.
7.	No painel de árvore **Pesquisador de Objetos**, abra **Segurança**.
8.	Clique com o botão direito do mouse em **Logons** e clique em **Novo Logon**.
9.	Em **Nome de Logon**, digite **CORP\\User1**.
10.	Na página **Funções de Servidor**, clique em **sysadmin** e, em seguida, clique em **OK**.
11.	Feche o Microsoft SQL Server Management Studio.

Esta é a configuração atual.

![](./media/virtual-networks-setup-lobapp-hybrid-cloud-testing/CreateLOBAppHybridCloud_2.png)
 
## Fase 3: Configurar o servidor LOB (LOB1)

Primeiro, crie uma máquina virtual do Azure para o LOB1 com estes comandos no prompt de comando do PowerShell do Azure em seu computador local.

	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for LOB1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	$image = Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name LOB1 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Depois, conecte-se à máquina virtual LOB1 com as credenciais de CORP\\User1.

Em seguida, configure uma regra do Firewall do Windows para permitir o tráfego e testar a conectividade básica. Em um prompt de comando com nível de administrador do Windows PowerShell no LOB1, execute estes comandos.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

O comando ping deve resultar em quatro respostas bem-sucedidas do endereço IP 10.0.0.1.

Em seguida, configure o LOB1 para IIS e teste o acesso do CLIENT1.

1.	Execute o Gerenciador do Servidor e clique em **Adicionar funções e recursos**.
2.	Na página Antes de começar, clique em **Avançar**.
3.	Na página Selecionar tipo de instalação, clique em **Avançar**.
4.	Na página Selecionar servidor de destino, clique em **Avançar**.
5.	Na página Funções do servidor, clique em **Servidor Web (IIS)** na lista **Funções**.
6.	Quando solicitado, clique em **Adicionar Recursos** e clique em **Avançar**.
7.	Na página Selecionar recursos, clique em **Avançar**.
8.	Na página Servidor Web (IIS), clique em **Avançar**.
9.	Na página Selecionar serviços da função, marque ou desmarque as caixas de seleção dos serviços de que você precisa para testar seu aplicativo LOB e, em seguida, clique em **Avançar**.
10.	Na página Confirmar seleções da instalação, clique em **Instalar**.
11.	Aguarde a conclusão da instalação dos componentes e clique em **Fechar**.
12.	Faça logon no computador CLIENT1 com as credenciais da conta CORP\\User1 e inicie o Internet Explorer.
13.	Na barra de endereços, digite **http://lob1/** e pressione ENTER. Você verá a página da Web do IIS 8 padrão. 
Esta é a configuração atual.

![](./media/virtual-networks-setup-lobapp-hybrid-cloud-testing/CreateLOBAppHybridCloud_3.png)
 
Este ambiente agora está pronto para que você implante seu aplicativo baseado na Web no LOB1 e teste a funcionalidade e o desempenho da sub-rede Corpnet.

## Recursos adicionais

[Diagramas e planos gráficos da arquitetura de software da Microsoft](http://msdn.microsoft.com/dn630664)

[Plataforma de servidor Web de hospedagem amigável (IIS)](http://technet.microsoft.com/library/hh831818)

[Configurar um ambiente de nuvem híbrida para teste](virtual-networks-setup-hybrid-cloud-environment-testing.md)

[Configurar um farm de intranet do SharePoint em uma nuvem híbrida para teste](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[Configurar a Sincronização de Diretórios (DirSync) do Office 365 em uma nuvem híbrida para teste](virtual-networks-setup-dirsync-hybrid-cloud-testing.md)

[Configurar um ambiente de nuvem híbrida simulado para testes](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)

[Ambientes de teste de nuvem híbrida do Azure](../virtual-machines/virtual-machines-hybrid-cloud-test-environments.md)

[Diretrizes de implementação dos Serviços de Infraestrutura do Azure](../virtual-machines/virtual-machines-infrastructure-services-implementation-guidelines.md)
 

<!---HONumber=July15_HO2-->