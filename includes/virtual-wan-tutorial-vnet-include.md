---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 660bbf50e1a8ae73bd7bbe1f7c42691ed62d276a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552954"
---
Se você ainda não tem uma rede virtual, crie uma rapidamente usando o PowerShell. Você também pode criar uma rede virtual usando o portal do Azure.

* Verifique se o espaço de endereço para a VNET criada não corresponde a nenhum dos intervalos de endereço de outras VNETs às quais você deseja se conectar ou a espaços de endereço de rede local. 
* Se você já tiver uma VNET, verifique se ela atende aos critérios necessários e não tem um gateway de rede virtual.

Você pode criar sua rede virtual facilmente clicando em "Experimentar" neste artigo para abrir um console do PowerShell. Ajuste os valores e copie e cole os comandos na janela de console.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Ajuste os comandos do PowerShell e crie um grupo de recursos.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Criar uma VNET

Ajuste os comandos do PowerShell para criar a VNET compatível com seu ambiente.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```