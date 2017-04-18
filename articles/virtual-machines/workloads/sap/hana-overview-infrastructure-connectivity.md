---
title: "Infraestrutura e conectividade para o SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs"
description: "Configure a infraestrutura de conectividade necessária para usar SAP HANA no Azure (Instâncias Grandes)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 0c87a162eb478aecf207738fd6c9515aa515a847
ms.lasthandoff: 04/03/2017


---
# <a name="sap-hana-large-instances-infrastructure-and-connectivity-on-azure"></a>Infraestrutura e conectividade para o SAP HANA (instâncias grandes) no Azure 

Após a compra do SAP HANA no Azure (Instâncias Grandes) ser finalizada entre você e a equipe de conta empresarial da Microsoft, são necessárias as seguintes informações:

- Nome do cliente
- Informações de contatos comerciais (incluindo endereço de email e número de telefone)
- Informações de contatos técnicos (incluindo endereço de email e número de telefone)
- Informações de contatos da rede técnica (incluindo endereço de email e número de telefone)
- Região de implantação do Azure (Oeste dos EUA ou Leste dos EUA a partir de setembro de 2016)
- Confirmar SAP HANA no Azure (Instâncias Grandes) SKU (configuração)
- Para todas as regiões do Azure que estão sendo implantadas em:
  - Um intervalo de endereços IP /29 para conexões P2P
  - Um bloco CIDR (usado para o pool de NAT de instâncias grandes HANA; /24 recomendado)
- Para cada rede virtual do Azure se conectar a instâncias grandes HANA, independente da região do Azure:
  - Um ou mais intervalos de endereços IP /28s ou /27 (para a sub-rede de gateway de rede virtual do cliente)
  - Um ou mais blocos CIDR (para sub-rede de locatário de rede virtual do cliente; /24 recomendado)
- Dados para cada sistema de Instâncias Grandes HANA:
  - Nome de host desejado
  - Endereço IP desejado do pool de NAT
- Número de assinaturas do Azure para a assinatura do Azure no qual o SAP HANA nas instâncias grandes HANA do Azure serão conectadas diretamente
- Nome do SID SAP HANA para a instância SAP HANA (necessária para criar os volumes de disco necessários relacionados ao SAP HANA)

Depois de fornecer as informações, a Microsoft provisiona o SAP HANA no Azure (Instâncias Grandes).

As informações de configuração de rede, são depois fornecidas a você para:

- Conectar suas Redes Virtuais do Azure ao circuito de ExpressRoute que conecta redes virtuais do Azure a instâncias grandes HANA
  - Para Azure Resource Manager:
     - Chaves de autorização
     - PeerID de ExpressRoute
- Acessando instâncias grandes HANA com o circuito de ExpressRoute estabelecido e a Rede Virtual do Azure

## <a name="creating-an-azure-vnet"></a>Como criar uma Rede Virtual do Azure

Esta Rede Virtual do Azure deve ser criada usando o modelo de implantação do Azure Resource Manager O antigo modelo de implantação do Azure, conhecido como ASM, não tem suporte nesta solução.

A rede virtual do Azure que foi criada deve ter pelo menos uma sub-rede de locatário e uma sub-rede de gateway. A eles devem ser atribuídos os intervalos de endereços IP conforme especificado e enviados à Microsoft.

> [!IMPORTANT] 
> Apenas blocos de endereço de gateway e de locatário devem ser atribuídos à rede virtual na assinatura do Azure. Blocos de endereço de pool de NAT e P2P devem ser separados dos espaços de endereço de rede virtual e sub-rede que existem fora da assinatura do Azure.

Várias sub-redes de locatário podem ser usadas (mesmo utilizando intervalos de endereços não contíguos), mas como mencionado anteriormente, esses intervalos de endereços devem ser enviados à Microsoft com antecedência.

Você pode usar qualquer padrão de nomenclatura que desejar para essas sub-redes de locatário. No entanto, **sempre deve haver uma e apenas uma sub-rede de gateway para cada rede virtual** que se conecta ao SAP HANA no circuito de ExpressRoute do Azure (instâncias grandes), e **esta sub-rede de gateway deve sempre ser denominada &quot;GatewaySubnet&quot;** para garantir a colocação correta do gateway de ExpressRoute.

> [!WARNING] 
> É importante que a sub-rede de gateway seja sempre denominada &quot;GatewaySubnet&quot;.

A rede virtual pode ser criada usando o Portal do Azure, PowerShell, o modelo do Azure ou CLI do Azure (confira [Criar uma rede virtual usando o portal do Azure](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)).

## <a name="creating-a-gateway-subnet"></a>Como criar uma sub-rede de gateway

Depois de criar a rede virtual do Azure, um gateway de ExpressRoute deve ser criado na rede virtual para vincular a rede virtual ao circuito de ExpressRoute que se conecta ao locatário do cliente no carimbo de instância grande.

> [!NOTE] 
> Esta etapa pode levar até 30 minutos para ser concluída, pois o novo gateway é criado na assinatura do Azure designada e depois conectado à rede virtual do Azure especificada.

Se já existir um gateway, verifique se ele é um gateway de ExpressRoute ou não. Caso contrário, o gateway deve ser excluído e recriado como um gateway de ExpressRoute. Se um gateway de ExpressRoute já estiver estabelecido, uma vez que a rede virtual do Azure já está conectada ao circuito de ExpressRoute para conectividade local, vá para a seção Vinculação de redes virtuais abaixo.

- Use o (novo) [Portal do Azure](https://portal.azure.com/) ou o PowerShell para criar um gateway de VPN de ExpressRoute conectado à sua rede virtual.
  - Se você usar o Portal do Azure, adicione um novo **Gateway de rede Virtual** e, em seguida, selecione **ExpressRoute** como o tipo de gateway.
  - Se você escolher o PowerShell, primeiro baixe e use a versão mais recente do [SDK do Azure PowerShell](https://azure.microsoft.com/downloads/) para garantir uma experiência ideal. Os comandos a seguir criam um gateway de ExpressRoute. O texto é precedido por um _$_ que são variáveis definidas pelo usuário que precisam ser atualizadas com as informações específicas.

```
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

Neste exemplo, a SKU de gateway HighPerformance foi usada. As opções são HighPerformance ou UltraPerformance — as únicas SKUs de gateway que são suportadas para SAP HANA no Azure (Instâncias Grandes).

## <a name="linking-vnets"></a>Como vincular redes virtuais

Agora que a rede virtual do Azure tem um gateway de ExpressRoute, você usa as informações de autorização fornecidas pela Microsoft para conectar o gateway de ExpressRoute para o SAP HANA no circuito de ExpressRoute do Azure (Instâncias Grandes) criado para essa conectividade. Isso só pode ser executado usando o PowerShell (ele não é suportado atualmente por meio do Portal do Azure).

- Faça o seguinte para cada gateway de rede virtual usando um AuthGUID diferente para cada conexão. As duas primeiras entradas exibidas abaixo fazem parte das informações fornecidas pela Microsoft. Além disso, o AuthGUID é específico para cada rede virtual e seu gateway.

```
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01-5Gb"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Talvez seja necessário executar essa etapa mais de uma vez, se você quiser conectar o gateway para vários circuitos de ExpressRoute diferentes que estão associados à sua assinatura.

## <a name="adding-more-ip-addresses-or-subnets"></a>Como adicionar mais endereços IP ou sub-redes

Use o Portal do Azure, PowerShell ou CLI ao adicionar mais endereços IP ou sub-redes.

Se você ainda não declarou o intervalo de espaço de endereço IP adicional com o SAP HANA no Gerenciamento de Serviços do Azure, abra uma solicitação de suporte do Azure para adicioná-la. Após receber a confirmação, realize as próximas etapas.

Para criar uma sub-rede adicional no portal do Azure, confira o artigo [Criar uma rede virtual usando o portal do Azure](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e para criar a partir do PowerShell, confira [Criar uma rede virtual usando o PowerShell](../../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="adding-vnets"></a>Como adicionar redes virtuais

Depois de conectar inicialmente uma ou mais redes virtuais do Azure, talvez você queira adicionar mais que acessam SAP HANA no Azure (Instâncias Grandes). Primeiro, envie uma solicitação de suporte do Azure. Nessa solicitação inclua as informações específicas identificando a implantação específica do Azure e os intervalos de espaço de endereço IP para as sub-redes de locatário e as sub-redes de gateway das redes virtuais adicionais do Azure. O SAP HANA no Gerenciamento de Serviços do Azure fornece as informações necessárias que você precisa para conectar as redes virtuais adicionais e a ExpressRoute.

Etapas para adicionar uma nova rede virtual do Azure:

1. Conclua a primeira etapa no processo de integração e confira a seção _Criar rede virtual do Azure_ acima.
2. Conclua a segunda etapa no processo de integração e confira a seção _Criar sub-rede de gateway_ acima.
3. Abra uma solicitação de suporte do Azure com informações sobre a nova rede virtual e solicite uma nova Chave de Autorização para conectar suas redes virtuais adicionais ao circuito de ExpressRoute de instância grande HANA.
4. Quando notificado de que a autorização foi concluída, use as informações de autorização fornecidas pela Microsoft para concluir a terceira etapa no processo de integração e confira a seção _Vinculando redes virtuais_ acima.

## <a name="increasing-expressroute-circuit-bandwidth"></a>Como aumentar a largura de banda do circuito de ExpressRoute

Consulte com o SAP HANA no Gerenciamento de Serviços do Azure. Se você for aconselhado a aumentar a largura de banda do SAP HANA no circuito de ExpressRoute do Azure (Instâncias Grandes), crie uma solicitação de suporte do Azure. (Você pode solicitar um aumento para uma largura de banda do circuito único até um máximo de 10 Gbps). Em seguida você receberá uma notificação após a operação ser concluída. Nenhuma ação adicional é necessária para habilitar essa velocidade superior no Azure.

## <a name="adding-an-additional-expressroute-circuit"></a>Como adicionar um circuito de ExpressRoute adicional

Consulte com o SAP HANA no Gerenciamento de Serviços do Azure, se você estiver ciente de que um circuito de ExpressRoute adicional é necessário. Crie uma solicitação de suporte do Azure para criar um novo circuito de ExpressRoute (e para obter informações de autorização para se conectar a ela). O espaço de endereço que será usado nas redes virtuais deve ser definido antes de fazer a solicitação, para que o SAP HANA, no Gerenciamento de Serviços do Azure, forneça autorização.

Depois que o novo circuito for criado e o SAP HANA na configuração de Gerenciamento de Serviços do Azure estiver concluído, você receberá uma notificação com as informações que você precisa para continuar. Siga as etapas fornecidas acima para criar e conectar-se à nova rede virtual para este circuito adicional. Você não poderá se conectar às redes virtuais do Azure para este circuito adicional se eles já estiverem conectados a outro circuito de ExpressRoute.

## <a name="deleting-a-subnet"></a>Como excluir uma sub-rede

Para remover uma sub-rede de rede virtual, o Portal do Azure, o PowerShell ou a CLI podem ser usados. Se um espaço de endereço for removido, o SAP HANA no Gerenciamento de Serviços do Azure deve ser notificado sobre a alteração no espaço de endereço para removê-lo dos intervalos com os quais o SAP HANA no Azure (Instâncias Grandes) tem permissão para se comunicar.

Enquanto ainda não for específico, as diretrizes dedicadas no Azure.com sobre a remoção de sub-redes, o processo de remoção de sub-redes é o inverso do processo para adicioná-los. Confira o artigo do portal do Azure [Criar uma rede virtual usando o portal do Azure](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter mais informações sobre a criação de sub-redes.

## <a name="deleting-a-vnet"></a>Como excluir uma rede virtual

Ao excluir uma rede virtual, use o Portal do Azure, PowerShell ou CLI. O SAP HANA no Gerenciamento de Serviços do Azure remove as autorizações existentes no SAP HANA no circuito de ExpressRoute do Azure (Instâncias Grandes) e remove os intervalos de endereços IP (intervalos de locatário e de gateway) para a comunicação com instâncias grandes HANA.

Depois que a rede virtual tiver sido removida, abra uma solicitação de suporte do Azure para fornecer os intervalos de endereço IP a serem removidos.

Enquanto ainda não for específico, as diretrizes dedicadas no Azure.com sobre a remoção de redes virtuais, o processo de remoção de redes virtuais é o inverso do processo para adicioná-los, que é descrito acima. Confira os artigos [Criar uma rede virtual usando o portal do Azure](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [Criar uma rede virtual usando o PowerShell](../../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter mais informações sobre a criação de redes virtuais.

Para garantir que tudo seja removido, exclua os itens a seguir:

- **Para o Azure Resource Manager:** a conexão de ExpressRoute, o gateway da rede virtual, o IP público do gateway de rede virtual e a rede virtual
- **Para VM clássica:** o gateway de rede virtual e a rede virtual

## <a name="deleting-an-expressroute-circuit"></a>Como excluir um circuito de ExpressRoute

Para remover um SAP HANA adicional no circuito de ExpressRoute do Azure (Instâncias Grandes), abra uma solicitação de suporte do Azure com o SAP HANA no Gerenciamento de Serviços do Azure e solicite que o circuito seja excluído. Na assinatura do Azure, você pode excluir ou manter a rede virtual conforme necessário. No entanto, você deve excluir a conexão (se o Azure Resource Manager ou desvincular a conexão (se for o Clássico) entre o circuito de ExpressRoute de instâncias grandes HANA e o gateway de rede virtual vinculado.

Se você também quiser remover uma rede virtual, siga as diretrizes sobre Como excluir uma rede virtual na seção acima.



