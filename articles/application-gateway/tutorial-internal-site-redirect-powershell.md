---
title: "Criar um gateway de aplicativo com redirecionamento interno – Azure PowerShell | Microsoft Docs"
description: "Saiba como criar um gateway de aplicativo que redireciona o tráfego interno da Web ao pool back-end adequado de servidores usando o Azure Powershell."
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2018
ms.author: davidmu
ms.openlocfilehash: c319d4f9aa3f607bccdc7237ce1f678b338180d2
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-application-gateway-with-internal-redirection-using-azure-powershell"></a>Criar um gateway de aplicativo com redirecionamento interno usando o Azure PowerShell

Você pode usar o Azure PowerShell para configurar o [redirecionamento do tráfego da Web](application-gateway-multi-site-overview.md) ao criar um [gateway de aplicativo](application-gateway-introduction.md). Neste tutorial, você define pools de back-end usando um conjunto de dimensionamento de máquinas virtuais. Em seguida, configura ouvintes e regras com base em domínios que você tem, para garantir que o tráfego da Web chegue ao pool apropriado. Este tutorial presume que você tem vários domínios e usa os exemplos *www.contoso.com* e *www.contoso.org*.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Configurar a rede
> * Criar um Gateway de Aplicativo
> * Adicionar ouvintes e regras de redirecionamento
> * Criar um conjunto de dimensionamento de máquinas virtuais com o pool de back-end
> * Criar um registro CNAME no seu domínio

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 3.6 ou posterior. Para saber qual é a versão, execute ` Get-Module -ListAvailable AzureRM`. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzureRmAccount` para criar uma conexão com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Crie um grupo de recursos do Azure usando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).  

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Criar recursos da rede

Criar as configurações de sub-rede para *myBackendSubnet* e *myAGSubnet* usando [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Crie a rede virtual denominada *myVNet* usando [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) com as configurações de sub-rede. E, finalmente, crie o endereço IP público denominado *myAGPublicIPAddress* usando [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Esses recursos são usados para fornecer conectividade de rede ao gateway de aplicativo e seus recursos associados.

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

Associe a *myAGSubnet* que você criou anteriormente para o gateway de aplicativo usando [New-AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration). Atribua o *myAGPublicIPAddress* ao gateway de aplicativo usando [New-AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig). E, em seguida, você pode criar a porta HTTP usando [New-AzureRmApplicationGatewayFrontendPort](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendport).

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
$pip = Get-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Name myAGPublicIPAddress
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendPort = New-AzureRmApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool-and-settings"></a>Criar o pool de back-end e as configurações

Crie um pool de back-end denominado *contosoPool* para o gateway de aplicativo usando [New-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool). Defina as configurações para o pool de back-end usando [New-AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings).

```azurepowershell-interactive
$contosoPool = New-AzureRmApplicationGatewayBackendAddressPool `
  -Name contosoPool 
$poolSettings = New-AzureRmApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-first-listener-and-rule"></a>Criar o primeiro ouvinte e a regra

Um ouvinte é necessário para habilitar o gateway de aplicativo para rotear o tráfego corretamente para o pool de back-end. Neste tutorial, você cria dois ouvintes para os seus dois domínios. Neste exemplo, são criados ouvintes para os domínios *www.contoso.com* e *www.contoso.org*.

Crie o primeiro ouvinte denominado *contosoComListener* usando [New-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) com a configuração de front-end e porta de front-end que você criou anteriormente. Uma regra é necessária para o ouvinte saber qual pool de back-end deve ser usado para tráfego de entrada. Crie uma regra básica denominada *contosoComRule* usando [New-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$contosoComlistener = New-AzureRmApplicationGatewayHttpListener `
  -Name contosoComListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendPort `
  -HostName "www.contoso.com"
$frontendRule = New-AzureRmApplicationGatewayRequestRoutingRule `
  -Name contosoComRule `
  -RuleType Basic `
  -HttpListener $contosoComListener `
  -BackendAddressPool $contosoPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Criar o gateway de aplicativo

Agora que você criou os recursos de suporte necessários, especifique os parâmetros para o gateway de aplicativo denominado *myAppGateway* usando [New-AzureRmApplicationGatewaySku](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku) e, em seguida, crie-o usando [New-AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway).

```azurepowershell-interactive
$sku = New-AzureRmApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzureRmApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $contosoPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendPort `
  -HttpListeners $contosoComListener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

### <a name="add-the-second-listener"></a>Adicionar o segundo ouvinte

Adicione o ouvinte denominado *contosoOrgListener* que é necessário para redirecionar o tráfego usando [Add-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/add-azurermapplicationgatewayhttplistener).

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$frontendPort = Get-AzureRmApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -ApplicationGateway $appgw
$ipconfig = Get-AzureRmApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -ApplicationGateway $appgw
Add-AzureRmApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name contosoOrgListener `
  -Protocol Http `
  -FrontendIPConfiguration $ipconfig `
  -FrontendPort $frontendPort `
  -HostName "www.contoso.org"
Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-the-redirection-configuration"></a>Adicionar a configuração de redirecionamento

É possível configurar o redirecionamento para o ouvinte usando [Add-AzureRmApplicationGatewayRedirectConfiguration](/powershell/module/azurerm.network/add-azurermapplicationgatewayredirectconfiguration). 

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$contosoComlistener = Get-AzureRmApplicationGatewayHttpListener `
  -Name contosoComListener `
  -ApplicationGateway $appgw
$contosoOrglistener = Get-AzureRmApplicationGatewayHttpListener `
  -Name contosoOrgListener `
  -ApplicationGateway $appgw
Add-AzureRmApplicationGatewayRedirectConfiguration `
  -ApplicationGateway $appgw `
  -Name redirectOrgtoCom `
  -RedirectType Found `
  -TargetListener $contosoComListener `
  -IncludePath $true `
  -IncludeQueryString $true
Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-the-second-routing-rule"></a>Adicionar a segunda regra de roteamento

Você pode associar a configuração de redirecionamento a uma nova regra chamada *contosoOrgRule* usando [AzureRmApplicationGatewayRequestRoutingRule adicionar](/powershell/module/azurerm.network/add-azurermapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$contosoOrglistener = Get-AzureRmApplicationGatewayHttpListener `
  -Name contosoOrgListener `
  -ApplicationGateway $appgw
$redirectConfig = Get-AzureRmApplicationGatewayRedirectConfiguration `
  -Name redirectOrgtoCom `
  -ApplicationGateway $appgw   
Add-AzureRmApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name contosoOrgRule `
  -RuleType Basic `
  -HttpListener $contosoOrgListener `
  -RedirectConfiguration $redirectConfig
Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-virtual-machine-scale-set"></a>Criar conjunto de dimensionamento de máquinas virtuais

Neste exemplo, você criará um conjunto de dimensionamento de máquinas virtuais que oferece suporte ao pool de back-end criado. O conjunto de dimensionamento criado é chamado *myvmss* e contém duas instâncias de máquina virtual em que você instala o IIS. Você atribui o conjunto de dimensionamento para o pool de back-end quando define as configurações de IP.

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -Name contosoPool `
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

## <a name="create-cname-record-in-your-domain"></a>Criar um registro CNAME em seu domínio

Depois de criar o gateway de aplicativo com seu endereço IP público, você pode obter o endereço DNS e usá-lo para criar um registro CNAME em seu domínio. Você pode usar [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) para obter o endereço DNS do gateway de aplicativo. Copie o valor de *fqdn* em DNSSettings e use-o como o valor do registro CNAME a ser criado. O uso de registros A não é recomendado, pois o VIP pode mudar quando o gateway de aplicativo for reiniciado.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

Digite seu nome de domínio na barra de endereços do navegador. Por exemplo, http://www.contoso.com.

![Testar o site contoso no gateway do aplicativo](./media/tutorial-internal-site-redirect-powershell/application-gateway-iistest.png)

Altere o endereço para o seu outro domínio, por exemplo http://www.contoso.org, e você verá que o tráfego foi redirecionado de volta para o ouvinte de www.contoso.com.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Configurar a rede
> * Criar um Gateway de Aplicativo
> * Adicionar ouvintes e regras de redirecionamento
> * Criar um conjunto de dimensionamento de máquinas virtuais com os pools de back-end
> * Criar um registro CNAME no seu domínio

> [!div class="nextstepaction"]
> [Saiba mais sobre o que você pode fazer com o gateway de aplicativo](./application-gateway-introduction.md)