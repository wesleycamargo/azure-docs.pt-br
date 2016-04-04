<properties
	pageTitle="Redefinir a senha ou a Área de Trabalho Remota em uma VM do Windows | Microsoft Azure"
	description="Redefina a senha de administrador ou os serviços de Área de Trabalho Remota em uma VM do Windows criada com o modelo de implantação do Gerenciador de Recursos."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/17/2016"
	ms.author="dkshir"/>

# Como redefinir o serviço Área de Trabalho Remota ou sua senha de logon em uma VM do Azure baseada no Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)].


Se não for possível se conectar a uma máquina virtual do Windows porque você esqueceu a senha ou devido a um problema com a configuração do serviço Área de Trabalho Remota, este artigo descreve como redefinir a senha do administrador local ou a configuração do serviço Área de Trabalho Remota.

Dependendo do modelo de implantação de sua máquina virtual, você pode usar o portal ou a extensão de acesso da VM no Azure PowerShell. Se estiver usando o Azure PowerShell, verifique se você tem o módulo do Azure PowerShell mais recente instalado no computador de trabalho e se está conectado à sua assinatura do Azure. Para ver etapas detalhadas, confira [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).


> [AZURE.TIP] Você pode verificar a versão do Azure PowerShell que tem instalada com o comando `Get-Module azure | format-table version`.


## VMs do Windows no modelo de implantação clássica

### Portal do Azure

Para as máquinas virtuais criadas usando o modelo de implantação clássica, você pode usar o [portal do Azure](https://portal.azure.com) para redefinir o serviço Área de Trabalho Remota. Clique em **Procurar** > **Máquinas virtuais (clássicas)** > *sua máquina virtual do Windows* > **Redefinir Acesso**.... A página a seguir será exibida.


![](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

Você também pode tentar redefinir o nome e a senha da conta do administrador local. Clique em **Procurar** > **Máquinas virtuais (clássicas)** > *sua máquina virtual do Windows* > **Todas as configurações** > **Redefinir senha**. A página a seguir será exibida.

![](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

Clique em **Salvar** depois de inserir o novo nome de usuário e senha.

### Extensão VMAccess e PowerShell

Verifique se o Agente da VM está instalado na máquina virtual. A extensão VMAccess não precisa ser instalada para que você possa usá-la, desde que o Agente da VM esteja disponível. Verifique se o Agente da VM já está instalado usando o comando a seguir. Substitua "myCloudService" e "myVM" pelos nomes do seu serviço de nuvem e da sua VM, respectivamente. É possível encontrá-los executando `Get-AzureVM` sem qualquer parâmetro.

	$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
	write-host $vm.VM.ProvisionGuestAgent

Se o comando **write-host** exibir **True**, o agente de VM está instalado. Se ele exibir **False**, confira as instruções e um link para download na postagem do blog do Azure [Agente de VM e extensões - parte 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409).

Se você criou a máquina virtual com o portal, verifique se `$vm.GetInstance().ProvisionGuestAgent` retorna **True**. Caso contrário, defina-o usando este comando:

	$vm.GetInstance().ProvisionGuestAgent = $true

Este comando impedirá que o erro "O Agente Convidado de Provisionamento deve ser habilitado no objeto de VM antes de configurar a Extensão de Acesso de IaaS VM" ao executar o comando **Set-AzureVMExtension** nas seções a seguir.

#### **Redefinir a senha da conta de administrador local**

Crie uma credencial de logon com o nome da conta de administrador local atual e uma nova senha, em seguida, execute `Set-AzureVMAccessExtension` como se segue.

	$cred=Get-Credential
	Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

Se você digitar um nome diferente daquele da conta atual, a extensão VMAccess renomeará a conta de administrador local, atribuirá a senha a essa conta e emitirá um logoff da Área de Trabalho Remota. Se a conta de administrador local estiver desabilitada, a extensão VMAccess a habilita.

Esses comandos também redefinem a configuração do serviço da Área de Trabalho Remota.

#### **Redefinir a configuração dos serviços de Área de Trabalho Remota**

Para redefinir a configuração de serviço da Área de Trabalho Remota, execute o comando a seguir.

	Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

A extensão VMAccess executa estes dois comandos na máquina virtual:

a. `netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes`

Este comando habilita o grupo interno do Firewall do Windows que permite o tráfego de entrada da Área de Trabalho Remota, que usa a porta TCP 3389.

b. `Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0`

Este comando define o valor de Registro fDenyTSConnections como 0, habilitando as conexões de Área de Trabalho Remota.


## VMs do Windows no modelo de implantação Resource Manager

Atualmente, o portal do Azure não permite a redefinição do acesso remoto ou das credenciais de logon para máquinas virtuais criadas usando o Resource Manager.


### Extensão VMAccess e PowerShell

Verifique se você tem o Azure PowerShell 1.0 ou superior instalado e se conectou-se à sua conta usando o cmdlet `Login-AzureRmAccount`.

#### **Redefinir a senha da conta de administrador local**

É possível redefinir a senha e/ou o nome de usuário do administrador usando o comando [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) do PowerShell.

Crie as credenciais da conta de administrador local usando o seguinte comando:

	$cred=Get-Credential

Se você digitar um nome diferente daquele da conta atual, o comando de extensão VMAccess abaixo renomeará a conta de administrador local, atribuirá a senha a essa conta e emitirá um logoff da Área de Trabalho Remota. Se a conta de administrador local estiver desabilitada, a extensão VMAccess a habilitará.
	
Use a extensão de acesso da VM para definir as novas credenciais da seguinte maneira:

	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -Location Westus -UserName $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password


Substitua `myRG`, `myVM`, `myVMAccess` e o local por valores relevantes à sua instalação.


#### **Redefinir a configuração dos serviços de Área de Trabalho Remota**

É possível redefinir o acesso remoto para sua VM usando [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) ou Set-AzureRmVMAccessExtension, como se segue. Substitua `myRG`, `myVM`, `myVMAccess` e o local pelos seus próprios valores.

	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -ExtensionType "VMAccessAgent" -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" -Location Westus

OR<br>

	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -Location Westus

	
> [AZURE.TIP] Tanto `Set-AzureRmVMAccessExtension`, quanto `Set-AzureRmVMExtension` adicionam um novo agente de acesso de VM nomeado à máquina virtual. A qualquer momento, uma VM pode ter apenas um único agente de acesso de VM. Para definir as propriedades do agente de acesso de VM sucessivamente, remova o agente de acesso definido anteriormente usando `Remove-AzureRmVMAccessExtension` ou `Remove-AzureRmVMExtension`. A partir da versão 1.2.2 do Azure PowerShell, você pode evitar essa etapa ao usar `Set-AzureRmVMExtension` com um opção `-ForceRerun`. Garanta o uso do mesmo nome para o agente de acesso de VM, conforme definido pelo comando anterior ao usar a opção `-ForceRerun`.


Se você ainda não consegue se conectar remotamente à máquina virtual, veja mais etapas a serem testadas em [Troubleshoot Remote Desktop connections to a Windows-based Azure virtual machine (Solucionar problemas de conexões da Área de trabalho Remota com uma máquina virtual do Azure baseada no Windows)](virtual-machines-windows-troubleshoot-rdp-connection.md).


## Recursos adicionais

[Recursos e extensões de VM do Azure](virtual-machines-windows-extensions-features.md)

[Conectar-se a uma máquina virtual do Azure com RDP ou SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Solucionar problemas de conexões de Área de Trabalho Remota para uma máquina virtual do Azure baseada em Windows](virtual-machines-windows-troubleshoot-rdp-connection.md)

<!---HONumber=AcomDC_0323_2016-->