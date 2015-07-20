<properties
   pageTitle="Locais de Rota Expressa"
   description="Esta página fornece uma visão geral detalhada dos locais onde os serviços são oferecidos e de como se conectar a regiões do Azure."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/07/2015"
   ms.author="cherylmc" />

# Locais de emparelhamento e parceiros da Rota Expressa
As tabelas nesta página fornecem informações sobre provedores de conectividade da Rota Expressa (EXPs e NSPs), a cobertura geográfica da Rota Expressa, serviços em nuvem da Microsoft com suporte ao longo da Rota Expressa e SIs (integradores de serviço) da Rota Expressa.

## Provedores de conectividade da Rota Expressa
Há suporte para Rota Expressa em todos os locais e regiões do Azure. O mapa a seguir fornece uma lista de locais de Rota Expressa e regiões do Azure. Os locais de Rota Expressa são aqueles onde a Microsoft emparelha-se a vários provedores de serviços.
 
![](./media/expressroute-locations/expressroute-locations-map.png)

Você terá acesso aos serviços do Azure em todas as regiões dentro de uma região geopolítica se estiver conectado a pelo menos um local de Rota Expressa dentro da região geopolítica. A tabela a seguir fornece um mapa das regiões do Azure para locais de Rota Expressa em uma região geopolítica.

|**Região Geopolítica**|**Regiões do Azure**|**Locais de Rota Expressa**|
|---|---|---|
|**EUA**|Todas as regiões dos EUA - Leste dos EUA, Oeste dos EUA, Leste dos EUA 2, Centro dos EUA, Centro-Sul dos EUA, Centro-Norte dos EUA|Atlanta, Chicago, Dallas, Los Angeles, Nova York, Seattle, Vale do Silício, Washington, D.C.|
|**América do Sul**|Sul do Brasil|São Paulo|
|**Europa**|Norte da Europa, Europa Ocidental|Amsterdã, Londres|
|**Ásia**|Ásia Oriental, Sudeste Asiático|Hong Kong, Cingapura|
|**Japão**|Oeste do Japão, Leste do Japão|Tóquio|
|**Austrália**|Sudeste da Austrália, Leste da Austrália|Melbourne, Sydney|
|**Índia**|Oeste da Índia, Índia Central, Sul da Índia|Chennai, Mumbai|

Não há suporte para a conectividade entre regiões geopolíticas. Você pode trabalhar com seu provedor de conectividade para estender a conectividade entre regiões geopolíticas usando a rede desse provedor.


## Locais de EXP (provedor do Exchange)

| **Provedor de Serviços** |**Microsoft Azure** | **Office 365** | **Locais** |
|-----------------------|--------------------|----------------|---------------|
| **[Aryaka Networks](http://www.aryaka.com/)** | Suportado | Sem suporte | Vale do Silício, Cingapura, Washington, D.C. |
| **[Colt Ethernet](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Suportado | Sem suporte | Amsterdã, Londres |
| **Comcast** | Suportado | Sem suporte | Vale do Silício, Washington, D.C. |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Suportado | Em breve | Amsterdã, Atlanta, Chicago, Dallas, Hong Kong, Londres, Los Angeles, Londres, Nova York, São Paulo, Seattle, Vale do Silício, Cingapura, Sydney, Tóquio, Washington, D.C. |
| **[InterCloud](https://www.intercloud.com/)** | Suportado | Sem suporte | Amsterdã, Londres, Cingapura, Washington, D.C. |
| **Internet Solutions - Cloud Connect** | Suportado | Sem suporte | Amsterdã, Londres |
| **Interxion** | Suportado | Sem suporte | Amsterdã |
| **[Comunicações de nível 3 - Exchange](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Suportado | Sem suporte | Chicago, Dallas, Londres, Seattle, Vale do Silício, Washington, D.C. |
| **NEXTDC** | Suportado | Sem suporte | Melbourne, Sydney+ |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | Suportado | Em breve | Amsterdã, Londres |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Suportado | Sem suporte | Melbourne+, Sydney |
| **[Zayo Group](http://www.zayo.com/)** | Suportado | Sem suporte | Washington, D.C. |

 **+** indica que haverá em breve

Consulte [Configurar sua conexão EXP](expressroute-configuring-exps.md) para ver as etapas de como configurar sua conexão.

## Locais do NSP (provedor de serviços de rede)


| **Provedor de Serviços** |**Microsoft Azure** | **Office 365** | **Locais** |
|-----------------------|--------------------|----------------|---------------|
| **[AT&T](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Suportado | Em breve | Amsterdã +, Londres + Dallas, Vale do Silício, Washington, D.C. |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | Suportado | Em breve | Amsterdã, Londres, Vale do Silício+, Washington, D.C. |
|**China Telecom Global** | Em breve | Sem suporte | Hong Kong+ |
| **[Colt IPVPN](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Suportado | Sem suporte | Amsterdã, Londres |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2013/pdf/Azure_E.pdf)** | Suportado | Sem suporte | Tóquio |
| **[Comunicações de nível 3 - IPVPN](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Suportado | Sem suporte | Chicago, Dallas, Londres, Seattle, Vale do Silício, Washington, D.C. |
| **NTT Communications** | Em breve | Sem suporte | Tóquio + | 
| **[Orange](http://www.orange-business.com/)** | Suportado | Sem suporte | Amsterdã, Londres, Vale do Silício, Washington, D.C. |
| **PCCW Global Limited** | Suportado | Sem suporte | Hong Kong |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** | Suportado | Sem suporte | Cingapura |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | Suportado | Em breve | Amsterdã, Madras +, Hong Kong, Londres, Mumbai +, Cingapura |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Suportado | Sem suporte | Melbourne+, Sydney |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | Suportado | Sem suporte | Londres, Hong Kong, Vale do Silício, Washington, D.C. |

 **+** indica que haverá em breve

Consulte [Configurar sua conexão NSP](expressroute-configuring-nsps.md) para conhecer as etapas a seguir para configurar sua conexão.

## Conectividade por meio de provedores de serviços não listados 

Se seu provedor de conectividade não estiver nas seções listadas acima, você ainda pode criar uma conexão.

- Verifique com seu provedor de conectividade para ver se eles estão conectados a qualquer um dos provedores Exchange nos locais de EXP listados. Você pode verificar os links abaixo para obter mais informações sobre os serviços oferecidos por provedores do Exchange. Vários provedores de conectividade já estão conectados a Exchanges de Ethernet dos EXPs.
	- [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/) 
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
- Faça com que seu provedor de conectividade estenda sua rede para o local do Exchange de sua escolha.
	- Certifique-se de que seu provedor de conectividade estenda sua conectividade de maneira altamente disponível para que não exista nenhum ponto de falha.
	- Provedores de conectividade (especificamente provedores Ethernet) podem exigir a aquisição de um par de circuitos para Ethernet Exchange, para garantir alta disponibilidade. 
- Solicite um circuito de Rota Expressa por meio do provedor do Exchange para se conectar ao Azure.
	- Siga as etapas em [Configurar sua conexão EXP](expressroute-configuring-exps.md) para configurar a conectividade.

|**Provedor de conectividade**|**Provedores do Exchange**|**Locais de emparelhamento**|
|---|---|---|
|**[Comunicações XO](http://www.xo.com/)**|Equinix|Vale do Silício|

## Integradores de sistema de Rota Expressa
Habilitar a conectividade privada para atender às suas necessidades pode ser desafiador, dependendo da escala de sua rede. Você pode trabalhar com qualquer dos integradores de sistema listados na tabela a seguir para ajudá-lo com integração à Rota Expressa.


|**Integrador de Sistema**|**Continent**|
|---|---|
|**[Nimbo](http://www.nimbo.com/)**|EUA||
|**[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)**|EMEA|

## Próximas etapas
- Verifique se você satisfaz os [Pré-requisitos para Rota Expressa](expressroute-prerequisites.md).
- Visite as [Perguntas Frequentes](expressroute-faqs.md) para obter mais informações.
- Se você deseja configurar uma conexão Rota Expressa, consulte [Configurar sua conexão EXP](expressroute-configuring-exps.md) ou [Configurar sua conexão NSP](expressroute-configuring-nsps.md).
- Se você deseja configurar uma conexão site a site VPN e uma Rota Expressa para a mesma rede virtual, consulte [Configurar conexões de VPN Site a Site e de Rota Expressa que coexistam](expressroute-coexist.md).
 

<!---HONumber=July15_HO2-->