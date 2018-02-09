---
title: Criar uma rede virtual no Azure - PowerShell | Microsoft Docs
description: Aprenda rapidamente a criar uma rede virtual usando o PowerShell. Uma rede virtual permite que muitos tipos de recursos do Azure comuniquem-se em modo privado.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: 
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 091e7e6cabf325cdd9d4289e7d22e71c583d91db
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-virtual-network-using-powershell"></a>Criar uma rede virtual usando o PowerShell

Neste artigo, você aprenderá como criar uma rede virtual. Após criar uma rede virtual, você implantará duas máquinas virtuais na rede virtual e se comunicarão em modo privado.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 5.1.1 ou posterior. Para localizar a versão instalada, execute ` Get-Module -ListAvailable AzureRM`. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzureRmAccount` para criar uma conexão com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*. Todos os recursos do Azure são criados dentro de uma localização (ou região) do Azure.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual com [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). O exemplo a seguir cria uma rede virtual padrão chamada *myVirtualNetwork* no local *EastUS*:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/24
```

Todas as redes virtuais têm um ou mais prefixos de endereço atribuídos a elas. O espaço de endereço é especificado na notação CIDR. O espaço de endereços 10.0.0.0/24 abrange 10.0.0.0-10.0.0.254. As redes virtuais têm zero ou mais sub-redes dentro delas. Os recursos são implantados em uma sub-rede em uma rede virtual. 

Crie uma configuração de sub-rede com [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Todas as sub-redes têm um prefixo de endereço que existe dentro do prefixo de endereço da rede virtual. Neste exemplo, é criada uma configuração de sub-rede com o mesmo prefixo de endereço como o prefixo de endereço da rede virtual:

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Embora o prefixo de endereço de sub-rede abrange 10.0.0.0-10.0.0.254, apenas os endereços 10.0.0.4-10.0.0.254 estão disponíveis, pois o Azure reserva os primeiros quatro endereços (0-3) e o último endereço em cada sub-rede. Como o prefixo de endereço de sub-rede é o mesmo que o prefixo de endereço de rede virtual, apenas uma sub-rede pode existir nesta rede virtual.

Grave a configuração da sub-rede na rede virtual com [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), que cria a sub-rede:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Uma rede virtual permite que vários tipos de recursos do Azure comuniquem-se em modo privado. Um tipo de recurso que pode ser implantado em uma rede virtual é uma máquina virtual. Crie duas máquinas virtuais na rede virtual para que você possa validar e entender como a comunicação entre máquinas virtuais em uma rede virtual funciona em uma etapa posterior.

Crie uma máquina virtual com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Ao executar esta etapa, credenciais serão solicitadas de você. Os valores que você insere são configurados como o nome de usuário e senha para a máquina virtual. A localização em que uma máquina virtual é criada deve ser a mesma localização na qual a rede virtual existe. Não é necessário que a máquina virtual esteja no mesmo grupo de recursos que a máquina virtual, embora isso seja abordado neste artigo. O parâmetro `-AsJob` permite que o comando seja executado em plano de fundo para que você possa continuar com a próxima tarefa.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

Saída semelhante à seguinte saída do exemplo é retornada e o Azure começa a criar a máquina virtual em plano de fundo.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

O DHCP do Azure atribui automaticamente 10.0.0.4 à máquina virtual durante a criação, porque é o primeiro endereço disponível na sub-rede *padrão*.

Crie uma segunda máquina virtual. 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```
A criação da máquina virtual demora alguns minutos. Após a criação, o Azure retorna o resultado sobre a máquina virtual criada. Embora não apareça na saída retornada, o Azure atribuiu *10.0.0.5* para a máquina virtual *myVm2* porque era o próximo endereço disponível na sub-rede.

## <a name="connect-to-a-virtual-machine"></a>Conectar-se a uma máquina virtual

Utilize o comando [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) para retornar o endereço IP público de uma máquina virtual. O Azure atribui, por padrão, um endereço IP roteável de internet público para cada máquina virtual. O endereço IP público é atribuído à máquina virtual a partir de um [pool de endereços atribuídos a cada região do Azure](https://www.microsoft.com/download/details.aspx?id=41653). Enquanto o Azure sabe qual endereço IP público é atribuído a uma máquina virtual, o sistema operacional executado em uma máquina virtual não reconhece nenhum endereço IP público atribuído a ele. O exemplo a seguir retorna o endereço IP público da máquina virtual *myVm1*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -Name myVm1 -ResourceGroupName myResourceGroup | Select IpAddress
```

Use o comando a seguir para criar uma sessão de área de trabalho remota com a máquina virtual *myVm1* do seu computador local. Substitua `<publicIpAddress>` pelo o endereço IP retornado do comando anterior.

```
mstsc /v:<publicIpAddress>
```

Um arquivo .rdp (protocolo RDP) é criado, baixado para seu computador e aberto. Insira o nome de usuário e a senha que você especificou ao criar a máquina virtual e, em seguida, clique em **OK**. Você pode receber um aviso do certificado durante o processo de logon. Clique em **Sim** ou em **Continuar** para prosseguir com o processo de conexão.

## <a name="validate-communication"></a>Validar a comunicação

A tentativa de executar ping de uma máquina virtual do Windows falha porque, por padrão, executar ping não é permitido por meio do firewall do Windows. Para permitir executar ping na *myVm1*, insira o seguinte comando a partir de um prompt de comando:

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Para validar a comunicação com *myVm2*, insira o seguinte comando a partir de um prompt de comando na máquina virtual *myVm1*. Forneça as credenciais utilizadas quando a máquina virtual foi criada e, em seguida, complete a conexão:

```
mstsc /v:myVm2
```

A conexão de área de trabalho remota ocorre com êxito porque ambas as máquinas virtuais possuem endereços IP privados atribuídos da sub-rede *padrão* e porque a área de trabalho remota, por padrão, é aberta pelo firewall do Windows. Você pode se conectar em *myVm2* pelo nome do host porque o Azure fornece automaticamente a resolução de nomes DNS para todos os hosts dentro de uma rede virtual. Em um prompt de comando, execute ping *myVm1*, a partir *myVm2*.

```
ping myvm1
```

O ping é executado com êxito porque você permitiu através do firewall do Windows na máquina virtual *myVm1* na etapa anterior. Para confirmar a comunicação de saída para a Internet, insira o comando a seguir:

```
ping bing.com
```

Você recebe quatro respostas do bing.com. Por padrão, qualquer máquina virtual em uma rede virtual pode se comunicar com a Internet.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos que contém. Saia da sessão da área de trabalho remota e, em seguida, execute o seguinte comando do seu computador para excluir o grupo de recursos:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você implantou uma rede virtual padrão com uma sub-rede e duas máquinas virtuais. Para saber como criar uma rede virtual personalizada com várias sub-redes e executar tarefas básicas de gerenciamento de rede virtual, continue no tutorial para criar uma rede virtual personalizada e gerenciá-la.


> [!div class="nextstepaction"]
> [Criar uma rede virtual personalizada e gerenciá-la](virtual-networks-create-vnet-arm-pportal.md#powershell)
