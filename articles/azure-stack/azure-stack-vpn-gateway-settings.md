---
title: "Configurações de gateway VPN para a pilha do Azure | Microsoft Docs"
description: "Saiba mais sobre as configurações para usar com a pilha do Azure de gateways VPN."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: fa8d3adc-8f5a-4b4f-8227-4381cf952c56
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/01/2017
ms.author: brenduns
ms.openlocfilehash: 1276310a35d0d69a4111a58b9675f15bb5285a08
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2018
---
# <a name="vpn-gateway-configuration-settings-for-azure-stack"></a>Definições de configuração de gateway VPN para a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Um gateway de VPN é um tipo de gateway de rede virtual que envia tráfego criptografado entre sua rede virtual na pilha do Azure e um gateway VPN remoto. O gateway VPN remoto pode ser no Azure, um dispositivo em seu data center ou em um dispositivo em outro site.  Se há conectividade de rede entre os dois pontos de extremidade, você pode estabelecer uma conexão VPN Site a Site (S2S) segura entre as duas redes.

Uma conexão de Gateway de VPN tem base na configuração de vários recursos, cada um deles contendo definições configuráveis. As seções neste artigo abordam os recursos e configurações relacionados a um gateway de VPN para uma rede virtual criada no modelo de implantação do Resource Manager. Você pode encontrar descrições e diagramas de topologia para cada solução de conexão do [sobre o Gateway de VPN para o Azure pilha](azure-stack-vpn-gateway-about-vpn-gateways.md).

## <a name="vpn-gateway-settings"></a>Configurações de gateway VPN

### <a name="gateway-types"></a>Tipos de gateway
Cada rede virtual da pilha do Azure oferece suporte a um gateway de rede virtual único, que deve ser do tipo **Vpn**.  Esse suporte é diferente do Azure, que dá suporte a tipos adicionais.  

Quando estiver criando um gateway de rede virtual, você deve garantir que o tipo de gateway seja o correto para sua configuração. Requer um gateway VPN de `-GatewayType Vpn`.

Exemplo:
```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn
-VpnType RouteBased
```

### <a name="gateway-skus"></a>SKUs de gateway
Ao criar um gateway de rede virtual, você precisa especificar o SKU do gateway que você deseja usar. Selecione as SKUs que atendem às suas necessidades com base nos tipos de SLAs, taxas de transferência, recursos e cargas de trabalho.

>[!NOTE]
> As redes virtuais clássicas devem continuar a usar as SKUs antigas. Para saber mais sobre as SKUs antigas do gateway, confira [Trabalhando com SKUs de gateway de rede virtual (antigas)](/azure/vpn-gateway/vpn-gateway-about-skus-legacy).

A pilha do Azure oferece o seguinte SKUs de gateway de VPN:

|   | Taxa de transferência de Gateway de VPN |Túneis IPsec máximos de Gateway de VPN |
|-------|-------|-------|
|**SKU Básico**  | 100 Mbps  | 10    |
|**SKU padrão**           | 100 Mbps  | 10    |
|**SKU de Alto Desempenho** | 200 Mbps    | 30    |

### <a name="resizing-gateway-skus"></a>Redimensionando SKUs de gateway
A pilha do Azure não oferece suporte a um redimensionamento de SKUs entre SKUs herdados com suporte.

Da mesma forma, a pilha do Azure não suporta um redimensionamento de uma SKU herdados com suporte (Basic, Standard e alto desempenho), para os SKUs mais recentes com suporte do Azure (VpnGw1, VpnGw2 e VpnGw3).

### <a name="configure-the-gateway-sku"></a>Configurar o SKU de gateway
#### <a name="azure-stack-portal"></a>Portal do Azure de pilha
Se você usar o portal de pilha do Azure para criar um gateway de rede virtual do Gerenciador de recursos, você pode selecionar o SKU do gateway usando o menu suspenso. As opções apresentadas correspondem ao tipo de Gateway e ao tipo de VPN que você selecionar.

#### <a name="powershell"></a>PowerShell
O exemplo a seguir do PowerShell Especifica o GatewaySku - como VpnGw1.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku VpnGw1
-GatewayType Vpn -VpnType RouteBased
```
### <a name="connection-types"></a>Tipos de Conexão
No modelo de implantação do Resource Manager, cada configuração exige um tipo específico de conexão de gateway de rede virtual. Os valores do Gerenciador de recursos do PowerShell disponíveis para - ConnectionType são:

- IPsec

No exemplo a seguir do PowerShell, uma conexão S2S é criada que requer que o tipo de conexão IPsec.  

```PowerShell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

### <a name="vpn-types"></a>Tipos de VPN
Quando você cria o gateway de rede virtual para uma configuração de gateway de VPN, é preciso especificar um tipo de VPN. O tipo de VPN que você escolhe depende da topologia de conexão que quer criar.  Um tipo de VPN também pode depender do hardware que você usa. As configurações S2S requerem um dispositivo VPN. Alguns dispositivos VPN recebem suporte apenas de um determinado tipo de VPN.

> [!IMPORTANT]  
> Neste momento, a pilha do Azure suporta apenas o tipo de rota com base em VPN. Se seu dispositivo só dá suporte a política com base em VPNs, conexões com os dispositivos de pilha do Azure não têm suporte.

- **PolicyBased**: *(com suporte pelo Azure, mas não pela pilha do Azure)* VPNs baseadas em política criptografar e direcionar os pacotes por meio de túneis IPsec com base nas políticas de IPsec que estão configuradas com as combinações de prefixos de endereço entre sua rede local e a VNet de pilha do Azure. A política (ou o seletor de tráfego) normalmente é definida como uma lista de acesso na configuração de dispositivo VPN.

- **RouteBased**: RouteBased VPNs usar "rotas" IP de encaminhamento ou tabela de roteamento direto pacotes em suas interfaces de túnel correspondente. As interfaces de túnel criptografam ou descriptografam então os pacotes para dentro e para fora dos túneis. A política (ou seletor de tráfego) para as VPNs RouteBased são configurados como qualquer para qualquer (ou curingas). O valor de um tipo de VPN RouteBased é RouteBased.

O exemplo a seguir do PowerShell Especifica o VpnType - como RouteBased. Ao criar um gateway, você deve garantir que o -VpnType seja correto para sua configuração.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig
-GatewayType Vpn -VpnType RouteBased
```
### <a name="gateway-requirements"></a>Requisitos do gateway
A tabela a seguir lista os requisitos para gateways de VPN.

| |Gateway VPN PolicyBased Basic | Gateway VPN RouteBased Basic | Gateway VPN padrão RouteBased | Gateway VPN RouteBased alto desempenho|
|--|--|--|--|--|
| **Conectividade site a Site (S2S conectividade)** | Sem suporte | Configuração de VPN RouteBased | Configuração de VPN RouteBased | Configuração de VPN RouteBased |
| **Método de autenticação**  | Sem suporte | Chave pré-compartilhada para conectividade S2S  | Chave pré-compartilhada para conectividade S2S  | Chave pré-compartilhada para conectividade S2S  |   
| **Número máximo de conexões S2S**  | Sem suporte | 10 | 10| 30|
|**Suporte ao roteamento ativo (BGP)** | Sem suporte | Sem suporte | Com suporte | Com suporte |

### <a name="gateway-subnet"></a>Gateway de sub-rede
Antes de criar um gateway de VPN, crie uma sub-rede de gateway. A sub-rede de gateway contém os endereços IP que as VMs do gateway de rede virtual e os serviços usam. Quando você cria o gateway de rede virtual, as VMs de gateway são implantadas na sub-rede de gateway e definidas com as configurações necessárias de gateway de VPN. Não implante tudo (por exemplo, VMs adicionais) para a sub-rede de gateway. A sub-rede do gateway deve ser nomeada como GatewaySubnet para funcionar corretamente. A sub-rede de gateway de nomenclatura 'GatewaySubnet' permite que a pilha do Azure para identificar a sub-rede para implantar as VMs de gateway de rede virtual e serviços.

Quando você cria a sub-rede de gateway, pode especificar o número de endereços IP que contém a sub-rede. Os endereços IP na sub-rede do gateway são alocados para as VMs de gateway e para os serviços de gateway. Algumas configurações exigem mais endereços IP do que outras. Examine as instruções da configuração que você deseja criar e verifique se a sub-rede de gateway que você quer criar atende a esses requisitos. Além disso, convém certificar-se de que sua sub-rede de gateway contenha endereços IP suficientes para acomodar possíveis configurações adicionais futuras. Embora seja possível criar uma sub-rede de gateway tão pequena quanto /29, é recomendável criar uma sub-rede de gateway de /28 ou maior (/28, /27, /26 etc.). Dessa forma, se você adicionar a funcionalidade no futuro, você não precisa subdividir seu gateway, em seguida, exclua e recrie a sub-rede de gateway para permitir mais endereços IP.

O exemplo de PowerShell do Resource Manager a seguir mostra uma sub-rede de gateway chamada GatewaySubnet. Você pode ver que a notação CIDR especifica /27, que permite endereços IP suficientes para a maioria das configurações existentes no momento.

```PowerShell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> Ao trabalhar com sub-redes de gateway, evite a associação de um NSG (grupo de segurança de rede) à sub-rede de gateway. Associar um grupo de segurança de rede a essa sub-rede pode fazer com que seu gateway de VPN para parar de funcionar conforme o esperado. Para obter mais informações sobre grupos de segurança de rede, consulte [o que é um grupo de segurança de rede?](/azure/virtual-network/virtual-networks-nsg).

### <a name="local-network-gateways"></a>Gateways de rede local
Ao criar uma configuração de gateway VPN no Azure, o gateway de rede local geralmente representa sua localização no local. Na pilha do Azure, ele representa qualquer dispositivo de VPN remoto que fica fora do Azure pilha.  Isso pode ser um dispositivo VPN em seu data center, um data center remoto ou um Gateway de VPN no Azure.

Nomeie o gateway de rede local, o endereço IP público do dispositivo VPN e especificar os prefixos de endereço que estão no local no local. O Azure examina os prefixos de endereço de destino para o tráfego de rede, consulta a configuração que você especificou para o gateway de rede local e roteia os pacotes adequadamente.

O seguinte exemplo de PowerShell cria um novo gateway de rede local:

```PowerShell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```
Às vezes, você precisa modificar as configurações do gateway de rede local. Por exemplo, quando você adicionar ou modificar o intervalo de endereços, ou se o endereço IP do dispositivo VPN mudar. Consulte [Modificar as configurações de gateway de rede local usando o PowerShell](/azure/vpn-gateway/vpn-gateway-modify-local-network-gateway).

## <a name="ipsecike-parameters"></a>Parâmetros IPsec/IKE
Quando você configura uma Conexão VPN na pilha do Azure, você precisa configurar a conexão em ambas as extremidades.  Se você estiver configurando uma Conexão VPN entre a pilha do Azure e um dispositivo de hardware, como um comutador ou roteador, que está atuando como um Gateway de VPN, esse dispositivo pode pedir configurações adicionais.

Ao contrário do Azure, que oferece suporte a várias ofertas como um iniciador e o respondedor, pilha do Azure oferece suporte apenas a uma oferta.

###  <a name="ike-phase-1-main-mode-parameters"></a>Parâmetros da Fase 1 de IKE (Modo Principal)
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
|(Criptografia) de algoritmos de hash e de criptografia     | GCMAES256|
|(Autenticação) de algoritmos de hash e de criptografia | GCMAES256|
|Tempo de vida da SA (Tempo)  | 3.600 segundos |
|Tempo de vida da SA (Bytes) | 819,200       |
|PFS (Perfect Forward Secrecy) |PFS2048 |
|Detecção de par inativo | Com suporte|  
