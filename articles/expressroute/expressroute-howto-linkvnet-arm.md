---
title: Vincular uma rede virtual a um circuito de Rota Expressa usando o PowerShell | Microsoft Docs
description: Este documento fornece uma visão geral de como vincular as redes virtuais (VNets) aos circuitos de Rota Expressa usando o modelo de implantação do Gerenciador de Recursos e do PowerShell.
services: expressroute
documentationcenter: na
author: ganesr
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: ganesr

---
# <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Vincular uma rede virtual a um circuito de Rota Expressa
> [!div class="op_single_selector"]
> * [Portal do Azure - Gerenciador de Recursos](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell – Resource Manager](expressroute-howto-linkvnet-arm.md)
> * [PowerShell - clássico](expressroute-howto-linkvnet-classic.md)
> 
> 

Este artigo o ajudará a vincular as redes virtuais (VNets) aos circuitos de Rota Expressa do Azure usando o modelo de implantação do Gerenciador de Recursos e do PowerShell. As redes virtuais podem estar na mesma assinatura ou fazer parte de outra assinatura.

**Sobre modelos de implantação do Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Pré-requisitos de configuração
* Você precisará da versão mais recente dos módulos do Azure PowerShell (pelo menos a versão 1.0). Confira [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar os cmdlets do PowerShell.
* Leia os [pré-requisitos](expressroute-prerequisites.md), os [requisitos de roteamento](expressroute-routing.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
* Você deve ter um circuito da Rota Expressa ativo. 
  * Siga as instruções para [criar um circuito da Rota Expressa](expressroute-howto-circuit-arm.md) e para que o circuito seja habilitado pelo provedor de conectividade. 
  * Verifique se o emparelhamento privado do Azure está configurado para seu circuito. Veja o artigo [Configurar roteamento](expressroute-howto-routing-arm.md) para obter instruções sobre roteamento. 
  * Verifique se o emparelhamento privado do Azure está configurado e se o emparelhamento BGP entre sua rede e a Microsoft está ativo para que você possa habilitar a conectividade de ponta a ponta.
  * Verifique se tem uma rede virtual e um gateway de rede virtual criados e totalmente provisionados. Siga as instruções para criar um [gateway de VPN](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), mas lembre-se de usar `-GatewayType ExpressRoute`.

Você pode vincular até 10 redes virtuais a um circuito de Rota Expressa padrão. Todas as redes virtuais deverão estar na mesma região geopolítica ao usar um circuito de Rota Expressa padrão. 

Você poderá vincular uma rede virtual fora da região geopolítica do circuito da Rota Expressa ou conectar um grande número de redes virtuais ao circuito de Rota Expressa, se tiver habilitado o complemento premium da Rota Expressa. Confira as [perguntas frequentes](expressroute-faqs.md) para obter mais detalhes sobre o complemento premium.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Conectar uma rede virtual na mesma assinatura a um circuito
Você pode vincular um gateway de rede virtual a um circuito da Rota Expressa usando o cmdlet a seguir. Verifique se o gateway de rede virtual foi criado e se está pronto para vinculação antes de executar o cmdlet:

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Conectar uma rede virtual em uma assinatura diferente a um circuito
Você pode compartilhar um circuito da Rota Expressa entre várias assinaturas. A figura a seguir mostra um esquema simples de como funciona o compartilhamento de circuitos da Rota Expressa entre várias assinaturas.

Cada uma das nuvens menores dentro da nuvem grande é usada para representar assinaturas pertencentes a diferentes departamentos dentro de uma organização. Cada um dos departamentos dentro da organização pode usar sua própria assinatura para implantar seus serviços, mas pode compartilhar um único circuito da Rota Expressa para se conectar de volta à respectiva rede local. Um único departamento (neste exemplo: TI) pode ter o circuito da Rota Expressa. Outras assinaturas dentro da organização podem usar o circuito de Rota Expressa.

> [!NOTE]
> As cobranças por conectividade e largura de banda do circuito dedicado serão aplicadas ao proprietário do circuito da Rota Expressa. Todas as redes virtuais compartilham a mesma largura de banda.
> 
> 

![Conectividade entre assinaturas](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administração
O *proprietário do circuito* é um usuário avançado autorizado do recurso de circuito da Rota Expressa. O proprietário do circuito pode criar autorizações que podem ser resgatadas pelos *usuários do circuito*. *Usuários do circuito* são proprietários de gateways de rede virtual (que não estão na mesma assinatura que o circuito da Rota Expressa). *Circuit users* podem resgatar autorizações (uma autorização por rede virtual).

O *proprietário do circuito* tem a capacidade de modificar e revogar autorizações a qualquer momento. Revogar uma autorização faz com que todas as conexões de links sejam excluídas da assinatura cujo acesso foi revogado.

### <a name="circuit-owner-operations"></a>Operações do proprietário do circuito
#### <a name="creating-an-authorization"></a>Criando uma autorização
O proprietário do circuito cria uma autorização. Isso resulta na criação de uma chave de autorização que pode ser usada por um usuário do circuito para conectar seus gateways de rede virtual ao circuito da Rota Expressa. Uma autorização é válida apenas para uma conexão.

O seguinte trecho de cmdlet mostra como criar uma autorização:

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

        $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"


A resposta para isso conterá a chave de autorização e o status:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



#### <a name="reviewing-authorizations"></a>Examinando autorizações
O proprietário do circuito pode examinar todas as autorizações emitidas em um circuito específico executando o seguinte cmdlet:

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit


#### <a name="adding-authorizations"></a>Adicionando autorizações
O proprietário do circuito pode adicionar autorizações usando o cmdlet a seguir.

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit


#### <a name="deleting-authorizations"></a>Excluindo autorizações
O proprietário do circuito pode revogar/excluir autorizações usando o seguinte cmdlet:

    Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit    

### <a name="circuit-user-operations"></a>Operações do usuário do circuito
O usuário do circuito precisa da ID do par e de uma chave de autorização do proprietário do circuito. A chave de autorização é um GUID.

A ID de Par pode ser verificada com o seguinte comando.

    Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"

#### <a name="redeeming-connection-authorizations"></a>Resgatando autorizações de conexão
O usuário de circuito pode executar o seguinte cmdlet para resgatar uma autorização de vínculo:

    $id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"  
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"

#### <a name="releasing-connection-authorizations"></a>Liberando autorizações de conexão
É possível liberar uma autorização excluindo a conexão que vincula o circuito da Rota Expressa à rede virtual.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre a Rota Expressa, consulte [Perguntas Frequentes sobre Rota Expressa](expressroute-faqs.md).

<!--HONumber=Oct16_HO2-->


