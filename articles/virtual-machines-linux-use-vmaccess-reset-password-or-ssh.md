<properties 
	pageTitle="Como usar VMAccess para máquinas virtuais Linux" 
	description="Como usar a extensão VMAccess para Linux para redefinir senhas e chaves SSH, para reenviar configurações de SSH e excluir usuários do Linux" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2015" 
	ms.author="kathydav"/>

# Como redefinir uma senha ou SSH para máquinas virtuais Linux #

Se não puder se conectar a uma máquina virtual Linux devido a uma senha esquecida, uma chave incorreta do Secure Shell (SSH) ou um problema com a configuração de SSH, use a extensão VMAccessForLinux para redefinir a senha ou a chave SSH ou corrigir a configuração de SSH. 

## Requisitos

- Microsoft Azure Linux Agent versão 2.0.5 ou posterior. A maioria das imagens do Linux na Galeria de máquinas virtuais incluem a versão 2.0.5. Para descobrir qual versão está instalada, execute `waagent -version`. Para atualizar o agente, siga as instruções no [Guia do usuário do agente Linux do Azure].
- PowerShell do Azure. Você usará comandos no cmdlet **Set-AzureVMExtension** para carregar e configurar automaticamente a extensão **VMAccessForLinux**. Para obter detalhes sobre como configurar o PowerShell do Azure, consulte [Como instalar e configurar o PowerShell do Azure].
- Uma nova senha ou conjunto de chaves SSH, se quiser redefinir um deles. Você não precisa deles para redefinir a configuração de SSH. 

## Nenhuma instalação é necessária

A extensão VMAccess não precisa ser instalada para que você possa usá-la. Desde que o agente Linux esteja instalado na máquina virtual, a extensão será carregada automaticamente quando você executar um comando do PowerShell do Azure que usa o cmdlet **Set-AzureVMExtension**. 

## Usar a extensão para redefinir uma senha, chave SSH ou a configuração de SSH, ou para excluir um usuário

Você usará o cmdlet **Set-AzureVMExtension** para fazer as alterações que o VMAccess permite que você faça. Em todos os casos, comece usando o nome do serviço de nuvem e o nome de máquina virtual para obter o objeto de máquina virtual e armazená-lo em uma variável. 

Preencha os nomes do serviço de nuvem e da máquina virtual e execute os seguintes comandos em um prompt de comando com nível de administrador do PowerShell do Azure. Substitua tudo entre aspas, incluindo os caracteres < e >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName

Se você não souber o nome da máquina virtual e serviço de nuvem, execute **Get-AzureVM** para exibir essas informações para todas as VMs em sua assinatura atual.


> [AZURE.NOTE] As linhas de comando que começam com $ definem variáveis do PowerShell que são usadas mais tarde em comandos do PowerShell.

Se tiver criado a máquina virtual com o Portal de Gerenciamento do Azure, execute o seguinte comando adicional:

	$vm.GetInstance().ProvisionGuestAgent = $true

Este comando impedirá que o erro "O Agente Convidado de Provisionamento deve ser habilitado no objeto de VM antes de configurar a extensão de Acesso a VM por IaaS" ao executar o comando Set-AzureVMExtension nas seções a seguir. 

Em seguida, você pode realizar as seguintes tarefas:

+ [Redefinir a senha](#password)
+ [Redefinir uma chave SSH](#SSHkey)
+ [Redefinir a senha e a chave SSH](#both)
+ [Redefinir a configuração de SSH](#config)
+ [Excluir um usuário](#delete)

### <a name="password"></a>Redefinir a senha

Preencha o nome de usuário Linux atual e a nova senha; em seguida, execute estes comandos.

	$UserName = "<current Linux account name>"
	$Password = "<new password>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}' 
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] Se você deseja redefinir a senha ou a chave SSH para uma conta de usuário existente, certifique-se de digitar o nome exato do usuário. Se você digitar um nome diferente, a extensão VMAccess cria uma nova conta de usuário e atribui a senha para essa conta.


### <a name="SSHKey"></a>Redefinir uma chave SSH

Preencha o nome de usuário Linux atual e o caminho para o certificado que contém as chaves SSH; em seguida, execute estes comandos.

	$UserName = "<current Linux user name>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "ssh_key":"' + $cert + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="both"></a>Redefinir a senha e a chave SSH

Preencha o nome de usuário Linux atual, a senha e o caminho para o certificado que contém as chaves SSH; em seguida, execute estes comandos.

	$UserName = "<current Linux user name>"
	$Password = "<new password>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '", "ssh_key":"' + $cert + '"}' 
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="config"></a>Redefinir a configuração de SSH

Erros na configuração de SSH podem impedir que você acesse a máquina virtual. Você pode corrigir isso redefinindo a configuração de SSH para seu estado padrão. Isso remove os novos parâmetros de acesso na configuração, como nome de usuário, senha e a chave SSH, mas isso não altera a senha ou chaves SSH da conta de usuário. A extensão reinicia o servidor SSH, abre a porta SSH na sua máquina virtual e redefine a configuração de SSH como para o padrão. 

Execute estes comandos.

	$PrivateConfig = '{"reset_ssh": "True"}' 
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] O arquivo de configuração SSH está localizado em /etc/ssh/sshd_config.

### <a name="delete"></a> Excluir um usuário

Preencha o nome de usuário Linux a excluir e execute estes comandos.

	$UserName = "<Linux user name to delete>"
	$PrivateConfig = "{"remove_user": "' + $UserName + '"}"
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

## Recursos adicionais

[Recursos e extensões de VM do Azure] []

[Conectar-se a uma máquina virtual do Azure com RDP ou SSH] []


<!--Link references-->
[Guia do usuário do agente Linux para o Azure]: ../virtual-machines-linux-agent-user-guide
[Como instalar e configurar o PowerShell do Azure]: ../install-configure-powershell
[Recursos e extensões de VM do Azure]: http://msdn.microsoft.com/library/azure/dn606311.aspx
[Conectar-se a uma máquina virtual do Azure com RDP ou SSH]: http://msdn.microsoft.com/library/azure/dn535788.aspx






<!--HONumber=45--> 
