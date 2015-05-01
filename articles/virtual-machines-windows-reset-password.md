<properties 
	pageTitle="Como redefinir uma senha ou o serviço de Área de Trabalho Remota para máquinas virtuais do Windows" 
	description="Rapidamente redefina uma senha de administrador local ou o Serviço de Área de Trabalho Remota para máquinas virtuais do Windows usando comandos do PowerShell." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/25/2015" 
	ms.author="josephd"/>

# Como redefinir uma senha ou o serviço de Área de Trabalho Remota para máquinas virtuais do Windows

Se você não conseguir se conectar a uma máquina virtual do Windows por ter esquecido a senha ou haver um problema com a configuração de serviços de Área de Trabalho Remota, use a extensão VMAccess para redefinir a senha de administrador local ou a configuração de serviços de Área de Trabalho Remota.
 
## Requisitos

Antes de começar, você precisará do seguinte:

- Módulo PowerShell do Azure, versão 0.8.5 ou mais nova. Você pode verificar a versão do PowerShell do Azure instalado com o comando **Get-Module azure | format-table version**. Para obter instruções e um link para a versão mais recente, consulte [Como instalar e configurar o PowerShell do Azure](http://go.microsoft.com/fwlink/p/?linkid=320552&clcid=0x409). 
- A nova senha da conta de administrador local. Isso não é necessário se você quiser redefinir a configuração de serviços da Área de Trabalho Remota. 
- O Agente de VM. 

A extensão VMAccess não precisa ser instalada para que você possa usá-la. Desde que o Agente da VM esteja instalado na máquina virtual, a extensão será carregada automaticamente quando você executar um comando do PowerShell do Azure que use o cmdlet **Set-AzureVMExtension**.
 
Primeiramente, verifique se que o agente de VM já está instalado. Preencha o nome do serviço de nuvem e o nome da máquina virtual e, em seguida, execute os seguintes comandos em um prompt de comando do PowerShell do Azure com nível de administrador. Substitua tudo entre aspas, incluindo os caracteres < e >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName 
	write-host $vm.VM.ProvisionGuestAgent

Se você não souber o nome da máquina virtual e serviço de nuvem, execute **Get-AzureVM** para exibir essas informações para todas as máquinas virtuais em sua assinatura atual.

Se o comando **write-host** exibe **True**, o agente de VM está instalado. Se ele exibir **False**, consulte as instruções e um link para download na postagem do blog do Azure [Agente de VM e extensões - parte 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409).

Se tiver criado a máquina virtual com o Portal de Gerenciamento do Azure, execute o seguinte comando adicional:

	$vm.GetInstance().ProvisionGuestAgent = $true

Este comando impedirá que o erro "O Agente Convidado de Provisionamento deve ser habilitado no objeto de VM antes de configurar a extensão de Acesso a VM por IaaS" ao executar o comando Set-AzureVMExtension nas seções a seguir. 

Agora, você pode realizar essas tarefas:

- Redefinir uma senha da conta de administrador local
- Redefinir a configuração dos serviços de Área de Trabalho Remota

## Redefinir a senha da conta de administrador local

Preencha o nome de usuário Linux atual e a nova senha, então execute estes comandos.

	$cred=Get-Credential -Message "Type the name of the current local administrator account and the new password."	
	Set-AzureVMAccessExtension -vm $vm -UserName $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

- Se você digitar um nome diferente daquele da conta atual, a extensão VMAccess renomeará a conta de administrador local, atribuirá a senha a essa conta e emitirá um logoff da Área de Trabalho Remota.
- Se a conta de administrador local estiver desabilitada, a extensão VMAccess a habilita.
 
Esses comandos também redefinem a configuração do serviço da Área de Trabalho Remota.

## Redefinir a configuração dos serviços de Área de Trabalho Remota

Para redefinir a configuração de serviços da Área de Trabalho Remota, execute este comando.

	Set-AzureVMAccessExtension -vm $vm | Update-AzureVM

A extensão VMAccess executa estes dois comandos na VM:

- **netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes**

	Este comando habilita o grupo interno do Firewall do Windows que permite o tráfego de entrada da Área de Trabalho Remota, que usa a porta TCP 3389.

- **Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0**

	Este comando define o valor de Registro fDenyTSConnections como 0, habilitando as conexões de Área de Trabalho Remota.

Se isso não resolver o problema de acesso de Área de Trabalho Remota, execute o [Pacote de diagnóstico do Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864). 

1.	Clique em **Pacote de diagnóstico do Microsoft Azure IaaS (Windows)** nesta página para criar uma nova sessão de diagnóstico.
2.	Na página **Quais dos seguintes problemas você está enfrentando com sua VM do Azure?**, selecione o problema **Conectividade RDP a uma VM do Azure (necessário reinicializar)** 

Para obter mais informações, consulte o [artigo da Base de Conhecimento do pacote de diagnóstico do Microsoft Azure IaaS (Windows)](http://support.microsoft.com/kb/2976864). 

Se você conseguir executar o pacote de diagnóstico do Azure IaaS (Windows) ou se executá-lo não resolver o problema, consulte [Solucionar problemas de conexões da Área de Trabalho Remota para uma máquina virtual do Azure baseada em Windows](virtual-machines-troubleshoot-remote-desktop-connections.md).


## Recursos adicionais

[Recursos e extensões de VM do Azure](http://msdn.microsoft.com/library/azure/dn606311.aspx)

[Conectar-se a uma máquina virtual do Azure com RDP ou SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)



<!--HONumber=52-->