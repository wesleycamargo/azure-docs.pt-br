---
title: "Redefinir a senha ou a configuração da Área de Trabalho Remota em uma VM do Windows | Microsoft Docs"
description: "Saiba como redefinir uma senha de conta ou serviços da Área de Trabalho Remota em uma VM do Windows usando o Portal do Azure ou o Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: 6700ea97bea02d68329b923f8715d84e5df1de33


---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Como redefinir o serviço Área de Trabalho Remota ou sua senha de logon em uma VM do Windows
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Se não conseguir se conectar a uma máquina virtual do Windows (VM), você pode redefinir a senha de administrador local ou a configuração do serviço de Área de Trabalho Remota. Você pode usar o Portal do Azure ou a extensão VM Access no Azure PowerShell para redefinir a senha. Se estiver usando o PowerShell, verifique se você tem o módulo do PowerShell mais recente instalado no computador de trabalho e se está conectado à sua assinatura do Azure. Para ver etapas detalhadas, confira [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs).

> [!TIP]
> Verifique a versão do PowerShell instalada usando:
>
> `Import-Module Azure, AzureRM; Get-Module Azure, AzureRM | Format-Table Name, Version`


## <a name="ways-to-reset-configuration-or-credentials"></a>Maneiras para redefinir a configuração ou as credenciais
É possível redefinir as credenciais e os serviços de Área de Trabalho Remota de várias maneiras diferentes, dependendo de suas necessidades. Para VMs criadas com o modelo de implantação do Resource Manager:

- [Redefinir usando o portal do Azure](#azure-portal---resource-manager)
- [Redefinir usando o Azure PowerShell](#vmaccess-extension-and-powershell---resource-manager)

Para VMs criadas com o modelo de implantação Clássico:

- [Redefinir usando o portal do Azure](#azure-portal---classic)
- [Redefinir usando o Azure PowerShell](#vmaccess-extension-and-powershell---classic)

## <a name="azure-portal---resource-manager"></a>Portal do Azure – Resource Manager
Selecione sua VM clicando em **Procurar** > **Máquinas virtuais** > *sua máquina virtual do Windows* > **Todas as configurações** > **Redefinir senha**. A folha de redefinição de senha é exibida:

![Página Redefinir senha](./media/virtual-machines-windows-reset-rdp/Portal-RM-PW-Reset-Windows.png)

Insira o nome de usuário e uma nova senha e clique em **Salvar**. Tente se conectar à VM novamente.


## <a name="vmaccess-extension-and-powershell---resource-manager"></a>Extensão VMAccess e PowerShell – Resource Manager
Verifique se você tem o Azure PowerShell 1.0 ou superior instalado e se entrou na sua conta usando o cmdlet `Login-AzureRmAccount` .

### <a name="reset-the-local-administrator-account-password"></a>**Redefinir a senha da conta de administrador local**
É possível redefinir a senha e/ou o nome de usuário do administrador usando o comando [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) do PowerShell.

Crie as credenciais de sua conta de administrador local usando o seguinte comando:

```powershell
$cred=Get-Credential
```

Se você digitar um nome diferente daquele da conta atual, a extensão VMAccess renomeará a conta de administrador local, atribuirá a senha a essa conta e emitirá um evento de logoff da Área de Trabalho Remota. Se a conta de administrador local estiver desabilitada, a extensão VMAccess a habilita.

Use a extensão de acesso da VM para definir as novas credenciais da seguinte maneira:

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" `
    -Name "myVMAccess" -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

Substitua `myResourceGroup`, `myVM`, `myVMAccess` e o local por valores relevantes à sua instalação.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Redefinir a configuração dos serviços de Área de Trabalho Remota**
É possível redefinir o acesso remoto para sua VM usando [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) ou [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx), como segue. (Substitua `myResourceGroup`, `myVM`, `myVMAccess` e o local por seus próprios valores.)

```powershell
Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" `
    -Name "myVMAccess" -ExtensionType "VMAccessAgent" -Location WestUS `
    -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0"
```

Ou: 

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" `
    -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0
```

> [!TIP]
> Ambos os comandos adicionam um novo agente de acesso de VM nomeado à máquina virtual. A qualquer momento, uma VM pode ter apenas um único agente de acesso de VM. Para definir as propriedades do agente de acesso de VM com êxito, remova o agente de acesso definido anteriormente usando `Remove-AzureRmVMAccessExtension` ou `Remove-AzureRmVMExtension`. 
>
> Da versão 1.2.2 do Azure PowerShell em diante, você pode evitar essa etapa ao usar `Set-AzureRmVMExtension` com uma opção `-ForceRerun`. Ao usar a opção `-ForceRerun`, garanta o uso do mesmo nome para o agente de acesso de VM, conforme definido pelo comando anterior.

Se você ainda não consegue se conectar remotamente à máquina virtual, veja mais etapas a serem testadas em [Solucionar problemas de conexões da Área de Trabalho Remota com uma máquina virtual do Azure baseada no Windows](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


## <a name="azure-portal---classic"></a>Portal do Azure – Clássico
Para as máquinas virtuais criadas usando o modelo de implantação clássico, você pode usar o [Portal do Azure](https://portal.azure.com) para redefinir o serviço de Área de Trabalho Remota. Clique em **Procurar** > **Máquinas virtuais (clássico)** > *sua máquina virtual do Windows* > **Redefinir Acesso....**. A página a seguir será exibida.

![Página Redefinir configuração de RDP](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

Você também pode tentar redefinir o nome e a senha da conta do administrador local. Clique em **Procurar** > **Máquinas virtuais (clássico)** > *sua máquina virtual do Windows* > **Todas as Configurações** > **Redefinir senha**. A página a seguir será exibida.

![Página Redefinir senha](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

Depois de inserir o novo nome de usuário e a senha, clique em **Salvar**.

## <a name="vmaccess-extension-and-powershell---classic"></a>Extensão VMAccess e PowerShell – Clássico
Verifique se o Agente da VM está instalado na máquina virtual. A extensão VMAccess não precisa ser instalada para que você possa usá-la, desde que o Agente da VM esteja disponível. Verifique se o Agente da VM já está instalado usando o comando a seguir. (Substitua "myCloudService" e "myVM" pelos nomes do seu serviço de nuvem e da sua VM, respectivamente. É possível conhecer esses nomes executando `Get-AzureVM` sem nenhum parâmetro).

```powershell
$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
write-host $vm.VM.ProvisionGuestAgent
```

Se o comando **write-host** exibir **True**, o agente de VM está instalado. Se ele exibir **False**, confira as instruções e um link para download na postagem do blog do Azure [Agente de VM e extensões - parte 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) .

Se você criou a máquina virtual pelo uso do portal, verifique se `$vm.GetInstance().ProvisionGuestAgent` retorna **True**. Caso contrário, você pode defini-lo usando esse comando:

```powershell
$vm.GetInstance().ProvisionGuestAgent = $true
```

Este comando impedirá o erro a seguir ao executar o comando **Set-AzureVMExtension** nas etapas a seguir: “O Agente Convidado de Provisionamento deve estar habilitado no objeto da VM antes de configurar a Extensão de Acesso à VM IaaS.”

### <a name="reset-the-local-administrator-account-password"></a>**Redefinir a senha da conta de administrador local**
Crie uma credencial de entrada com o nome da conta de administrador local atual e uma nova senha, em seguida, execute `Set-AzureVMAccessExtension` como a seguir.

```powershell
$cred=Get-Credential
Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password  | Update-AzureVM
```

Se você digitar um nome diferente daquele da conta atual, a extensão VMAccess renomeará a conta de administrador local, atribuirá a senha a essa conta e emitirá uma saída da Área de Trabalho Remota. Se a conta de administrador local estiver desabilitada, a extensão VMAccess a habilita.

Esses comandos também redefinem a configuração do serviço da Área de Trabalho Remota.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Redefinir a configuração dos serviços de Área de Trabalho Remota**
Para redefinir a configuração de serviço da Área de Trabalho Remota, execute o comando a seguir:

```powershell
Set-AzureVMAccessExtension –vm $vm | Update-AzureVM
```

A extensão VMAccess executa dois comandos na máquina virtual:

```powershell
netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes
```

Este comando habilita o grupo interno do Firewall do Windows que permite o tráfego de entrada da Área de Trabalho Remota, que usa a porta TCP 3389.

```powershell
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0
```

Este comando define o valor de Registro fDenyTSConnections como 0, habilitando as conexões de Área de Trabalho Remota.

## <a name="next-steps"></a>Próximas etapas
Se a extensão de acesso a VM do Azure não responder e não for possível redefinir a senha, você pode [redefinir a senha local do Windows offline](virtual-machines-windows-reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Esse método é um processo mais avançado e exige que você conecte o disco rígido virtual da VM problemática em outra VM. Siga as etapas documentadas neste artigo primeiro; e só tente o método de redefinição de senha offline como último recurso.

[Recursos e extensões de VM do Azure](virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Conectar-se a uma máquina virtual do Azure com RDP ou SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Solucionar problemas de conexões de Área de Trabalho Remota para uma máquina virtual do Azure baseada em Windows](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Dec16_HO2-->


