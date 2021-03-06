---
title: Criar um Gateway de Aplicativo - Azure PowerShell | Microsoft Docs
description: Saiba como criar um gateway de aplicativo usando o Azure PowerShell.
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: azurepowershell
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: victorh
ms.openlocfilehash: 412ede9895e6684d039dcad4df4189c8b3774d76
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2019
ms.locfileid: "57313813"
---
# <a name="create-an-application-gateway-using-azure-powershell"></a>Criar um gateway de aplicativo usando o Azure PowerShell

Você pode usar o Azure PowerShell para criar ou gerenciar os gateways de aplicativos de linha de comando ou em scripts. Este guia de início rápido mostra como criar recursos de rede, servidores de back-end e um gateway de aplicativo.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial requer o módulo Azure PowerShell versão 1.0.0 ou posterior. Para saber qual é a versão, execute `Get-Module -ListAvailable Az`. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um grupo de recursos do Azure usando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Criar recursos da rede 

Criar usando as configurações de sub-rede [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Crie a rede virtual usando [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) com as configurações de sub-rede. E, finalmente, crie o endereço IP público usando [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Esses recursos são usados para fornecer conectividade de rede ao gateway de aplicativo e seus recursos associados.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```
## <a name="create-backend-servers"></a>Criar servidores de back-end

Neste exemplo, você cria duas máquinas virtuais para serem usadas como servidores de back-end para o gateway do aplicativo. Você também instala o IIS nas máquinas virtuais para verificar se o gateway do aplicativo foi criado com êxito.

### <a name="create-two-virtual-machines"></a>Criar duas máquinas virtuais

Crie um adaptador de rede com [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Crie uma configuração de máquina virtual com [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). Quando você executa os comandos a seguir, as credenciais são solicitadas a você. Digite *azureuser* para o nome de usuário e *Azure123456!* para a senha. Crie a máquina virtual com [New-AzVM](/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -SubnetId $vnet.Subnets[1].Id
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2
  $vm = Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  $vm = Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  $vm = Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  $vm = Set-AzVMBootDiagnostics `
    -VM $vm `
    -Disable
  New-AzVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```

## <a name="create-an-application-gateway"></a>Criar um Gateway de Aplicativo

### <a name="create-the-ip-configurations-and-frontend-port"></a>Criar as configurações de IP e porta de front-end

Use [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) para criar a configuração que associa a sub-rede que você criou anteriormente com o gateway de aplicativo. Use [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) para criar a configuração que atribui o endereço IP público que você criou anteriormente também ao gateway de aplicativo. Use [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) para atribuir a porta 80 para ser usado para acessar o gateway de aplicativo.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$pip = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
$subnet=$vnet.Subnets[0]
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool"></a>Criar o pool de back-end

Use [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) para criar o pool de back-end para o gateway de aplicativo. Definir as configurações para o pool de back-end usando [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsettings).

```azurepowershell-interactive
$address1 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool `
  -BackendIPAddresses $address1.ipconfigurations[0].privateipaddress, $address2.ipconfigurations[0].privateipaddress
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-add-a-rule"></a>Criar o ouvinte e adicionar uma regra

Um ouvinte é necessário para habilitar o gateway de aplicativo para rotear o tráfego corretamente para o pool de back-end. Criar um ouvinte usando [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) com a configuração de front-end e porta de front-end que você criou anteriormente. Uma regra é necessária para o ouvinte saber qual pool de back-end deve ser usado para tráfego de entrada. Use [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) para criar uma regra denominada *rule1*.

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Criar o gateway de aplicativo

Agora que você criou os recursos de suporte necessários, use [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) para especificar parâmetros para o gateway de aplicativo e, em seguida, use [New AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) para criá-lo.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $backendPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

Use [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) para obter o endereço IP público do gateway de aplicativo. Copie o endereço IP público e cole-o na barra de endereços do seu navegador.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Teste o gateway de aplicativo](./media/application-gateway-create-gateway-arm/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o [AzResourceGroup remover](/powershell/module/az.resources/remove-azresourcegroup) de comando para remover o grupo de recursos, o gateway de aplicativo e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um grupo de recursos, recursos de rede e servidores de back-end. Você depois usou esses recursos para criar um gateway de aplicativo. Para saber mais sobre os gateways de aplicativo e seus recursos associados, continue para os artigos de instrução.

