<properties 
   pageTitle="Introdução à Rota Expressa"
   description="Esta página fornece uma visão geral do serviço de Rota Expressa."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="04/29/2015"
   ms.author="cherylmc"/>

# Visão Geral Técnica da Rota Expressa

A Rota Expressa do Microsoft Azure permite a criação de conexões privadas entre os datacenters da Microsoft e a infraestrutura no seu local ou em um ambiente de colocalização. Com a Rota Expressa, é possível estabelecer conexões com os serviços em nuvem da Microsoft como o Microsoft Azure e Office 365 em uma instalação de colocalização de parceiro da Rota Expressa, ou conectar-se diretamente pela rede WAN existente (como VPN MLPS fornecidas por um provedor de serviços de rede).
 
As conexões de Rota Expressa oferecem maior segurança, mais confiabilidade, velocidades maiores e latências menores do que conexões típicas pela Internet. Em alguns casos, o uso de conexões de Rota Expressa para transferir dados entre sua rede local e o Azure também pode proporcionar relações custo/benefício significativas. Se você já tiver criado uma conexão entre locais de sua rede local para o Azure, você pode migrar para uma conexão de Rota Expressa, mantendo a sua rede virtual intacta.

Consulte as [Perguntas Frequentes sobre Rota Expressa](expressroute-faqs.md) para obter mais detalhes.

## Como funciona uma conexão de Rota Expressa?

Para conectar sua WAN a serviços em nuvem da Microsoft, você deve solicitar um circuito dedicado e tê-lo habilitado por meio de um provedor de conectividade. Há dois tipos de provedor de conectividade entre os quais escolher: camada direta 3 por meio de um provedor do Exchange (EXP) ou de camada 3 por meio de um provedor de serviços de rede (NSP). Você pode optar por habilitar um ou ambos os tipos de conectividade entre sua WAN e a nuvem da Microsoft.

## Provedores de Serviços de Rede e Provedores do Exchange
Provedores de Rota Expressa são classificados como NSPs (provedores de serviços de rede) e EXPs (provedores do Exchange).

![](./media/expressroute-introduction/expressroute-nsp-exp.png)


| |**Provedor do Exchange**|**Provedor de Serviços de Rede**|
|---|---|---|
|**Modelo típico de conectividade**| Conexões Ethernet ponto a ponto ou conectividade em um Cloud Exchange | Conectividade de qualquer tipo a qualquer tipo por meio de uma VPN de telecomunicações |
|**Larguras de banda com suporte**|200 Mbps, 500 Mbps, 1 Gbps e 10 Gbps|10 Mbps, 50 Mbps, 100 Mbps, 500 Mbps, 1 Gbps|
|**Provedores de conectividade**|[página Provedores do Exchange](https://msdn.microsoft.com/library/azure/4da69a0f-8f52-49ea-a990-dacd4202150a#BKMK_EXP)|[página Provedores de Serviços de Rede](https://msdn.microsoft.com/library/azure/4da69a0f-8f52-49ea-a990-dacd4202150a#BKMK_NSP)|
|**Roteamento**|Sessões BGP diretamente com roteadores de borda do cliente| Sessões BGP com telecomunicações|
|**Preços**|[Preços de EXP](http://azure.microsoft.com/pricing/details/expressroute/)|[Preços de NSP](http://azure.microsoft.com/pricing/details/expressroute/)|

### EXPs (Provedores do Exchange)
Temos parceria com provedores de serviços de nuvem do Exchange como grupo Equinix e TeleCity e também com provedores de serviços de conectividade ponto a ponto, como Cole e Level 3, para oferecer conectividade entre o Azure e o local do cliente. Oferecemos larguras de banda de circuito de 200 Mbps a 10 Gbps (200 Mbps, 500 Mbps, 1 Gbps e 10 Gbps).

Se você quiser uma conexão de camada direta 3 através de um provedor do Exchange, você pode fazer isso de uma entre três maneiras diferentes:

- Você pode ser colocalizado com os Cloud Exchanges, como Cloud Exchange do Equinix ou Cloud IX da TeleCity, nos locais em que oferecemos serviços. Nesses casos, você solicitará conectividade redundante para o Cloud Exchange. 
- Você pode trabalhar com provedores de Ethernet como Level 3 para instalar circuitos de Ethernet entre seus datacenters e a Microsoft. 
- Você pode trabalhar com seu provedor de conectividade local para obter conectividade redundante para a instalação do provedor do Exchange mais próxima e se conectar ao Cloud Exchange.

É requerido que você tenha conectividade redundante para atender aos requisitos de nosso SLA. Não há suporte para conectividade direta com o Microsoft Edge. Circuitos dedicados serão sempre habilitados por meio de um provedor de Ethernet ou do Cloud Exchange local. Enquanto isso configura a conectividade de camada 2 entre a Microsoft e sua rede, nós não damos suporte à extensão do domínio de camada 2. Para ter conectividade de camada 3, você deve configurar sessões de roteamento redundante entre seus os roteadores de borda e os roteadores de borda da Microsoft.

Para obter mais informações sobre a configuração e para ver exemplos do mundo real, você pode seguir este guia passo a passo: [Configurar uma conexão de Rota Expressa por meio de um EXP](expressroute-configuring-exps.md).


### NSPs (Provedores de Serviços de Rede)

Estabelecemos parcerias com empresas de telecomunicações como AT&T e British Telecom para oferecer conectividade entre o Azure e sua WAN. Oferecemos larguras de banda de circuito de 10 Mbps a 1 Gbps (10 Mbps, 50 Mbps, 100 Mbps, 500 Mbps e 1 Gbps).

Se você usar os serviços de qualquer um dos provedores de serviços de rede VPN com os quais temos parceria, eles podem estender as redes ao Azure sem precisar implantar nenhum novo hardware nem fazer alterações importantes de configuração em suas redes existentes.

Para obter mais informações sobre a configuração e para ver exemplos do mundo real, você pode seguir este guia passo a passo: [Configurar circuitos de Rota Expressa por meio de NSPs](expressroute-configuring-nsps.md).

## Emparelhamentos de Rota Expressa
A figura abaixo fornece uma representação lógica de conectividade entre sua WAN e a Microsoft. Para conectar sua WAN à Microsoft por meio de um provedor de conectividade (NSP/EXP), você deve solicitar um “circuito dedicado”. Um "circuito dedicado" representa uma conexão lógica entre sua WAN e a Microsoft por meio do provedor de conectividade. Você pode solicitar vários circuitos dedicados, todos eles podem estar na mesma região ou em regiões diferentes e podem ser conectados à sua WAN por meio de provedores de serviços diferentes.

![](./media/expressroute-introduction/expressroute-basic.png)

Um circuito dedicado terá vários domínios de roteamento associados a ele - públicos, privados e da Microsoft. Cada um dos domínios de roteamento é configurado de modo idêntico em um par de roteadores (na configuração ativo-ativo ou de compartilhamento de carga) para alta disponibilidade.

![](./media/expressroute-introduction/expressroute-peerings.png)


### Emparelhamento privado
IaaS (serviços de computação do Azure, ou seja, máquinas virtuais) e PaaS (serviços de nuvem) implantados em uma rede virtual podem ser conectados por meio de domínio de emparelhamento privado. O domínio de emparelhamento privado é considerado uma extensão confiável de sua rede principal para o Microsoft Azure. Você pode configurar a conectividade bidirecional entre sua rede principal e VNets (redes virtuais do Azure). Isso permitirá a você se conectar a máquinas virtuais e serviços de nuvem diretamente em seus endereços IP privados.

Você pode conectar mais de uma rede virtual ao domínio de emparelhamento privado. Examine a [Página de perguntas Frequentes](expressroute-faqs.md) para obter informações sobre limites e limitações.
  

### Emparelhamento público
Serviços como o Armazenamento do Azure, Sites e Bancos de dados SQL são oferecidos em endereços IP públicos. Você pode se conectar de modo privado a serviços hospedados em endereços IP públicos (incluindo VIPs de seus serviços de nuvem) por meio do domínio de roteamento de emparelhamento público. É possível conectar o domínio de emparelhamento público à sua extranet e conectar-se a todos os serviços do Azure em seus endereços IP públicos de sua WAN sem precisar se conectar por meio da Internet. A conectividade é sempre iniciada por meio de sua WAN para serviços do Microsoft Azure. Os serviços do Microsoft Azure não poderão iniciar conexões a sua rede por meio desse domínio de roteamento. Depois que o emparelhamento público for habilitado, você poderá se conectar a todos os serviços do Azure (não permitimos que você escolha seletivamente serviços para os quais anunciamos rotas). Você pode examinar a lista de prefixos que anunciamos para você por meio desse emparelhamento na página [Intervalos de IP do Datacenter do Microsoft Azure](http://www.microsoft.com/pt-br/download/details.aspx?id=41653). Você pode definir filtros de rota personalizados dentro da sua rede para consumir apenas as rotas que você precisa.

Examine a [Página de perguntas Frequentes](expressroute-faqs.md) para obter mais informações sobre serviços de suporte por meio do domínio de roteamento de emparelhamento público.
 
### Emparelhamento da Microsoft
A conectividade com todos os outros serviços online da Microsoft (como serviços do Office 365) será por emparelhamento da Microsoft. Habilitamos a conectividade bidirecional entre sua WAN e os serviços em nuvem da Microsoft por meio do domínio de roteamento de emparelhamento da Microsoft. Você deve se conectar aos serviços em nuvem da Microsoft somente em endereços IP públicos que pertençam a você ou seu provedor de conectividade, e deve seguir todas as regras que definirmos. Examine a página [Pré-requisitos de Rota Expressa](expressroute-prerequisites.md) para obter mais informações.

Examine a [Página de perguntas Frequentes](expressroute-faqs.md) para obter mais informações sobre serviços com suporte, custos e detalhes de configuração. Examine a página [Locais de Rota Expressa](expressroute-locations.md) para obter informações sobre a lista de provedores de conectividade que dão suporte a emparelhamento da Microsoft.


A tabela abaixo compara os três domínios de roteamento.

||**Emparelhamento privado**|**Emparelhamento público**|**Emparelhamento da Microsoft**| 
|---|---|---|---| 
|**N°. máx. de prefixos com suporte por emparelhamento**|4.000 por padrão, 10.000 com Rota Expressa Premium|4.000 por padrão, 10.000 com Rota Expressa Premium|200| 
|**Intervalos de endereços IP com suporte**|Qualquer endereço IPv4 válido contido em sua WAN|Endereços IPv4 públicos pertencentes a você ou seu provedor de conectividade|Endereços IPv4 públicos pertencentes a você ou seu provedor de conectividade| 
|**Solicitações de números AS**|Números AS privados e públicos. Cliente deve possuir número AS público. | Números AS públicos e privados. Cliente deve possuir número AS público. | Somente números AS públicos. O número AS deve ser validado em relação a registros de roteamento para validar a propriedade.| 
|**Endereços IP de Interface de Roteamento**|Endereços IP públicos e RFC1918|Endereços IP públicos registrados para clientes / NSP nos registros de roteamento.| Endereços IP públicos registrados para clientes / NSP nos registros de roteamento.| 
|**Suporte a hash do MD5**| Sim|Sim|Sim|

Você pode optar por habilitar um ou mais dos domínios de roteamento como parte do seu circuito dedicado. Você pode optar por ter todos os domínios de roteamento colocados na mesma VPN (para o caso do NSP) caso queiram inseri-los em um único domínio de roteamento. Você também pode colocá-los em diferentes domínios de roteamento, de modo similar ao representado no diagrama acima. A configuração recomendada é conectar o emparelhamento privado diretamente à sua rede principal, enquanto os links de emparelhamento público e da Microsoft devem ser conectados à sua extranet.
 
Se você optar por ter todas as três sessões de emparelhamento, você deve ter três pares de sessões BGP (um par para cada tipo de emparelhamento). Os pares de sessões BGP fornecem um link altamente disponível. Se você estiver se conectando por meio de EXPs, você será responsável por configurar e gerenciar o roteamento (a menos que o EXP se ofereça para gerenciar o roteamento para você). Se você decidir se conectar por meio de NSPs, poderá contar com o NSP para gerenciar o roteamento para você. Você pode aprender mais examinando os fluxos de trabalho para configuração da Rota Expressa

- [Configurar uma conexão de Rota Expressa por meio de um provedor de serviços de rede](expressroute-configuring-nsps.md)
- [Configurar uma conexão de Rota Expressa por meio de um provedor do Exchange](expressroute-configuring-exps.md)

## Próximas etapas

- [Locais e provedores de serviços de Rota Expressa](expressroute-locations.md) 
- [Configurar uma conexão de Rota Expressa por meio de um provedor de serviços de rede](expressroute-configuring-nsps.md)
- [Configurar uma conexão de Rota Expressa por meio de um provedor do Exchange](expressroute-configuring-exps.md)

<!--HONumber=54-->