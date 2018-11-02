---
title: Criar um gateway de aplicativo com dimensionamento automático, redundância de zona e endereço IP reservado – Azure PowerShell
description: Saiba como criar um gateway de aplicativo com dimensionamento automático, redundância de zona e endereço IP reservado usando o Azure PowerShell.
services: application-gateway
author: amitsriva
ms.service: application-gateway
ms.topic: tutorial
ms.date: 9/26/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: d86ce2e1bac2fb58df8df748381a00eac21e65cb
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48016927"
---
# <a name="tutorial-create-an-autoscaling-zone-redundant-application-gateway-with-a-reserved-virtual-ip-address-using-azure-powershell"></a>Tutorial: criar um gateway de aplicativo com dimensionamento automático, redundância de zona e endereço IP virtual reservado usando o Azure PowerShell

Este tutorial descreve como criar um Gateway de Aplicativo do Azure usando cmdlets do Azure PowerShell e o modelo de implantação do Azure Resource Manager. Este tutorial se concentra nas diferenças entre o novo SKU de Dimensionamento Automático e o SKU Standard existente. Especificamente, nos recursos para dar suporte ao dimensionamento automático, à redundância de zona e a VIPs reservados (IP estático).

Para obter mais informações sobre a redundância de zona e o dimensionamento automático no gateway de aplicativo, confira [Dimensionamento automático e gateway de aplicativo com redundância de zona (versão prévia pública)](application-gateway-autoscaling-zone-redundant.md).

> [!IMPORTANT]
> O dimensionamento automático e o gateway de aplicativo com redundância de zona SKU está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar o parâmetro de configuração de dimensionamento automático
> * Usar o parâmetro de zona
> * Usar um VIP estático
> * Criar o gateway de aplicativo


Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este tutorial requer que você execute o Azure PowerShell localmente. Você precisa ter o módulo do Azure PowerShell versão 6.9.0 ou posterior instalado. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Depois de verificar a versão do PowerShell, execute `Login-AzureRmAccount` para criar uma conexão com o Azure.

## <a name="sign-in-to-your-azure-account"></a>Entre na sua conta do Azure

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -Subscription "<sub name>"
```
## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos em um dos locais disponíveis.

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzureRmResourceGroup -Name $rg -Location $location
```

## <a name="create-a-vnet"></a>Criar uma VNET
Crie uma rede virtual com uma sub-rede dedicada para um gateway de aplicativo com dimensionamento automático. Atualmente, apenas um gateway de aplicativo com dimensionamento automático pode ser implantado em cada sub-rede dedicada.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzureRmVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Criar um IP público reservado

Especifique o método de alocação de PublicIPAddress como **Estático**. O VIP de um gateway de aplicativo com dimensionamento automático só pode ser estático. Não há suporte para IPs dinâmicos. Há suporte somente para o SKU de PublicIpAddress padrão.

```azurepowershell
#Create static public IP
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Recuperar detalhes

Recupere detalhes do grupo de recursos, da sub-rede e do IP em um objeto local para criar os detalhes de configuração do IP do gateway de aplicativo.

```azurepowershell
$resourceGroup = Get-AzureRmResourceGroup -Name $rg
$publicip = Get-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```
## <a name="configure-application-gateway-infrastructure"></a>Configurar a infraestrutura do gateway de aplicativo
Defina as configurações do IP e do IP de front-end, do pool de back-end, de http, do certificado, da porta, do ouvinte e da regra em formato idêntico ao do gateway de aplicativo padrão existente. O novo SKU segue o mesmo modelo de objeto do SKU Standard.

```azurepowershell
$ipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzureRmApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "SSLPort" -Port 443
$fp02 = New-AzureRmApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$securepfxpwd = ConvertTo-SecureString -String "scrap" -AsPlainText -Force
$sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -Password $securepfxpwd `
            -CertificateFile "D:\Networking\ApplicationGateway\scrap.pfx"
$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "SSLListener" `
             -Protocol Https -FrontendIPConfiguration $fip -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp02

$setting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "Rule2" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener02 -BackendAddressPool $pool
```

## <a name="specify-autoscale"></a>Especificar o dimensionamento automático

Agora, você pode especificar a configuração de dimensionamento automático do gateway de aplicativo. Há suporte para dois tipos de configuração de dimensionamento automático:

- **Modo de capacidade fixa**. Nesse modo, o gateway de aplicativo não é dimensionado automaticamente e opera com uma capacidade de unidade de escala fixa.

   ```azurepowershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```
- **Modo de dimensionamento automático**. Neste modo, o gateway de aplicativo é dimensionado automaticamente com base no padrão de tráfego de aplicativo.

   ```azurepowershell
   $autoscaleConfig = New-AzureRmApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicativo

Criar Gateway de Aplicativo e incluir zonas de redundância. 

A configuração de zona tem suporte apenas nas regiões em que as Zonas do Azure estão disponíveis. Nas regiões em que as Zonas do Azure não estão disponíveis, o parâmetro de zona não deve ser usado. Um gateway de aplicativo também pode ser implantado em uma única zona, em duas zonas ou nas três zonas. O PublicIPAddress de um gateway de aplicativo com um única zona deve ser associado à mesma zona. Para gateway de aplicativo com redundância que tem duas ou três zonas, o PublicIPAddress também deve ter redundância de zona e, portanto, nenhuma zona especificada.

```azurepowershell
$appgw = New-AzureRmApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

Use [Get-AzureRmPublicIPAddress](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermpublicipaddress) para obter o endereço IP público do gateway do aplicativo. Copie o endereço IP público ou o nome DNS e cole-o na barra de endereços do seu navegador.

`Get-AzureRmPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP`

## <a name="clean-up-resources"></a>Limpar recursos
Primeiro, explore os recursos que foram criados com o gateway de aplicativo e, quando não forem mais necessários, use o comando `Remove-AzureRmResourceGroup` para remover o grupo de recursos, o gateway de aplicativo e todos os recursos relacionados.

`Remove-AzureRmResourceGroup -Name $rg`

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Usar um VIP estático
> * Configurar o parâmetro de configuração de dimensionamento automático
> * Usar o parâmetro de zona
> * Criar o gateway de aplicativo

> [!div class="nextstepaction"]
> [Criar um gateway de aplicativo com regras de roteamento baseadas em caminhos de URL](./tutorial-url-route-powershell.md)
