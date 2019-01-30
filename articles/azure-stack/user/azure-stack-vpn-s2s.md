---
title: Configurar conexões de VPN site a site do Azure Stack | Microsoft Docs
description: Saiba mais sobre a política de IPsec/IKE para conexões de VNet para VNet ou VPN site a site no Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2019
ms.author: sethm
ms.lastreviewed: 01/19/2019
ms.openlocfilehash: e9e24bd19243f13b89d54f0105195b5c17505925
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251496"
---
# <a name="configure-ipsecike-policy-for-site-to-site-vpn-or-vnet-to-vnet-connections"></a>Configurar a política de IPsec/IKE para conexões de VNet para VNet ou VPN site a site

Este artigo explica as etapas para configurar uma política de IPsec/IKE para VPN site a site (S2S) conexões no Azure Stack.

## <a name="ipsec-and-ike-policy-parameters-for-vpn-gateways"></a>Parâmetros de política de IPsec e IKE para gateways de VPN

O protocolo IPsec e IKE standard dá suporte a uma ampla variedade de algoritmos criptográficos em várias combinações. Para ver quais parâmetros têm suporte no Azure Stack, consulte [parâmetros IPsec/IKE](azure-stack-vpn-gateway-settings.md#ipsecike-parameters), que pode ajudar a atender às suas necessidades de segurança ou conformidade.

Este artigo fornece instruções sobre como criar e configurar uma política de IPsec/IKE e aplicar a uma conexão nova ou existente.

## <a name="considerations"></a>Considerações

Observe as seguintes considerações importantes ao usar essas políticas:

- A política de IPsec/IKE só funciona na *Standard* e *HighPerformance* SKUs de gateway (baseado em rota).

- Você só pode especificar **uma** combinação de políticas para uma determinada conexão.

- Você deve especificar todos os algoritmos e parâmetros para IKE (modo principal) e IPsec (modo rápido). A especificação de política parcial não é permitida.

- Consulte as especificações do fornecedor do dispositivo VPN para garantir que a política tem suporte em seus dispositivos VPN local. Não não possível estabelecer conexões site a site, se as políticas são incompatíveis.

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a>Parte 1 - fluxo de trabalho para criar e definir a política de IPsec/IKE

Esta seção descreve o fluxo de trabalho necessário para criar e atualizar a política de IPsec/IKE em uma conexão de VPN site a site:

1. Crie uma rede virtual e um gateway de VPN.

2. Crie um gateway de rede local para a conexão entre locais.

3. Crie uma política de IPsec/IKE com parâmetros e algoritmos selecionados.

4. Crie uma conexão IPSec com a política de IPsec/IKE.

5. Adicionar/atualizar/remover uma política de IPsec/IKE para uma conexão existente.

As instruções na Ajuda neste artigo você instalar e configurar políticas de IPsec/IKE, conforme mostrado na figura a seguir:

![Instalar e configurar políticas de IPsec/IKE](media/azure-stack-vpn-s2s/site-to-site.png)

## <a name="part-2---supported-cryptographic-algorithms-and-key-strengths"></a>Parte 2 - suporte para algoritmos de criptografia e forças de chave

A tabela a seguir lista os algoritmos de criptografia compatíveis e restrições de chave configuráveis pelos clientes do Azure Stack:

| IPsec/IKEv2                                          | Opções                                                                  |
|------------------------------------------------------|--------------------------------------------------------------------------|
| Criptografia IKEv2                                     | AES256, AES192, AES128, DES3, DES                                        |
| Integridade do IKEv2                                      | SHA384, SHA256, SHA1, MD5                                                |
| Grupo DH                                             | ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, nenhum         |
| Criptografia IPsec                                     | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, nenhum |
| Integridade do IPsec                                      | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                       |
| Grupo PFS                                            | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, nenhum                         |
| Tempo de vida da QM SA                                       | (Opcional: valores padrão serão usados se não especificados)<br />                         Segundos (inteiro; mín., 300/padrão de 27000 segundos)<br />                         KBytes (inteiro; min. 1024/padrão de 102400000 KBytes) |                                                                          |
| Seletor de tráfego                                     | Não há suporte para seletores de tráfego com base em política no Azure Stack.         |

- A configuração do dispositivo VPN local deve corresponder ou conter os seguintes algoritmos e parâmetros que você especifica na política de IPsec/IKE do Azure:

  - Algoritmo de criptografia IKE (Modo Principal/Fase 1)
  - Algoritmo de integridade IKE (Modo Principal/Fase 1)
  - Grupo DH (Modo Principal/Fase 1)
  - Algoritmo de criptografia IPsec (Modo Rápido/Fase 2)
  - Algoritmo de integridade IPsec (Modo Rápido/Fase 2)
  - Grupo PFS (Modo Rápido/Fase 2)
  - Os tempos de vida da SA são apenas especificações locais, portanto não precisam ser correspondentes.

- Se GCMAES for usado como para o algoritmo de criptografia IPsec, você deve selecionar o mesmo algoritmo GCMAES e comprimento de chave para integridade IPsec; Por exemplo, usando GCMAES128 para ambos.

- Na tabela anterior:

  - IKEv2 corresponde ao Modo Principal ou Fase 1
  - IPsec corresponde ao Modo Rápido ou Fase 2
  - O Grupo DH especifica o Grupo Diffie-Hellmen usado no Modo Principal ou Fase 1
  - O Grupo PFS especificou o Grupo Diffie-Hellmen usado no Modo Rápido ou Fase 2

- Tempo de vida da SA de modo principal IKEv2 é fixo em 28.800 segundos nos gateways de VPN do Azure Stack.

A tabela abaixo lista os Grupos Diffie-Hellman correspondentes que têm suporte na política personalizada:

| Grupo Diffie-Hellman | DHGroup   | PFSGroup      | Comprimento da chave    |
|----------------------|-----------|---------------|---------------|
| 1                    | DHGroup1  | PFS1          | MODP de 768 bits  |
| 2                    | DHGroup2  | PFS2          | MODP de 1024 bits |
| 14                   | DHGroup14<br/>DHGroup2048 | PFS2048       | MODP de 2048 bits |
| 19                   | ECP256    | ECP256        | ECP de 256 bits   |
| 20                   | ECP384    | ECP384        | ECP de 384 bits   |
| 24                   | DHGroup24 | PFS24         | MODP de 2048 bits |

Para obter mais informações, consulte [RFC3526](https://tools.ietf.org/html/rfc3526) e [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name="part-3---create-a-new-site-to-site-vpn-connection-with-ipsecike-policy"></a>Parte 3 - criar uma nova conexão de VPN site a site com a política de IPsec/IKE

Esta seção orienta você pelas etapas para criar uma conexão de VPN site a site com uma política de IPsec/IKE. As seguintes etapas criam a conexão, conforme mostrado na figura a seguir:

![site a site política](media/azure-stack-vpn-s2s/site-to-site.png)

Para obter mais instruções passo a passo para a criação de uma conexão de VPN site a site, consulte [criar uma conexão de VPN site a site](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que você tenha os seguintes pré-requisitos:

- Uma assinatura do Azure. Se você ainda não tiver uma assinatura do Azure, você poderá ativar sua [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), ou se inscrever para uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Os cmdlets do PowerShell do Azure Resource Manager. Ver [instalar o PowerShell para Azure Stack](../azure-stack-powershell-install.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Etapa 1 - Criar a rede virtual, o gateway de VPN e o gateway de rede local

#### <a name="1-declare-variables"></a>1. Declarar variáveis

Para este exercício, comece declarando variáveis a seguir. Certifique-se de substituir os espaços reservados pelos seus próprios valores ao configurar para a produção:

```powershell
$Sub1 = "\<YourSubscriptionName\>"
$RG1 = "TestPolicyRG1"
$Location1 = "East US 2"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPconf1 = "gw1ipconf1"
$Connection16 = "VNet1toSite6"
$LNGName6 = "Site6"
$LNGPrefix61 = "10.61.0.0/16"
$LNGPrefix62 = "10.62.0.0/16"
$LNGIP6 = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Conectar à sua assinatura do Azure e criar um novo grupo de recursos

Alterne para o modo do PowerShell para usar os cmdlets do Gerenciador de Recursos. Para obter mais informações, consulte [conectar-se ao Azure Stack com o PowerShell como um usuário](azure-stack-powershell-configure-user.md).

Abra o console do PowerShell e conecte-se à sua conta. Use o exemplo a seguir para ajudar com a conexão:

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Criar a rede virtual, o gateway de VPN e o gateway de rede local

O exemplo a seguir cria a rede virtual, **TestVNet1**, com três sub-redes e o gateway de VPN. Ao substituir valores, é importante que você sempre Nomeie sua sub-rede de gateway especificamente **GatewaySubnet**. Se você usar outro nome, a criação do gateway falhará.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1 = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1

$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
-VirtualNetwork $vnet1

$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1
-Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
-Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn
-VpnType RouteBased -GatewaySku VpnGw1

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1
-Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix
$LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-site-to-site-vpn-connection-with-an-ipsecike-policy"></a>Etapa 2: criar uma conexão de VPN site a site com uma política de IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Criar uma política de IPsec/IKE

Este exemplo de script cria uma política de IPsec/IKE com os parâmetros e os algoritmos seguintes:

- IKEv2: AES128, SHA1, DHGroup14
- IPsec: AES256, SHA256, nenhum, tempo de vida do SA 14400 segundos e 102400000KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup none -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Se você usar GCMAES para IPsec, deverá usar o mesmo algoritmo GCMAES e comprimento de chave tanto para criptografia quanto para integridade IPsec.

#### <a name="2-create-the-site-to-site-vpn-connection-with-the-ipsecike-policy"></a>2. Criar a conexão de VPN site a site com a política de IPsec/IKE

Criar uma conexão de VPN site a site e aplicar a política de IPsec/IKE criada anteriormente.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'Azs123'
```

> [!IMPORTANT]
> Quando uma política de IPsec/IKE é especificada em uma conexão, o gateway de VPN do Azure só irá enviar ou aceitar a proposta de IPsec/IKE com algoritmos de criptografia especificados e as restrições de chave naquela conexão determinada. Verifique se o dispositivo VPN local para a conexão usa ou aceita a combinação de política exata, caso contrário, que não será possível estabelecer o túnel VPN site a site.

## <a name="part-4---update-ipsecike-policy-for-a-connection"></a>Parte 4 - política de IPsec/IKE de atualização para uma conexão

A seção anterior mostrou como gerenciar a política de IPsec/IKE para uma conexão site a site existente. A seção a seguir explica as seguintes operações em uma conexão:

1. Mostrar a política de IPsec/IKE de uma conexão
2. Adicionar ou atualizar a política de IPsec/IKE para uma conexão
3. Remover a política de IPsec/IKE de uma conexão

> [!NOTE]
> Política de IPsec/IKE tem suporte em *Standard* e *HighPerformance* baseado em rota somente gateways VPN. Ele não funciona na *básica* SKU de gateway.

### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Mostrar a política de IPsec/IKE de uma conexão

O exemplo a seguir mostra como obter a política de IPsec/IKE configurada em uma conexão. Os scripts também continuam de exercícios anteriores:

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

O último comando lista a atual política de IPsec/IKE configurada sobre a conexão, se houver. O exemplo a seguir está um exemplo de saída para a conexão:

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

Se não houver nenhuma política de IPsec/IKE configurado, o comando `$connection6.policy` obtém vazio retornado. Isso não significa que IPsec/IKE não está configurado na conexão; Isso significa que não há nenhuma política de IPsec/IKE personalizada. A conexão real usa a política padrão negociada entre o dispositivo VPN no local e o gateway de VPN do Azure.

### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Adicionar e atualizar uma política de IPsec/IKE para uma conexão

As etapas para adicionar uma nova política ou atualizar uma política existente em uma conexão são as mesmas: criar uma nova política e, em seguida, aplicar a nova política para a conexão.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1

$newpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

$connection6.SharedKey = "AzS123"

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Você pode obter a conexão novamente para verificar se a política for atualizada:

```powershell
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Você deve ver a saída da última linha, conforme mostrado no exemplo abaixo:

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Remover uma política de IPsec/IKE de uma conexão

Após você remove a política personalizada de uma conexão, o gateway de VPN do Azure reverterá para o [proposta de IPsec/IKE padrão](azure-stack-vpn-gateway-settings.md#ipsecike-parameters)e renegociará com o dispositivo VPN local.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.SharedKey = “AzS123”
$currentpolicy = $connection6.IpsecPolicies\[0\]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Você pode usar o mesmo script para verificar se a política foi removida da conexão.

## <a name="next-steps"></a>Próximas etapas

- [Definições de configuração de gateway VPN para o Azure Stack](azure-stack-vpn-gateway-settings.md)
