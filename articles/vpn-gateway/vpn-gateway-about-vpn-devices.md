---
title: "Sobre dispositivos VPN para conexões do Azure entre locais | Microsoft Docs"
description: "Este artigo discute dispositivos VPN e os parâmetros de IPsec para conexões de Gateway de VPN S2S entre locais. São fornecidos links para exemplos e instruções de configuração."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: ba449333-2716-4b7f-9889-ecc521e4d616
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2016
ms.author: yushwang;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: ba659fe42fa2264708833f5674711334845defcc
ms.openlocfilehash: 283e71f03f3907fd1e72283059ba7acbdac054d4
ms.lasthandoff: 02/21/2017


---
# <a name="about-vpn-devices-for-site-to-site-vpn-gateway-connections"></a>Sobre dispositivos VPN para conexões de Gateway de VPN Site a Site
Um dispositivo VPN é necessário para configurar uma conexão de VPN Site a Site (S2S) entre locais usando um gateway de VPN. Conexões Site a Site podem ser usadas para criar uma solução híbrida ou sempre que desejar uma conexão segura entre sua rede local e sua rede virtual. Este artigo aborda os dispositivos VPN compatíveis e parâmetros de configuração.

> [!NOTE]
> Ao configurar uma conexão Site a Site, um endereço IP IPv4 público é necessário para seu dispositivo VPN.                                                                                                                                                                               
>
>

Se o dispositivo não aparecer na tabela [Dispositivos VPN validados](#devicetable), veja a seção [Dispositivos VPN não validados](#additionaldevices) deste artigo. É possível que seu dispositivo ainda possa funcionar com o Azure. Para suporte ao dispositivo VPN, entre em contato com o fabricante do dispositivo.

> [!IMPORTANT]
> Consulte [Problemas conhecidos de compatibilidade de dispositivos](#known) se você estiver com problemas de conectividade entre os dispositivos VPN locais e os gateways de VPN do Azure.

**Itens a ser observados ao exibir as tabelas:**

* Houve uma mudança de terminologia do roteamento estático e dinâmico. Provavelmente, você executará em ambos os termos. Não há alterações de funcionalidade, somente os nomes estão sendo alterados.
  * Roteamento estático = PolicyBased
  * Roteamento dinâmico = RouteBased
* As especificações de gateway de VPN de Alto Desempenho e de gateway de VPN RouteBased são as mesmas, salvo indicação em contrário. Por exemplo, os dispositivos VPN validados compatíveis com os gateways de VPN RouteBased também são compatíveis com o gateway de VPN de Alto Desempenho do Azure.

## <a name="a-namedevicetableavalidated-vpn-devices"></a><a name="devicetable"></a>Dispositivos de VPN validados
Validamos um conjunto de dispositivos VPN padrão em parceria com fornecedores de dispositivos. Todos os dispositivos nas famílias de dispositivos contidos na lista abaixo devem funcionar com os gateways de VPN do Azure. Consulte [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md) para verificar o tipo de gateway que você precisa criar para a solução que deseja configurar.

Para ajudar a configurar seu dispositivo VPN, veja os links que correspondem à família de dispositivos apropriada. Para suporte ao dispositivo VPN, entre em contato com o fabricante do dispositivo.

| **Fornecedor** | **Família do dispositivo** | **Versão mínima do sistema operacional** | **PolicyBased** | **RouteBased** |
| --- | --- | --- | --- | --- |
| Allied Telesis |Série AR de roteadores VPN |2.9.2 |Em breve |Não compatível |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall F-series |PolicyBased: 5.4.3<br>RouteBased: 6.2.0 |[Instruções de configuração](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) |[Instruções de configuração](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW) |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall X-series |Barracuda Firewall 6.5 |[Barracuda Firewall](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) |Não compatível |
| Brocade |Vyatta 5400 vRouter |Roteador virtual 6.6R3 GA |[Instruções de configuração](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) |Não compatível |
| Ponto de Verificação |Gateway de segurança |R77.30 |[Instruções de configuração](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[Instruções de configuração](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco |ASA |8.3 |[Exemplos da Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA) |Não compatível |
| Cisco |ASR |PolicyBased: IOS 15.1<br>RouteBased: IOS 15.2 |[Exemplos da Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |[Exemplos da Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |
| Cisco |ISR |PolicyBased: IOS 15.0<br>RouteBased*: IOS 15.1 |[Exemplos da Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |[Exemplos da Cisco*](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |
| Citrix |NetScaler MPX, SDX, VPX |10.1 e acima |[Instruções de integração](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |Não compatível |
| Dell SonicWALL |Série TZ, série NSA<br>Série SuperMassive<br>Série NSA E-Class |SonicOS 5.8.x<br>[SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850)<br>[SonicOS 6.x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646) |[Guia de configuração para o SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646)<br>[Guia de configuração para o SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |[Guia de configuração para o SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646)<br>[Guia de configuração para o SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |
| F5 |Série BIG-IP |12.0 |[Instruções de configuração](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[Instruções de configuração](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.4.2 |[Instruções de configuração](http://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-54) |[Instruções de configuração](http://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-54) |
| Internet Initiative Japan (IIJ) |Série SEIL |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[Instruções de configuração](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |Não compatível |
| Juniper |SRX |PolicyBased: JunOS 10.2<br>Routebased: JunOS 11.4 |[Exemplos do Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |[Exemplos do Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |
| Juniper |Série J |PolicyBased: JunOS 10.4r9<br>RouteBased: JunOS 11.4 |[Exemplos do Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |[Exemplos do Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |
| Juniper |ISG |ScreenOS 6.3 |[Exemplos do Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |[Exemplos do Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |
| Juniper |SSG |ScreenOS 6.2 |[Exemplos do Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |[Exemplos do Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |
| Microsoft |Serviço de Roteamento e Acesso Remoto |Windows Server 2012 |Não compatível |[Exemplos da Microsoft](http://go.microsoft.com/fwlink/p/?LinkId=717761) |
| AG de sistemas abertos |Gateway de Segurança de Controle de Missão |N/D |[Guia de instalação](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |[Guia de instalação](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |
| Openswan |Openswan |2.6.32 |(Em breve) |Não compatível |
| Redes Palo Alto |Todos os dispositivos executando PAN-OS |PAN-OS<br>PolicyBased: 6.1.5 ou posterior<br>RouteBased: 7.1.4 |[Instruções de configuração](https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065) |[Instruções de configuração](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340) |
| WatchGuard |Todos |Fireware XTM<br> PolicyBased: v11.11.x<br>RouteBased: v11.12.x |[Instruções de configuração](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[Instruções de configuração](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|

(*) Os roteadores da Série ISR 7200 só oferecem suporte a VPNs PolicyBased.

## <a name="a-nameadditionaldevicesanon-validated-vpn-devices"></a><a name="additionaldevices"></a>Dispositivos VPN não validados
Se você não vir seu dispositivo listado na tabela de dispositivos VPN Validados, ele ainda pode funcionar com uma conexão Site a Site. Verifique se seu dispositivo VPN atende aos requisitos mínimos descritos na seção Requisitos do Gateway do artigo [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md) . Os dispositivos que atendem aos requisitos mínimos devem funcionar bem com os gateways de VPN. Entre em contato com o fabricante do dispositivo para obter instruções adicionais de configuração e suporte.

## <a name="editing-device-configuration-samples"></a>Edição de exemplos de configuração de dispositivo
Depois de baixar o exemplo de configuração de dispositivo VPN fornecido, você precisará substituir alguns dos valores para refletir as configurações do seu ambiente.

**Para editar um exemplo:**

1. Abra o exemplo usando o Bloco de Notas.
2. Pesquise e substitua todas as cadeias de caracteres de <*texto*> por valores condizentes com seu ambiente. Inclua < e >. Quando um nome é especificado, o nome que você selecionar deve ser exclusivo. Se um comando não funcionar, consulte a documentação do fabricante do dispositivo.

| **Texto de exemplo** | **Alterar para** |
| --- | --- |
| &lt;RP_OnPremisesNetwork&gt; |O nome que você escolheu para este objeto. Exemplo: myOnPremisesNetwork |
| &lt;RP_AzureNetwork&gt; |O nome que você escolheu para este objeto. Exemplo: myAzureNetwork |
| &lt;RP_AccessList&gt; |O nome que você escolheu para este objeto. Exemplo: myAzureAccessList |
| &lt;RP_IPSecTransformSet&gt; |O nome que você escolheu para este objeto. Exemplo: myIPSecTransformSet |
| &lt;RP_IPSecCryptoMap&gt; |O nome que você escolheu para este objeto. Exemplo: myIPSecCryptoMap |
| &lt;SP_AzureNetworkIpRange&gt; |Especifique o intervalo. Exemplo: 192.168.0.0 |
| &lt;SP_AzureNetworkSubnetMask&gt; |Especificar máscara de sub-rede. Exemplo: 255.255.0.0 |
| &lt;SP_OnPremisesNetworkIpRange&gt; |Especifique o intervalo local. Exemplo: 10.2.1.0 |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; |Especifique a máscara de sub-rede local. Exemplo: 255.255.255.0 |
| &lt;SP_AzureGatewayIpAddress&gt; |Estas informações são específicas à sua rede virtual e estão localizadas no Portal de Gerenciamento como o **endereço IP do Gateway**. |
| &lt;SP_PresharedKey&gt; |Essas informações são específicas da sua rede virtual e estão localizadas no Portal de Gerenciamento como Gerenciar Chave. |

## <a name="ipsec-parameters"></a>Parâmetros IPsec
> [!NOTE]
> Embora os valores listados na tabela a seguir sejam suportados pelo Gateway de VPN do Azure, atualmente, não é possível especificar nem selecionar uma combinação específica no Gateway de VPN do Azure. Você deve especificar quaisquer restrições no dispositivo VPN local. Além disso, você deve fixar MSS em 1350.
>
>

### <a name="ike-phase-1-setup"></a>Fase 1 da configuração IKE
| **Propriedade** | **PolicyBased** | **Gateway de VPN RouteBased e Standard ou de Alto Desempenho** |
| --- | --- | --- |
| Versão IKE |IKEv1 |IKEv2 |
| Grupo Diffie-Hellman |Grupo 2 (1024 bits) |Grupo 2 (1024 bits) |
| Método de autenticação |Chave Pré-Compartilhada |Chave Pré-Compartilhada |
| Algoritmos de criptografia |AES256 AES128 3DES |AES256 3DES |
| Algoritmo de hash |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Tempo de vida (tempo) da SA (associação de segurança) da fase 1 |28.800 segundos |10.800 segundos |

### <a name="ike-phase-2-setup"></a>Fase 2 da configuração IKE
| **Propriedade** | **PolicyBased** | **Gateway de VPN RouteBased e Standard ou de Alto Desempenho** |
| --- | --- | --- |
| Versão IKE |IKEv1 |IKEv2 |
| Algoritmo de hash |SHA1(SHA128), SHA2(SHA256) |SHA1(SHA128), SHA2(SHA256) |
| Tempo de vida (tempo) da SA (associação de segurança) da fase 2 |3.600 segundos |3.600 segundos |
| Tempo de vida (produtividade) da SA (Associação de Segurança) da Fase 2 |102.400.000 KB |- |
| Ofertas de criptografia e de autenticação IPsec SA (em ordem de preferência) |1. ESP-AES256 2. ESP-AES128 3. ESP-3DES 4. N/D |Veja *Ofertas de SA (Associação de Segurança) de IPsec do gateway RouteBased* (abaixo) |
| PFS (Perfect Forward Secrecy) |Não |Não (*) |
| Detecção de par inativo |Sem suporte |Suportado |

(*) Gateway do Azure como Respondente IKE pode aceitar PFS DH Grupo 1, 2, 5, 14, 24.

### <a name="routebased-gateway-ipsec-security-association-sa-offers"></a>Ofertas de SA (Associação de Segurança) de IPsec do gateway RouteBased
A tabela a seguir lista as Ofertas de Criptografia e Autenticação IPsec SA. Ofertas estão listadas na ordem de preferência que a oferta é apresentada ou aceita.

| **Ofertas de criptografia e autenticação IPsec SA** | **Gateway do Azure como iniciador** | **Gateway do Azure como respondente** |
| --- | --- | --- |
| 1 |ESP AES_256 SHA |ESP AES_128 SHA |
| 2 |ESP AES_128 SHA |ESP 3_DES MD5 |
| 3 |ESP 3_DES MD5 |ESP 3_DES SHA |
| 4 |ESP 3_DES SHA |AH SHA1 com ESP AES_128 com HMAC nulo |
| 5 |AH SHA1 com ESP AES_256 com HMAC nulo |AH SHA1 com ESP 3_DES com HMAC nulo |
| 6 |AH SHA1 com ESP AES_128 com HMAC nulo |AH MD5 com ESP 3_DES com HMAC nulo, sem tempos de vida propostos |
| 7 |AH SHA1 com ESP 3_DES com HMAC nulo |AH SHA1 com ESP 3_DES SHA1, sem tempos de vida |
| 8 |AH MD5 com ESP 3_DES com HMAC nulo, sem tempos de vida propostos |AH MD5 com ESP 3_DES MD5, sem tempos de vida |
| 9 |AH SHA1 com ESP 3_DES SHA1, sem tempos de vida |ESP DES MD5 |
| 10 |AH MD5 com ESP 3_DES MD5, sem tempos de vida |ESP DES SHA1, sem tempos de vida |
| 11 |ESP DES MD5 |AH SHA1 com ESP DES HMAC nulo, sem tempos de vida propostos |
| 12 |ESP DES SHA1, sem tempos de vida |AH MD5 com ESP DES HMAC nulo, sem tempos de vida propostos |
| 13 |AH SHA1 com ESP DES HMAC nulo, sem tempos de vida propostos |AH SHA1 com ESP DES SHA1, sem tempos de vida |
| 14 |AH MD5 com ESP DES HMAC nulo, sem tempos de vida propostos |AH MD5 com ESP DES MD5, sem tempos de vida |
| 15 |AH SHA1 com ESP DES SHA1, sem tempos de vida |ESP SHA, sem tempos de vida |
| 16 |AH MD5 com ESP DES MD5, sem tempos de vida |ESP MD5, sem tempos de vida |
| 17 |- |AH SHA, sem tempos de vida |
| 18 |- |AH MD5, sem tempos de vida |

* Você pode especificar a criptografia NULL de IPsec ESP com gateways de VPN RouteBased e de Alto Desempenho. Criptografia com base em nulo não oferece proteção para dados em trânsito, devendo ser usada apenas quando for exigido o máximo de taxa de transferência com o mínimo de latência mínima.  Os clientes podem optar por usar isso nos cenários de comunicação entre VNets ou quando a criptografia está sendo aplicada em outro lugar na solução.
* Para a conectividade entre locais através da Internet, use as configurações padrão de gateway de VPN do Azure com criptografia e algoritmos de hash listados nas tabelas acima para garantir a segurança da comunicação crítica.

## <a name="a-nameknownaknown-device-compatibility-issues"></a><a name="known"></a>Problemas conhecidos de compatibilidade de dispositivos

> [!IMPORTANT]
> Estes são problemas conhecidos de compatibilidade entre dispositivos VPN de terceiros e gateways de VPN do Azure. A equipe do Azure está trabalhando ativamente com os fornecedores para resolver os problemas listados aqui. Depois que os problemas forem resolvidos, esta página será atualizada com as informações mais recentes. Verifique periodicamente.

###<a name="feb-16-2017"></a>16 de fevereiro de 2017

**Dispositivos Palo Alto Networks com versão anterior à 7.1.4** para VPN do Azure baseada em rota: se você estiver usando dispositivos VPN da Palo Alto Networks com versão do PAN-OS anterior à 7.1.4 e estiver com problemas de conectividade com os gateways de VPN do Azure baseados em rota, execute as seguintes etapas:

1. Verifique a versão do firmware do dispositivo Palo Alto Networks. Se a versão do PAN-OS for anterior à 7.1.4, atualize para a 7.1.4
2. No dispositivo Palo Alto Networks, altere o tempo de vida da SA da Fase 2 (ou SA de Modo Rápido) para 28.800 segundos (8 horas) ao conectar-se com o gateway de VPN do Azure
3. Se ainda estiver enfrentando o problema de conectividade, abra uma solicitação de suporte no Portal do Azure 

