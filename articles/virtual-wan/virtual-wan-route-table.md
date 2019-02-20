---
title: Criar uma tabela de rotas do hub virtual da WAN Virtual do Azure para conduzir a NVA | Microsoft Docs
description: Tabela de rotas do hub virtual da WAN Virtual para conduzir o tráfego para uma solução de virtualização de rede.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: 45e5c43cf5eb8df1df5b26ffae50d2881bb086e4
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56115191"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>Criar uma tabela de rotas do Hub Virtual para conduzir o tráfego para uma solução de virtualização de rede

Este artigo mostra como conduzir tráfego de um Hub Virtual para uma Solução de Virtualização de Rede. 

![Diagrama de WAN virtual](./media/virtual-wan-route-table/vwanroute.png)

Neste artigo, você aprenderá a:

* Criar uma WAN
* Criar um hub
* Criar conexões de rede virtual do hub
* Criar uma rota do hub
* Criar uma tabela de rotas
* Aplicar a tabela de rotas

## <a name="before-you-begin"></a>Antes de começar

Verifique se você atende aos seguintes critérios:

1. Você tem uma NVA (Solução de Virtualização de Rede), que é um software de terceiros de sua escolha, que normalmente é provisionado no Azure Marketplace (Link) em uma rede virtual.
2. Você tem um IP privado atribuído ao adaptador de rede da NVA. 
3. A NVA não pode ser implantada no hub virtual. Ela deve ser implantada em uma VNet separada. Neste artigo, a VNet NVA é chamada de 'VNet DMZ'.
4. A “VNet DMZ” pode ter uma ou várias redes virtuais conectadas a ela. Neste artigo, essa VNet é chamada de “VNet de spoke indireto”. Essas VNets podem ser conectadas à rede VNets DMZ usando o emparelhamento VNet.
5. Verifique se você tem duas VNets já criadas. Elas serão usadas como VNets spoke. Neste artigo, os espaços de endereço da VNets spoke são 10.0.2.0/24 e 10.0.3.0/24. Se você precisar obter informações sobre como criar uma VNets, consulte [Criar uma rede virtual usando o PowerShell](../virtual-network/quick-create-powershell.md).
6. Certifique-se de que não há nenhum gateway de rede virtual nas VNets.

## <a name="signin"></a>1. Entrar

Certifique-se de instalar a versão mais recente dos cmdlets do PowerShell do Resource Manager. Para saber mais sobre como instalar cmdlets do PowerShell, confira [Como instalar e configurar o Azure PowerShell](/powershell/azure/azurerm/overview). Isso é importante porque as versões anteriores dos cmdlets não contêm os valores atuais que são necessários para este exercício. Os módulos nos exemplos a seguir são o Azure RM. Este artigo será atualizado para o Azure Az no futuro.

1. Abra o console do PowerShell com privilégios elevados e entre na sua conta do Azure. Esse cmdlet solicita as credenciais de entrada. Depois de entrar, ele baixa as configurações da conta para que elas estejam disponíveis para o Azure PowerShell.

  ```powershell
  Connect-AzureRmAccount
  ```
2. Obtenha uma lista das assinaturas do Azure.

  ```powershell
  Get-AzureRmSubscription
  ```
3. Especifique a assinatura que você deseja usar.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```

## <a name="rg"></a>2. Criar recursos

1. Crie um grupos de recursos.

  ```powershell
  New-AzureRmResourceGroup -Location "West US" -Name "testRG"
  ```
2. Crie uma WAN virtual.

  ```powershell
  $virtualWan = New-AzureRmVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
  ```
3. Crie um hub virtual.

  ```powershell
  New-AzureRmVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24"
  ```

## <a name="connections"></a>3. Criar conexões

Crie conexões de rede virtual de hub da VNet de spoke indireto e a VNet DMZ para o hub virtual.

  ```powershell
  $remoteVirtualNetwork1= Get-AzureRmVirtualNetwork -Name “indirectspoke1” -ResourceGroupName “testRG”
  $remoteVirtualNetwork2= Get-AzureRmVirtualNetwork -Name “indirectspoke2” -ResourceGroupName “testRG”
  $remoteVirtualNetwork3= Get-AzureRmVirtualNetwork -Name “dmzvnet” -ResourceGroupName “testRG”

  New-AzureRmVirtualHubVnetConnection -ResourceGroupName “testRG” -VirtualHubName “westushub” -Name  “testvnetconnection1” -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzureRmVirtualHubVnetConnection -ResourceGroupName “testRG” -VirtualHubName “westushub” -Name  “testvnetconnection2” -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzureRmVirtualHubVnetConnection -ResourceGroupName “testRG” -VirtualHubName “westushub” -Name  “testvnetconnection3” -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="route"></a>4. Crie uma rota de hub virtual

Neste artigo, os espaços de endereço da VNet de spoke indireto são 10.0.2.0/24 e 10.0.3.0/24 e o endereço IP privado do adaptador de rede da NVA DMZ é 10.0.4.5.

```powershell
$route1 = New-AzureRmVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="applyroute"></a>5. Crie uma tabela de rota de hub virtual

Crie uma tabela de rota do hub virtual e, em seguida, aplique a rota criada.
 
```powershell
$routeTable = New-AzureRmVirtualHubRouteTable -Route @($route1)
```

## <a name="commit"></a>6. Confirmar as alterações

Confirme as alterações no hub virtual.

```powershell
Update-AzureRmVirtualHub -VirtualWanId $virtualWan.Id -ResourceGroupName "testRG" -Name "westushub” -RouteTable $routeTable
```

## <a name="cleanup"></a>Limpar recursos

Quando não precisar mais desses recursos, você poderá utilizar [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém. Substitua "myResourceGroup" pelo nome do grupo de recursos e execute o seguinte comando do PowerShell:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a WAN Virtual, consulte a página [Visão geral de WAN Virtual](virtual-wan-about.md).
