---
title: Sobre o gateway VPN para o Azure Stack | Microsoft Docs
description: Saiba mais sobre e configurar os gateways de VPN, que você pode usar com o Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 0e30522f-20d6-4da7-87d3-28ca3567a890
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/02/2018
ms.author: brenduns
ms.openlocfilehash: 0ff3402115ae9f4c736bf9058fc09de16eaefb1e
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2018
ms.locfileid: "37347176"
---
# <a name="about-vpn-gateway-for-azure-stack"></a>Sobre o gateway VPN para o Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Antes de enviar o tráfego de rede entre sua rede virtual do Azure e seu site local, você deve criar um gateway de rede virtual para sua rede virtual.

Um gateway de VPN é um tipo de gateway de rede virtual que envia o tráfego criptografado em uma conexão pública. Você pode usar gateways de VPN para enviar o tráfego com segurança entre uma rede virtual no Azure Stack e uma rede virtual no Azure. Você também pode enviar o tráfego com segurança entre uma rede virtual e outra rede que está conectado a um dispositivo VPN.

Ao criar um gateway de rede virtual, especifique o tipo de gateway que você deseja criar. O Azure Stack oferece suporte a um tipo de gateway de rede virtual, o **Vpn** tipo.

Cada rede virtual pode ter dois gateways de rede virtual, mas somente um de cada tipo. Dependendo das configurações que você escolher, é possível criar várias conexões a um único gateway de VPN. Um exemplo é uma configuração de conexão de múltiplos sites.

Antes de criar e configurar os Gateways de VPN para o Azure Stack, examine os [considerações sobre a rede do Azure Stack](/azure/azure-stack/user/azure-stack-network-differences) para saber como as configurações para o Azure Stack diferem do Azure.

>[!NOTE]
>No Azure, a taxa de transferência de largura de banda para o gateway de VPN SKU que você escolher deve ser dividida em todas as conexões que estão conectadas ao gateway. Mas, no Azure Stack, o valor de largura de banda para a SKU de gateway VPN é aplicado a cada recurso de Conexão que está conectado ao gateway.
>
> Por exemplo: 
> * No Azure, a SKU de Gateway de VPN básica pode acomodar aproximadamente 100 Mbps de taxa de transferência agregada. Se você cria duas conexões para esse Gateway de VPN e uma conexão está usando 50 Mbps de largura de banda, em seguida, 50 Mbps está disponível para a outra Conexão.
> * No Azure Stack *cada* Conexão para o SKU de Gateway VPN básico é alocado de taxa de transferência de 100 Mbps.

## <a name="configuring-a-vpn-gateway"></a>Configurando um Gateway de VPN

Uma conexão de gateway VPN depende de vários recursos que são configurados com configurações específicas. A maioria desses recursos pode ser configurada separadamente, mas em alguns casos, eles devem ser configurados em uma ordem específica.

### <a name="settings"></a>Configurações

As configurações que você escolheu para cada recurso são essenciais para a criação de uma conexão bem-sucedida.

Para obter informações sobre os recursos individuais e as configurações para um gateway de VPN, consulte [configurações de gateway de VPN sobre para o Azure Stack](azure-stack-vpn-gateway-settings.md). Este artigo ajudará você a entender:

* Tipos de gateway, tipos de VPN e os tipos de conexão.
* As sub-redes de gateway, gateways de rede local e outras configurações de recursos que você talvez queira considerar.

### <a name="deployment-tools"></a>Ferramentas de implantação

Você pode criar e configurar recursos usando uma ferramenta de configuração, como o portal do Azure. Posteriormente, você pode alternar para outra ferramenta como o PowerShell para configurar recursos adicionais ou modificar os recursos existentes, quando aplicável. Atualmente, não é possível configurar cada recurso e definição de recursos no portal do Azure. As instruções nos artigos para cada topologia de conexão especificam quando uma ferramenta de configuração específica é necessária.

## <a name="connection-topology-diagrams"></a>Diagramas de topologia de Conexão

É importante saber que há diferentes configurações disponíveis para conexões de gateway de VPN. Determine qual configuração melhor atende às suas necessidades. Nas seções a seguir, você pode exibir diagramas de topologia e informações sobre as seguintes conexões de gateway VPN:

* Modelo de implantação disponível
* Ferramentas de configuração disponíveis
* Links que levam você diretamente a um artigo, se disponível

Os diagramas e descrições nas seções a seguir podem ajudá-lo a selecionar uma topologia de conexão para corresponder aos seus requisitos. Os diagramas mostram as topologias de linha de base principais, mas é possível criar topologias mais complexas usando os diagramas como guia.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Site a Site e Multissite (túnel VPN IPsec/IKE)

### <a name="site-to-site"></a>Site a site

Uma conexão de gateway VPN Site a Site (S2S) é uma conexão por túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Esse tipo de conexão exige um dispositivo VPN que está localizada no local e é atribuído um endereço IP público. Este dispositivo não pode estar localizado atrás de NAT. As conexões S2S podem ser usadas para configurações entre instalações e híbridas.

![Exemplo de configuração de conexão de VPN site a site](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>Multissite

Esse tipo de conexão é uma variação da conexão Site a Site. Você pode criar mais de uma conexão de VPN em seu gateway de rede virtual, normalmente se conectando a vários sites locais. Ao trabalhar com várias conexões, você deve usar um tipo de VPN RouteBased (de conhecido como gateway dinâmico ao trabalhar com redes virtuais clássicas.) Como cada rede virtual pode ter apenas um gateway de VPN, todas as conexões por meio do gateway compartilham a largura de banda disponível. Isso é geralmente chamado de conexão com "vários sites".

![Exemplo de conexão Multissite do Gateway de VPN do Azure](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)

## <a name="gateway-skus"></a>SKUs de gateway

Quando você cria um gateway de rede virtual para o Azure Stack, você pode especificar o SKU do gateway que você deseja usar. Há suporte para os seguintes SKUs de gateway VPN:

* Basic
* Standard
* HighPerformance

Quando você seleciona um SKU, como Standard básica ou HighPerformance de gateway maior sobre Standard ou básica, mais CPUs e largura de banda de rede são alocados para o gateway. Como resultado, o gateway pode dar suporte a maior taxa de transferência de rede para a rede virtual.

O Azure Stack não dá suporte a SKU, que é usado exclusivamente com a rota expressa do gateway UltraPerformance.

Quando você seleciona o SKU, considere o seguinte:

* O Azure Stack não dá suporte a gateways baseados em política.
* Não há suporte para o Border Gateway Protocol (BGP) na SKU básica.
* Coexistência de Gateway de VPN de ExpressRoute configurações não têm suporte no Azure Stack.
* As conexões de Gateway de VPN S2S ativa-ativa só podem ser configuradas na SKU HighPerformance.

## <a name="estimated-aggregate-throughput-by-sku"></a>Taxa de transferência agregada estimada por SKU

A tabela a seguir mostra os tipos de gateway e a produtividade agregada estimada pela SKU do gateway.

|   | Taxa de transferência de Gateway de VPN *(1)* | Túneis IPsec máximo de Gateway de VPN *(2)* |
|-------|-------|-------|
|**SKU básica** ***(3)***    | 100 Mbps  | 10    |
|**SKU padrão**       | 100 Mbps  | 10    |
|**SKU de alto desempenho** | 200 Mbps    | 5 |

**Notas de tabela:**

*Observação (1)* -taxa de transferência VPN não é uma taxa de transferência garantida para conexões entre locais na Internet. É a medida de taxa de transferência máxima possível.  
*Observação (2)* -Max túneis é o total por implantação do Azure Stack para todas as assinaturas.  
*Observação (3)* -roteamento de BGP não tem suporte para a SKU básica.

## <a name="next-steps"></a>Próximas etapas

[Definições de configuração de gateway VPN para o Azure Stack](azure-stack-vpn-gateway-settings.md)
