<properties
	pageTitle="Ambiente de teste de configuração básica"
	description="Aprenda a criar um ambiente de desenvolvimento/teste simples que simule uma intranet simplificada no Microsoft Azure."
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
	ms.date="10/05/2015"
	ms.author="josephd"/>

# Ambiente de teste de configuração básica

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-machines-base-configuration-test-environment-resource-manager.md).

Este artigo apresenta instruções passo a passo para criar o ambiente de teste de Configuração de Base em uma Rede Virtual do Azure.

Você pode usar o ambiente de teste resultante:

- Para testes e desenvolvimento de aplicativos.
- Para o [ambiente simulado de nuvem híbrida](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md).
- Para estendê-lo com máquinas virtuais e serviços do Azure adicionais para um ambiente de teste com seu próprio projeto.

O ambiente de teste Configuração de Base consiste na sub-rede Corpnet em uma rede virtual do Azure somente de nuvem denominada TestLab, que simula uma intranet simplificada e privada conectada à Internet.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG04.png)

Ele contém:

- Uma máquina virtual do Azure que executa o Windows Server 2012 R2, denominada DC1, que é configurada como um controlador de domínio de intranet e um servidor DNS (Sistema de Nomes de Domínio).
- Uma máquina virtual do Azure que executa o Windows Server 2012 R2, denominada APP1, que é configurada como um servidor Web e de aplicativos geral.
- Uma máquina virtual do Azure que executa o Windows Server 2012 R2, denominada CLIENT1, que atuará como um cliente de intranet.

Essa configuração permite que os computadores DC1, APP1, CLIENT1 e computadores adicionais da sub-rede Corpnet sejam:

- Conectados à Internet para instalar atualizações, acessar recursos da Internet em tempo real e participar de tecnologias de nuvem pública, como o Microsoft Office 365 e outros serviços do Azure.
- Gerenciados remotamente usando Conexões de Área de Trabalho Remota de seu computador que está conectado à Internet ou à rede de sua organização.

Há quatro fases para configurar a sub-rede Corpnet do ambiente de teste de Configuração de Base do Windows Server 2012 R2 no Azure.

1.	Crie a rede virtual.
2.	Configurar o DC1.
3.	Configurar o APP1.
4.	Configurar o CLIENT1.

Se ainda não tiver uma conta do Azure, inscreva-se para obter uma avaliação gratuita em [Avaliação gratuita de um mês](http://azure.microsoft.com/pricing/free-trial/). Se tiver uma assinatura do MSDN, consulte [Benefício do Azure para assinantes do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

> [AZURE.NOTE]As máquinas virtuais no Azure incorrem em um custo monetário contínuo quando estão em execução. Esse custo é cobrado em sua avaliação gratuita, assinatura do MSDN ou assinatura paga. Para obter mais informações sobre os custos da execução de máquinas virtuais do Azure, consulte [Detalhes de preços de máquinas virtuais](http://azure.microsoft.com/pricing/details/virtual-machines/) e [Calculadora de preços do Azure](http://azure.microsoft.com/pricing/calculator/). Para reduzir os custos, consulte [Minimizando os custos de máquinas de virtuais do ambiente de teste no Azure](#costs).

## Fase 1: Criar a rede virtual

Primeiro, crie a rede virtual TestLab do Azure que hospedará a sub-rede Corpnet da configuração de base.

1.	Na barra de tarefas do [Portal do Azure](https://manage.windowsazure.com), clique em **Novo > Serviços de Rede > Rede Virtual > Criação Personalizada**.
2.	Na página Detalhes da Rede Virtual, digite **TestLab** em **Nome**.
3.	Em **Local**, selecione a região apropriada.
4.	Clique na seta Avançar.
5.	Na página Servidores DNS e Conectividade de VPN, em **Servidores DNS**, digite **DC1** em **Selecionar ou digitar o nome**, digite **10.0.0.4** em **Endereço IP** e, em seguida, clique na seta Avançar.
6.	Na página Espaços de Endereço de Rede Virtual, em **Sub-redes**, clique em **Subnet-1** e substitua o nome por **Corpnet**.
7.	Na coluna **CIDR (Contagem de Endereços)** da sub-rede Corpnet, clique em **/24 (256)**.
8.	Clique no ícone Concluído. Aguarde a rede virtual ser criada antes de continuar.

Em seguida, use as instruções em [Como instalar e configurar o PowerShell do Azure](../install-configure-powershell.md) para instalar o PowerShell do Azure no computador local. Abra um prompt de comando do Azure PowerShell.

Primeiro, selecione a assinatura correta do Azure com estes comandos. Substitua tudo o que estiver entre aspas, inclusive os caracteres < and >, pelos nomes corretos.

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

Você pode obter o nome da assinatura na propriedade **SubscriptionName** da exibição do comando **Get-AzureSubscription**.

Em seguida, crie um serviço de nuvem do Azure. O serviço de nuvem age como um limite de segurança e um contêiner lógico para as máquinas virtuais colocadas na rede virtual. Ele também fornece uma maneira para se conectar remotamente e gerenciar as máquinas virtuais na sub-rede Corpnet.

Você deve escolher um nome exclusivo para seu serviço de nuvem. *O nome do serviço de nuvem pode conter apenas letras, números e hifens. O primeiro e o último caractere no campo devem ser uma letra ou um número.*

Por exemplo, você poderia nomear seu serviço de nuvem como TestLab-*UniqueSequence*, em que *UniqueSequence* é uma abreviação de sua organização. Por exemplo, se sua organização se chamasse Tailspin Toys, você poderia chamar o serviço de nuvem de TestLab-Tailspin.

Você pode testar a exclusividade do nome com este comando do PowerShell do Azure.

	Test-AzureName -Service <Proposed cloud service name>

Se este comando retornar "False", o nome proposto é exclusivo. Em seguida, crie o serviço de nuvem com estes comandos.

	$loc="<the same location (region) as your TestLab virtual network>"
	New-AzureService -Service <Unique cloud service name> -Location $loc

Registre o nome real de seu serviço de nuvem.

Configure então uma conta de armazenamento que conterá os discos de suas máquinas virtuais e discos de dados extras. *Você deve escolher um nome exclusivo que contenha apenas letras minúsculas e números.* Você pode testar a exclusividade do nome da conta de armazenamento com este comando do PowerShell do Azure.

	Test-AzureName -Storage <Proposed storage account name>

Se este comando retornar "False", o nome proposto é exclusivo. Crie a conta de armazenamento e defina a assinatura para usá-la com estes comandos.

	$stAccount="<your storage account name>"
	New-AzureStorageAccount -StorageAccountName $stAccount -Location $loc
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $stAccount

Esta é a configuração atual.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG01.png)

## Fase 2: configurar o DC1

DC1 é um controlador de domínio para o domínio corp.contoso.com do AD DS (Serviços de Domínio do Active Directory) e um servidor DNS para as máquinas virtuais da rede virtual TestLab.

Primeiro, preencha o nome de seu serviço de nuvem e execute estes comandos no prompt de comando do PowerShell do Azure em seu computador local para criar uma Máquina Virtual do Azure para DC1.

	$serviceName="<your cloud service name>"
	$cred=Get-Credential –Message "Type the name and password of the local administrator account for DC1."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DC1 -InstanceSize Small -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel "AD" -LUN 0 -HostCaching None
	$vm1 | Set-AzureSubnet -SubnetNames Corpnet
	$vm1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

Em seguida, conecte-se à máquina virtual DC1.

1.	No portal do Azure, clique em **máquinas virtuais** no painel esquerdo e, em seguida, clique **iniciado** na coluna **Status** para a máquina virtual DC1.  
2.	Na barra de tarefas, clique em **Conectar**.
3.	Quando solicitado a abrir DC1.rdp, clique em **Abrir**.
4.	Quando receber uma caixa de mensagem de Conexão de Área de Trabalho Remota, clique em **Conectar**.
5.	Quando solicitado a fornecer credenciais, use estas:
- Nome: **DC1\**[nome da conta do administrador local]
- Senha: [senha da conta de administrador local]
6.	Quando receber uma caixa de mensagem de Conexão de Área de Trabalho Remota referindo-se aos certificados, clique em **Sim**.

Em seguida, adicione um disco de dados extra como um novo volume com a letra da unidade F:.

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

Em seguida, configure DC1 como um controlador de domínio e servidor DNS para o domínio corp.contoso.com. Execute estes comandos em um prompt de comando do Windows PowerShell de nível de administrador.

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSForest -DomainName corp.contoso.com -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

Após a reinicialização de DC1, reconecte-se à máquina virtual DC1.

1.	Na página de máquinas virtuais do portal do Azure, clique em **Executando** na coluna **Status** para a máquina virtual DC1.
2.	Na barra de tarefas, clique em **Conectar**.
3.	Quando solicitado a abrir DC1.rdp, clique em **Abrir**.
4.	Quando receber uma caixa de mensagem de Conexão de Área de Trabalho Remota, clique em **Conectar**.
5.	Quando solicitado a fornecer credenciais, use estas:
- Nome: **CORP\**[nome da conta do administrador local]
- Senha: [senha da conta de administrador local]
6.	Quando receber uma caixa de mensagem de Conexão de Área de Trabalho Remota referindo-se aos certificados, clique em **Sim**.

Em seguida, crie uma conta de usuário no Active Directory que será usada ao fazer logon em computadores membros do domínio CORP. Execute estes comandos, um de cada vez, em um prompt de comando do Windows PowerShell com nível de administrador.

	New-ADUser -SamAccountName User1 -AccountPassword (read-host "Set user password" -assecurestring) -name "User1" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false
	Add-ADPrincipalGroupMembership -Identity "CN=User1,CN=Users,DC=corp,DC=contoso,DC=com" -MemberOf "CN=Enterprise Admins,CN=Users,DC=corp,DC=contoso,DC=com","CN=Domain Admins,CN=Users,DC=corp,DC=contoso,DC=com"

Observe que o primeiro comando resulta em um prompt para fornecer a senha da conta User1. Como essa conta será usada para conexões de área de trabalho remota para todos os computadores membros do domínio CORP, escolha uma senha forte. Para verificar a força da senha, consulte [Verificador de senha: usando senhas fortes](https://www.microsoft.com/security/pc-security/password-checker.aspx). Registre a senha da conta User1 e armazene-a em um local seguro.

Reconecte-se à máquina virtual DC1 usando a conta CORP\\User1.

Em seguida, para permitir o tráfego para a ferramenta Ping, execute este comando em um prompt de comando do Windows PowerShell com nível de administrador.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True

Esta é a configuração atual.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG02.png)

## Fase 3: configurar o APP1

O APP1 fornece serviços Web e de compartilhamento de arquivos.

Primeiro, preencha o nome de seu serviço de nuvem e execute estes comandos no prompt de comando do PowerShell do Azure em seu computador local para criar uma Máquina Virtual do Azure para APP1.

	$serviceName="<your cloud service name>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for APP1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name APP1 -InstanceSize Small -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames Corpnet
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

Em seguida, conecte-se à máquina virtual APP1 usando as credenciais de CORP\\User1 e abra um prompt de comando do Windows PowerShell com nível de administrador.

Para verificar a comunicação de rede e a resolução de nomes entre APP1 e DC1, execute o comando **ping dc1.corp.contoso.com** e verifique se há quatro respostas.

Em seguida, torne APP1 um servidor Web com este comando no prompt de comando do Windows PowerShell.

	Install-WindowsFeature Web-WebServer –IncludeManagementTools

Crie uma pasta compartilhada e um arquivo de texto dentro da pasta em APP1 com estes comandos.

	New-Item -path c:\files -type directory
	Write-Output "This is a shared file." | out-file c:\files\example.txt
	New-SmbShare -name files -path c:\files -changeaccess CORP\User1

Esta é a configuração atual.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG03.png)

## Fase 4: configurar o CLIENT1

CLIENT1 atua como um tablet, computador laptop ou desktop típico na intranet da Contoso.

Primeiro, preencha o nome de seu serviço de nuvem e execute estes comandos no prompt de comando do PowerShell do Azure em seu computador local para criar uma Máquina Virtual do Azure para CLIENT1.

	$serviceName="<your cloud service name>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for CLIENT1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name CLIENT1 -InstanceSize Small -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames Corpnet
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

Depois, conecte-se à máquina virtual CLIENT1 com as credenciais de CORP\\User1.

Para verificar a comunicação de rede e a resolução de nomes entre CLIENT1 e DC1, execute o comando **ping dc1.corp.contoso.com** em um prompt de comando do Windows PowerShell e verifique se há quatro respostas.

Verifique se que você pode acessar recursos Web e de compartilhamento de arquivos em APP1 por meio de CLIENT1.

1.	No Gerenciador de Servidores, no painel de árvore, clique em **Servidor Local**.
2.	Em **Propriedades para CLIENT1**, clique em **Ativado** ao lado de **Configuração de Segurança Aprimorada do IE**.
3.	Em **Configuração de Segurança Aprimorada do Internet Explorer**, clique em **Desativado** para **Administradores** e **Usuários** e, em seguida, clique em **OK**.
4.	Na tela Inicial, clique em **Internet Explorer** e em **OK**.
5.	Na barra de endereços, digite ****http://app1.corp.contoso.com/** e pressione ENTER. Você verá a página da Web de Serviços de Informações da Internet padrão para APP1.
6.	Na barra de tarefas da área de trabalho, clique no ícone do Gerenciador de Arquivos.
7.	Na barra de endereços, digite **\\\app1\\Files** e pressione ENTER.
8.	Você deverá ver uma janela de pasta com o conteúdo da pasta compartilhada Arquivos.
9.	Na janela de pasta compartilhada **Arquivos**, clique duas vezes no arquivo **Example.txt**. Você deverá ver o conteúdo do arquivo Example.txt.
10.	Feche as janelas de **example.txt - Bloco de Notas** e da pasta compartilhada **Arquivos**.

Essa é a configuração final.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG04.png)

A configuração básica no Azure agora está pronta para desenvolvimento e teste de aplicativos ou para ambientes de teste adicionais, como o [ambiente simulado de nuvem híbrida](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md).

## Recursos adicionais

[Laboratório de teste do Azure](http://social.technet.microsoft.com/wiki/contents/articles/24092.azure-test-lab.aspx)

[Ambientes de teste de nuvem híbrida](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md)

[Ambiente de teste Configuração de Base com o Gerenciador de Recursos do Azure](virtual-machines-base-configuration-test-environment-resource-manager.md)

## <a id="costs"></a>Minimizando os custos de máquinas virtuais do ambiente de teste no Azure

Para minimizar o custo da execução de máquinas virtuais no ambiente de teste, você pode fazer o seguinte:

- Crie o ambiente de teste e execute seus testes e demonstrações necessários o mais rápido possível. Ao concluir, exclua as máquinas virtuais do ambiente de teste.
- Desligue as máquinas virtuais do ambiente teste para um estado desalocado.

Para desligar as máquinas virtuais com o PowerShell do Azure, preencha o nome do serviço de nuvem e execute estes comandos.

	$serviceName="<your cloud service name>"
	Stop-AzureVM -ServiceName $serviceName -Name "CLIENT1"
	Stop-AzureVM -ServiceName $serviceName -Name "APP1"
	Stop-AzureVM -ServiceName $serviceName -Name "DC1" -Force -StayProvisioned


Para garantir que suas máquinas virtuais funcionem corretamente ao iniciar todas elas no estado Parado (Desalocado), você deve iniciá-las na seguinte ordem:

1.	DC1
2.	APP1
3.	CLIENT1

Para iniciar as máquinas virtuais em ordem com o PowerShell do Azure, preencha o nome do serviço de nuvem e execute estes comandos.

	$serviceName="<your cloud service name>"
	Start-AzureVM -ServiceName $serviceName -Name "DC1"
	Start-AzureVM -ServiceName $serviceName -Name "APP1"
	Start-AzureVM -ServiceName $serviceName -Name "CLIENT1"

<!---HONumber=AcomDC_1125_2015-->