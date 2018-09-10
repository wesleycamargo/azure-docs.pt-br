---
title: Exemplo de script do Azure PowerShell – Rotear o tráfego por meio de uma solução de virtualização de rede | Microsoft Docs
description: Exemplo de script do Azure PowerShell – Rotear o tráfego por meio de uma solução de virtualização de rede de firewall.solução de virtualização.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: b74498da80391624025887546fcfdf137bacc9d8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/19/2018
ms.locfileid: "31601127"
---
# <a name="route-traffic-through-a-network-virtual-appliance-script-sample"></a>Rotear o tráfego por meio de um exemplo de script de solução de virtualização de rede

Este exemplo de script cria uma rede virtual com sub-redes de front-end e back-end. Ele também cria uma VM com o encaminhamento de IP habilitado para rotear o tráfego entre as duas sub-redes. Depois de executar o script, você pode implantar o software de rede, como um aplicativo de firewall, à VM.

Você pode executar o script do Azure [Cloud Shell](https://shell.azure.com/powershell) ou de uma instalação local do PowerShell. Se você usar o PowerShell localmente, este script exigirá o módulo do AzureRM PowerShell versão 5.4.1 ou posterior. Para localizar a versão instalada, execute `Get-Module -ListAvailable AzureRM`. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzureRmAccount` para criar uma conexão com o Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo


[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.ps1 "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```
## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para criar um grupo de recursos, uma rede virtual e grupos de segurança de rede. Cada comando na tabela a seguir redireciona para a documentação específica do comando:

| Comando | Observações |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)  | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Cria uma rede virtual do Azure e uma sub-rede front-end. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Cria as sub-redes back-end e DMZ. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Cria um endereço IP público para acessar a VM da Internet. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Cria uma interface de rede virtual e habilita o encaminhamento IP nela. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Cria um grupo de segurança de rede (NSG). |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Cria regras de NSG que permitem as portas HTTP e HTTPS de entrada para a VM. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)| Associa os NSGs e tabelas de rotas às sub-redes. |
| [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable)| Cria uma tabela de rotas com todas as rotas. |
| [New-AzureRMRouteConfig](/powershell/module/azurerm.network/new-azurermrouteconfig)| Cria as rotas para rotear o tráfego entre sub-redes e a Internet por meio da VM. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Cria uma máquina virtual e anexa o NIC a ela. Este comando também especifica a imagem de máquina virtual a ser usada e as credenciais administrativas. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup)  | Exclui um grupo de recursos e todos os seus recursos contidos nele. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Amostras de script PowerShell de rede virtual adicionais podem ser encontradas em [Amostras de PowerShell de rede virtual](../powershell-samples.md).
