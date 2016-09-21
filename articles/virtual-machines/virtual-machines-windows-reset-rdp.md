<properties
	pageTitle="Redefinir a senha ou a configuração da Área de Trabalho Remota em uma VM do Windows | Microsoft Azure"
	description="Saiba como redefinir uma senha de conta ou serviços da Área de Trabalho Remota em uma VM do Windows usando o Portal do Azure ou o Azure PowerShell."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2016"
	ms.author="iainfou"/>

# Como redefinir o serviço Área de Trabalho Remota ou sua senha de logon em uma VM do Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Você pode redefinir a senha de administrador local ou redefinir a configuração do serviço de Área de Trabalho Remota, se não for possível se conectar a uma máquina virtual do Windows. Você pode usar o Portal do Azure ou a extensão VM Access no Azure PowerShell para redefinir a senha. Se estiver usando o PowerShell, verifique se você tem o módulo do PowerShell mais recente instalado no computador de trabalho e se está conectado à sua assinatura do Azure. Para ver etapas detalhadas, confira [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

> [AZURE.TIP] Você pode verificar a versão do PowerShell instalada usando `Import-Module Azure, AzureRM; Get-Module Azure, AzureRM | Format-Table Name, Version`

## VMs do Windows no modelo de implantação Resource Manager

### Portal do Azure
Selecione sua VM clicando em **Procurar** > **Máquinas virtuais** > *sua máquina virtual do Windows* > **Todas as configurações** > **Redefinir senha**. A folha de redefinição de senha é exibida:

![Página Redefinir senha](./media/virtual-machines-windows-reset-rdp/Portal-RM-PW-Reset-Windows.png)

Insira o nome de usuário e uma nova senha e clique em **Salvar**. Tente se conectar à VM novamente.

### Extensão VMAccess e PowerShell

Verifique se você tem o Azure PowerShell 1.0 ou superior instalado e se entrou na sua conta usando o cmdlet `Login-AzureRmAccount`.

#### **Redefinir a senha da conta de administrador local**

É possível redefinir a senha e/ou o nome de usuário do administrador usando o comando [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) do PowerShell.

Crie as credenciais de sua conta de administrador local usando o seguinte comando:

	$cred=Get-Credential

Se você digitar um nome diferente daquele da conta atual, a extensão VMAccess renomeará a conta de administrador local, atribuirá a senha a essa conta e emitirá um logoff da Área de Trabalho Remota. Se a conta de administrador local estiver desabilitada, a extensão VMAccess a habilita.

Use a extensão de acesso da VM para definir as novas credenciais da seguinte maneira:

	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" `
		-Location WestUS -UserName $cred.GetNetworkCredential().Username `
		-Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"


Substitua `myRG`, `myVM`, `myVMAccess` e o local por valores relevantes à sua instalação.


#### **Redefinir a configuração dos serviços de Área de Trabalho Remota**

É possível redefinir o acesso remoto para sua VM usando [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) ou [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx), conforme demonstrado a seguir. (Substitua `myRG`, `myVM`, `myVMAccess` e o local por seus próprios valores.)

	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" `
		-Name "myVMAccess" -ExtensionType "VMAccessAgent" -Location WestUS `
		-Publisher "Microsoft.Compute" -typeHandlerVersion "2.0"

Ou: <br>

	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" `
		-Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0


> [AZURE.TIP] Ambos os comandos adicionam um novo agente de acesso de VM nomeado à máquina virtual. A qualquer momento, uma VM pode ter apenas um único agente de acesso de VM. Para definir as propriedades do agente de acesso de VM com êxito, remova o agente de acesso definido anteriormente usando `Remove-AzureRmVMAccessExtension` ou `Remove-AzureRmVMExtension`. Da versão 1.2.2 do Azure PowerShell em diante, você pode evitar essa etapa ao usar `Set-AzureRmVMExtension` com uma opção `-ForceRerun`. Ao usar a opção `-ForceRerun`, garanta o uso do mesmo nome para o agente de acesso de VM, conforme definido pelo comando anterior.

Se você ainda não consegue se conectar remotamente à máquina virtual, veja mais etapas a serem testadas em [Solucionar problemas de conexões da Área de Trabalho Remota com uma máquina virtual do Azure baseada no Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).


## VMs do Windows no modelo de implantação clássico

### Portal do Azure

Para as máquinas virtuais criadas usando o modelo de implantação clássico, você pode usar o [Portal do Azure](https://portal.azure.com) para redefinir o serviço de Área de Trabalho Remota. Clique em **Procurar** > **Máquinas virtuais (clássico)** > *sua máquina virtual do Windows* > **Redefinir Acesso...**. A página a seguir será exibida.

![Página Redefinir configuração de RDP](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

Você também pode tentar redefinir o nome e a senha da conta do administrador local. Clique em **Procurar** > **Máquinas virtuais (clássico)** > *sua máquina virtual do Windows* > **Todas as configurações** > **Redefinir senha**. A página a seguir será exibida.

![Página Redefinir senha](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

Depois de inserir o novo nome de usuário e a senha, clique em **Salvar**.

### Extensão VMAccess e PowerShell

Verifique se o Agente da VM está instalado na máquina virtual. A extensão VMAccess não precisa ser instalada para que você possa usá-la, desde que o Agente da VM esteja disponível. Verifique se o Agente da VM já está instalado usando o comando a seguir. (Substitua "myCloudService" e "myVM" pelos nomes do seu serviço de nuvem e da sua VM, respectivamente. É possível conhecer esses nomes executando `Get-AzureVM` sem nenhum parâmetro).

	$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
	write-host $vm.VM.ProvisionGuestAgent

Se o comando **write-host** exibir **True**, o agente de VM está instalado. Se ele exibir **False**, confira as instruções e um link para download na postagem do blog do Azure [Agente de VM e extensões - parte 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409).

Se você criou a máquina virtual pelo uso do portal, verifique se `$vm.GetInstance().ProvisionGuestAgent` retorna **True**. Caso contrário, você pode defini-lo usando esse comando:

	$vm.GetInstance().ProvisionGuestAgent = $true

Este comando impedirá o erro a seguir ao executar o comando **Set-AzureVMExtension** nas etapas a seguir: “O Agente Convidado de Provisionamento deve estar habilitado no objeto da VM antes de configurar a Extensão de Acesso à VM IaaS.”

#### **Redefinir a senha da conta de administrador local**

Crie uma credencial de entrada com o nome da conta de administrador local atual e uma nova senha, em seguida, execute `Set-AzureVMAccessExtension` como a seguir.

	$cred=Get-Credential
	Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
		-Password $cred.GetNetworkCredential().Password  | Update-AzureVM

Se você digitar um nome diferente daquele da conta atual, a extensão VMAccess renomeará a conta de administrador local, atribuirá a senha a essa conta e emitirá uma saída da Área de Trabalho Remota. Se a conta de administrador local estiver desabilitada, a extensão VMAccess a habilita.

Esses comandos também redefinem a configuração do serviço da Área de Trabalho Remota.

#### **Redefinir a configuração dos serviços de Área de Trabalho Remota**

Para redefinir a configuração de serviço da Área de Trabalho Remota, execute o comando a seguir:

	Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

A extensão VMAccess executa dois comandos na máquina virtual:

a. `netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes`

Este comando habilita o grupo interno do Firewall do Windows que permite o tráfego de entrada da Área de Trabalho Remota, que usa a porta TCP 3389.

b. `Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0`

Este comando define o valor de Registro fDenyTSConnections como 0, habilitando as conexões de Área de Trabalho Remota.


## Recursos adicionais

[Recursos e extensões de VM do Azure](virtual-machines-windows-extensions-features.md)

[Conectar-se a uma máquina virtual do Azure com RDP ou SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Solucionar problemas de conexões de Área de Trabalho Remota para uma máquina virtual do Azure baseada em Windows](virtual-machines-windows-troubleshoot-rdp-connection.md)

<!---HONumber=AcomDC_0907_2016-->