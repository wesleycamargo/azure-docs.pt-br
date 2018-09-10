---
title: Criar um gateway de rede virtual com redundância de zona em Zonas de Disponibilidade do Azure – Versão prévia | Microsoft Docs
description: Implante Gateway de VPN e gateways ExpressRoute em Zonas de Disponibilidade – Versão prévia.
services: vpn-gateway
documentationcenter: na
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/09/2018
ms.author: cherylmc
ms.openlocfilehash: fa349555a5effd41ca519cbd5a29005203d79543
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952548"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones---preview"></a>Criar um gateway de rede virtual com redundância de zona em Zonas de Disponibilidade do Azure – Versão prévia

Você pode implantar gateways de VPN e ExpressRoute em [Zonas de Disponibilidade do Azure](../availability-zones/az-overview.md). Isso traz resiliência, escalabilidade e maior disponibilidade para gateways de rede virtual. A implantação de gateways em Zonas de Disponibilidade do Azure separa de forma física e lógica os gateways em uma região, enquanto protege a conectividade de rede local com o Azure de falhas no nível na zona.

Os gateways em zona e com redundância de zona têm melhorias de desempenho fundamentais em relação aos gateways de rede virtual regulares. Além disso, a criação de um gateway de rede virtual em zona ou com redundância de zona é mais rápida do que a criação de outros gateways. Em vez de levar 45 minutos, a criação leva aproximadamente 15 minutos para um gateway ExpressRoute e 19 minutos para um Gateway de VPN.

### <a name="zrgw"></a>Gateways com redundância de zona

Para implantar automaticamente seus gateways de rede virtual entre zonas de disponibilidade, você pode usar gateways de rede virtual com redundância de zona. Com os gateways de redundância de zona, você pode aproveitar 99,99% do SLA de tempo de atividade em GA para acessar seus serviços escalonáveis e de missão crítica no Azure.

<br>
<br>

![gráfico de gateways com redundância de zona](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>Gateways em zona

Para implantar gateways em uma zona específica, você deve usar gateways em zona. Quando você implanta um gateway em zona, ambas as instâncias do gateway são implantadas na mesma Zona de Disponibilidade.

<br>
<br>

![gráfico de gateways em zona](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>SKUs do Gateway

Os gateways em zona e com redundância de zona devem usar as novas SKUs de gateway. Depois que você [se registrar na versão prévia](#enroll), verá as novas SKUs de gateway de rede virtual em todas as regiões do Azure AZ. Essas SKUs são semelhantes às SKUs correspondentes para o Gateway de VPN e ExpressRoute, exceto que eles são específicos para gateways com redundância de zona e em zona.

As novas SKUs de gateway são:

### <a name="vpn-gateway"></a>Gateway de VPN

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>SKUs de IP público

Os gateways com redundância de zona e os gateways em zona contam com o SKU *Standard* de recurso de IP público do Azure. A configuração do recurso de IP público do Azure determina se o gateway implantado é do tipo em zona ou com redundância de zona. Se você criar um recurso de IP público com uma SKU *Básica*, o gateway não terá redundância de zona e os recursos dele serão regionais.

### <a name="pipzrg"></a>Gateways com redundância de zona

Quando você cria um endereço IP público usando a SKU de IP público **Standard** sem especificar uma zona, o comportamento é diferente dependendo se o gateway é um Gateway de VPN ou ExpressRoute. 

* Para um Gateway de VPN, as duas instâncias do gateway serão implantadas em quaisquer duas zonas entre essas três para fornecer a redundância de zona. 
* Para um gateway ExpressRoute, uma vez que pode haver mais de duas instâncias, o gateway poderá ser distribuído entre todas as três zonas.

### <a name="pipzg"></a>Gateways em zona

Quando você cria um endereço IP público usando a SKU de IP público **Standard** e especifica a Zona (1, 2 ou 3), todas as instâncias de gateway são implantadas na mesma zona.

### <a name="piprg"></a>Gateways regionais

Quando você cria um endereço IP público usando a SKU de IP público **Básica**, o gateway é implantado como um gateway regional e não tem nenhuma redundância de zona integrada no gateway.

## <a name="before-you-begin"></a>Antes de começar

Você pode usar o PowerShell instalado localmente em seu computador ou o Azure Cloud Shell. Se você optar por instalar e usar o PowerShell localmente, esse recurso exigirá a versão mais recente do módulo do PowerShell.

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>Para usar o PowerShell localmente

Se você estiver usando o PowerShell localmente no seu computador em vez de usar o Cloud Shell, será necessário instalar o módulo do PowerShell 6.1.1 ou superior. Para verificar a versão do PowerShell que você instalou, use o comando a seguir:

```azurepowershell
Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
```

Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="enroll"></a>1. Registrar-se na versão prévia

Antes de poder configurar um gateway com redundância de zona ou em zona, primeiro será necessário registrar sua assinatura na versão prévia. Depois que sua assinatura tiver sido configurada, você começará a ver as novas SKUs de gateway em todas as regiões do Azure AZ. 

Certifique-se de ter entrado na sua conta do Azure e de estar usando a assinatura que deseja incluir na lista de permissões para essa versão prévia. Use o seguinte exemplo para se registrar:

```azurepowershell-interactive
Register-AzureRmProviderFeature -FeatureName AllowVMSSVirtualNetworkGateway -ProviderNamespace Microsoft.Network
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

Use o comando a seguir para verificar se o recurso 'AllowVMSSVirtualNetworkGateway' está registrado na sua assinatura:

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network
```

O resultado será semelhante a este exemplo:

![provisionado](./media/create-zone-redundant-vnet-gateway/verifypreview.png)

## <a name="variables"></a>2. Declare as variáveis

Os valores usados para as etapas de exemplo estão listados abaixo. Além disso, alguns dos exemplos usam variáveis declaradas nas etapas. Se você estiver usando estas etapas em seu próprio ambiente, certifique-se de substituir esses valores pelos seus. Ao especificar a localização, verifique se a região especificada é compatível. Consulte mais informações em [Perguntas Frequentes](#faq).

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="configure"></a>3. Criar a rede virtual

Crie um grupos de recursos.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

Crie uma rede virtual.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzureRmVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="gwsub"></a>4. Adicionar a sub-rede de gateway

A sub-rede de gateway contém os endereços IP reservados que usam os serviços de gateway de rede virtual. Use os exemplos a seguir para adicionar e configurar uma sub-rede de gateway:

Adicione a sub-rede de gateway.

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Defina a configuração de sub-rede do gateway para a rede virtual.

```azurepowershell-interactive
$getvnet | Set-AzureRmVirtualNetwork
```
## <a name="publicip"></a>5. Solicitar um endereço IP público
 
Nesta etapa, escolha as instruções que se aplicam ao gateway que você deseja criar. A seleção de zonas para implantar os gateways depende das zonas especificadas para o endereço IP público.

### <a name="ipzoneredundant"></a>Para gateways com redundância de zona

Solicite um endereço IP público com a SKU PublicIpaddress **Standard** e não especifique nenhuma zona. Nesse caso, o endereço IP público Standard criado será um IP público com redundância de zona.   

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>Para gateways em zona

Solicite um endereço IP público com a SKU PublicIpaddress **Standard**. Especifique a zona (1, 2 ou 3). Todas as instâncias de gateway serão implantadas nesta zona.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>Para gateways regionais

Solicite um endereço IP público com a SKU PublicIpaddress **Básica**. Nesse caso, o gateway é implantado como um gateway regional e não tem nenhuma redundância de zona integrada no gateway. As instâncias de gateway são criadas em quaisquer as zonas, respectivamente.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>6. Criar a configuração de IP

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>7. Criar o gateway

Crie o gateway de rede virtual.

### <a name="for-expressroute"></a>Para ExpressRoute

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>Para Gateway de VPN

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="feedback"></a>Como fornecer comentários

Agradecemos por seus comentários. Envie um email para aznetworkgateways@microsoft.com para relatar problemas ou fornecer comentários (positivos ou negativos) para os gateways VPN e ExpressRoute com redundância de zona e por zona. Inclua nome da sua empresa entre "[ ]" na linha do assunto. Também inclua sua ID de assinatura se estiver relatando um problema.

## <a name="faq"></a>Perguntas frequentes

### <a name="how-do-i-sign-up-for-the-preview"></a>Como fazer para me inscrever para a versão prévia?

Você pode [se registrar](#enroll) usando os comandos do PowerShell neste artigo.

### <a name="what-will-change-when-i-enroll"></a>O que mudará quando eu me registrar?

Da sua perspectiva, durante a versão prévia, você poderá implantar seus gateways com redundância de zona. Isso significa que todas as instâncias dos gateways serão implantadas em Zonas de Disponibilidade do Azure e cada Zona de Disponibilidade será um domínio de atualização e falha diferente. Isso torna seus gateways mais confiáveis, disponíveis e resilientes a falhas de zona.

### <a name="can-i-use-the-azure-portal"></a>Posso usar o portal do Azure?

Sim. Você pode usar o portal do Azure para a Versão Prévia. No entanto, você ainda precisará se registrar usando o PowerShell ou não poderá usar o portal durante a Versão Prévia.

### <a name="what-regions-are-available-for-the-preview"></a>Quais regiões estão disponíveis para a versão prévia?

Os gateways com redundância de zona e em zona estão disponíveis em regiões pública do Azure/produção.

### <a name="will-i-be-billed-for-participating-in-this-preview"></a>Serei cobrado por participar dessa versão prévia?

Você não será cobrado por seus gateways durante a versão prévia. No entanto, não há um SLA vinculado à implantação. Estamos muito interessados em ouvir seus comentários.

> [!NOTE]
> Para gateways ExpressRoute, o gateway não é cobrado. No entanto, o circuito em si (não o gateway) será cobrado.

### <a name="what-regions-are-available-for-me-to-try-this-in"></a>Quais regiões estão disponíveis para eu testar isso?

A versão prévia pública está disponível nas regiões EUA Central e França Central (regiões do Azure que tem as Zonas de Disponibilidade disponíveis de forma geral). No futuro, disponibilizaremos os Gateways com Redundância de zona em outras Regiões Públicas do Azure.

### <a name="can-i-change-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Posso alterar meus gateways de rede virtual existentes para gateways com redundância de zona ou em zona?

No momento, a migração de gateways de rede virtual existentes para gateways com redundância de zona ou em zona não é compatível. No entanto, você pode excluir o gateway existente e recriar um gateway com redundância de zona ou em zona.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Posso implantar gateways de VPN e ExpressRoute na mesma rede virtual?

A coexistência de gateways de VPN e ExpressRoute na mesma rede virtual é compatível durante a versão prévia pública. No entanto, lembre-se dos requisitos e limitações a seguir:

* Reserve um intervalo de endereços IP /27 para a sub-rede de gateway.
* Os gateways ExpressRoute com redundância de zona/em zona podem coexistir apenas com gateways de VPN com redundância de zona/em zona.
* Implante o gateway ExpressRoute com redundância de zona/em zona antes de implantar o Gateway de VPN com redundância de zona/em zona.
* Um gateway ExpressRoute com redundância de zona/em zona pode ser conectado a, no máximo, quatro circuitos.

## <a name="next-steps"></a>Próximas etapas

Agradecemos por seus comentários. Envie um email para aznetworkgateways@microsoft.com para relatar problemas ou fornecer comentários (positivos ou negativos) para os gateways VPN e ExpressRoute com redundância de zona e por zona. Inclua nome da sua empresa entre "[ ]" na linha do assunto. Também inclua sua ID de assinatura se estiver relatando um problema.
