---
title: Criar um gateway de aplicativo com o firewall do aplicativo Web - Azure PowerShell| Microsoft Docs
description: Saiba como criar um gateway de aplicativo com o firewall do aplicativo Web usando o Azure PowerShell.
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: davidmu
ms.openlocfilehash: fe36076988e65837340ec70982de788e532c455d
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-azure-powershell"></a>Criar um gateway de aplicativo com um firewall do aplicativo Web usando Azure PowerShell

Você pode usar Azure PowerShell para criar um [gateway de aplicativo](application-gateway-introduction.md) com um [firewall do aplicativo Web](application-gateway-web-application-firewall-overview.md) (WAF) que usa um [conjunto de dimensionamento de máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) para servidores back-end. O WAF usa as regras de [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) para proteger o seu aplicativo. Essas regras incluem proteção contra ataques, como injeção de SQL, ataques de script entre sites e sequestros de sessão. 

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Configurar a rede
> * Criar um gateway de aplicativo com o WAF habilitado
> * Criar um conjunto de dimensionamento de máquinas virtuais
> * Criar uma conta de armazenamento e configurar diagnósticos

![Exemplo de Firewall do aplicativo Web](./media/application-gateway-web-application-firewall-powershell/scenario-waf.png)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 3.6 ou posterior. Execute ` Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzureRmAccount` para criar uma conexão com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Crie um grupo de recursos do Azure usando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).  

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Criar recursos da rede 

Criar as configurações de sub-rede denominadas *myBackendSubnet* e *myAGSubnet* usando [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Crie a rede virtual denominada *myVNet* usando [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) com as configurações de sub-rede. E, finalmente, crie o endereço IP público denominado *myAGPublicIPAddress* usando [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Esses recursos são usados para fornecer conectividade de rede ao gateway do aplicativo e seus recursos associados.

```azurepowershell-interactive
$backendSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
$agSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Criar um Gateway de Aplicativo

### <a name="create-the-ip-configurations-and-frontend-port"></a>Criar as configurações de IP e porta de front-end

Associe a *myAGSubnet* que você criou anteriormente ao gateway de aplicativo usando [New-AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration). Atribua o *myAGPublicIPAddress* ao gateway de aplicativo usando [New-AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig).

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzureRmApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool-and-settings"></a>Criar o pool de back-end e as configurações

Crie o pool de back-end denominado *appGatewayBackendPool* para o gateway de aplicativo usando [New-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool). Defina as configurações para os pools de endereço de back-end usando [New-AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings).

```azurepowershell-interactive
$defaultPool = New-AzureRmApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool 
$poolSettings = New-AzureRmApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Criar a regra e o listener padrão

Um listener é necessário para habilitar o gateway de aplicativo para rotear o tráfego corretamente para o pool de back-end. Neste exemplo, você cria um listener básico que ouve o tráfego na URL da raiz. 

Crie um listener denominado *mydefaultListener* usando [New-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) com a configuração de front-end e porta de front-end que você criou anteriormente. Uma regra é necessária para o listener saber qual pool de back-end deve ser usado para o tráfego de entrada. Crie uma regra básica denominada *rule1* usando [New-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$defaultlistener = New-AzureRmApplicationGatewayHttpListener `
  -Name mydefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzureRmApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway-with-the-waf"></a>Criar o gateway de aplicativo com o WAF

Agora que você criou os recursos de suporte necessários, especifique os parâmetros para o gateway de aplicativo usando [New-AzureRmApplicationGatewaySku](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku). Especifique a configuração WAF usando [New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewaywebapplicationfirewallconfiguration). E, em seguida, crie o gateway de aplicativo denominado *myAppGateway* usando [New-AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway).

```azurepowershell-interactive
$sku = New-AzureRmApplicationGatewaySku `
  -Name WAF_Medium `
  -Tier WAF `
  -Capacity 2
$wafConfig = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration `
  -Enabled $true `
  -FirewallMode "Detection"
$appgw = New-AzureRmApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku `
  -WebApplicationFirewallConfig $wafConfig
```

## <a name="create-a-virtual-machine-scale-set"></a>Criar um conjunto de dimensionamento de máquinas virtuais

Neste exemplo, você criará um conjunto de dimensionamento de máquinas virtuais para fornecer servidores para o pool de back-end no gateway de aplicativo. Você atribui o conjunto de dimensionamento para o pool de back-end quando define as configurações de IP.

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw
$ipConfig = New-AzureRmVmssIpConfig `
  -Name myVmssIPConfig `
  -SubnetId $vnet.Subnets[1].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id
$vmssConfig = New-AzureRmVmssConfig `
  -Location eastus `
  -SkuCapacity 2 `
  -SkuName Standard_DS2 `
  -UpgradePolicyMode Automatic
Set-AzureRmVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher MicrosoftWindowsServer `
  -ImageReferenceOffer WindowsServer `
  -ImageReferenceSku 2016-Datacenter `
  -ImageReferenceVersion latest
Set-AzureRmVmssOsProfile $vmssConfig `
  -AdminUsername azureuser `
  -AdminPassword "Azure123456!" `
  -ComputerNamePrefix myvmss
Add-AzureRmVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name myVmssNetConfig `
  -Primary $true `
  -IPConfiguration $ipConfig
New-AzureRmVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmssConfig
```

### <a name="install-iis"></a>Instalar o IIS

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/davidmu1/samplescripts/master/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzureRmVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Criar uma conta de armazenamento e configurar diagnósticos

Neste tutorial, o gateway de aplicativo usa uma conta de armazenamento para armazenar dados para fins de detecção e prevenção. Você também pode usar o Log Analytics ou Hub de Eventos para registrar os dados.

### <a name="create-the-storage-account"></a>Criar a conta de armazenamento

Crie uma conta de armazenamento denominada *myagstore1* usando [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount).

```azurepowershell-interactive
$storageAccount = New-AzureRmStorageAccount `
  -ResourceGroupName myResourceGroupAG `
  -Name myagstore1 `
  -Location eastus `
  -SkuName "Standard_LRS"
```

### <a name="configure-diagnostics"></a>Configurar o diagnóstico

Configure o diagnóstico para registrar dados nos logs de ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog e ApplicationGatewayFirewallLog usando [Set-AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting).

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$store = Get-AzureRmStorageAccount `
  -ResourceGroupName myResourceGroupAG `
  -Name myagstore1
Set-AzureRmDiagnosticSetting `
  -ResourceId $appgw.Id `
  -StorageAccountId $store.Id `
  -Categories ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog, ApplicationGatewayFirewallLog `
  -Enabled $true `
  -RetentionEnabled $true `
  -RetentionInDays 30
```

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

Você pode usar [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) para obter o endereço IP público do gateway de aplicativo. Copie o endereço IP público e cole-o na barra de endereços do seu navegador.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testar a URL de base no gateway de aplicativo](./media/application-gateway-web-application-firewall-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Configurar a rede
> * Criar um gateway de aplicativo com o WAF habilitado
> * Criar um conjunto de dimensionamento de máquinas virtuais
> * Criar uma conta de armazenamento e configurar diagnósticos

Para saber mais sobre os gateways de aplicativo e seus recursos associados, siga até os artigos de instrução.