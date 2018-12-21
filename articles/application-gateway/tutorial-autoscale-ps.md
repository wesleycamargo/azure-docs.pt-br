---
title: 'Tutorial: Criar um gateway de aplicativo com dimensionamento automático, redundância de zona e endereço IP reservado – Azure PowerShell'
description: Neste tutorial, saiba como criar um gateway de aplicativo com dimensionamento automático, redundância de zona e endereço IP reservado usando o Azure PowerShell.
services: application-gateway
author: amitsriva
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 99fa5d6f0ba74b56a53f2d1af1b99c7e5c2896a7
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323183"
---
# <a name="tutorial-create-an-application-gateway-that-improves-web-application-access"></a>Tutorial: Criar um gateway de aplicativo que melhora o acesso a aplicativos Web

Se você for um administrador de TI preocupado com o aprimoramento do acesso a aplicativos Web, será possível otimizar o gateway de aplicativo para dimensionar com base na demanda do cliente e abranger várias zonas de disponibilidade. Este tutorial ajuda você a configurar os recursos do Gateway de Aplicativo do Azure que fazem: dimensionamento automático, redundância de zona e VIPs reservados (IP estático). Você usará os cmdlets do Azure PowerShell e o modelo de implantação do Azure Resource Manager para solucionar o problema.

> [!IMPORTANT] 
> O dimensionamento automático e o gateway de aplicativo com redundância de zona SKU está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma rede virtual de dimensionamento automático
> * Criar um IP público reservado
> * Configurar a infraestrutura do gateway de aplicativo
> * Especificar o dimensionamento automático
> * Criar o gateway de aplicativo
> * Testar o gateway de aplicativo

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer que você execute o Azure PowerShell localmente. Você precisa ter o módulo do Azure PowerShell versão 6.9.0 ou posterior instalado. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Depois de verificar a versão do PowerShell, execute `Login-AzureRmAccount` para criar uma conexão com o Azure.

## <a name="sign-in-to-azure"></a>Entrar no Azure

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

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

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

## <a name="configure-the-infrastructure"></a>Configurar a infraestrutura

Defina as configurações do IP, do IP de front-end, do pool de back-end, de HTTP, do certificado, da porta, do ouvinte e da regra em formato idêntico ao do gateway de aplicativo padrão existente. O novo SKU segue o mesmo modelo de objeto do SKU Standard.

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

* **Modo de capacidade fixa**. Nesse modo, o gateway de aplicativo não é dimensionado automaticamente e opera com uma capacidade de unidade de escala fixa.

   ```azurepowershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **Modo de dimensionamento automático**. Neste modo, o gateway de aplicativo é dimensionado automaticamente com base no padrão de tráfego de aplicativo.

   ```azurepowershell
   $autoscaleConfig = New-AzureRmApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicativo

Crie o gateway de aplicativo e inclua zonas de redundância e a configuração de dimensionamento automático.

```azurepowershell
$appgw = New-AzureRmApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

Use Get-AzureRmPublicIPAddress para obter o endereço IP público do gateway de aplicativo. Copie o endereço IP público ou o nome DNS e cole-o na barra de endereços do seu navegador.

`Get-AzureRmPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP`

## <a name="clean-up-resources"></a>Limpar recursos

Primeiro, explore os recursos criados com o gateway de aplicativo. Em seguida, quando não for mais necessário, você pode usar o comando `Remove-AzureRmResourceGroup` para remover o grupo de recursos, o gateway de aplicativo e todos os recursos relacionados.

`Remove-AzureRmResourceGroup -Name $rg`

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um gateway de aplicativo com regras de roteamento baseadas em caminhos de URL](./tutorial-url-route-powershell.md)
