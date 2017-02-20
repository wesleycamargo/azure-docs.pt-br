---
title: 'Locais e provedores de conectividade: Azure ExpressRoute | Microsoft Docs'
description: "Este artigo fornece uma visão geral detalhada dos locais onde os serviços são oferecidos e de como se conectar a regiões do Azure. Classificado por provedor de conectividade."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/13/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0afada6b5ade6c1f246b99e924a972cd7d34fdfa
ms.openlocfilehash: b17defc732be9f1fbdb816727d5602eb037b7e06


---
# <a name="expressroute-partners-and-peering-locations"></a>Locais de emparelhamento e parceiros da Rota Expressa

> [!div class="op_single_selector"]
> * [Locais por provedor](expressroute-locations.md)
> * [Provedores por local](expressroute-locations-providers.md)


As tabelas neste artigo fornecem informações sobre provedores de conectividade da Rota Expressa, a cobertura geográfica da Rota Expressa, serviços em nuvem da Microsoft com suporte ao longo da Rota Expressa e SIs (Integradores de Serviço) da Rota Expressa.

## <a name="a-namepartnersaexpressroute-connectivity-providers"></a><a name="partners"></a>Provedores de conectividade da Rota Expressa
Há suporte para Rota Expressa em todos os locais e regiões do Azure. O mapa a seguir fornece uma lista de locais de Rota Expressa e regiões do Azure. Os locais de Rota Expressa são aqueles onde a Microsoft emparelha-se a vários provedores de serviços.

![Mapa de localização][0]

Você terá acesso aos serviços do Azure em todas as regiões dentro de uma região geopolítica se estiver conectado a pelo menos um local de Rota Expressa dentro da região geopolítica.

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Regiões do Azure para locais de ExpressRoute em uma região geopolítica.
A tabela a seguir fornece um mapa das regiões do Azure para locais de Rota Expressa em uma região geopolítica.

| **Região Geopolítica** | **Regiões do Azure** | **Locais de Rota Expressa** |
| --- | --- | --- |
| **América do Norte** |Leste dos EUA, Oeste dos EUA, Leste dos EUA 2, Oeste dos EUA 2, Centro dos EUA, Centro-Sul dos EUA, Centro-Norte dos EUA, Centro-Oeste dos EUA, Centro do Canadá, Leste do Canadá |Atlanta, Chicago, Dallas, Las Vegas, Los Angeles, Nova York, Seattle, Vale do Silício, Washington, D.C., Montreal, Cidade de Quebec, Toronto |
| **América do Sul** |Sul do Brasil |São Paulo |
| **Europa** |Europa Setentrional, Europa Ocidental, Oeste do Reino Unido, Sul do Reino Unido |Amsterdã, Dublin, Londres, Newport(Gales), Paris |
| **Ásia** |Ásia Oriental, Sudeste Asiático |Hong Kong, Cingapura |
| **Japão** |Oeste do Japão, Leste do Japão |Osaka, Tóquio |
| **Austrália** |Sudeste da Austrália, Leste da Austrália |Melbourne, Sydney |
| **Índia** |Oeste da Índia, Índia Central, Sul da Índia |Chennai, Mumbai |
| **Coreia do Sul** |Coreia Central, Sul da Coreia |Busan, Seul |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Regiões e limites geopolíticos para nuvens nacionais
A tabela a seguir fornece informações sobre regiões e limites geopolíticos para nuvens nacionais.

| **Região Geopolítica** | **Regiões do Azure** | **Locais de Rota Expressa** |
| --- | --- | --- | --- |
| **Nuvem do Governo dos EUA** |Gov. EUA - Iowa, Gov. EUA - Virgínia, US DoD Central, US DoD Leste  |Chicago, Los Angeles, Nova York, Vale do Silício, Washington, D.C. |
| **China** |Norte da China, Leste da China |Pequim, Xangai |
| **Alemanha** |Alemanha Central, Alemanha Oriental |Berlim+, Frankfurt |

Não há suporte para conectividade entre regiões geopolíticas no SKU de Rota Expressa padrão. Você precisará habilitar o complemento premium de Rota Expressa para dar suporte a conectividade global. Não há suporte a conectividade para ambientes de nuvem nacionais. Você pode trabalhar com seu provedor de conectividade se surgir necessidade de fazê-lo.

## <a name="a-namelocationsaconnectivity-provider-locations"></a><a name="locations"></a>Locais de provedor de conectividade

A tabela a seguir mostra locais pelo provedor de serviços. Se você quiser exibir os provedores disponíveis por local, confira [Provedores de serviço por local](expressroute-locations-providers.md#locations).


### <a name="production-azure"></a>Produção do Azure
| **Provedor de serviços** | **Microsoft Azure** | **Office 365 e CRM Online** | **Locais** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |Suportado |Suportado |Melbourne, Sydney |
| **[Aryaka Networks](http://www.aryaka.com/)** |Suportado |Suportado |Amsterdã, Dallas, Vale do Silício, Cingapura, Tóquio, Washington, D.C. |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Suportado |Suportado |Amsterdã, Chicago, Dallas, Londres, Vale do Silício, Cingapura, Sydney, Washington, D.C. |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Suportado |Suportado |Toronto |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** |Suportado |Suportado |Amsterdã, Hong Kong, Londres, Vale do Silício, Cingapura, Sydney, Tóquio, Washington, D.C. |
| **[CenturyLink](http://www.centurylink.com/business/enterprise/services/data-network/mpls-vpn.html)** |Em breve |Em breve |Vale do Silício |
| **China Telecom Global** |Suportado |Sem suporte |Hong Kong |
| **[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |Suportado |Suportado |Dallas, Montreal, Toronto |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Suportado |Suportado |Amsterdã, Dublin, Londres, Tóquio |
| **Comcast** |Suportado |Suportado |Chicago, Vale do Silício, Washington D.C. |
| **Console**| Suportado | Suportado |Vale do Silício |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Suportado |Suportado |Los Angeles, Nova York |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Suportado |Suportado |Amsterdã, Atlanta, Chicago, Dallas, Hong Kong, Londres, Los Angeles, Melbourne, Nova York, Osaka, Paris+, São Paulo, Seattle, Vale do Silício, Cingapura, Sydney, Tóquio, Toronto, Washington, D.C. |
| **euNetworks** |Suportado |Suportado |Amsterdã |
| **Global Cloud Exchange (GCX)** | Em breve | Em breve | Chennai |
| **GÉANT** |Suportado |Suportado |Amsterdã |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Suportado |Suportado |Osaka, Tóquio |
| **[InterCloud](https://www.intercloud.com/)** |Suportado |Suportado |Amsterdã, Londres, Cingapura, Washington, D.C. |
| **Internet Solutions - Cloud Connect** |Suportado |Suportado |Amsterdã, Londres |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)** |Suportado |Suportado |Amsterdã, Londres, Paris |
| **Jisc** |Suportado |Suportado |Londres |
| **KINX** |Suportado |Suportado |Seul |
| **[KPN](http://www.kpn.com/cloudconnect)** | Suportado | Suportado | Amsterdã | 
| **[Comunicações de Nível 3](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Suportado |Suportado |Amsterdã, Chicago, Dallas, Las Vegas+, Londres, Seattle, Vale do Silício, Cingapura, Washington, D.C. |
| **LG CNS** |Em breve |Em breve |Busan+ |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Suportado |Suportado |Dallas, Hong Kong, Las Vegas, Los Angeles, Melbourne, Nova Iorque, Cidade de Quebec, Seattle, Cingapura, Sydney, Toronto, Washington D.C. |
| **MTN** |Suportado |Suportado |Londres |
| **[Dados da Próxima Geração](http://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Suportado |Suportado |Newport (País de Gales) |
| **NEXTDC** |Suportado |Suportado |Melbourne, Sydney |
| **[NTT Communications](http://www.ntt.com/en/services/network/virtual-private-network.html)** |Suportado |Suportado |Londres, Los Angeles, Osaka, Cingapura, Tóquio, Washington, D.C. |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** |Suportado |Suportado |Amsterdã, Hong Kong, Londres, Vale do Silício, Cingapura, Sydney, Washington, D.C. |
| **PCCW Global Limited** |Suportado |Suportado |Hong Kong |
| **Sejong Telecom** |Suportado |Suportado |Busan, Seul |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Suportado |Suportado |Chennai |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Suportado |Suportado |Cingapura |
| **Softbank** |Suportado |Suportado |Osaka, Tóquio |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Suportado |Suportado |Amsterdã, Chennai, Hong Kong, Londres, Mumbai, Vale do Silício, Cingapura, Washington, D.C. |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** |Suportado |Suportado |Amsterdã, Dublin, Londres |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Suportado |Suportado |São Paulo |
| **Telenor** |Suportado |Suportado |Amsterdã, Londres |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Suportado |Suportado |Melbourne, Sydney |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** |Suportado |Suportado |Amsterdã, Hong Kong, Londres, Vale do Silício, Cingapura, Sydney, Tóquio, Washington, D.C. |
| **[Vodafone](http://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Suportado |Sem suporte |Londres |
| **[Zayo Group](http://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Suportado |Suportado |Chicago, Los Angeles, Nova Iorque, Vale do Silício, Toronto, Washington, D.C. |

 **+** indica que haverá em breve

### <a name="national-cloud-environment"></a>Ambientes de nuvem nacionais

### <a name="us-government-cloud"></a>Nuvem do Governo dos EUA
| **Provedor de serviços** | **Microsoft Azure** | **Office 365** | **Locais** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Suportado |Suportado |Chicago, Washington D.C. |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Suportado |Suportado |Chicago, Los Angeles, Nova York, Vale do Silício, Washington, D.C. |
| **[Comunicações de Nível 3](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Suportado |Suportado |Chicago, Nova York+, Washington, D.C. |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Suportado | Suportado | Dallas |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Suportado |Suportado |Chicago, Dallas, Nova York, Washington, D.C. |

### <a name="china"></a>China
| **Provedor de serviços** | **Microsoft Azure** | **Office 365** | **Locais** |
| --- | --- | --- | --- |
| **China Telecom** |Suportado |Sem suporte |Pequim, Xangai |

Para saber mais, confira [Rota Expressa na China](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Alemanha
| **Provedor de serviços** | **Microsoft Azure** | **Office 365** | **Locais** |
| --- | --- | --- | --- |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Suportado |Sem suporte |Berlin+, Frankfurt |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Suportado |Sem suporte |Frankfurt |
| **e-shelter** |Suportado |Sem suporte |Berlim |
| **Interxion** |Suportado |Sem suporte |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Suportado  | Sem suporte | Berlim |

## <a name="a-namenonpartnersaconnectivity-through-service-providers-not-listed"></a><a name="nonpartners"></a>Conectividade por meio de provedores de serviços não listados
Se seu provedor de conectividade não estiver listado em seções anteriores, você ainda pode criar uma conexão.

* Verifique com seu provedor de conectividade para ver se eles estão conectados a qualquer um dos Exchanges na tabela acima. Você pode verificar os links a seguir para obter mais informações sobre os serviços oferecidos por provedores do Exchange. Vários provedores de conectividade já estão conectados a Exchanges de Ethernet.
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](http://www.nextdc.com/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* Faça com que seu provedor de conectividade estenda sua rede para o local de emparelhamento de sua escolha.
  * Certifique-se de que seu provedor de conectividade estenda sua conectividade de maneira altamente disponível para que não exista nenhum ponto de falha.
* Solicite um circuito da Rota Expressa com o Exchange como o provedor de conectividade para conectar-se à Microsoft.
  * Siga as etapas em [Criar um circuito da Rota Expressa](expressroute-howto-circuit-classic.md) para configurar a conectividade.

| **Provedor de conectividade** | **Exchange** | **Locais** |
| --- | --- | --- |
| **[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Cingapura |
| **[Arteria Networks Corporation](https://arteria-net.com/business/service/cloud_access/sca/)** |Equinix |Tóquio |
| **[Alaska Communications](http://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Cogeco Peer 1](https://www.cogecopeer1.com/en/)**| Equinix | Montreal, Toronto |
| **[Dados Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amsterdã |
| **[E Exponencial](http://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Londres |
| **[HSO](http://www.hso.co.uk/products/cloud-direct)** |Equinix | Londres, Slough |
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix |Nova Iorque, Washington, D.C. |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington, D.C. |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | Amsterdã, Frankfurt |  
| **[Transtelco](http://www.transtelco.net/tcloud/microsoft)** |Equinix | Dallas, Los Angeles |  
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Frankfurt |  
| **[Windstream](http://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix |Chicago, Vale do Silício, Washington D.C. |
| **[XO Communications](http://www.xo.com/)** |Equinix |Vale do Silício |
| **[Zertia](http://www.zertia.es/index.php/novedades)**| Nível 3 | Madri |


## <a name="expressroute-system-integrators"></a>Integradores de sistema de Rota Expressa
Habilitar a conectividade privada para atender às suas necessidades pode ser desafiador, dependendo da escala de sua rede. Você pode trabalhar com qualquer dos integradores de sistema listados na tabela a seguir para ajudá-lo com integração à Rota Expressa.

| **Integrador de Sistema** | **Continent** |
| --- | --- |
| **[Avanade Inc.](http://www.avanade.com/)** | Ásia, Europa, América do Norte, América do Sul |
| **[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)** | Europa |
| **[Equinix Professional Services](http://www.equinix.com/services/consulting/)** | América do Norte |
| **[O grupo de consultoria de TI](http://itconsult.com.au/microsoft-expressroute)** | Austrália |
| **[Serviços de Mensagem](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europa (Alemanha) |
| **[Nelite](http://nelite.com/)** | Europa |
| **[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | Ásia |
| **[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | América do Norte |
| **[Project Leadership](http://www.projectleadership.net/azure)** | América do Norte |


## <a name="next-steps"></a>Próximas etapas
* Para obter mais informações sobre a Rota Expressa, consulte [Perguntas Frequentes sobre Rota Expressa](expressroute-faqs.md).
* Certifique-se que todos os pré-requisitos foram atendidos. Consulte [Pré-requisitos da Rota Expressa](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa de localização"



<!--HONumber=Feb17_HO2-->


