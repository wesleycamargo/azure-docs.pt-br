<properties 
	pageTitle="Como usar VMAccess para máquinas virtuais Linux" 
	description="Como usar a extensão VMAccess para Linux para redefinir senhas, chaves SSH e configurações SSH" 
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
	ms.date="10/30/2014" 
	ms.author="kathydav"/>

#Como redefinir uma senha ou SSH para máquinas virtuais Linux#

Se você não pode se conectar a uma VM Linux porque esqueceu a senha, chave SSH ou um problema com a configuração SSH, use a extensão VMAccessforLinux para redefinir a senha, a chave SSH ou a configuração SSH. 


##Requisitos

- Microsoft Azure Linux Agent versão 2.0.5 ou posterior. A maioria das imagens do Linux na Galeria de máquinas virtuais incluem a versão 2.0.5. Para descobrir qual versão está instalada, execute `waagent -version`. Para atualizar o agente, siga as instruções no [Guia do usuário do agente Linux do Azure].

- O módulo do PowerShell do Azure. O módulo inclui o cmdlet **Set-AzureVMExtension**, que você irá executar comandos com para usar a extensão **VMAccessForLinux**. Para obter detalhes sobre como configurar o módulo, consulte [Como instalar e configurar o PowerShell do Azure].

- Uma nova senha ou chaves SSH, se você deseja redefinir qualquer um deles. Você não precisa deles para corrigir a configuração SSH. 

##Nenhuma instalação é necessária

O VMAccess não precisa ser instalado antes que você possa usá-lo. Enquanto o agente do Linux e o módulo do Azure são instalados, a extensão é carregada automaticamente quando você executa um comando que chama o cmdlet **Set-AzureVMExtension**. 

##Usar a extensão para redefinir uma senha, configuração ou chave SSH ou adicionar um usuário

Você usará o cmdlet **Set-AzureVMExtension** para fazer as alterações que o VMAccess permite que você faça. Em todos os casos, comece usando o nome do serviço de nuvem e o nome de máquina virtual para obter o objeto de máquina virtual e armazená-lo em uma variável.   

Abra o PowerShell do Azure e digite o seguinte no prompt de comando. Certifique-se de substituir os espaços reservados MyServiceName e MyVMName pelos nomes reais:

	PS C:\> $vm = Get-AzureVM -ServiceName 'MyServiceName' -Name 'MyVMName'

Em seguida, você pode realizar as seguintes tarefas:

+ [Redefinir a senha](#password)
+ [Redefinir uma chave SSH](#SSHkey)
+ [Redefinir a senha e a chave SSH](#both)
+ [Redefinir a configuração SSH](#config)

### <a name="password"></a>Redefinir a senha
Digite o nome de usuário e senha e armazene-os em variáveis e crie uma única variável para armazenar os valores para que os próximos comandos possam usá-los.

	PS C:\> $UserName = "CurrentName"
	PS C:\> $Password = "NewPassword"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}' 

Armazene o nome, o editor e o número de versão em variáveis: 

	PS C:\> ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'

Com todos os valores necessários armazenados em variáveis, execute o seguinte comando:

	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] Se você deseja redefinir a senha ou a chave SSH para uma conta de usuário existente, certifique-se de digitar o nome exato do usuário. Se você digitar um nome diferente, a extensão VMAccess cria uma nova conta de usuário e atribui a senha para essa conta.

### <a name="SSHkey"></a>Redefinir uma chave SSH

Digite o nome de usuário e o caminho da sua nova chave SSH pública e armazene-os em variáveis:

	PS C:\> $UserName = "CurrentName"
	PS C:\> $cert = Get-Content "CertPath"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "ssh_key":"' + $cert + '"}'

Execute os seguintes comandos:

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM


### <a name="both"></a>Redefinir a senha e a chave SSH

Para o usuário atual, digite uma nova senha e o caminho do novo certificado com a chave pública SSH e armazene-os em variáveis: 

	PS C:\> $UserName = "CurrentName"	
	PS C:\> $Password = "NewPassword"
	PS C:\> $cert = Get-Content "CertPath"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '", "ssh_key":"' + $cert + '"}' 

Execute os seguintes comandos:

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

###  <a name="config"></a>Redefinir a configuração SSH

Erros na configuração SSH podem impedir que você acesse a VM. Você pode corrigir isso, redefinindo a configuração para o padrão. Isso remove os novos parâmetros de acesso na configuração (nome de usuário, senha ou chave SSH). Isso não altera a senha ou chaves SSH da conta de usuário. A extensão reinicia o servidor SSH, abre a porta SSH na sua VM e redefine a configuração SSH como padrão.  

Defina o sinalizador que indica que você deseja redefinir a configuração e o armazene-o em uma variável: 
	
	PS C:\> $PrivateConfig = '{"reset_ssh": "True"}' 

Execute os seguintes comandos:

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] O arquivo de configuração SSH está localizado em /etc/ssh/sshd_config.

## Solução de problemas

Quando você usa o cmdlet **Set-AzureVMExtension**, você poderá receber esse erro: "O Agente convidado de provisão deve estar habilitado no objeto da VM antes de configurar a extensão de acesso à VM IaaS". 

Isso pode acontecer se você usou o Portal de Gerenciamento para criar a VM do Linux, porque o valor da propriedade de agente convidado não pode ser definido como "True". Para corrigir esse problema, execute os seguintes comandos:

	PS C:\> $vm = Get-AzureVM -ServiceName 'MyServiceName' -Name 'MyVMName'

	PS C:\> $vm.GetInstance().ProvisionGuestAgent = $true

#Recursos adicionais
[Recursos e extensões de VM do Azure] []

[Conectar-se a uma máquina virtual do Azure com RDP ou SSH] []


<!--Link references-->
[Guia do usuário do agente Linux do Azure]: ../virtual-machines-linux-agent-user-guide
[Como instalar e configurar o PowerShell do Azure]: ../install-configure-powershell
[Recursos e extensões de VM do Azure]: http://msdn.microsoft.com/pt-br/library/azure/dn606311.aspx
[Conectar-se a uma máquina virtual do Azure com RDP ou SSH]: http://msdn.microsoft.com/pt-br/library/azure/dn535788.aspx

<!--HONumber=42-->
