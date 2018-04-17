---
title: Exemplo de script do Azure PowerShell – Criar uma rede para aplicativos de várias camadas | Microsoft Docs
description: Exemplo de script do Azure PowerShell – Criar uma rede virtual para aplicativos de várias camadas.
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
ms.openlocfilehash: 7999dbad10aaaeacfdb6fcbeccd744a2a5391326
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2018
---
# <a name="create-a-network-for-multi-tier-applications-script-sample"></a>Criar uma rede de exemplo do script de aplicativos de várias camadas

Este exemplo de script cria uma rede virtual com sub-redes de front-end e back-end. O tráfego para a sub-rede de front-end é limitado a HTTP e SSH, enquanto o tráfego para a sub-rede de back-end é limitado a MySQL, porta 3306. Depois de executar o script, você terá duas máquinas virtuais, um em cada sub-rede, nas quais você pode implantar o servidor Web e o software MySQL.

Você pode executar o script do Azure [Cloud Shell](https://shell.azure.com/powershell) ou de uma instalação local do PowerShell. Se você usar o PowerShell localmente, este script exigirá o módulo do AzureRM PowerShell versão 5.4.1 ou posterior. Para localizar a versão instalada, execute `Get-Module -ListAvailable AzureRM`. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzureRmAccount` para criar uma conexão com o Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo


[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.ps1  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para criar um grupo de recursos, uma rede virtual e grupos de segurança de rede. Cada comando na tabela a seguir redireciona para a documentação específica do comando:

| Comando | Observações |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Cria uma rede virtual do Azure e uma sub-rede front-end. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Cria uma sub-rede back-end. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Cria um endereço IP público para acessar a VM da Internet. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Cria as interfaces de rede virtual e as anexa às sub-redes de front-end e back-end da rede virtual. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Cria NSG (grupos de segurança de rede) associados às sub-redes de front-end e back-end. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) |Cria regras NSG que permitem ou bloqueiam portas específicas para sub-redes específicas. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Cria máquinas virtuais e anexa um NIC a cada VM. Este comando também especifica a imagem de máquina virtual a ser usada e as credenciais administrativas. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Exclui um grupo de recursos e todos os seus recursos contidos nele. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/azure/overview).

Amostras de script PowerShell de rede virtual adicionais podem ser encontradas em [Amostras de PowerShell de rede virtual](../powershell-samples.md).