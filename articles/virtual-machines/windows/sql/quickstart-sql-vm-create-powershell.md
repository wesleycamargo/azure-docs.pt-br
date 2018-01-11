---
title: Criar VM do Windows do SQL Server com o Azure PowerShell | Microsoft Docs
description: "Este tutorial mostra como criar uma máquina virtual do Windows SQL Server 2017 com Azure PowerShell."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/12/2017
ms.author: jroth
ms.openlocfilehash: 9af08fd46314ff102eff95e0832f7ce96bc161d6
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2017
---
# <a name="create-a-sql-server-windows-virtual-machine-with-azure-powershell"></a>Criar uma máquina virtual do Windows do SQL Server com o Azure PowerShell

Esse início rápido percorre a criação de uma máquina virtual do SQL Server com o Azure PowerShell.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este início rápido requer o módulo Azure PowerShell versão 3.6 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa instalar ou atualizar, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="configure-powershell"></a>Configurar o PowerShell

1. Abra o PowerShell e estabeleça o acesso à sua conta do Azure executando o comando **Add-AzureRmAccount**.

   ```PowerShell
   Add-AzureRmAccount
   ```

1. Você deve ver uma tela de entrada para inserir suas credenciais. Use o mesmo email e senha usados para entrar no Portal do Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

1. Defina uma variável com um nome exclusivo de grupo de recurso. Para simplificar o restante do início rápido, o restante dos comandos usa esse nome como base para outros nomes de recurso.

   ```PowerShell
   $ResourceGroupName = "sqlvm1"
   ```

1. Defina um local de uma região de destino do Azure para todos os recursos de VM.

   ```PowerShell
   $Location = "East US"
   ```

1. Crie o grupo de recursos.

   ```PowerShell
   New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>Definir as configurações de rede

1. Crie uma rede virtual, sub-rede e um endereço IP público. Esses recursos são usados para fornecer conectividade de rede para a máquina virtual e conectá-la à Internet.

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzureRmPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. Crie um grupo de segurança de rede. Configure regras para permitir conexões da área de trabalho remota (RDP) e do SQL Server.

   ```PowerShell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. Crie a interface de rede.

   ```PowerShell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>Criar a VM de SQL

1. Defina suas credenciais para entrar na VM. O nome de usuário é "azureadmin". Verifique se você alterou a senha antes de executar o comando.

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString 'Change.This!000' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. Crie um objeto de configuração de máquina virtual e depois crie a VM. O comando a seguir cria uma VM do SQL Server 2017 Developer Edition no Windows Server 2016.

   ```PowerShell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzureRmVMConfig -VMName $VMName -VMSize Standard_DS13 | `
      Set-AzureRmVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate | `
      Set-AzureRmVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" | `
      Add-AzureRmVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > São necessários vários minutos para criar a VM.

## <a name="install-the-sql-iaas-agent"></a>Instalar o SQL IaaS Agent

Para obter a integração do portal e recursos da VM do SQL, você deve instalar a [Extensão do SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md). Para instalar o agente na nova VM, execute o seguinte comando após sua criação.

   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remote-desktop-into-the-vm"></a>Área de trabalho remota na VM

1. Usar o comando a seguir recupera o endereço IP público da nova VM.

   ```PowerShell
   Get-AzureRmPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. Depois, obtenha o endereço IP retornado e passe-o como um parâmetro de linha de comando para **mstsc** para iniciar uma sessão de Área de Trabalho Remota na nova VM.

   ```
   mstsc /v:<publicIpAddress>
   ```

1. Quando as credenciais forem solicitadas, opte por inserir as credenciais para uma conta diferente. Insira o nome de usuário precedido de uma barra invertida (por exemplo, `\azureadmin`), e a senha que você definiu anteriormente neste guia de início rápido.

## <a name="connect-to-sql-server"></a>Conectar-se ao SQL Server

1. Depois de fazer logon na sessão da Área de Trabalho Remota, inicie o **SQL Server Management Studio 2017** no menu Iniciar.

1. Na caixa de diálogo **Conectar ao Servidor**, mantenha os padrões. O nome do servidor é o mesmo da VM. A autenticação está definida como **Autenticação do Windows**. Clique em **Conectar**.

Agora você está conectado ao SQL Server localmente. Se você quiser se conectar remotamente, precisa [configurar a conectividade](virtual-machines-windows-sql-connect.md) no portal ou manualmente.

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisa que a VM seja executada continuamente, é possível interrompê-la quando não estiver em uso para evitar encargos desnecessários. O comando a seguir interrompe a VM, mas a deixa disponível para uso futuro.

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Você também pode excluir permanentemente todos os recursos associados à máquina virtual com o comando **Remove-AzureRmResourceGroup**. Isso excluirá permanentemente a máquina virtual também, portanto, use esse comando com cuidado.

## <a name="next-steps"></a>Próximas etapas

Nesse guia de início rápido, você criou uma máquina virtual do SQL Server 2017 no Azure PowerShell. Para saber mais sobre como migrar seus dados para o novo SQL Server, consulte o artigo a seguir.

> [!div class="nextstepaction"]
> [Migrar um banco de dados para uma VM do SQL](virtual-machines-windows-migrate-sql.md)