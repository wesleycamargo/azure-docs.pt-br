---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1fae3c3889242dfbf8f270d3762ea7434ceda6da
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47004116"
---
Se você ainda não tem uma rede virtual, crie uma rapidamente usando o PowerShell. Você também pode criar uma rede virtual usando o portal do Azure.

* Verifique se o espaço de endereço para a VNET criada não corresponde a nenhum dos intervalos de endereço de outras VNETs às quais você deseja se conectar ou a espaços de endereço de rede local. 
* Se você já tiver uma VNET, verifique se ela atende aos critérios necessários e não tem um gateway de rede virtual.

Você pode criar sua rede virtual facilmente clicando em "Experimentar" neste artigo para abrir um console do PowerShell. Ajuste os valores e copie e cole os comandos na janela de console.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Ajuste os comandos do PowerShell e crie um grupo de recursos.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Criar uma VNET

Ajuste os comandos do PowerShell para criar a VNET compatível com seu ambiente.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzureRmVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```