<properties
	pageTitle="Ambiente de teste Configuração de Base com o Gerenciador de Recursos do Azure"
	description="Aprenda a criar um ambiente de desenvolvimento/teste simples que simule uma intranet simplificada no Microsoft Azure."
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
	ms.date="04/25/2016"
	ms.author="josephd"/>

# Ambiente de teste de configuração básica

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

Este artigo apresenta instruções passo a passo para criar o ambiente de teste Configuração de Base em uma Rede Virtual do Microsoft Azure usando máquinas virtuais criadas no Gerenciador de Recursos.

Você pode usar o ambiente de teste resultante:

- Para testes e desenvolvimento de aplicativos.
- Como a configuração inicial de um ambiente de teste estendido do seu próprio design que inclua máquinas virtuais adicionais e serviços do Azure.

O ambiente de teste Configuração de Base consiste na sub-rede Corpnet em uma rede virtual do Azure somente de nuvem denominada TestLab, que simula uma intranet simplificada e privada conectada à Internet.

![](./media/virtual-machines-windows-test-config-env/virtual-machines-windows-test-config-env-ph4.png)

Ele contém:

- Uma máquina virtual do Azure que executa o Windows Server 2012 R2, denominada DC1, que é configurada como um controlador de domínio de intranet e um servidor DNS (Sistema de Nomes de Domínio).
- Uma máquina virtual do Azure que executa o Windows Server 2012 R2, denominada APP1, que é configurada como um servidor Web e de aplicativos geral.
- Uma máquina virtual do Azure que executa o Windows Server 2012 R2, denominada CLIENT1, que atua como um cliente de intranet.

Essa configuração permite que os computadores DC1, APP1, CLIENT1 e computadores adicionais da sub-rede Corpnet sejam:

- Conectados à Internet para instalar atualizações, acessar recursos da Internet em tempo real e participar de tecnologias de nuvem pública, como o Microsoft Office 365 e outros serviços do Azure.
- Gerenciados remotamente usando Conexões de Área de Trabalho Remota de seu computador que está conectado à Internet ou à rede de sua organização.

Há quatro fases para configurar a sub-rede Corpnet do ambiente de teste de Configuração de Base do Windows Server 2012 R2 no Azure.

1.	Crie a rede virtual.
2.	Configurar o DC1.
3.	Configurar o APP1.
4.	Configurar o CLIENT1.

Se ainda não tiver uma conta do Azure, você poderá se inscrever para obter uma avaliação gratuita em [Experimentar o Azure](https://azure.microsoft.com/pricing/free-trial/). Se você tiver uma assinatura do MSDN ou do Visual Studio, confira [Crédito mensal do Azure para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

> [AZURE.NOTE] As máquinas virtuais no Azure incorrem em um custo monetário contínuo quando estão em execução. Esse custo é cobrado em sua avaliação gratuita, assinatura do MSDN ou assinatura paga. Para obter mais informações sobre os custos da execução de máquinas virtuais do Azure, consulte [Detalhes de preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/) e [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/). Para reduzir os custos, consulte [Minimizando os custos de máquinas de virtuais do ambiente de teste no Azure](#costs).

## Fase 1: Criar a rede virtual

Primeiro, inicie um prompt do Azure PowerShell.

> [AZURE.NOTE] O comando a seguir define o uso do Azure PowerShell 1.0 e posterior. Para obter mais informações, consulte [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Faça logon em sua conta.

	Login-AzureRMAccount

Obtenha o nome da sua assinatura usando o comando a seguir.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Defina sua assinatura do Azure. Substitua tudo que estiver entre aspas, inclusive os caracteres < and >, pelos nomes corretos.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Depois crie um novo grupo de recursos para seu laboratório de teste Configuração de Base. Para determinar um nome exclusivo de grupo de recursos, use este comando para listar os grupos de recursos existentes.

	Get-AzureRMResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Crie seu novo grupo de recursos com estes comandos. Substitua tudo que estiver entre aspas, inclusive os caracteres < and >, pelos nomes corretos.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureRMResourceGroup -Name $rgName -Location $locName

Máquinas virtuais baseadas no Gerenciador de Recursos exigem uma conta de armazenamento baseada no Gerenciador de Recursos. Você deve escolher um nome global exclusivo para sua conta de armazenamento que contenha apenas letras minúsculas e números. Você pode usar este comando para listar as contas de armazenamento existentes.

	Get-AzureRMStorageAccount | Sort StorageAccountName | Select StorageAccountName

Crie uma nova conta de armazenamento para o novo ambiente de teste com estes comandos.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<storage account name>"
	New-AzureRMStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

Em seguida, crie a Rede Virtual TestLab do Azure que hospedará a sub-rede Corpnet da configuração de base e proteja-a com um grupo de segurança de rede.

	$rgName="<name of your new resource group>"
	$locName="<Azure location name, such as West US>"
	$locShortName="<the location of your new resource group in lowercase with spaces removed, example: westus>"
	$corpnetSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name Corpnet -AddressPrefix 10.0.0.0/24
	New-AzureRMVirtualNetwork -Name TestLab -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/8 -Subnet $corpnetSubnet –DNSServer 10.0.0.4
	$rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
	New-AzureRMNetworkSecurityGroup -Name Corpnet -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
	$vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name TestLab
	$nsg=Get-AzureRMNetworkSecurityGroup -Name Corpnet -ResourceGroupName $rgName
	Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name Corpnet -AddressPrefix "10.0.0.0/24" -NetworkSecurityGroup $nsg

Esta é a configuração atual.

![](./media/virtual-machines-windows-test-config-env/virtual-machines-windows-test-config-env-ph1.png)

## Fase 2: configurar o DC1

DC1 é um controlador de domínio para o domínio corp.contoso.com do AD DS (Serviços de Domínio do Active Directory) e um servidor DNS para as máquinas virtuais da rede virtual TestLab.

Em primeiro lugar, preencha o nome do grupo de recursos, o local do Azure, o nome da conta de armazenamento e execute estes comandos no prompt de comando do Azure PowerShell no computador local para criar uma Máquina Virtual do Azure para DC1.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzureRMPublicIpAddress -Name DC1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRMNetworkInterface -Name DC1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 10.0.0.4
	$vm=New-AzureRMVMConfig -VMName DC1 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC1-TestLab-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC1."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC1-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name DC1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Em seguida, conecte-se à máquina virtual DC1.

1.	No portal do Azure, clique em **Máquinas Virtuais** e, em seguida, clique na máquina virtual **DC1**.  
2.	No painel **DC1**, clique em **Conectar**.
3.	Quando solicitado, abra o arquivo DC1.rdp baixado.
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

Observe que esses comandos podem levar alguns minutos para ser concluídos.

Após a reinicialização de DC1, reconecte-se à máquina virtual DC1.

1.	No portal do Azure, clique em **Máquinas Virtuais** e, em seguida, clique na máquina virtual **DC1**.
2.	No painel **DC1**, clique em **Conectar**.
3.	Quando solicitado a abrir DC1.rdp, clique em **Abrir**.
4.	Quando receber uma caixa de mensagem de Conexão de Área de Trabalho Remota, clique em **Conectar**.
5.	Quando solicitado a fornecer credenciais, use estas:
- Nome: **CORP\**[nome da conta do administrador local]
- Senha: [senha da conta de administrador local]
6.	Quando receber uma caixa de mensagem de Conexão de Área de Trabalho Remota referindo-se aos certificados, clique em **Sim**.

Em seguida, crie uma conta de usuário no Active Directory que será usada ao fazer logon em computadores membros do domínio CORP. Execute este comando em um prompt de comando com nível de administrador do Windows PowerShell.

	New-ADUser -SamAccountName User1 -AccountPassword (read-host "Set user password" -assecurestring) -name "User1" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

Observe que esse comando solicita que você forneça a senha da conta User1. Como essa conta será usada para conexões de área de trabalho remota para todos os computadores membros do domínio CORP, *escolha uma senha forte*. Para verificar a força da senha, consulte [Verificador de senha: usando senhas fortes](https://www.microsoft.com/security/pc-security/password-checker.aspx). Registre a senha da conta User1 e armazene-a em um local seguro.

Em seguida, configure a nova conta User1 como um Administrador Corporativo. Execute este comando no prompt de comando com nível de administrador do Windows PowerShell.

	Add-ADPrincipalGroupMembership -Identity "CN=User1,CN=Users,DC=corp,DC=contoso,DC=com" -MemberOf "CN=Enterprise Admins,CN=Users,DC=corp,DC=contoso,DC=com","CN=Domain Admins,CN=Users,DC=corp,DC=contoso,DC=com"

Feche a sessão Área de Trabalho Remota com DC1 e reconecte usando a conta CORP\\User1.

Em seguida, para permitir o tráfego para a ferramenta Ping, execute este comando em um prompt de comando do Windows PowerShell com nível de administrador.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True

Esta é a configuração atual.

![](./media/virtual-machines-windows-test-config-env/virtual-machines-windows-test-config-env-ph2.png)

## Fase 3: configurar o APP1

O APP1 fornece serviços Web e de compartilhamento de arquivos.

Em primeiro lugar, preencha o nome do grupo de recursos, o local do Azure, o nome da conta de armazenamento e execute estes comandos no prompt de comando do Azure PowerShell no computador local para criar uma Máquina Virtual do Azure para APP1.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzureRMPublicIpAddress -Name APP1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRMNetworkInterface -Name APP1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$vm=New-AzureRMVMConfig -VMName APP1 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for APP1."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName APP1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/APP1-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name APP1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Em seguida, conecte-se à máquina virtual APP1 usando o nome e a senha da conta do administrador local da APP1 e, em seguida, abra um prompt de comando do Windows PowerShell no nível de administrador.

Para verificar a comunicação de rede e a resolução de nomes entre APP1 e DC1, execute o comando **ping dc1.corp.contoso.com** e verifique se há quatro respostas.

Em seguida, integre a máquina virtual APP1 ao domínio CORP com estes comandos no prompt do Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Observe que você deve fornecer as credenciais de conta de domínio CORP\\User1 depois de inserir o comando Add-Computer.

Depois de reiniciar APP1, conecte-se a ela usando o nome e a senha da conta CORP\\User1 e abra um prompt de comando do Windows PowerShell no nível de administrador.

Em seguida, torne APP1 um servidor Web com este comando no prompt de comando do Windows PowerShell.

	Install-WindowsFeature Web-WebServer –IncludeManagementTools

Crie uma pasta compartilhada e um arquivo de texto dentro da pasta em APP1 com estes comandos.

	New-Item -path c:\files -type directory
	Write-Output "This is a shared file." | out-file c:\files\example.txt
	New-SmbShare -name files -path c:\files -changeaccess CORP\User1

Esta é a configuração atual.

![](./media/virtual-machines-windows-test-config-env/virtual-machines-windows-test-config-env-ph3.png)

## Fase 4: configurar o CLIENT1

CLIENT1 atua como um tablet, computador laptop ou desktop típico na intranet da Contoso.

> [AZURE.NOTE] O conjunto de comandos a seguir cria o CLIENT1 executando o Windows Server 2012 R2 Datacenter, o que pode ser feito para todos os tipos de assinaturas do Azure. Caso você tenha uma assinatura do Azure baseada no MSDN, é possível criar CLIENT1 executando o Windows 10, Windows 8 ou Windows 7 usando o [Portal do Azure](virtual-machines-windows-hero-tutorial.md).

Em primeiro lugar, preencha o nome do grupo de recursos, o local do Azure, o nome da conta de armazenamento e execute estes comandos no prompt de comando do Azure PowerShell no computador local para criar uma Máquina Virtual do Azure para CLIENT1.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzureRMPublicIpAddress -Name CLIENT1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRMNetworkInterface -Name CLIENT1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$vm=New-AzureRMVMConfig -VMName CLIENT1 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for CLIENT1."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName CLIENT1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/CLIENT1-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name CLIENT1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Em seguida, conecte-se à máquina virtual CLIENT1 usando o nome e a senha da conta do administrador local CLIENT1 e, em seguida, abra um prompt de comando do Windows PowerShell no nível de administrador.

Para verificar a comunicação de rede e a resolução de nomes entre CLIENT1 e DC1, execute o comando **ping dc1.corp.contoso.com** em um prompt de comando do Windows PowerShell e verifique se há quatro respostas.

Em seguida, integre a máquina virtual CLIENT1 ao domínio CORP com estes comando no prompt do Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Observe que você deve fornecer as credenciais de conta de domínio CORP\\User1 depois de inserir o comando Add-Computer.

Depois de reiniciar CLIENT1, conecte-se a ela usando o nome e a senha da conta CORP\\User1 e abra um prompt de comando do Windows PowerShell no nível de administrador.

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

![](./media/virtual-machines-windows-test-config-env/virtual-machines-windows-test-config-env-ph4.png)

A configuração básica no Azure agora está pronta para desenvolvimento e teste de aplicativos ou para ambientes de teste adicionais.

## Próximas etapas

- Adicionar uma nova máquina virtual usando o [Portal do Azure](virtual-machines-windows-hero-tutorial.md).
- Criar o [ambiente de teste de nuvem híbrida simulado](virtual-machines-setup-simulated-hybrid-cloud-environment-testing.md).


## <a id="costs"></a>Minimizando os custos de máquinas virtuais do ambiente de teste no Azure

Para minimizar o custo da execução de máquinas virtuais no ambiente de teste, você pode fazer o seguinte:

- Crie o ambiente de teste e execute seus testes e demonstrações necessários o mais rápido possível. Ao concluir, exclua as máquinas virtuais do ambiente de teste.
- Desligue as máquinas virtuais do ambiente teste para um estado desalocado.

Para desligar as máquinas virtuais com o Azure PowerShell, preencha o nome do grupo de recursos e execute estes comandos.

	$rgName="<your resource group name>"
	Stop-AzureRMVM -ResourceGroupName $rgName -Name "CLIENT1"
	Stop-AzureRMVM -ResourceGroupName $rgName -Name "APP1"
	Stop-AzureRMVM -ResourceGroupName $rgName -Name "DC1" -Force -StayProvisioned

Para garantir que suas máquinas virtuais funcionem corretamente ao iniciar todas elas no estado Parado (Desalocado), você deve iniciá-las na seguinte ordem:

1.	DC1
2.	APP1
3.	CLIENT1

Para iniciar as máquinas virtuais na ordem com o Azure PowerShell, preencha o nome do grupo de recursos e execute estes comandos.

	$rgName="<your resource group name>"
	Start-AzureRMVM -ResourceGroupName $rgName -Name "DC1"
	Start-AzureRMVM -ResourceGroupName $rgName -Name "APP1"
	Start-AzureRMVM -ResourceGroupName $rgName -Name "CLIENT1"

<!---HONumber=AcomDC_0511_2016-->