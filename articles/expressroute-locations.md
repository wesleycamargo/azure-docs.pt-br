<properties
   pageTitle="Locais de Rota Expressa"
   description="Esta página fornece uma visão geral detalhada dos locais onde os serviços são oferecidos e de como se conectar a regiões do Azure."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" /> <tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/29/2015"
   ms.author="cherylmc" />

# Locais de Emparelhamento e Parceiros da Rota Expressa
Esta tabela fornece detalhes sobre

1. Provedores de conectividade de Rota Expressa (EXPs e NSPs)
2. Cobertura geográfica da Rota Expressa
3. Serviços em nuvem da Microsoft com suporte na Rota Expressa
4. SIs (Integradores de sistema) de Rota Expressa

## Provedores de conectividade de Rota Expressa
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
|**Austrália**|Sudeste da Austrália, Leste da Austrália|Sydney|

Não há suporte para a conectividade entre regiões geopolíticas. Você pode trabalhar com seu provedor de conectividade para estender a conectividade entre regiões geopolíticas usando a rede desse provedor.


### Locais de EXP (provedor do Exchange)
- Consulte esta [tabela](https://msdn.microsoft.com/library/azure/4da69a0f-8f52-49ea-a990-dacd4202150a#BKMK_EXP) para obter uma lista de provedores do Exchange e locais onde eles têm suporte.
-  Consulte [Configurar sua conexão EXP](expressroute-configuring-exps.md) para conhecer as etapas a seguir para configurar sua conexão.

### Locais do NSP (provedor de serviços de rede)
- Consulte esta [tabela](https://msdn.microsoft.com/library/azure/4da69a0f-8f52-49ea-a990-dacd4202150a#BKMK_NSP) para obter uma lista de provedores de serviços de rede e locais onde eles têm suporte.
- Consulte [Configurar sua conexão NSP](expressroute-configuring-nsps.md) para conhecer as etapas a seguir para configurar sua conexão.

### Conectividade por meio de provedores de serviços não listados acima

Se seu provedor de conectividade não estiver nas seções listadas acima, você ainda pode criar uma conexão.

- Verifique com seu provedor de conectividade para ver se eles estão conectados a qualquer um dos provedores Exchange nos locais de EXP listados. Você pode verificar os links abaixo para obter mais informações sobre os serviços oferecidos por provedores do Exchange. Vários provedores de conectividade já estão conectados a Exchanges de Ethernet dos EXPs.
	- [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/) 
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
- Faça com que seu provedor de conectividade estenda sua rede para o local do Exchange de sua escolha.
	- Certifique-se de que seu provedor de conectividade estenda sua conectividade de maneira altamente disponível para que não exista nenhum ponto de falha.
	- Provedores de conectividade (especificamente provedores Ethernet) podem exigir a aquisição de um par de circuitos para Ethernet Exchanges, para garantir alta disponibilidade 
- Solicite um circuito de Rota Expressa por meio do provedor do Exchange para se conectar ao Azure
	- Siga as etapas em [Configurar sua conexão EXP](expressroute-configuring-exps.md) para configurar a conectividade.

|**Provedor de conectividade**|**Provedores do Exchange**|**Locais de emparelhamento**|
|---|---|---|
|**[Comunicações XO](http://www.xo.com/)**|Equinix|Vale do Silício|


## Serviços em nuvem da Microsoft e Rota Expressa
As tabelas abaixo fornecem detalhes sobre os provedores de conectividade e a lista de serviços em nuvem da Microsoft aos quais esses provedores dão suporte. Entre em contato com seu provedor de serviços

**EXPs (Provedores do Exchange)**

|**Provedor de Serviços**|**Serviços do Microsoft Azure**|**Serviços do Office 365**|
|---|---|---|
|**Aryaka**|Suportado||
|**Colt Ethernet**|Suportado||
|**Equinix**|Suportado|Em breve|
|**InterCloud**|Suportado||
|**Serviço EVPL da Level 3**|Suportado||
|**Grupo TeleCity**|Suportado||
|**Grupo Zayo**|Suportado||

**NSPs (Provedores de Serviços de Rede)**

|**Provedor de Serviços**|**Serviços do Microsoft Azure**|**Serviços do Office 365**|
|---|---|---|
|**AT&T**|Suportado|Em breve|
|**British Telecom**|Suportado|Em breve|
|**Colt IPVPN**|Suportado||
|**Internet Initiative Japan Inc. - IIJ**|Suportado||
|**Level 3 IPVPN**|Suportado||
|**Orange**|Suportado|| 
|**SingTel**|Suportado||
|**Tata Communications**|Suportado||
|**Telstra Corporation**|Suportado||
|**Verizon**|Suportado|| 


## Integradores de sistema de Rota Expressa
Habilitar a conectividade privada para atender às suas necessidades pode ser desafiador, dependendo da escala de sua rede. Você pode trabalhar com qualquer dos integradores de sistema listados na tabela a seguir para ajudá-lo com integração à Rota Expressa.


|**Integrador de Sistema**|**Continent**|
|---|---|
|**[Nimbo](http://www.nimbo.com/)**|EUA||
|**[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)**|EMEA|

## Próximas etapas
- Verifique se você satisfaz os [Pré-requisitos para Rota Expressa](expressroute-prerequisites.md).
- Visite as [Perguntas Frequentes](expressroute-faqs.md) para obter mais informações.
- Selecione seu provedor e configure sua conexão. Consulte [Configurar sua conexão EXP](expressroute-configuring-exps.md) ou [Configurar sua conexão NSP](expressroute-configuring-nsps.md) para informações de configuração.
<!--HONumber=54-->