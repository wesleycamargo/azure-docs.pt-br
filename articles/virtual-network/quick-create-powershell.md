---
title: Criar uma rede virtual – início rápido – Azure PowerShell | Microsoft Docs
description: Neste início rápido, você aprende como criar uma rede virtual usando o Portal do Azure. Uma rede virtual permite que recursos do Azure, como máquinas virtuais, comuniquem-se em modo privado e com a Internet.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 3d4c8e130f96c1b89247fe0c092363c33032ec3d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2018
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Início Rápido: criar uma rede virtual usando o PowerShell

Uma rede virtual permite que recursos do Azure, como VMs (máquinas virtuais), comuniquem-se em modo privado e com a Internet. Neste início rápido, você aprende como criar uma rede virtual. Após criar uma rede virtual, você implantará duas VMs na rede virtual. Em seguida, você irá conectar uma VM a partir da Internet e executar comunicação entre duas VMs em modo privado.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este início rápido exigirá o módulo do AzureRM PowerShell versão 5.4.1 ou posterior. Para localizar a versão instalada, execute ` Get-Module -ListAvailable AzureRM`. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzureRmAccount` para criar uma conexão com o Azure.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Antes de poder criar uma rede virtual, você deverá criar um grupo de recursos para conter a rede virtual. Crie um grupo de recursos com [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Crie uma rede virtual com [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). O exemplo a seguir cria uma rede virtual padrão chamada *myVirtualNetwork* no local *EastUS*:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Os recursos do Azure são implantados em uma sub-rede dentro de uma rede virtual, portanto, é necessário criar uma sub-rede. Crie uma configuração de sub-rede com [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). 

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Grave a configuração da sub-rede na rede virtual com [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), que cria a sub-rede dentro da rede virtual:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual:

### <a name="create-the-first-vm"></a>Criar a primeira VM

Crie uma VM com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Ao executar o comando a seguir, as credenciais serão solicitadas. Os valores que você inseriu são configurados como o nome de usuário e senha para a VM. A opção `-AsJob` cria a VM em segundo plano para que você possa prosseguir para a próxima etapa.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

A saída semelhante ao exemplo a seguir é retornada e o Azure inicia a criação da VM em segundo plano.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

### <a name="create-the-second-vm"></a>Criar a segunda VM 

Digite o seguinte comando:

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

A VM demora alguns minutos para criar. Não prossiga para a próxima etapa até que o comando anterior seja executado e a saída retornada ao PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Conectar uma VM a partir da Internet

Use [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) para retornar o endereço IP público de uma VM. O exemplo a seguir retorna o endereço IP público da VM *myVm1*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Substitua `<publicIpAddress>` no seguinte comando pelo endereço IP público retornado do comando anterior e, em seguida, insira o comando a seguir: 

```
mstsc /v:<publicIpAddress>
```

Um arquivo .rdp (Remote Desktop Protocol) é criado e baixado para o computador. Abra o arquivo rdp baixado. Se solicitado, selecione **Conectar**. Insira o nome de usuário e senha que você especificou ao criar a VM. Talvez seja necessário selecionar **Mais escolhas** e, em seguida, **Usar uma conta diferente** para especificar as credenciais inseridas durante a criação da VM. Selecione **OK**. Você pode receber um aviso do certificado durante o processo de logon. Se você receber o aviso, selecione **Sim** ou **Continuar**, para prosseguir com a conexão.

## <a name="communicate-between-vms"></a>Comunicação entre VMs

A partir do PowerShell na VM *myVm1*, insira `ping myvm2`. O ping falha porque ele usa o protocolo ICMP e, por padrão, o ICMP não é permitido através do firewall do Windows.

Para permitir ping de *myVm2* para *myVm1* em uma etapa posterior, insira o comando do PowerShell a seguir, que permite a entrada de ICMP através do firewall do Windows:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Feche a conexão da área de trabalho remota para *myVm1*. 

Complete as etapas em [Conecte uma VM a partir da Internet](#connect-to-a-vm-from-the-internet) novamente, mas conecte para *myVm2*. 

A partir de um prompt de comando na VM *myVm2*, insira `ping myvm1`.

Você recebe respostas de *myVm1*, porque você permitiu ICMP através do firewall do Windows na VM *myVm1* em uma etapa anterior.

Feche a conexão da área de trabalho remota para *myVm2*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você poderá utilizar [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou uma rede virtual padrão e duas VMs. Você se conectou a uma VM a partir da Internet e fez comunicação entre esta e outra VM em modo privado. Para saber mais sobre configurações de rede virtual, consulte [Gerenciar uma rede virtual](manage-virtual-network.md). 

Por padrão, o Azure permite comunicação privada irrestrita entre VMs, mas só permite conexões de área de trabalho remota de entrada para VMs do Windows a partir da Internet. Para saber como permitir ou restringir diferentes tipos de comunicação de rede, de e para VMs, siga até o próximo tutorial sobre [Filtrar tráfego](tutorial-filter-network-traffic.md).
