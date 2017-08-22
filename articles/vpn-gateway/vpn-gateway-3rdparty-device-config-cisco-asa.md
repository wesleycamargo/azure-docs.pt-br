---
title: "Exemplo de configuração – dispositivo Cisco ASA conectando-se a gateways de VPN do Azure | Microsoft Docs"
description: "Este artigo apresenta um exemplo de configuração de dispositivo Cisco ASA conectando-se a gateways de VPN do Azure."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 10466b8928e2cd687f7961a2956b6d60823b82be
ms.contentlocale: pt-br
ms.lasthandoff: 07/10/2017


---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>Exemplo de configuração: dispositivo Cisco ASA (IKEv2/não BGP)
Este artigo apresenta exemplos de configuração de dispositivos Cisco ASA conectando-se a gateways de VPN do Azure.

## <a name="device-at-a-glance"></a>Visão rápida do dispositivo

|                        |                                   |
| ---                    | ---                               |
| Fornecedor do dispositivo          | Cisco                             |
| Modelo do dispositivo           | ASA (Dispositivo de Segurança Adaptável) |
| Versão de destino         | 8.4+                              |
| Modelo testado           | ASA 5505                          |
| Versão testada         | 9.2                               |
| Versão do IKE            | **IKEv2**                         |
| BGP                    | **Não**                            |
| Tipo de gateway VPN do Azure | Gateway VPN **baseado em rota**       |
|                        |                                   |

> [!NOTE]
> 1. A configuração a seguir conecta um dispositivo ASA Cisco a um gateway de VPN **baseado em rota** do Azure usando a política IPsec/IKE personalizada com a opção "UserPolicyBasedTrafficSelectors", conforme descrito [neste artigo](vpn-gateway-connect-multiple-policybased-rm-ps.md).
> 2. Exige que dispositivos ASA usem **IKEv2** com configurações baseada em lista de acesso, não com base em VTI.
> 3. Consulte as especificações do fornecedor do dispositivo VPN para garantir que a política tem suporte em seus dispositivos VPN local.

## <a name="vpn-device-requirements"></a>Requisitos do dispositivo VPN
Gateways VPN do Azure usam conjuntos padrão de protocolo IPsec/IKE para estabelecer túneis VPN S2S. Consulte [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md) para os parâmetros detalhados de protocolo IPsec/IKE e algoritmos de criptografia padrão para gateways VPN do Azure. Opcionalmente, você pode especificar a combinação exata de algoritmos de criptografia e forças de chave para uma conexão específica, conforme descrito em [Sobre os requisitos de criptografia](vpn-gateway-about-compliance-crypto.md). Se você selecionar uma combinação específica de algoritmos de criptografia e forças de chave, use as especificações correspondentes em seus dispositivos VPN.

## <a name="single-vpn-tunnel"></a>Túnel VPN único
Essa topologia consiste em um único túnel VPN S2S entre um gateway de VPN do Azure e o dispositivo VPN local. Opcionalmente, você pode configurar o BGP por meio do túnel VPN.

![túnel único](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Consulte [Configuração de túnel único](vpn-gateway-3rdparty-device-config-overview.md#singletunnel) para instruções detalhadas passo a passo para criar as configurações do Azure.

### <a name="network-and-vpn-gateway-information"></a>Informações de gateway de VPN e de rede
Esta seção lista os parâmetros para este exemplo.

| **Parâmetro**                | **Valor**                    |
| ---                          | ---                          |
| Prefixos de endereços da VNET        | 10.11.0.0/16<br>10.12.0.0/16 |
| IP do gateway de VPN do Azure         | Azure_Gateway_Public_IP      |
| Prefixos de endereço local | 10.51.0.0/16<br>10.52.0.0/16 |
| IP do dispositivo VPN local    | OnPrem_Device_Public_IP     |
| *ASN de BGP da VNet                | 65010                        |
| *IP de par no nível de protocolo BGP do Azure           | 10.12.255.30                 |
| * ASN de BGP local         | 65050                        |
| * IP de par no nível de protocolo BGP local     | 10.52.255.254                |
|                              |                              |

* (*) Parâmetros opcionais apenas para o BGP.

### <a name="ipsecike-policy--parameters"></a>Parâmetros e política de IPsec/IKE

A tabela a seguir lista os algoritmos e os parâmetros IPsec/IKE usados no exemplo. Consulte as especificações do dispositivo VPN para assegurar que todos os algoritmos listados acima tenham suporte com seus modelos de dispositivo VPN e versões de firmware.

| **IPsec/IKEv2**  | **Valor**                            |
| ---              | ---                                  |
| Criptografia IKEv2 | AES256                               |
| Integridade do IKEv2  | SHA384                               |
| Grupo DH         | DHGroup24                            |
| Criptografia IPsec | AES256                               |
| Integridade do IPsec  | SHA1                                 |
| Grupo PFS        | PFS24                                |
| Tempo de vida da QM SA   | 7.200 segundos                         |
| Seletor de tráfego | UsePolicyBasedTrafficSelectors $True |
| Chave Pré-Compartilhada   | PreSharedKey                         |
|                  |                                      |

- (*) Em alguns dispositivos, a integridade de IPsec deverá ser "nula" se o GCM-AES for usado como o algoritmo de criptografia IPsec.

### <a name="device-notes"></a>Notas do dispositivo

>[!NOTE]
>
> 1. Suporte ao IKEv2 requer ASA versão 8.4 e acima.
> 2. Suporte de grupo DH e PFS superior (além do Grupo de 5) requer ASA versão 9. x.
> 3. Criptografia IPsec com integridade de AES-GCM e IPsec com suporte para SHA-256, SHA-384 e SHA-512 requer ASA versão 9. x em hardware ASA mais recente; ASA 5505, 5510, 5520, 5540, 5550, 5580 **não** têm suporte. (Consulte as especificações do fornecedor para confirmar.)
>


### <a name="sample-device-configurations"></a>Exemplos de configurações de dispositivo
O script a seguir apresenta um exemplo de configuração com base na topologia e nos parâmetros listados acima. A configuração de túnel VPN S2S consiste nas seguintes partes:

1. Interfaces e rotas
2. Listas de acesso
3. Política e parâmetros IKE (fase 1 ou modo principal)
4. Política e parâmetros IPsec (fase 2 ou modo rápido)
5. Outros parâmetros (fixação de MSS TCP etc.)

>[!IMPORTANT] 
>Conclua a configuração adicional listada abaixo e substitua os espaços reservados pelos valores reais:
> 
> - Configuração de interface para as interfaces interna e externa
> - Rotas para suas redes privadas/internas e externas/públicas
> - Assegure que todos os nomes e números de política sejam exclusivos no dispositivo
> - Assegure que os algoritmos de criptografia tenham suporte em seu dispositivo
> - Substitua os espaços reservados a seguir pelos valores reais
>   - Nome da interface externa: "externa"
>   - Azure_Gateway_Public_IP
>   - OnPrem_Device_Public_IP
>   - IKE Pre_Shared_Key
>   - Nomes do gateway de rede local e da VNet (VNetName, LNGName)
>   - Prefixos de endereço de rede local e da VNet
>   - Máscaras de rede adequadas

#### <a name="sample-configuration"></a>Exemplo de configuração

```
! Sample ASA configuration for connecting to Azure VPN gateway
!
! Tested hardware: ASA 5505
! Tested version:  ASA version 9.2(4)
!
! Replace the following place holders with your actual values:
!   - Interface names - default are "outside" and "inside"
!   - <Azure_Gateway_Public_IP>
!   - <OnPrem_Device_Public_IP>
!   - <Pre_Shared_Key>
!   - <VNetName>*
!   - <LNGName>* ==> LocalNetworkGateway - the Azure resource that represents the
!     on-premises network, specifies network prefixes, device public IP, BGP info, etc.
!   - <PrivateIPAddress> ==> Replace it with a private IP address if applicable
!   - <Netmask> ==> Replace it with appropriate netmasks
!   - <Nexthop> ==> Replace it with the actual nexthop IP address
!
! (*) Must be unique names in the device configuration
!
! ==> Interface & route configurations
!
!     > <OnPrem_Device_Public_IP> address on the outside interface or vlan
!     > <PrivateIPAddress> on the inside interface or vlan; e.g., 10.51.0.1/24
!     > Route to connect to <Azure_Gateway_Public_IP> address
!
!     > Example:
!
!       interface Ethernet0/0
!        switchport access vlan 2
!       exit
!
!       interface vlan 1
!        nameif inside
!        security-level 100
!        ip address <PrivateIPAddress> <Netmask>
!       exit
!
!       interface vlan 2
!        nameif outside
!        security-level 0
!        ip address <OnPrem_Device_Public_IP> <Netmask>
!       exit
!
!       route outside 0.0.0.0 0.0.0.0 <NextHop IP> 1
!
! ==> Access lists
!
!     > Most firewall devices deny all traffic by default. Create access lists to
!       (1) Allow S2S VPN tunnels between the ASA and the Azure gateway public IP address
!       (2) Construct traffic selectors as part of IPsec policy or proposal
!
access-list outside_access_in extended permit ip host <Azure_Gateway_Public_IP> host <OnPrem_Device_Public_IP>
!
!     > Object group that consists of all VNet prefixes (e.g., 10.11.0.0/16 &
!       10.12.0.0/16)
!
object-group network Azure-<VNetName>
 description Azure virtual network <VNetName> prefixes
 network-object 10.11.0.0 255.255.0.0
 network-object 10.12.0.0 255.255.0.0
exit
!
!     > Object group that corresponding to the <LNGName> prefixes.
!       E.g., 10.51.0.0/16 and 10.52.0.0/16. Note that LNG = "local network gateway".
!       In Azure network resource, a local network gateway defines the on-premises
!       network properties (address prefixes, VPN device IP, BGP ASN, etc.)
!
object-group network <LNGName>
 description On-Premises network <LNGName> prefixes
 network-object 10.51.0.0 255.255.0.0
 network-object 10.52.0.0 255.255.0.0
exit
!
!     > Specify the access-list between the Azure VNet and your on-premises network.
!       This access list defines the IPsec SA traffic selectors.
!
access-list Azure-<VNetName>-acl extended permit ip object-group <LNGName> object-group Azure-<VNetName>
!
!     > No NAT required between the on-premises network and Azure VNet
!
nat (inside,outside) source static <LNGName> <LNGName> destination static Azure-<VNetName> Azure-<VNetName>
!
! ==> IKEv2 configuration
!
!     > General IKEv2 configuration - enable IKEv2 for VPN
!
group-policy DfltGrpPolicy attributes
 vpn-tunnel-protocol ikev1 ikev2
exit
!
crypto isakmp identity address
crypto ikev2 enable outside
!
!     > Define IKEv2 Phase 1/Main Mode policy
!       - Make sure the policy number is not used
!       - integrity and prf must be the same
!       - DH group 14 and above require ASA version 9.x.
!
crypto ikev2 policy 1
 encryption       aes-256
 integrity        sha384
 prf              sha384
 group            24
 lifetime seconds 86400
exit
!
!     > Set connection type and pre-shared key
!
tunnel-group <Azure_Gateway_Public_IP> type ipsec-l2l
tunnel-group <Azure_Gateway_Public_IP> ipsec-attributes
 ikev2 remote-authentication pre-shared-key <Pre_Shared_Key> 
 ikev2 local-authentication  pre-shared-key <Pre_Shared_Key> 
exit
!
! ==> IPsec configuration
!
!     > IKEv2 Phase 2/Quick Mode proposal
!       - AES-GCM and SHA-2 requires ASA version 9.x on newer ASA models. ASA
!         5505, 5510, 5520, 5540, 5550, 5580 are not supported.
!       - ESP integrity must be null if AES-GCM is configured as ESP encryption
!
crypto ipsec ikev2 ipsec-proposal AES-256
 protocol esp encryption aes-256
 protocol esp integrity  sha-1
exit
!
!     > Set access list & traffic selectors, PFS, IPsec protposal, SA lifetime
!       - This sample uses "Azure-<VNetName>-map" as the crypto map name
!       - ASA supports only one crypto map per interface, if you already have
!         an existing crypto map assigned to your outside interface, you must use
!         the same crypto map name, but with a different sequence number for
!         this policy
!       - "match address" policy uses the access-list "Azure-<VNetName>-acl" defined 
!         previously
!       - "ipsec-proposal" uses the proposal "AES-256" defined previously 
!       - PFS groups 14 and beyond requires ASA version 9.x.
!
crypto map Azure-<VNetName>-map 1 match address Azure-<VNetName>-acl
crypto map Azure-<VNetName>-map 1 set pfs group24
crypto map Azure-<VNetName>-map 1 set peer <Azure_Gateway_Public_IP>
crypto map Azure-<VNetName>-map 1 set ikev2 ipsec-proposal AES-256
crypto map Azure-<VNetName>-map 1 set security-association lifetime seconds 7200
crypto map Azure-<VNetName>-map interface outside
!
! ==> Set TCP MSS to 1350
!
sysopt connection tcpmss 1350
!
```

## <a name="simple-debugging-commands"></a>Comandos de depuração simples

Aqui estão alguns comandos ASA para fins de depuração:

1. Mostrar SAs IKE e IPsec
    - "show crypto ipsec sa"
    - "show crypto ikev2 sa"
2. Inserir o modo de depuração – isso pode ficar ruidoso no console
    - "debug crypto ikev2 platform <level>"
    - "debug crypto ikev2 protocol <level>"
3. Listar configurações atuais
    - "show run" – mostra as configurações atuais no dispositivo. Você pode usar os vários subcomandos para listar partes específicas da configuração. Por exemplo, "show run crypto", "show run access-list", "show run tunnel-group" etc.


## <a name="next-steps"></a>Próximas etapas
Consulte [Configurando os Gateways de VPN Ativos para Conexões de VNet com VNet e Entre Locais](vpn-gateway-activeactive-rm-powershell.md) para ver as etapas de configuração das conexões ativas entre os locais e conexões VNet com VNet.


