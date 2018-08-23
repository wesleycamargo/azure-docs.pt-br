---
title: Exemplo de script do Azure PowerShell - Criar ambiente de teste do Firewall do Azure
description: Exemplo de script do Azure PowerShell - Criar ambiente de teste do Firewall do Azure.
services: virtual-network
author: vhorne
ms.service: firewall
ms.devlang: powershell
ms.topic: sample
ms.date: 8/13/2018
ms.author: victorh
ms.openlocfilehash: b65a5dec63bdc625dda64e101620f56cd6dd7308
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2018
ms.locfileid: "41924712"
---
# <a name="create-an-azure-firewall-test-environment"></a>Criar um ambiente de teste do Firewall do Azure

[!INCLUDE [firewall-preview-notice](../../../includes/firewall-preview-notice.md)]

Os exemplos nos artigos do Firewall do Azure supõem que você já habilitou a visualização pública do Firewall do Azure. Para obter mais informações, consulte [Habilitar a versão prévia do Firewall do Azure](../public-preview.md).

Este exemplo de script cria um firewall e um ambiente de rede de teste. A rede tem uma VNet, com três sub-redes: uma *AzureFirewallSubnet*, uma *ServersSubnet* e uma *JumpboxSubnet*. O ServersSubnet e o JumpboxSubnet têm, cada um, um Windows Server de dois núcleos neles.

O firewall está na AzureFirewallSubnet e é configurado com uma Coleção de regra de aplicativo com uma única regra que permite o acesso ao www.microsoft.com.

Uma rota definida pelo usuário é criada que aponta o tráfego de rede do ServersSubnet até o firewall, em que as regras de firewall são aplicadas.

Você pode executar o script do Azure [Cloud Shell](https://shell.azure.com/powershell) ou de uma instalação local do PowerShell. 

Se você executar o PowerShell localmente, este script exigirá o módulo do AzureRM PowerShell versão 6.4.0 ou posterior. Para localizar a versão instalada, execute `Get-Module -ListAvailable AzureRM`. 

Você pode usar `PowerShellGet` se precisar fazer upgrade, que é incorporado no Windows 10 e no Windows Server 2016.

> [!NOTE]
>Outra versão do Windows exigem a instalação do `PowerShellGet` antes que você possa usá-lo. Você pode executar `Get-Module -Name PowerShellGet -ListAvailable | Select-Object -Property Name,Version,Path` para determinar se ele está instalado em seu sistema. Se a saída estiver em branco, você precisará instalar a versão mais recente do [Windows Management framework](https://www.microsoft.com/download/details.aspx?id=54616).

Para saber mais, veja [Instalar o Azure PowerShell no Windows com o PowerShellGet](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-6.4.0)

Qualquer instalação existente do Azure PowerShell feita com o Web Platform installer entrará em conflito com a instalação do PowerShellGet e precisa ser removido.

Além disso, você deve instalar a versão prévia do AzureRM.Network (versão 6.4.0). Se tiver um módulo mais antigo, execute `Uninstall-Module AzureRM.Network -Force` para removê-lo. Em seguida, execute:

 `Install-Module -Name AzureRM.Network -Repository PSGallery -RequiredVersion 6.4.0-preview -AllowPrerelease -Force`

para instalar a versão 6.4.0.

Lembre-se de que, se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzureRmAccount` para criar uma conexão com o Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo


[!code-azurepowershell-interactive[main](../../../powershell_scripts/firewall/create-fw-test.ps1  "Create a firewall test environment")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados:

```powershell
Remove-AzureRmResourceGroup -Name AzfwSampleScriptEastUS -Force
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para criar um grupo de recursos, uma rede virtual e grupos de segurança de rede. Cada comando na tabela a seguir redireciona para a documentação específica do comando:

| Comando | Observações |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Cria um objeto de configuração de sub-rede |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Cria uma rede virtual do Azure e uma sub-rede front-end. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Cria regras de segurança a serem atribuídas a um grupo de segurança de rede. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) |Cria regras NSG que permitem ou bloqueiam portas específicas para sub-redes específicas. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) | Associa os NSGs às sub-redes. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Cria um endereço IP público para acessar a VM da Internet. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Cria as interfaces de rede virtual e as anexa às sub-redes de front-end e back-end da rede virtual. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Cria uma configuração de VM. Essa configuração inclui informações como nome da VM, sistema operacional e credenciais administrativas. A configuração é usada durante a criação da VM. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Crie uma máquina virtual. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Remove um grupo de recursos e todos os recursos contidos nele. |
|[New-AzureRmFirewall](https://github.com/Azure/azure-powershell/blob/Networking-AzureFirewall/src/ResourceManager/Network/Commands.Network/help/New-AzureRmFirewall.md)| Cria um novo Firewall do Azure.|
|[Get-AzureRmFirewall](https://github.com/Azure/azure-powershell/blob/Networking-AzureFirewall/src/ResourceManager/Network/Commands.Network/help/Get-AzureRmFirewall.md)|Obtém um objeto de Firewall do Azure.|
|[New-AzureRmFirewallApplicationRule](https://github.com/Azure/azure-powershell/blob/Networking-AzureFirewall/src/ResourceManager/Network/Commands.Network/help/New-AzureRmFirewallApplicationRule.md)|Cria uma nova regra de aplicativo do Firewall do Azure.|
|[Set-AzureRmFirewall](https://github.com/Azure/azure-powershell/blob/Networking-AzureFirewall/src/ResourceManager/Network/Commands.Network/help/Set-AzureRmFirewall.md)|Confirma as alterações para o objeto do Firewall do Azure.|


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/azure/overview).

