---
title: Cenário de Aplicação do Alcance Global do Azure ExpressRoute | Microsoft Docs
description: Esta página fornece um cenário de aplicação do Alcance Global.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,global reach
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/14/2019
ms.author: rambala
ms.openlocfilehash: 5174baae40d7f14720affe90f702c024541a5745
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54333003"
---
# <a name="expressroute-global-reach-application-scenario"></a>Cenário de aplicação do Alcance Global do ExpressRoute

Para saber mais sobre o Alcance Global do ExpressRoute, confira [Alcance Global do ExpressRoute][Global Reach]. Neste artigo, vamos examinar um cenário de aplicação, comparar a solução de Alcance Global do ExpressRoute com algumas outras soluções, configurar o Alcance Global no cenário de exemplo e verificar as conexões. 

##<a name="application-scenario"></a>Cenário de aplicação

A Fabrikam Inc. tem uma grande presença física e implantação do Azure no leste dos EUA. A Fabrikam tem conectividade de back-end entre seus locais e implantações do Azure por meio do ExpressRoute. A Contoso Ltd. tem uma presença e implantação do Azure no oeste dos EUA. A Contoso tem conectividade de back-end entre seus locais e implantações do Azure por meio do ExpressRoute.  

A Fabrikam Inc. adquire a Contoso Ltd. Seguindo a fusão, a Fabrikam deseja interconectar as redes. A figura a seguir ilustra o cenário:

 [![1]][1]

As setas tracejadas no meio da figura acima indicam as interconexões de rede necessárias. A tabela a seguir mostra a tabela de rotas de emparelhamento privado primário do ExpressRoute da Contoso Ltd. antes da fusão.

[![2]][2]

A tabela a seguir mostra a tabela de rotas de emparelhamento privado primário do ExpressRoute da Fabrikam Inc. antes da fusão.

[![3]][3]

## <a name="traditional-solutions"></a>Soluções tradicionais

### <a name="option-1-interconnect-on-premises-networks"></a>Opção 1: Interconectar as redes locais

A figura a seguir ilustra essa opção. Como mostrado, você pode interconectar as duas redes locais usando a VPN site a site ou outras opções de conexão de banda larga e gerenciar todo o roteamento entre as duas entidades. 

[![4]][4]

Essa opção tem as seguintes desvantagens:

- Você está forçando a comunicação do Azure entre regiões para a implantação em uma rota abaixo do ideal.
- Você está negando o benefício de alta disponibilidade da rede de backbone da Microsoft para a comunicação entre regiões.
- Você está assumindo a responsabilidade total de roteamento da comunicação entre regiões.

### <a name="option-2-expressroute-cross-connectivity-and-interconnect-on-premises-networks"></a>Opção 2: Conectividade do ExpressRoute e interconexão de redes locais

A figura a seguir ilustra essa opção.

[![5]][5]

Como mostrado na figura acima, você também pode estabelecer a conectividade entre os circuitos do ExpressRoute e as VNets regionais. A conectividade regional entre a VNet e os circuitos do ExpressRoute permitiria as seguintes comunicações:

- As VNets do oeste/leste dos EUA para se comunicarem, respectivamente, com as redes locais da Fabrikam/Contoso.
- A VNet do oeste dos EUA para se comunicar com a VNet do leste dos EUA.

A interconexão entre as duas redes locais ainda é necessária para as redes locais se comunicarem entre si.

Essa opção permite a comunicação entre regiões do Azure para a implantação particular utilizar o backbone da Microsoft e a comunicação entre a rede local ser realizada pela rede externa. No entanto, a principal desvantagem da solução é que você precisa estabelecer várias conexões regionais que complicam a manutenção e a solução de problemas. Além disso, a opção não permite que você aproveite a alta disponibilidade do backbone global da Microsoft na comunicação entre as duas redes locais.

### <a name="option-3-vnet-peering-and-interconnect-on-premises-networks"></a>Opção 3: Emparelhamento VNet e interconexão de redes locais

A figura a seguir ilustra essa opção. A opção usa o emparelhamento VNet na comunicação da VNet entre regiões. A opção, como ilustrado, está incompleta, pois não aborda a VNet entre regiões à comunicação local (indicada pelas duas linhas de seta pontilhada no meio). Use a conexão local para local (como na opção 1) ou a conectividade do ExpressRoute (como na opção 2) na comunicação entre regiões com o local.

[![6]][6]

Essa opção fornece o roteamento ideal para a comunicação da VNet entre regiões. No entanto, ela não será suficiente se a Fabrikam ou a Contoso tiver uma implantação mais complexa do Azure, como um modelo VNet hub-spoke. Além disso, como nas duas opções anteriores, esta opção não permite que você aproveite a alta disponibilidade do backbone global da Microsoft na comunicação entre as duas redes locais.

## <a name="global-reach"></a>Alcance Global

O Alcance Global do ExpressRoute vincula o emparelhamento privado dos circuitos do ExpressRoute, como ilustrado na figura a seguir:

[![7]][7]

Configurar o Alcance Global entre os dois circuitos do ExpressRoute permite a comunicação privada entre as duas redes locais e a implantação do Azure em duas regiões. Portanto, o Alcance Global satisfaz toda a comunicação desejada (indicada por meio de uma linha tracejada na primeira figura deste artigo) pela rede de backbone da Microsoft.

### <a name="configure-global-reach"></a>Configurar o Alcance Global

Para saber como configurar o Alcance Global do ExpressRoute, confira [Configurar o Alcance Global][Configure Global Reach]. 

Como a Fabrikam Inc. e a Contoso Ltd. integraram o Azure como empresas separadas, os circuitos do ExpressRoute das duas empresas estão em duas assinaturas diferentes do Azure. Para criar o Alcance Global entre as assinaturas, você precisa criar uma autorização no circuito do ExpressRoute pertencente a Contoso Ltd. e passá-lo para a Fabrikam, Inc. Circuito do ExpressRoute.


Para criar uma autorização do circuito do ExpressRoute da Contoso, primeiro faça logon na conta do Azure da Contoso e escolha a assinatura apropriada (se houver várias assinaturas). Os comandos do PowerShell para essas etapas são:

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```
As etapas para criar uma autorização de circuito do ExpressRoute estão listadas abaixo:

```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```

A saída 'Set-AzureRmExpressRouteCircuit' listará o ExpressRouteCircuit. Observe a ID de emparelhamento privada e a chave de autorização que seriam listadas no final da listagem. Veja um exemplo do trecho de saída do PowerShell abaixo:

```powershell
ServiceKey                       : <serviceKey>
Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"78dfeeae-0485-4cda-b5aa-99e780589677\"",
                                       "Id": "/subscriptions/<subscriptionId>/resourceGroups/SEA-Cust11/providers/Microsoft.Network/expressRouteCircuits/SEA-Cust11-ER/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 65020,
                                       "PrimaryPeerAddressPrefix": "192.168.11.16/30",
                                       "SecondaryPeerAddressPrefix": "192.168.11.20/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 110,
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "4",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
Authorizations                   : [
                                     {
                                       "Name": "Auth4-ASH-Cust11",
                                       "Etag": "W/\"78dfeeae-0485-4cda-b5aa-99e780589677\"",
                                       "Id": "/subscriptions/<subscriptionId>/resourceGroups/SEA-Cust11/providers/Microsoft.Network/expressRouteCircuits/SEA-Cust11-ER/authorizations/Auth4-ASH-Cust11",
                                       "AuthorizationKey": "<authorizationKey>",
                                       "AuthorizationUseStatus": "Used",
                                       "ProvisioningState": "Succeeded"
                                     }
                                   ]
AllowClassicOperations           : False
GatewayManagerEtag               : 4
```

Com a ID de emparelhamento e a chave de autorização, você pode criar o Alcance Global em um circuito do ExpressRoute da Fabrikam. Faça logon na conta do Azure da Fabrikam. Se houver mais de uma assinatura, escolha a assinatura apropriada.

O Alcance Global cria um conjunto redundante de conexões ponto a ponto entre os dois pares de MSEE. Para as duas conexões ponto a ponto, você precisa especificar um prefixo de endereço /29 (para o exemplo em execução usaremos 192.168.11.64/29). Use os comandos a seguir para criar a conexão de Alcance Global:

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix "__.__.__.__/29" -AuthorizationKey "########-####-####-####-############"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Depois que os comandos acima forem executados, levará alguns minutos até que as conexões redundantes com Alcance Global sejam criadas.

### <a name="verify-global-reach"></a>Verificar o Alcance Global

A tabela a seguir mostra a tabela de rotas de emparelhamento privado primário do ExpressRoute da Contoso Ltd. após a configuração do Alcance Global.

[![8]][8]

A tabela a seguir mostra a tabela de rotas de emparelhamento privado primário do ExpressRoute da Fabrikam Ltd. após a configuração do Alcance Global.

[![9]][9]

Nas tabelas acima, vemos todos os prefixos de "REDE" de destino esperados e o "PRÓXIMO SALTO" apropriado é listado.

Acima, você vê a folha "Obter tabela de rotas" que pode ser acessada no portal do Azure, em "Emparelhamento privado" de um circuito do ExpressRoute. Você também pode listar uma tabela de rotas do ExpressRoute usando o seguinte comando do PowerShell:

```powershell
Get-AzExpressRouteCircuitRouteTable -DevicePath 'primary' -ExpressRouteCircuitName "Your_circuit_name" -PeeringType AzurePrivatePeering -ResourceGroupName "Your_resource_group"
```

Para ver a tabela de rotas do MSEE secundário, substitua "primary" pela palavra-chave "secondary" no comando acima.

Também verificaremos a conectividade do plano de dados executando um ping de destino diferente de redes diferentes. Os três pings a seguir verificam a conectividade do plano de dados com a rede local da Contoso, com a VNet do Azure da Contoso e com a VNet do Azure da Fabrikam na rede local da Fabrikam.


    C:\Users\FabrikamUser>ping 10.1.11.10
    
    Pinging 10.1.11.10 with 32 bytes of data:
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    
    Ping statistics for 10.1.11.10:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 69ms, Maximum = 69ms, Average = 69ms
    
    C:\Users\FabrikamUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=82ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=81ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=124
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 76ms, Maximum = 82ms, Average = 78ms
    
    C:\Users\FabrikamUser>ping 10.10.11.4
    
    Pinging 10.10.11.4 with 32 bytes of data:
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=2ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    
    Ping statistics for 10.10.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 2ms, Maximum = 3ms, Average = 2ms


Os dois pings a seguir verificam a conectividade do plano de dados para a VNet do Azure da Contoso e a VNet do Azure da Fabrikam na rede local da Contoso.

    C:\Users\ContosoUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=6ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 5ms, Maximum = 6ms, Average = 5ms
    
    C:\Users\ContosoUser>ping 10.10.11.4
    
    Pinging 10.10.11.4 with 32 bytes of data:
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=74ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    
    Ping statistics for 10.10.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 73ms, Maximum = 74ms, Average = 73ms

O ping a seguir verifica a conectividade do plano de dados para a VNet do Azure da Contoso na VNet do Azure da Fabrikam.

    C:\Users\FabrikamUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=77ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=77ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=78ms TTL=125
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 76ms, Maximum = 78ms, Average = 77ms

## <a name="optimization-for-latency-sensitive-traffic"></a>Otimização para tráfego sensível à latência

O Alcance Global roteia o tráfego por meio de dispositivos do Microsoft Edge. Para o cenário específico neste artigo, você pode obter o roteamento ideal entre as duas VNets habilitando o emparelhamento VNet entre elas. Da mesma forma, você pode obter o roteamento mais ideal entre as duas redes locais por meio de um provedor de serviços que pode fornecer uma conexão WAN mais direta entre os locais. Nesses cenários, é possível usar o roteamento de Alcance Global como uma opção de failback para essas conexões. 

## <a name="next-steps"></a>Próximas etapas

O Alcance Global é implementado por país. Para ver se o Alcance Global está disponível nos países desejados, confira [Alcance Global do ExpressRoute][Global Reach].

<!--Image References-->
[1]: ./media/expressroute-globalreach-applicationscenario/PreMergerScenario.png "O cenário de aplicação"
[2]: ./media/expressroute-globalreach-applicationscenario/ContosoExR-RT-Premerger.png "Tabela de rotas do ExpressRoute da Contoso antes de fusão"
[3]: ./media/expressroute-globalreach-applicationscenario/FabrikamExR-RT-Premerger.png "Tabela de rotas do ExpressRoute da Fabrikam antes de fusão"
[4]: ./media/expressroute-globalreach-applicationscenario/S2SVPN-Solution.png "Interconectar as redes locais"
[5]: ./media/expressroute-globalreach-applicationscenario/ExR-XConnect-Solution.png "Realizar a conexão cruzada do ExpressRoute"
[6]: ./media/expressroute-globalreach-applicationscenario/VNet-peering-Solution.png "Emparelhamento VNet"
[7]: ./media/expressroute-globalreach-applicationscenario/GlobalReach-Solution.png "Alcance Global"
[8]: ./media/expressroute-globalreach-applicationscenario/ContosoExR-RT-Postmerger.png "Tabela de rotas do ExpressRoute da Contoso com Alcance Global"
[9]: ./media/expressroute-globalreach-applicationscenario/FabrikamExR-RT-Postmerger.png "Tabela de rotas do ExpressRoute da Fabrikam com Alcance Global"

<!--Link References-->
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach





