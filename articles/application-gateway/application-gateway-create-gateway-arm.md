---
title: Criar e gerenciar um Gateway de Aplicativo do Azure - PowerShell | Microsoft Docs
description: "Esta página fornece instruções para criar, configurar, iniciar e excluir um gateway de aplicativo do Azure usando o Gerenciador de Recursos do Azure"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 866e9b5f-0222-4b6a-a95f-77bc3d31d17b
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 8a0eb841b1a41a14e443b6ce93e6b8fb8985a803
ms.contentlocale: pt-br
ms.lasthandoff: 05/02/2017


---
# <a name="create-start-or-delete-an-application-gateway-by-using-azure-resource-manager"></a>Criar, iniciar ou excluir um gateway de aplicativo usando o Gerenciador de Recursos do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-gateway-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Modelo do Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [CLI do Azure](application-gateway-create-gateway-cli.md)

O Azure Application Gateway é um balanceador de carga de camada 7. Ele fornece o failover e solicitações HTTP de roteamento de desempenho entre diferentes servidores, estejam eles na nuvem ou no local.
O Gateway de Aplicativo fornece muitos recursos do ADC (Controlador de Entrega de Aplicativos), incluindo o balanceamento de carga de HTTP, a afinidade de sessão baseada em cookies, o descarregamento de SSL (Secure Sockets Layer), as sondas de integridade personalizadas, suporte para vários sites e muitos outros.

Para localizar uma lista completa dos recursos com suporte, visite [Visão geral do Gateway de Aplicativo](application-gateway-introduction.md)

Este artigo orienta você pelas etapas para criar, configurar, iniciar e excluir um gateway de aplicativo.

> [!IMPORTANT]
> Antes de trabalhar com os recursos do Azure, é importante entender que, no momento, o Azure apresenta dois modelos de implantação: Gerenciador de Recursos e clássico. Verifique se você entendeu [os modelos e as ferramentas de implantação](../azure-classic-rm.md) antes de trabalhar com qualquer recurso do Azure. Você pode exibir a documentação para ferramentas diferentes clicando nas guias na parte superior deste artigo. Este documento aborda a criação de um gateway de aplicativo usando o Azure Resource Manager. Para usar a versão clássica, vá para [Criar uma implantação clássica do gateway de aplicativo usando o PowerShell](application-gateway-create-gateway.md).

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente dos cmdlets do Azure PowerShell usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente na seção **Windows PowerShell** da [página Downloads](https://azure.microsoft.com/downloads/).
1. Se você tiver uma rede virtual existente, selecione uma sub-rede vazia existente ou crie uma sub-rede na rede virtual existente unicamente para uso pelo gateway de aplicativo. Não é possível implantar o gateway de aplicativo para uma rede virtual diferente dos recursos que você pretende implantar por trás do gateway de aplicativo.
1. Os servidores que você configura para usar o gateway de aplicativo devem existir ou ter seus pontos de extremidade criados na rede virtual ou com um IP/VIP público atribuído.

## <a name="what-is-required-to-create-an-application-gateway"></a>O que é necessário para criar um gateway de aplicativo?

* **Pool de servidores back-end:** a lista de endereços IP, FQDNs ou NICs dos servidores back-end. Se os endereços IP forem usados, eles deverão pertencer à sub-rede da rede virtual ou ser um IP/VIP público.
* **Configurações do pool de servidores back-end:** cada pool tem configurações como porta, protocolo e afinidade baseada em cookie. Essas configurações são vinculadas a um pool e aplicadas a todos os servidores no pool.
* **Porta front-end:** essa porta é a porta pública aberta no gateway de aplicativo. O tráfego atinge essa porta e é redirecionado para um dos servidores back-end.
* **Ouvinte:** o ouvinte tem uma porta front-end, um protocolo (HTTP ou HTTPS, esses valores diferenciam maiúsculas de minúsculas) e o nome do certificado SSL (caso esteja configurando o descarregamento SSL).
* **Regra:** a regra vincula o ouvinte e o pool de servidores back-end e define a qual pool de servidores back-end o tráfego deve ser direcionado ao atingir um ouvinte específico.

## <a name="create-an-application-gateway"></a>Criar um Application Gateway

A diferença entre usar o Azure Classic e o Azure Resource Manager é a ordem em que você cria o gateway de aplicativo e os itens que precisam ser configurados.

Com o Gerenciador de Recursos, todos os itens que compõem um gateway de aplicativo serão configurados individualmente e, em seguida, reunidos para criar o recurso do gateway de aplicativo.

A seguir estão as etapas necessárias para criar um gateway de aplicativo.

## <a name="create-a-resource-group-for-resource-manager"></a>Criar um grupo de recursos para o Gerenciador de Recursos

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
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Etapa 4

Crie um grupo de recursos (ignore esta etapa se você estiver usando um grupo de recursos existente).

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Esse local é usado como o local padrão para os recursos do grupo de recursos em questão. Verifique se todos os comandos para criar um gateway de aplicativo usam o mesmo grupo de recursos.

No exemplo anterior, criamos um grupo de recursos denominado **appgw-RG** e a localização **Oeste dos EUA**.

> [!NOTE]
> Se você precisar configurar uma investigação personalizada para o gateway de aplicativo, visite [Criar um gateway de aplicativo com investigações personalizadas usando o PowerShell](application-gateway-create-probe-ps.md). Confira [investigações personalizadas e monitoramento de integridade](application-gateway-probe-overview.md) para saber mais.

## <a name="create-a-virtual-network-and-a-subnet"></a>Criar uma rede virtual e uma sub-rede

O exemplo a seguir mostra como criar uma rede virtual usando o Gerenciador de Recursos. Este exemplo cria uma VNET para o Gateway de Aplicativo. O Gateway de Aplicativo requer sua própria sub-rede e é por esse motivo que a sub-rede criada para o Gateway de Aplicativo é menor do que o espaço de endereço da rede virtual. O uso de uma sub-rede menor permite outros recursos, incluindo, dentre outras coisas, que servidores Web sejam configurados na mesma rede virtual.

### <a name="step-1"></a>Etapa 1

Atribua o intervalo de endereços 10.0.0.0/24 à variável de sub-rede a ser usada para criar uma rede virtual. Esta etapa cria o objeto de configuração de sub-rede para o Gateway de Aplicativo que é usado no exemplo a seguir.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Etapa 2

Crie uma rede virtual chamada **appgwvnet** no grupo de recursos **appgw-rg** para a região Oeste dos EUA usando o prefixo 10.0.0.0/16 com a sub-rede 10.0.0.0/24. Esta etapa conclui a configuração da rede virtual com uma única sub-rede em que o Gateway de Aplicativo deve residir.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>Etapa 3

Atribuir a variável de sub-rede para as próximas etapas. Essa variável é passada para o cmdlet `New-AzureRMApplicationGateway` em uma etapa futura.

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address"></a>Criar um endereço IP público

Crie um recurso IP público **publicIP01** no grupo de recursos **appgw-rg** para a região Oeste dos EUA. O Gateway de Aplicativo pode usar um endereço IP público, interno ou de ambos os tipos para receber solicitações de balanceamento de carga.  Este exemplo usa apenas um endereço IP público. No exemplo a seguir, nenhum nome DNS é configurado para criar o endereço IP público.  O Gateway de Aplicativo não dá suporte a nomes DNS personalizados em endereços IP públicos.  Se um nome personalizado for exigido para o ponto de extremidade público, um registro CNAME deve ser criado a fim de apontar para o nome DNS gerado automaticamente para o endereço IP público.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

> [!NOTE]
> Um endereço IP é atribuído ao application gateway quando o serviço é iniciado.

## <a name="create-the-application-gateway-configuration-objects"></a>Criar os objetos de configuração do gateway do aplicativo

Todos os itens de configuração devem estar configurados antes de criar o application gateway. As etapas a seguir criam os itens de configuração necessários para um recurso de gateway de aplicativo.

### <a name="step-1"></a>Etapa 1

Crie uma configuração de IP do gateway de aplicativo chamada **gatewayIP01**. Quando o Application Gateway é iniciado, ele escolhe um endereço IP na sub-rede configurada e no tráfego de rede da rota para os endereços IP no pool de IPs de back-end. Tenha em mente que cada instância usa um endereço IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>Etapa 2

Configure o pool de endereços IP de back-end denominado **pool01** com os endereços IP para**pool1**. Esses endereços IP são os endereços IP dos recursos que estão hospedando o aplicativo Web que será protegido pelo gateway de aplicativo. A integridade desses membros do pool de back-end é validada por investigações, sejam básicas ou personalizadas.  O tráfego é encaminhado para eles quando a solicitação chega no gateway de aplicativo. Os pools de back-end podem ser usados por várias regras dentro do gateway de aplicativo, o que significa que um pool de back-end pode ser usado para vários aplicativos Web que residem no mesmo host.

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50
```

Neste exemplo, haverá dois pools de back-end para rotear o tráfego de rede com base no caminho da URL. Um pool recebe o tráfego do caminho de URL "/video", e o outro pool recebe o tráfego do caminho "/image". Substitua os endereços IP anteriores para adicionar seus próprios pontos de extremidade de endereço IP do aplicativo.

### <a name="step-3"></a>Etapa 3

Defina as configurações de **poolsetting01** do gateway de aplicativo para o tráfego de rede com carga balanceada no pool de back-end. Cada pool de back-end pode ter sua própria configuração de pool de back-end.  As configurações HTTP de back-end são usadas pelas regras para rotear o tráfego para os membros do pool de back-end corretos. As configurações HTTP de back-end determinam o protocolo e a porta usados ao enviar tráfego para os membros do pool de back-end. As sessões baseadas em cookies também são determinadas pelas configurações HTTP de back-end.  Se habilitada, a afinidade de sessão baseada em cookies envia o tráfego para o mesmo back-end das solicitações anteriores para cada pacote.

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
```

### <a name="step-4"></a>Etapa 4

Configure a porta front-end para um gateway de aplicativo. O objeto de configuração de porta front-end é usado por um ouvinte para definir a porta cujo tráfego deve ser ouvido pelo Gateway de Aplicativo.

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

### <a name="step-5"></a>Etapa 5

Configure o IP front-end com o ponto de extremidade IP público. O objeto de configuração de IP front-end é usado por um ouvinte para relacionar o endereço IP voltado para fora com o ouvinte.

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
```

### <a name="step-6"></a>Etapa 6

Crie o ouvinte. Esta etapa configura o ouvinte para o endereço IP público, e a porta usada para receber o tráfego de rede. O exemplo a seguir usa a configuração de IP de front-end definida anteriormente, uma configuração de porta front-end e um protocolo (http ou https) e configura o ouvinte. Neste exemplo, o ouvinte ouve o tráfego HTTP na porta 80 no endereço IP público que foi criado anteriormente.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

### <a name="step-7"></a>Etapa 7

Crie a regra de roteamento do balanceador de carga chamada **rule01**, que configura o comportamento do balanceador de carga. As configurações do pool de back-end, o ouvinte e o pool de back-end criados nas etapas anteriores fazem a regra. Com base nos critérios definidos, o tráfego é roteado para o back-end apropriado.

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting01 -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-8"></a>Etapa 8

Configure o número de instâncias e o tamanho do gateway de aplicativo.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

> [!NOTE]
> O valor padrão para **InstanceCount** é 2, com um valor máximo de 10. O valor padrão para **GatewaySize** é Medium. Você pode escolher entre **Standard_Small**, **Standard_Medium** e **Standard_Large**.

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicativo

Crie um gateway de aplicativo com todos os itens de configuração das etapas anteriores. Neste exemplo, o gateway de aplicativo é chamado de **appgwtest**.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

Recupere os detalhes de DNS e VIP do gateway de aplicativo do recurso de IP público anexado ao gateway de aplicativo.

```powershell
Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName appgw-rg  
```

## <a name="delete-the-application-gateway"></a>Excluir o gateway de aplicativo

Para excluir um gateway de aplicativo, siga estas etapas:

### <a name="step-1"></a>Etapa 1

Obtenha o objeto do gateway de aplicativo e associe-o a uma variável `$getgw`.

```powershell
$getgw = Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Etapa 2

Use o `Stop-AzureRmApplicationGateway` para parar o gateway de aplicativo.

```powershell
Stop-AzureRmApplicationGateway -ApplicationGateway $getgw
```

Depois que o gateway de aplicativo estiver em um estado parado, use o cmdlet `Remove-AzureRmApplicationGateway` para remover o serviço.

```powershell
Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force
```

> [!NOTE]
> A opção **-force** pode ser usada para suprimir a mensagem de confirmação da remoção.

Para verificar se o serviço foi removido, você pode usar o cmdlet `Get-AzureRmApplicationGateway`. Essa etapa não é necessária.

```powershell
Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

## <a name="get-application-gateway-dns-name"></a>Obter um nome DNS de Application Gateway

Depois de criar o gateway, a próxima etapa será configurar o front-end para comunicação. Ao usar um IP público, o gateway de aplicativo requer um nome DNS atribuído dinamicamente, o que não é amigável. Para garantir que os usuários finais possam alcançar o gateway de aplicativo, um registro CNAME pode ser usado para apontar para o ponto de extremidade público do gateway de aplicativo. [Configurando um nome de domínio personalizado no Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Para encontrar o nome DNS criado dinamicamente, recupere detalhes do Gateway de Aplicativo e seu nome DNS/IP associado usando o elemento PublicIPAddress anexado ao Gateway de Aplicativo. O nome DNS do Gateway de Aplicativo deve ser usado para criar um registro CNAME que aponta os dois aplicativos Web para esse nome DNS. O uso de registros A não é recomendável, pois o VIP pode mudar na reinicialização do gateway de aplicativo.

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

## <a name="delete-all-resources"></a>Excluir todos os recursos

Para excluir todos os recursos criados neste artigo, conclua as seguintes etapas:

```powershell
Remove-AzureRmResourceGroup -Name appgw-RG
```

## <a name="next-steps"></a>Próximas etapas

Se desejar configurar o descarregamento SSL, visite [Configurar um Gateway de Aplicativo para descarregamento SSL](application-gateway-ssl.md).

Para configurar um Gateway de Aplicativo para usar com um balanceador de carga interno, visite [Criar um Gateway de Aplicativo com um ILB (balanceador de carga interno)](application-gateway-ilb.md).

Se deseja saber mais sobre as opções de balanceamento de carga no geral, visite:

* [Balanceador de carga do Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)


