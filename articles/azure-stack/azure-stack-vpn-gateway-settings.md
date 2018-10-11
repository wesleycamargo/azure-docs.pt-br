---
title: Configurações de gateway de VPN para o Azure Stack | Microsoft Docs
description: Saiba mais sobre as configurações para usar com o Azure Stack de gateways de VPN.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: fa8d3adc-8f5a-4b4f-8227-4381cf952c56
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/14/2018
ms.author: sethm
ms.openlocfilehash: 2fa062621e551ce7182facc45ec84b39d4c2dad7
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078742"
---
# <a name="vpn-gateway-configuration-settings-for-azure-stack"></a>Definições de configuração de gateway VPN para o Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Um gateway de VPN é um tipo de gateway de rede virtual que envia tráfego criptografado entre sua rede virtual no Azure Stack e um gateway VPN remoto. O gateway de VPN remoto pode ser no Azure, um dispositivo em seu datacenter ou um dispositivo em outro site.  Se há conectividade de rede entre os dois pontos de extremidade, você pode estabelecer uma conexão segura de VPN Site a Site (S2S) entre as duas redes.

Uma conexão de Gateway de VPN tem base na configuração de vários recursos, cada um deles contendo definições configuráveis. Este artigo discute os recursos e configurações que se relacionam ao gateway de VPN para uma rede virtual que você cria no modelo de implantação do Resource Manager. Você pode encontrar descrições e diagramas de topologia para cada solução de conexão no [sobre o Gateway de VPN para o Azure Stack](azure-stack-vpn-gateway-about-vpn-gateways.md).

## <a name="vpn-gateway-settings"></a>Configurações de gateway VPN

### <a name="gateway-types"></a>Tipos de gateway

Cada rede virtual do Azure Stack dá suporte a um gateway de rede virtual única, que deve ser do tipo **Vpn**.  Esse suporte é diferente do Azure, que dá suporte a tipos adicionais.  

Quando você estiver criando um gateway de rede virtual, certifique-se de que o tipo de gateway é correto para sua configuração. Um gateway de VPN exige o `-GatewayType Vpn`, por exemplo:

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn
-VpnType RouteBased
```

### <a name="gateway-skus"></a>SKUs de gateway

Ao criar um gateway de rede virtual, você precisa especificar o SKU do gateway que você deseja usar. Selecione as SKUs que atendem às suas necessidades com base nos tipos de SLAs, taxas de transferência, recursos e cargas de trabalho.

O Azure Stack oferece o gateway de VPN SKUs mostradas na tabela a seguir.

|   | Taxa de transferência de Gateway de VPN |Túneis IPsec máximo de Gateway de VPN |
|-------|-------|-------|
|**SKU Básico**  | 100 Mbps  | 10    |
|**SKU padrão**           | 100 Mbps  | 10    |
|**SKU de Alto Desempenho** | 200 Mbps    | 5 |

### <a name="resizing-gateway-skus"></a>Redimensionamento de SKUs de gateway

O Azure Stack não dá suporte a um redimensionamento de SKUs entre os SKUs herdados com suporte.

Da mesma forma, Azure Stack não dá suporte a um redimensionamento de uma SKU herdada com suporte (Basic, Standard e HighPerformance) para um SKU mais recente com suporte do Azure (VpnGw1, VpnGw2 e VpnGw3).

### <a name="configure-the-gateway-sku"></a>Configurar o SKU de gateway

#### <a name="azure-stack-portal"></a>Portal do Azure Stack

Se você usar o portal do Azure Stack para criar um gateway de rede virtual do Resource Manager, você pode selecionar o SKU de gateway usando a lista suspensa. As opções que lhe será apresentada correspondem ao tipo de Gateway e o tipo VPN que você selecionar.

#### <a name="powershell"></a>PowerShell

O exemplo de PowerShell a seguir especifica o **- GatewaySku** como VpnGw1.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku VpnGw1
-GatewayType Vpn -VpnType RouteBased
```

### <a name="connection-types"></a>Tipos de Conexão

No modelo de implantação do Resource Manager, cada configuração exige um tipo específico de conexão de gateway de rede virtual. Os valores para o Gerenciador de recursos disponíveis do PowerShell **- ConnectionType** são:

* IPsec

No exemplo a seguir do PowerShell, uma conexão S2S é criado que requer que o tipo de conexão IPsec.

```PowerShell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

### <a name="vpn-types"></a>Tipos de VPN

Quando você cria o gateway de rede virtual para uma configuração de gateway de VPN, é preciso especificar um tipo de VPN. O tipo de VPN que você escolhe depende da topologia de conexão que quer criar.  Um tipo de VPN também pode depender do hardware que você está usando. As configurações S2S requerem um dispositivo VPN. Alguns dispositivos VPN recebem suporte apenas de um determinado tipo de VPN.

> [!IMPORTANT]  
> Atualmente, o Azure Stack suporta apenas o tipo de rota com base em VPN. Se seu dispositivo dá suporte apenas a política com base em VPNs, conexões com os dispositivos do Azure Stack não são suportadas.  
>
> Além disso, Azure Stack não suporta o uso seletores de tráfego com base em política para os Gateways de rota com base no momento, porque não há suporte para configurações de política de IPSec/IKE personalizadas.

* **PolicyBased**: VPNs baseadas em política criptografam e direcionam pacotes por meio de túneis IPsec com base nas políticas de IPsec são configuradas com as combinações de prefixos de endereço entre sua rede local e a VNet do Azure Stack. A política ou o seletor de tráfego, geralmente é uma lista de acesso na configuração de dispositivo de VPN.

  >[!NOTE]
  >PolicyBased tem suporte no Azure, mas não no Azure Stack.

* **RouteBased**: as VPNs RouteBased usar rotas que estão configuradas na tabela de roteamento ou de encaminhamento de IP para direcionar pacotes para as interfaces de túnel correspondentes. As interfaces de túnel criptografam ou descriptografam então os pacotes para dentro e para fora dos túneis. A política ou o seletor de tráfego para as VPNs RouteBased são configurados como qualquer para qualquer (ou use caracteres curinga.) Por padrão, elas não podem ser alteradas. O valor para um tipo de VPN RouteBased é RouteBased.

O exemplo de PowerShell a seguir especifica o **- VpnType** como RouteBased. Quando você estiver criando um gateway, certifique-se que o **- VpnType** está correto para a sua configuração.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig
-GatewayType Vpn -VpnType RouteBased
```

### <a name="gateway-requirements"></a>Requisitos do gateway

A tabela a seguir lista os requisitos para gateways de VPN.

| |Gateway VPN PolicyBased Basic | Gateway VPN RouteBased Basic | Gateway VPN RouteBased Standard | Gateway VPN RouteBased alto desempenho|
|--|--|--|--|--|
| **Conectividade site a Site (S2S conectividade)** | Sem suporte | Configuração de VPN RouteBased | Configuração de VPN RouteBased | Configuração de VPN RouteBased |
| **Método de autenticação**  | Sem suporte | Chave pré-compartilhada para conectividade S2S  | Chave pré-compartilhada para conectividade S2S  | Chave pré-compartilhada para conectividade S2S  |   
| **Número máximo de conexões S2S**  | Sem suporte | 10 | 10| 5|
|**Suporte ao roteamento ativo (BGP)** | Sem suporte | Sem suporte | Com suporte | Com suporte |

### <a name="gateway-subnet"></a>Gateway de sub-rede

Antes de criar um gateway de VPN, crie uma sub-rede de gateway. A sub-rede de gateway tem os endereços IP que usam os serviços e VMs de gateway de rede virtual. Quando você cria o gateway de rede virtual, as VMs de gateway são implantadas na sub-rede de gateway e definidas com as configurações necessárias de gateway de VPN. **Não** implantar qualquer coisa (por exemplo, VMs adicionais) para a sub-rede de gateway.

>[!IMPORTANT]
>A sub-rede do gateway deve ser nomeada como **GatewaySubnet** para funcionar corretamente. Pilha do Azure usa esse nome para identificar a sub-rede para implantar as VMs de gateway de rede virtual e serviços.

Quando você cria a sub-rede de gateway, pode especificar o número de endereços IP que contém a sub-rede. Os endereços IP na sub-rede do gateway são alocados para as VMs de gateway e para os serviços de gateway. Algumas configurações exigem mais endereços IP do que outras. Examine as instruções da configuração que você deseja criar e verifique se a sub-rede de gateway que você quer criar atende a esses requisitos.

Além disso, você deve verificar se que sua sub-rede de gateway tem endereços IP suficientes para lidar com futuras configurações adicionais. Embora você possa criar uma sub-rede de gateway tão pequena quanto/29, recomendamos que você crie uma sub-rede de gateway de/28 ou maior (/ 28, / 27, / 26 e assim por diante.) Dessa forma, se você adicionar a funcionalidade no futuro, você não precisa subdividir o gateway, em seguida, excluir e recriar a sub-rede de gateway para permitir mais endereços IP.

O exemplo de PowerShell do Resource Manager a seguir mostra uma sub-rede de gateway chamada GatewaySubnet. Você pode ver que a notação CIDR especifica /27, que permite endereços IP suficientes para a maioria das configurações existentes no momento.

```PowerShell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> Ao trabalhar com sub-redes de gateway, evite a associação de um NSG (grupo de segurança de rede) à sub-rede de gateway. Associar um grupo de segurança de rede a essa sub-rede pode fazer com que seu gateway de VPN para parar de funcionar conforme o esperado. Para obter mais informações sobre grupos de segurança de rede, consulte [o que é um grupo de segurança de rede?](/azure/virtual-network/virtual-networks-nsg).

### <a name="local-network-gateways"></a>Gateways de rede local

Ao criar uma configuração de gateway VPN no Azure, o gateway de rede local geralmente representa sua localização no local. No Azure Stack, ele representa qualquer dispositivo de VPN remoto que fica fora do Azure Stack. Isso pode ser um dispositivo VPN em seu datacenter (ou um data center remoto), ou um Gateway de VPN no Azure.

Nomeie o gateway de rede local, o endereço IP público do dispositivo VPN e especificar os prefixos de endereço que estão no local no local. O Azure examina os prefixos de endereço de destino para o tráfego de rede, consulta a configuração que você especificou para o gateway de rede local e roteia os pacotes adequadamente.

O exemplo de PowerShell a seguir cria um novo gateway de rede local:

```PowerShell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Às vezes, você precisa modificar as configurações do gateway de rede local. Por exemplo, quando você adicionar ou modificar o intervalo de endereços, ou se o endereço IP do dispositivo VPN mudar. Consulte [Modificar as configurações de gateway de rede local usando o PowerShell](/azure/vpn-gateway/vpn-gateway-modify-local-network-gateway).

## <a name="ipsecike-parameters"></a>Parâmetros IPsec/IKE

Quando você configura uma Conexão VPN no Azure Stack, você precisará configurar a conexão em ambas as extremidades.  Se você estiver configurando uma Conexão VPN entre o Azure Stack e um dispositivo de hardware, como um comutador ou roteador que está atuando como um Gateway de VPN, que o dispositivo pode solicitar o configurações adicionais.

Ao contrário do Azure, que dá suporte a várias ofertas como um iniciador e um Respondente, o Azure Stack oferece suporte apenas a uma oferta.

### <a name="ike-phase-1-main-mode-parameters"></a>Parâmetros da Fase 1 de IKE (Modo Principal)

| Propriedade              | Valor|
|-|-|
| Versão IKE           | IKEv2 |
|Grupo Diffie-Hellman   | Grupo 2 (1024 bits) |
| Método de autenticação | Chave Pré-Compartilhada |
|Criptografia e algoritmos de hash | AES256, SHA256 |
|Tempo de vida da SA (Tempo)     | 28.800 segundos|

### <a name="ike-phase-2-quick-mode-parameters"></a>Parâmetros da Fase 2 de IKE (Modo Rápido)

| Propriedade| Valor|
|-|-|
|Versão IKE |IKEv2 |
|Criptografia e algoritmos (criptografia) de hash     | GCMAES256|
|Criptografia e algoritmos (autenticação) de hash | GCMAES256|
|Tempo de vida da SA (Tempo)  | 27.000 segundos  |
|Tempo de vida da SA (Bytes) | 33,553,408     |
|PFS (Perfect Forward Secrecy) |Nenhum<sup>veja a Observação 1</sup> |
|Detecção de par inativo | Com suporte|  

* *Observação 1:* antes da versão 1807, pilha do Azure usa o valor de PFS2048 para o Perfect Forward Secrecy (PFS).

## <a name="next-steps"></a>Próximas etapas

[Conectar-se usando o ExpressRoute](azure-stack-connect-expressroute.md)
