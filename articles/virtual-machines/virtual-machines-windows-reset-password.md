<properties
	pageTitle="Redefinir a senha ou a Área de Trabalho Remota em uma VM do Windows | Microsoft Azure"
	description="Redefina a senha de administrador ou os serviços de Área de Trabalho Remota em uma VM do Windows criada com o modelo de implantação do Gerenciador de Recursos."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2015"
	ms.author="dkshir"/>

# Como redefinir uma senha ou o serviço de Área de Trabalho Remota para máquinas virtuais Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.


Se você não conseguir se conectar a uma máquina virtual do Windows por ter esquecido a senha ou por um problema com a configuração de serviço da Área de Trabalho Remota, use o portal de visualização do Azure ou a extensão VMAccess para redefinir a senha do administrador local ou a configuração de serviço da Área de Trabalho Remota.

## Portal de visualização

Para redefinir o serviço de Área de Trabalho Remota no [portal de visualização](https://portal.azure.com), clique em **Procurar tudo** > **Máquinas virtuais (clássicas)** > *sua máquina virtual do Windows* > **Redefinir Acesso Remoto**. A página a seguir será exibida.


![](./media/virtual-machines-windows-reset-password/Portal-RDP-Reset-Windows.png)

Para redefinir o nome e a senha da conta de administrador local no [portal de visualização](https://portal.azure.com), clique em **Procurar tudo** > **Máquinas virtuais (clássicas)** > *sua máquina virtual Windows* > **Todas as configurações** > **Redefinição de senha**. A página a seguir será exibida.

![](./media/virtual-machines-windows-reset-password/Portal-PW-Reset-Windows.png)


## Extensão VMAccess e PowerShell

Antes de começar, você precisará do seguinte:

- O módulo do Azure PowerShell, versão 0.8.5 ou posterior. Você pode verificar a versão do PowerShell do Azure instalado com o comando **Get-Module azure | format-table version**. Para obter instruções e um link para a versão mais recente, consulte [Como instalar e configurar o Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320552&clcid=0x409).
- A nova senha da conta de administrador local. Isso não é necessário se você quiser redefinir a configuração de serviços da Área de Trabalho Remota.
- O Agente de VM.

A extensão VMAccess não precisa ser instalada para que você possa usá-la. Uma vez que o Agente da VM esteja instalado na máquina virtual, a extensão será carregada automaticamente quando você executar um comando do PowerShell do Azure que use o cmdlet **Set-AzureVMExtension**.

Primeiramente, verifique se que o agente de VM já está instalado. Adicione o nome do serviço de nuvem e o nome da máquina virtual e, em seguida, execute os comandos a seguir em um prompt de comando do Azure PowerShell no nível de administrador. Substitua tudo entre aspas, incluindo os caracteres < and >.

	$csName = "<cloud service name>"
	$vmName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $csName -Name $vmName
	write-host $vm.VM.ProvisionGuestAgent

Se você não souber o nome da máquina virtual e serviço de nuvem, execute **Get-AzureVM** para exibir essas informações para todas as máquinas virtuais em sua assinatura atual.

Se o comando **write-host** exibir **True**, o agente de VM está instalado. Se ele exibir **False**, confira as instruções e um link para download na postagem do blog do Azure [Agente de VM e extensões - parte 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409).

Se tiver criado a máquina virtual com o portal do Azure, execute o comando adicional a seguir.

	$vm.GetInstance().ProvisionGuestAgent = $true

Este comando impedirá que o erro "O Agente Convidado de Provisionamento deve ser habilitado no objeto de VM antes de configurar a Extensão de Acesso de IaaS VM" ao executar o comando **Set-AzureVMExtension** nas seções a seguir.

Agora, você pode realizar essas tarefas:

- Redefinir a senha da conta de administrador local.
- Redefinir a configuração de serviço da Área de Trabalho Remota.

## Redefinir a senha da conta de administrador local

Adicione o nome da conta do administrador local atual e a nova senha e, em seguida, execute os comandos a seguir.

	$cred=Get-Credential –Message "Type the name of the current local administrator account and the new password."
	Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

- Se você digitar um nome diferente daquele da conta atual, a extensão VMAccess renomeará a conta de administrador local, atribuirá a senha a essa conta e emitirá um logoff da Área de Trabalho Remota.
- Se a conta de administrador local estiver desabilitada, a extensão VMAccess a habilita.

Esses comandos também redefinem a configuração do serviço da Área de Trabalho Remota.

## Redefinir a configuração dos serviços de Área de Trabalho Remota

Para redefinir a configuração de serviço da Área de Trabalho Remota, execute o comando a seguir.

	Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

A extensão VMAccess executa estes dois comandos na máquina virtual:

- **netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes**

	Este comando habilita o grupo interno do Firewall do Windows que permite o tráfego de entrada da Área de Trabalho Remota, que usa a porta TCP 3389.

- **Set-ItemProperty -Path 'HKLM:\\System\\CurrentControlSet\\Control\\Terminal Server' -name "fDenyTSConnections" -Value 0**

	Este comando define o valor de Registro fDenyTSConnections como 0, habilitando as conexões de Área de Trabalho Remota.

Se isso não resolver o problema de acesso à Área de Trabalho Remota, execute o [Pacote de diagnóstico do Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864).

1.	No pacote de diagnóstico, clique no **pacote de diagnóstico do Microsoft Azure IaaS (Windows)** para criar uma nova sessão de diagnóstico.
2.	Na página **Quais dos seguintes problemas você está enfrentando com sua VM do Azure?**, selecione o problema **Conectividade RDP a uma VM do Azure (necessário reinicializar)**.

Para obter mais informações, consulte o artigo da Base de Dados de Conhecimento do [pacote de diagnóstico do Microsoft Azure IaaS (Windows)](http://support.microsoft.com/kb/2976864).

Se você não conseguir executar o pacote de diagnóstico do Azure IaaS (Windows) ou se executá-lo não resolver o problema, consulte [Solucionar Problemas de Conexões de Área de Trabalho Remota para uma máquina virtual do Azure baseada no Windows](virtual-machines-troubleshoot-remote-desktop-connections.md).


## Recursos adicionais

[Recursos e extensões de VM do Azure](http://msdn.microsoft.com/library/azure/dn606311.aspx)

[Conectar-se a uma máquina virtual do Azure com RDP ou SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Solucionar problemas de conexões de Área de Trabalho Remota para uma máquina virtual do Azure baseada em Windows](virtual-machines-troubleshoot-remote-desktop-connections.md)

<!---HONumber=Oct15_HO3-->