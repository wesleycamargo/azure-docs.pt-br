---
title: Conectar redes virtuais com o emparelhamento de rede virtual – PowerShell | Microsoft Docs
description: Saiba como conectar redes virtuais com o emparelhamento de rede virtual.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: c7b3fa2b566ab02e7fb4a03055db83f1545895e8
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Conectar redes virtuais com o emparelhamento de rede virtual usando PowerShell

Você pode conectar redes virtuais entre si com o emparelhamento de rede virtual. Depois que as redes virtuais são emparelhadas, os recursos de ambas as redes virtuais podem se comunicar entre si, com a mesma latência e largura de banda como se os recursos estivessem na mesma rede virtual. Este artigo aborda a criação e o emparelhamento de duas redes virtuais. Você aprenderá como:

> [!div class="checklist"]
> * Criar duas redes virtuais
> * Criar um emparelhamento entre redes virtuais
> * Testar o emparelhamento

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o módulo do Azure PowerShell versão 3.6 ou posterior. Execute ` Get-Module -ListAvailable AzureRM` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzureRmAccount` para criar uma conexão com o Azure. 

## <a name="create-virtual-networks"></a>Criar redes virtuais

Antes de criar uma rede virtual, será necessário criar um grupo de recursos para a rede virtual e todos os outros recursos criados neste artigo. Crie um grupo de recursos com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Crie uma rede virtual com [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). O exemplo a seguir cria uma rede virtual nomeada *myVirtualNetwork1* com o prefixo de endereço *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork1 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

Crie uma configuração de sub-rede com [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). O exemplo a seguir cria uma configuração de sub-rede com um prefixo de endereço 10.0.0.0/24:

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Grave a configuração da sub-rede na rede virtual com [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), que cria a sub-rede:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzureRmVirtualNetwork
```

Crie uma rede virtual com um prefixo de endereço 10.1.0.0/16 e uma sub-rede:

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzureRmVirtualNetwork
```

O prefixo de endereço para a rede virtual *myVirtualNetwork2* não sobrepõe com o prefixo de endereço da rede virtual *myVirtualNetwork1*. Não é possível comparar redes virtuais com prefixos de endereço sobrepostos.

## <a name="peer-virtual-networks"></a>Emparelhar redes virtuais

Crie um emparelhamento com [Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering). Os pares de exemplo a seguir *myVirtualNetwork1* a *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

Na saída retornada após a execução do comando anterior, você vê que o **PeeringState** é *Iniciado*. O emparelhamento permanece no estado *Iniciado* até que você crie o emparelhamento de *myVirtualNetwork2* para *myVirtualNetwork1*. Crie um emparelhamento de *myVirtualNetwork2* para *myVirtualNetwork1*. 

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

Na saída retornada após a execução do comando anterior, você vê que o **PeeringState** é *Conectado*. O Azure também alterou o estado de emparelhamento do emparelhamento *myVirtualNetwork1-myVirtualNetwork2* para *Conectado*. Confirme se o estado de emparelhamento para do emparelhamento de *myVirtualNetwork1-myVirtualNetwork2* alterou para *Conectado* com [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering).

```azurepowershell-interactive
Get-AzureRmVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Os recursos de uma rede virtual não podem se comunicar com os recursos da outra rede virtual até que o **PeeringState** dos emparelhamentos de ambas as redes virtuais seja *Conectado*. 

Os emparelhamentos são feitos entre duas redes virtuais, mas não são transitivos. Assim, por exemplo, se você desejar emparelhar *myVirtualNetwork2* com *myVirtualNetwork3*, precisará criar um emparelhamento adicional entre as redes virtuais *myVirtualNetwork2* e *myVirtualNetwork3*. Embora *myVirtualNetwork1* esteja emparelhada com *myVirtualNetwork2*, os recursos de *myVirtualNetwork1* só podem acessar os recursos de *myVirtualNetwork3* se *myVirtualNetwork1* também foi emparelhada com *myVirtualNetwork3*. 

Antes de emparelhar redes virtuais de produção, é recomendável que você se familiarize por completo com a [visão geral do emparelhamento](virtual-network-peering-overview.md), o [gerenciamento do emparelhamento](virtual-network-manage-peering.md) e os [limites da rede virtual](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Embora este artigo ilustre um emparelhamento entre duas redes virtuais na mesma assinatura e no mesmo local, você também pode emparelhar redes virtuais em [regiões diferentes](#register) e [assinaturas diferentes do Azure](create-peering-different-subscriptions.md#powershell). Você também pode criar [designs de rede de hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) com o emparelhamento.

## <a name="test-peering"></a>Testar o emparelhamento

Para testar a comunicação de rede entre máquinas virtuais em diferentes redes virtuais por meio de um emparelhamento, implante uma máquina virtual em cada sub-rede e, em seguida, estabeleça a comunicação entre as máquinas virtuais. 

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie uma máquina virtual em cada rede virtual para que você possa validar a comunicação entre elas em uma etapa posterior.

Crie uma máquina virtual com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). O exemplo a seguir cria uma máquina virtual nomeada *myVm1* na rede virtual *myVirtualNetwork1*. A opção `-AsJob` cria a máquina virtual em segundo plano para que você continue na próxima etapa. Quando solicitado, insira o nome de usuário e a senha com os quais deseja iniciar sessão na máquina virtual.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork1" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm1" `
  -AsJob
```

O Azure atribui automaticamente 10.0.0.4 como o endereço IP privado da máquina virtual, porque 10.0.0.4 é o primeiro endereço IP disponível na *Subnet1* de *myVirtualNetwork1*. 

Crie uma máquina virtual na rede virtual *myVirtualNetwork2*.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

A criação da máquina virtual demora alguns minutos. Embora não esteja na saída retornada, o Azure atribuiu 10.1.0.4 como o endereço IP privado da máquina virtual, porque 10.1.0.4 é o primeiro endereço IP disponível na *Subnet1* da *myVirtualNetwork2*. 

Não continue com as etapas posteriores até que o Azure crie a máquina virtual e retorne a saída para o PowerShell.

### <a name="test-virtual-machine-communication"></a>Testar a comunicação entre máquinas virtuais

É possível conectar ao endereço IP público de uma máquina virtual da Internet. Use [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) para retornar o endereço IP público de uma máquina virtual. O exemplo a seguir retorna o endereço IP público da máquina virtual *myVm1*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Use o comando a seguir para criar uma sessão de área de trabalho remota com a máquina virtual *myVm1* do seu computador local. Substitua `<publicIpAddress>` pelo o endereço IP retornado do comando anterior.

```
mstsc /v:<publicIpAddress>
```

Um arquivo .rdp (protocolo RDP) é criado, baixado para seu computador e aberto. Insira o nome de usuário e senha (talvez seja necessário selecionar **Mais escolhas**, em seguida, **Usar uma conta diferente**, para especificar as credenciais inseridas ao criar a máquina virtual) e clique em **OK**. Você pode receber um aviso do certificado durante o processo de logon. Clique em **Sim** ou em **Continuar** para prosseguir com o processo de conexão.

A partir de um prompt de comando, habilite o ping por meio do firewall do Windows para que seja possível executar ping nessa máquina virtual de *myVm2* em uma etapa posterior.

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Embora o ping seja usado neste artigo para testes, não é recomendável ICMP através do Firewall do Windows para implantações de produção.

Para conectar a máquina virtual *myVm2*, insira o seguinte comando a partir de um prompt de comando na máquina virtual *myVm1*:

```
mstsc /v:10.1.0.4
```

Uma vez habilitado o ping na *myVm1*, será possível executar ping por endereço IP a partir de um prompt de comando na máquina virtual *myVm2*:

```
ping 10.0.0.4
```

Você receberá quatro respostas. Se você executar ping pelo nome da máquina virtual (*myVm1*) em vez de pelo endereço IP, o ping falhará, porque *myVm1* é um nome de host desconhecido. A resolução de nomes padrão do Azure funciona entre máquinas virtuais na mesma rede virtual, mas não entre máquinas virtuais em redes virtuais diferentes. Para resolver nomes entre redes virtuais, você precisa [implantar seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md) ou usar [domínios privados do DNS do Azure](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Desconecte as sessões RDP para ambas *myVm1* e *myVm2*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, utilize [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

**<a name="register"></a>Registrar-se para a versão prévia de emparelhamento de rede virtual global**

O emparelhamento de redes virtuais na mesma região está disponível ao público em geral. Emparelhar redes virtuais em diferentes regiões está em versão prévia no momento. Consulte [Atualizações de rede virtual](https://azure.microsoft.com/updates/?product=virtual-network) para as regiões disponíveis. Para emparelhar redes virtuais entre regiões, primeiro registre-se para versão prévia concluindo as seguintes etapas (na assinatura de cada rede virtual que você deseja emparelhar):

1. Registre a assinatura na qual cada rede virtual que você deseja emparelhar está para a versão prévia inserindo os seguintes comandos:

    ```powershell-interactive
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
2. Digite o seguinte comando para confirmar que você está registrado para a versão prévia:

    ```powershell-interactive    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```

    Caso você tente emparelhar redes virtuais em regiões diferentes antes que a saída de **RegistrationState** recebida após a inserção do comando anterior seja **Registrado** para ambas as assinaturas, o emparelhamento falhará.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a conectar duas redes com o emparelhamento de rede virtual. Você pode [conectar seu próprio computador a uma rede virtual](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) por meio de uma VPN e interagir com os recursos de uma rede virtual ou de redes virtuais emparelhadas.

Continue lendo para obter amostras de scripts reutilizáveis para concluir muitas das tarefas abordadas nos artigos sobre redes virtuais.

> [!div class="nextstepaction"]
> [Amostras de script de rede virtual](../networking/powershell-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
