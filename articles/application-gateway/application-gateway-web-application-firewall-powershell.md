---
title: Configurar o firewall do aplicativo Web - Gateway de Aplicativo do Azure | Microsoft Docs
description: "Este artigo oferece orientação sobre como começar a usar o firewall do aplicativo Web em um gateway de aplicativo novo ou existente."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 52b7728c3fc702e37f5c5fe3d6544117a11464e8
ms.lasthandoff: 03/30/2017


---
# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway"></a>Configurar o firewall do aplicativo Web em um Gateway de Aplicativo novo ou existente

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-web-application-firewall-powershell.md)

O firewall de aplicativo Web (WAF) no Gateway de Aplicativo do Azure protege os aplicativos Web contra ataques comuns baseados na Web, como injeção de SQL, ataques de scripts entre sites e sequestros de sessão.

O Azure Application Gateway é um balanceador de carga de camada 7. Ele fornece o failover e solicitações HTTP de roteamento de desempenho entre diferentes servidores, estejam eles na nuvem ou no local. O aplicativo fornece muitos recursos do Controlador de Entrega de Aplicativos (ADC), incluindo o balanceamento de carga de HTTP, a afinidade de sessão baseada em cookies, o descarregamento de Secure Sockets Layer (SSL), as sondas de integridade personalizadas, suporte para vários sites e muitos outros. Para localizar uma lista completa dos recursos com suporte, visite Visão geral do Gateway de Aplicativo

O artigo a seguir mostra como [adicionar um firewall do aplicativo Web a um gateway de aplicativo existente](#add-web-application-firewall-to-an-existing-application-gateway) e como [criar um gateway de aplicativo que use o firewall do aplicativo Web](#create-an-application-gateway-with-web-application-firewall).

![imagem de cenário][scenario]

## <a name="waf-configuration-differences"></a>Diferenças de configuração do WAF

Se você tiver lido [Criar um Gateway de Aplicativo com o PowerShell](application-gateway-create-gateway-arm.md), compreende as configurações de SKU a serem definidas durante a criação de um gateway de aplicativo. O WAF fornece configurações adicionais para definir ao configurar a SKU em um gateway de aplicativo. Não há nenhuma alteração adicional feita no gateway de aplicativo em si.

**SKU** - um gateway de aplicativo normal sem WAF dá suporte aos tamanhos **Standard\_Small**, **Standard\_Medium** e **Standard\_Large**. Com a introdução do WAF, há duas SKUs adicionais, **WAF\_Medium** e **WAF\_Large**. Não há suporte para WAF em gateways de aplicativo pequenos.

**Camada** - os valores disponíveis são **Standard** ou **WAF**. Ao usar o firewall do aplicativo Web, **WAF** deve ser escolhido.

**Modo** - essa configuração é o modo de WAF. Os valores permitidos são **detecção** e **prevenção**. Quando WAF estiver configurado no modo de detecção, todas as ameaças são armazenadas em um arquivo de log. No modo de prevenção, os eventos ainda estão conectados, mas o invasor recebe uma resposta 403 não autorizado do gateway de aplicativo.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Adicionar o firewall do aplicativo Web a um gateway de aplicativo existente

Use a versão mais recente do Azure PowerShell. Há mais informações disponíveis em [Como usar o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Etapa 1

Faça logon na sua Conta do Azure.

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>Etapa 2

Selecione a assinatura a ser usada nesse cenário.

```powershell
Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
```

### <a name="step-3"></a>Etapa 3

Recupere o gateway ao qual você está adicionando o firewall do aplicativo Web.

```powershell
$gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
```

### <a name="step-4"></a>Etapa 4

Configure a sku do firewall do aplicativo Web. Os tamanhos disponíveis são **WAF\_Large** e **WAF\_Medium**. Quando o firewall do aplicativo Web é usado, a camada deve ser **WAF**. A capacidade deve ser confirmada ao definir o SKU.

```powershell
$gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
```

### <a name="step-5"></a>Etapa 5

Defina as configurações de WAF, conforme definido no exemplo a seguir:

Para a configuração **WafMode** , os valores disponíveis são Prevenção e Detecção.

```powershell
$gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
```

### <a name="step-6"></a>Etapa 6

Atualize o gateway de aplicativo com as configurações definidas na etapa anterior.

```powershell
Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

Este comando atualiza o gateway de aplicativo com o firewall do aplicativo Web. É recomendável exibir os [Diagnósticos do Gateway de Aplicativo](application-gateway-diagnostics.md) para entender como exibir os logs do seu gateway de aplicativo. Devido à natureza de segurança do WAF, será necessário examinar regularmente os logs para compreender a postura de segurança de seus aplicativos Web.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Criar um gateway de aplicativo com o firewall do aplicativo Web

As etapas a seguir guiarão você do início ao fim do processo para criar um Gateway de Aplicativo com o firewall do aplicativo Web.

Use a versão mais recente do Azure PowerShell. Há mais informações disponíveis em [Como usar o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Etapa 1

Fazer logon no Azure

```powershell
Login-AzureRmAccount
```

Você deve se autenticar com suas credenciais.

### <a name="step-2"></a>Etapa 2

Verificar as assinaturas da conta.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>Etapa 3

Escolha quais das suas assinaturas do Azure deseja usar.

```powershell
Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
```

### <a name="step-4"></a>Etapa 4

Crie um grupo de recursos (ignore esta etapa se você estiver usando um grupo de recursos existente).

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Esse local é usado como o local padrão para os recursos do grupo de recursos em questão. Verifique se todos os comandos para criar um gateway de aplicativo usam o mesmo grupo de recursos.

No exemplo anterior, criamos um grupo de recursos denominado "appgw-RG" e o local "Oeste dos EUA".

> [!NOTE]
> Se você precisar configurar uma investigação personalizada para o gateway de aplicativo, veja [Criar um gateway de aplicativo com investigações personalizadas usando o PowerShell](application-gateway-create-probe-ps.md). Confira [investigações personalizadas e monitoramento de integridade](application-gateway-probe-overview.md) para saber mais.

### <a name="step-5"></a>Etapa 5

Atribua um intervalo de endereços para que a sub-rede seja usada para o próprio Gateway de Aplicativo.

```powershell
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
```

> [!NOTE]
> Uma sub-rede para um aplicativo deve ter um mínimo de 28 bits de máscara. Esse valor deixa 10 endereços disponíveis na sub-rede para instâncias do Gateway de Aplicativo. Com uma sub-rede menor, talvez você não consiga adicionar mais instâncias do seu gateway de aplicativo no futuro.


### <a name="step-6"></a>Etapa 6

Atribua um intervalo de endereços a ser usado para o pool de endereços do Back-end.

```powershell
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
```

### <a name="step-7"></a>Etapa 7

Crie uma rede virtual com as sub-redes anteriores no grupo de recursos criado na etapa: [Criar o grupo de recursos](#create-the-resource-group)

```powershell
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="step-8"></a>Etapa 8

Recupere o recurso de rede virtual e os recursos de sub-rede a serem usados nestas etapas:

```powershell
$vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
```

### <a name="step-9"></a>Etapa 9

Crie um recurso IP público a ser usado para o gateway de aplicativo. Esse endereço IP público é usado em uma das seguintes etapas:

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> O Gateway de Aplicativo não dá suporte ao uso de um endereço IP público criado com um rótulo de domínio definido. Há suporte para apenas para um endereço IP público com um rótulo de domínio criado dinamicamente. Se você precisar de um nome amigável de dns para o gateway de aplicativo, é recomendável usar um registro cname como alias.


### <a name="step-10"></a>Etapa 10

Você deve configurar todos os itens de configuração antes de criar o gateway de aplicativo. As etapas a seguir criam os itens de configuração necessários para um recurso de gateway de aplicativo.

Crie uma configuração de IP do gateway de aplicativo para configurar qual sub-rede será usada pelo Gateway de Aplicativo. Quando o Application Gateway é iniciado, ele escolhe um endereço IP na sub-rede configurada e encaminha o tráfego de rede para os endereços IP no pool de IPs de back-end. Tenha em mente que cada instância usa um endereço IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
```

### <a name="step-11"></a>Etapa 11

Configure o pool de endereços IP do back-end com os endereços IP dos servidores Web de back-end. Esses endereços IP são os que receberão o tráfego de rede proveniente do ponto de extremidade do IP de front-end. Você substitui os endereços IP a seguir para adicionar seus próprios pontos de extremidade de endereço IP do aplicativo.

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
```

### <a name="step-12"></a>Etapa 12

Carregue o certificado a ser usado nos recursos do pool de back-end habilitado para ssl.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>
```

### <a name="step-13"></a>Etapa 13

Defina as configurações de http de back-end do gateway de aplicativo. Atribua o certificado carregado na etapa anterior às configurações de http.

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
```

### <a name="step-14"></a>Etapa 14

Configure a porta IP front-end para o ponto de extremidade IP público. Essa porta é a porta à qual os usuários finais se conectam.

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
```

### <a name="step-15"></a>Etapa 15

Crie uma configuração de IP de front-end para mapear um endereço ip público ou privado para o front-end do gateway de aplicativo. A etapa a seguir associa o endereço IP público da etapa anterior à configuração de IP do front-end.

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
```

### <a name="step-16"></a>Etapa 16

Configure o certificado para o gateway de aplicativo. Esse certificado é usado para descriptografar e criptografar novamente o tráfego no gateway de aplicativo.

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-17"></a>Etapa 17

Crie o ouvinte HTTP para o gateway de aplicativo. Atribua a configuração do ip, a porta e o certificado ssl do front-end a ser usado.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert
```

### <a name="step-18"></a>Etapa 18

Crie uma regra de roteamento do balanceador de carga que configure o comportamento do balanceador de carga. Neste exemplo, é criada uma regra básica de round robin.

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-19"></a>Etapa 19

Configure o tamanho da instância do gateway de aplicativo.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2
```

> [!NOTE]
> Você pode escolher entre **WAF\_Medium** e **WAF\_Large**, a camada ao usar WAF sempre será **WAF**. A capacidade é qualquer número entre 1 e 10.

### <a name="step-20"></a>Etapa 20

Configure o modo de WAF, os valores aceitáveis são **Prevenção** e **Detecção**.

```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

### <a name="step-21"></a>Etapa 21

Crie um gateway de aplicativo com todos os itens de configuração das etapas anteriores. Neste exemplo, o gateway de aplicativo é chamado de "appgwtest".

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert
```

> [!NOTE]
> Os gateways de aplicativo criados com a configuração básica do firewall do aplicativo Web são configurados com o CRS 3.0 para proteções.

## <a name="get-application-gateway-dns-name"></a>Obter um nome DNS de Application Gateway

Depois de criar o gateway, a próxima etapa será configurar o front-end para comunicação. Ao usar um IP público, o gateway de aplicativo requer um nome DNS atribuído dinamicamente, o que não é amigável. Para garantir que os usuários finais possam alcançar o gateway de aplicativo, um registro CNAME pode ser usado para apontar para o ponto de extremidade público do gateway de aplicativo. [Configurando um nome de domínio personalizado no Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Para isso, recupere detalhes do Gateway de Aplicativo e seu nome DNS/IP associado usando o elemento PublicIPAddress anexado ao Gateway de Aplicativo. O nome DNS do Gateway de Aplicativo deve ser usado para criar um registro CNAME que aponta os dois aplicativos Web para esse nome DNS. O uso de registros A não é recomendável, pois o VIP pode mudar na reinicialização do Application Gateway.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Próximas etapas

Saiba como configurar o log de diagnóstico para registrar os eventos detectados ou impedidos pelo firewall do aplicativo Web ao visitar o [Diagnóstico do Gateway de Aplicativo](application-gateway-diagnostics.md)

[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png

