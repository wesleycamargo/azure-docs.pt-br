<properties
	pageTitle="Redefinir senha de VM do Linux a partir da CLI do Azure"
	description="Como usar a extensão VMAccess no Portal do Azure ou na CLI para redefinir senhas de VM do Linux e chaves SSH, configurações de SSH e excluir contas de usuários."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/28/2015"
	ms.author="cynthn"/>

# Como redefinir uma senha ou SSH para máquinas virtuais Linux #

Se você não pode se conectar a uma máquina virtual Linux devido a uma senha esquecida, uma chave do Secure Shell (SSH) incorreta ou um problema com a configuração do SSH, use o Portal de Visualização do Azure ou a extensão de VMAccessForLinux para redefinir a senha ou chave SSH ou corrigir a configuração do SSH. Observe que este artigo se refere às máquinas virtuais criadas usando o modelo de implantação **Clássica**.

## Portal de visualização do Azure

Para redefinir a configuração do SSH no [Portal de Visualização do Azure](https://portal.azure.com), clique em **Procurar** > **Máquinas virtuais** > *sua máquina virtual Linux* > **Redefinir Acesso Remoto**. Aqui está um exemplo.

![](./media/virtual-machines-linux-use-vmaccess-reset-password-or-ssh/Portal-RDP-Reset-Linux.png)

Para redefinir o nome e a senha da conta de usuário com privilégios sudo ou a chave pública SSH no [Portal de Visualização do Azure](https://portal.azure.com), clique em **Procurar** > **Máquinas virtuais** > *sua máquina virtual Linux* > **Todas as configurações** > **Redefinição de senha**. Aqui está um exemplo.

![](./media/virtual-machines-linux-use-vmaccess-reset-password-or-ssh/Portal-PW-Reset-Linux.png)


## PowerShell e CLI do Azure

Você precisará do seguinte:

- Microsoft Azure Linux Agent versão 2.0.5 ou posterior. A maioria das imagens do Linux na Galeria de máquinas virtuais incluem a versão 2.0.5. Para descobrir qual versão está instalada, execute **waagent -version**. Para atualizar o agente, siga as instruções de [Guia do Usuário do Agente do Azure Linux].
- Interface de Linha de Comando (CLI) do Azure. Para obter detalhes sobre como configurar a CLI do Azure, confira [Instalar e Configurar a Interface de Linha de Comando do Azure](../xplat-cli.md).
- PowerShell do Azure. Você usará comandos no cmdlet Set-AzureVMExtension para carregar e configurar automaticamente a extensão VMAccessForLinux. Para obter detalhes sobre como configurar o PowerShell do Azure, confira [Como instalar e configurar o PowerShell do Azure].
- Uma nova senha ou conjunto de chaves SSH, se quiser redefinir um deles. Você não precisa deles para redefinir a configuração de SSH.

### Nenhuma instalação é necessária

A extensão VMAccess não precisa ser instalada para que você possa usá-la. Desde que o agente Linux esteja instalado na máquina virtual, a extensão será carregada automaticamente quando você executar um comando do PowerShell do Azure que usa o cmdlet **Set-AzureVMExtension**.

## Usar a CLI do Azure

Com a CLI do Azure, você poderá usar o comando **azure** na sua interface de linha de comando (Bash, Terminal, prompt de comando) para acessar comandos. Executar **definir a extensão de vm do azure – ajuda** para uso detalhado de extensão.

Com o CLI do Azure, você pode realizar as seguintes tarefas:

+ [Redefinir a senha](#pwresetcli)
+ [Redefinir a chave SSH](#sshkeyresetcli)
+ [Redefinir a senha e a chave SSH](#resetbothcli)
+ [Criar uma nova conta de usuário sudo](#createnewsudocli)
+ [Redefinir a configuração de SSH](#sshconfigresetcli)
+ [Excluir um usuário](#deletecli)
+ [Exibir o status da extensão VMAccess](#statuscli)

### <a name="pwresetcli"></a>Redefinir a senha

Etapa 1: Crie um arquivo chamado PrivateConf.json com esses conteúdos, substituindo os valores de espaço reservado.

	{
	"username":"currentusername",
	"password":"newpassword",
	"expiration":"2016-01-01",
	}

Etapa 2: Executar esse comando, substituindo o nome da máquina virtual para "vmname".

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

### <a name="sshkeyresetcli"></a>Redefinir a chave SSH

Etapa 1: Crie um arquivo chamado PrivateConf.json com esses conteúdos, substituindo os valores de espaço reservado.

	{
	"username":"currentusername",
	"ssh_key":"contentofsshkey",
	}

Etapa 2: Executar esse comando, substituindo o nome da máquina virtual para "vmname".

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="resetbothcli"></a>Redefinir a senha e a chave SSH

Etapa 1: Crie um arquivo chamado PrivateConf.json com esses conteúdos, substituindo os valores de espaço reservado.

	{
	"username":"currentusername",
	"ssh_key":"contentofsshkey",
	"password":"newpassword",
	}

Etapa 2: Executar esse comando, substituindo o nome da máquina virtual para "vmname".

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="createnewsudocli"></a>Criar uma nova conta de usuário sudo

Se você esquecer seu nome de usuário, você pode usar VMAccess para criar um novo com a autoridade sudo. Nesse caso, o nome de usuário e a senha existente não serão modificados.

Para criar um novo usuário sudo com senha de acesso, use o script em [Redefinir a senha](#pwresetcli) e especifique o novo nome de usuário.

Para criar um novo usuário sudo com senha de acesso, use o script em [Redefinir a chave SSH](#sshkeyresetcli) e especifique o novo nome de usuário.

Você também pode usar [Redefinir a senha e a chave SSH](#resetbothcli) para criar um novo usuário com senha e o acesso à chave de SSH.

### <a name="sshconfigresetcli"></a>Redefinir a configuração de SSH

Se a configuração do SSH está em um estado indesejado, você também pode perder o acesso à VM. Você pode usar a extensão VMAccess para redefinir a configuração para seu estado padrão. Para fazer isso, basta definir a chave "reset\_ssh" como "True". A extensão reinicia o servidor SSH, abre a porta SSH na sua VM e redefine a configuração SSH como padrão. A conta de usuário (nome, senha ou chaves SSH) não será alterada.

> [AZURE.NOTE]O arquivo de configuração SSH está localizado em /etc/ssh/sshd\_config.

Etapa 1: Crie um arquivo chamado PrivateConf.json com esse conteúdo.

	{
	"reset_ssh":"True",
	}

Etapa 2: Executar esse comando, substituindo o nome da máquina virtual para "vmname".

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="deletecli"></a>Excluir um usuário

Se você deseja excluir uma conta de usuário sem efetuar login à VM diretamente, você pode usar este script.

Etapa 1: Crie um arquivo chamado PrivateConf.json com esse conteúdo, substituindo os valores de espaço reservado.

	{
	"remove_user":"usernametoremove",
	}

Etapa 2: Executar esse comando, substituindo o nome da máquina virtual para "vmname".

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="statuscli"></a>Exibir o status da extensão VMAccess

Para exibir o status da extensão VMAccess, execute este comando.

	azure vm extension get


## Usar PowerShell do Azure

Você usará o cmdlet **Set-AzureVMExtension** para fazer as alterações que o VMAccess permite que você faça. Em todos os casos, comece usando o nome do serviço de nuvem e o nome de máquina virtual para obter o objeto de máquina virtual e armazená-lo em uma variável.

Preencha os nomes do serviço de nuvem e da máquina virtual e execute os seguintes comandos em um prompt de comando com nível de administrador do PowerShell do Azure. Substitua tudo entre aspas, incluindo os caracteres < and >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName

Se você não souber o nome da máquina virtual e serviço de nuvem, execute **Get-AzureVM** para exibir essas informações para todas as VMs em sua assinatura atual.


> [AZURE.NOTE]As linhas de comando que começam com $ definem variáveis do PowerShell que são usadas mais tarde em comandos do PowerShell.

Se tiver criado a máquina virtual com o Portal de Gerenciamento do Azure, execute o seguinte comando adicional:

	$vm.GetInstance().ProvisionGuestAgent = $true

Este comando impedirá que o erro "O Agente Convidado de Provisionamento deve ser habilitado no objeto de VM antes de configurar a extensão de Acesso a VM por IaaS" ao executar o comando Set-AzureVMExtension nas seções a seguir.

Em seguida, você pode realizar as seguintes tarefas:

+ [Redefinir a senha](#password)
+ [Redefinir uma chave SSH](#SSHkey)
+ [Redefinir a senha e a chave SSH](#both)
+ [Redefinir a configuração de SSH](#config)
+ [Excluir um usuário](#delete)
+ [Exibir o status da extensão VMAccess](#status)

### <a name="password"></a>Redefinir a senha

Preencha o nome de usuário Linux atual e a nova senha; em seguida, execute estes comandos.

	$UserName = "<current Linux account name>"
	$Password = "<new password>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE]Se você deseja redefinir a senha ou a chave SSH para uma conta de usuário existente, certifique-se de digitar o nome exato do usuário. Se você digitar um nome diferente, a extensão VMAccess cria uma nova conta de usuário e atribui a senha para essa conta.


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

> [AZURE.NOTE]O arquivo de configuração SSH está localizado em /etc/ssh/sshd\_config.

### <a name="delete"></a> Excluir um usuário

Preencha o nome de usuário Linux a excluir e execute estes comandos.

	$UserName = "<Linux user name to delete>"
	$PrivateConfig = "{"remove_user": "' + $UserName + '"}"
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM


### <a name="status"></a>Exibir o status da extensão VMAccess

Para exibir o status da extensão VMAccess, execute este comando.

	$vm.GuestAgentStatus


## Recursos adicionais

[Recursos e extensões de VM do Azure][]

[Conectar-se a uma máquina virtual do Azure com RDP ou SSH][]


<!--Link references-->
[Guia do Usuário do Agente do Azure Linux]: virtual-machines-linux-agent-user-guide.md
[Como instalar e configurar o PowerShell do Azure]: ../install-configure-powershell.md
[Recursos e extensões de VM do Azure]: http://msdn.microsoft.com/library/azure/dn606311.aspx
[Conectar-se a uma máquina virtual do Azure com RDP ou SSH]: http://msdn.microsoft.com/library/azure/dn535788.aspx

<!---HONumber=Sept15_HO4-->