---
title: Sobre o gateway VPN para a pilha do Azure | Microsoft Docs
description: "Saiba mais sobre e configurar os gateways de VPN, que você pode usar com a pilha do Azure."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 0e30522f-20d6-4da7-87d3-28ca3567a890
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/01/2017
ms.author: brenduns
ms.openlocfilehash: ba9642d8c51f57623aded44b84d7127334806bc1
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2017
---
# <a name="about-vpn-gateway-for-azure-stack"></a>Sobre o gateway VPN para a pilha do Azure
*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*


Antes de poder enviar tráfego de rede entre sua rede virtual do Azure e seu site local, você deve criar um gateway de rede virtual para sua rede virtual.

Um gateway de VPN é um tipo de gateway de rede virtual que envia o tráfego criptografado em uma conexão pública. Você pode usar gateways de VPN para enviar o tráfego com segurança entre uma rede virtual na pilha do Azure e uma rede virtual no Azure, ou entre uma rede virtual e a outra rede que esteja conectado a um dispositivo VPN.

Ao criar um gateway de rede virtual, especifique o tipo de gateway que você deseja criar. A pilha do Azure oferece suporte a um tipo de gateway de rede virtual: o tipo 'Vpn'.

Cada rede virtual pode ter dois gateways de rede virtual, mas somente um de cada tipo. Dependendo das configurações que você escolher, é possível criar várias conexões a um único gateway de VPN. Um exemplo disso é uma configuração de conexão de vários sites.

> [!NOTE]
> No Azure, a taxa de transferência de largura de banda para a SKU de Gateway de VPN você escolher deve ser dividida em todas as conexões que estão conectadas a ele.  Na pilha do Azure, o valor de largura de banda para a SKU de Gateway de VPN é aplicado a cada recurso de Conexão que está conectado a ele.     

> Por exemplo, no Azure, o SKU de Gateway de VPN básica pode acomodar aproximadamente 100 Mbps de taxa de transferência agregada.  Se você criar duas conexões para esse Gateway de VPN e uma conexão está usando de 50 Mbps de largura de banda, em seguida, 50 Mbps está disponível para a outra Conexão.   

> Na pilha do Azure, *cada* Conexão para a SKU de Gateway VPN básico é alocado de taxa de transferência de 100 Mbps.

## <a name="configuring-a-vpn-gateway"></a>Configurando um Gateway de VPN
Uma conexão de gateway VPN conta com vários recursos que são configurados com definições específicas. A maioria dos recursos pode ser configurada separadamente, embora eles devam ser configurados em uma determinada ordem em alguns casos.

### <a name="settings"></a>Configurações
As configurações que você escolheu para cada recurso são essenciais para a criação de uma conexão bem-sucedida. Para obter informações sobre configurações de Gateway de VPN e recursos individuais, consulte [configurações de gateway de VPN sobre para pilha Azure](azure-stack-vpn-gateway-settings.md). Você pode encontrar informações para ajudá-lo a entender os tipos de gateway, tipos de VPN, os tipos de conexão, sub-redes de gateway, gateways de rede local e várias outras configurações de recursos que você pode considerar.

### <a name="deployment-tools"></a>Ferramentas de implantação
Você pode começar criando e configurando os recursos usando uma ferramenta de configuração, como o portal do Azure. Você pode, posteriormente, decidir trocar para outra ferramenta, como o PowerShell, para configurar recursos adicionais ou modificar os recursos existentes, quando aplicável. Atualmente, não é possível configurar cada recurso e definição de recursos no portal do Azure. As instruções nos artigos para cada topologia de conexão especificam quando uma ferramenta de configuração específica é necessária.

## <a name="connection-topology-diagrams"></a>Diagramas de topologia de Conexão
É importante saber que há diferentes configurações disponíveis para conexões de gateway de VPN. Você precisa determinar qual configuração melhor atende às suas necessidades. Nas seções a seguir, você pode exibir diagramas de topologia e informações sobre as conexões de gateway de VPN a seguir: as seções a seguir contêm tabelas que listam:

- Modelo de implantação disponível
- Ferramentas de configuração disponíveis
- Links que levam você diretamente a um artigo, se disponível

Os diagramas e descrições nas seções a seguir podem ajudá-lo a selecionar uma topologia de conexão para atender às suas necessidades. Os diagramas mostram as principais topologias de linha de base, mas é possível criar topologias mais complexas usando os diagramas como uma diretriz.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Site a Site e Multissite (túnel VPN IPsec/IKE)
### <a name="site-to-site"></a>Site a site
Uma conexão de gateway VPN Site a Site (S2S) é uma conexão por túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Esse tipo de conexão exige um dispositivo VPN local com um endereço IP público atribuído a ele e não por uma NAT. As conexões S2S podem ser usadas para configurações entre instalações e híbridas.    
![Exemplo de configuração de conexão de VPN site a site](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>Multissite
Esse tipo de conexão é uma variação da conexão Site a Site. Você pode criar mais de uma conexão de VPN em seu gateway de rede virtual, normalmente se conectando a vários sites locais. Ao trabalhar com várias conexões, você deve usar um tipo de VPN baseado em rota (conhecido como gateway dinâmico ao trabalhar com redes virtuais clássicas). Como cada rede virtual pode ter apenas um gateway de VPN, todas as conexões por meio do gateway compartilham a largura de banda disponível. Isso é geralmente chamado de conexão com "vários sites".   
![Exemplo de conexão Multissite do Gateway de VPN do Azure](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)



## <a name="gateway-skus"></a>SKUs de gateway
Quando você criar um gateway de rede virtual para a pilha do Azure, você pode especificar o gateway SKU que você deseja usar. Há suporte para os seguintes SKUs de gateway VPN:
- Basic
- Standard
- HighPerformance

Quando você selecionar um gateway superior SKU, como padrão em Basic ou alto desempenho acima Standard ou Basic, mais CPUs e largura de banda de rede são alocadas para o gateway. Como resultado, o gateway pode dar suporte a maior taxa de transferência de rede para a rede virtual.

A pilha do Azure não oferece suporte para o gateway UltraPerformance SKU, que é usado exclusivamente com a rota expressa.

Ao selecionar uma SKU, considere o seguinte:
- Pilha do Azure não oferece suporte para gateways baseado em políticas.
- Border Gateway Protocol (BGP) não é suportada no SKU básico.
- Gateway VPN de rota expressa coexistir configurações não têm suporte na pilha do Azure
- As conexões de Gateway de VPN S2S ativa-ativa só podem ser configuradas na SKU HighPerformance.

## <a name="estimated-aggregate-throughput-by-sku"></a>Taxa de transferência agregada estimada por SKU
A tabela a seguir mostra os tipos de gateway e a produtividade agregada estimada pela SKU do gateway.

|   | Taxa de transferência de Gateway VPN *(1)* |Túneis IPsec máximos de Gateway de VPN |
|-------|-------|-------|
|**SKUS Basic** ***(2)***    | 100 Mbps  | 10    |
|**SKU padrão**       | 100 Mbps  | 10    |
|**SKU de Alto Desempenho** | 200 Mbps    | 30    |
***(1)***  VPN a taxa de transferência não é uma taxa de transferência garantida para conexões entre locais através da Internet. É a medida de taxa de transferência máxima possível.  
***(2)***  BGP não há suporte para a SKU básico.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [as configurações para gateways VPN](azure-stack-vpn-gateway-settings.md) pilha do Azure.
