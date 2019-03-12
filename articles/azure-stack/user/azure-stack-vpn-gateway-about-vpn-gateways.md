---
title: Sobre o gateway VPN para o Azure Stack | Microsoft Docs
description: Saiba mais sobre e configurar os gateways de VPN, que você pode usar com o Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 0e30522f-20d6-4da7-87d3-28ca3567a890
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: sethm
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 83d5215049976b67d22e29c2e4b75ec63a505b36
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57776607"
---
# <a name="about-vpn-gateway-for-azure-stack"></a>Sobre o gateway VPN para o Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Antes de enviar o tráfego de rede entre sua rede virtual do Azure e seu site local, você deve criar um gateway de rede virtual para sua rede virtual.

Um gateway de VPN é um tipo de gateway de rede virtual que envia o tráfego criptografado em uma conexão pública. Você pode usar gateways de VPN para enviar o tráfego com segurança entre uma rede virtual no Azure Stack e uma rede virtual no Azure. Você também pode enviar o tráfego com segurança entre uma rede virtual e outra rede que está conectado a um dispositivo VPN.

Ao criar um gateway de rede virtual, especifique o tipo de gateway que você deseja criar. O Azure Stack oferece suporte a um tipo de gateway de rede virtual: o **Vpn** tipo.

Cada rede virtual pode ter dois gateways de rede virtual, mas somente um de cada tipo. Dependendo das configurações que você escolher, é possível criar várias conexões a um único gateway de VPN. Um exemplo é uma configuração de conexão de múltiplos sites.

Antes de criar e configurar os Gateways de VPN para o Azure Stack, examine os [considerações sobre a rede do Azure Stack](azure-stack-network-differences.md) para saber como as configurações para o Azure Stack diferem do Azure.

>[!NOTE]
>No Azure, a taxa de transferência de largura de banda para o gateway VPN SKU que você escolher deve ser dividida em todas as conexões que estão conectadas ao gateway. No Azure Stack no entanto, o valor de largura de banda para a SKU de gateway VPN é aplicado a cada recurso de conexão que está conectado ao gateway.
>
> Por exemplo: 
> * No Azure, o SKU de gateway VPN básica pode acomodar aproximadamente 100 Mbps de taxa de transferência agregada. Se você cria duas conexões para esse gateway de VPN e uma conexão está usando 50 Mbps de largura de banda, em seguida, 50 Mbps está disponível para a outra conexão.
> * No Azure Stack *cada* conexão para o SKU de gateway VPN básica é alocado de taxa de transferência de 100 Mbps.

## <a name="configuring-a-vpn-gateway"></a>Configurar um gateway VPN

Uma conexão de gateway VPN depende de vários recursos que são configurados com configurações específicas. A maioria desses recursos pode ser configurada separadamente, mas em alguns casos, eles devem ser configurados em uma ordem específica.

### <a name="settings"></a>Configurações

As configurações que você escolhe para cada recurso são essenciais para a criação de uma conexão bem-sucedida.

Para obter informações sobre os recursos individuais e as configurações para um gateway de VPN, consulte [configurações de gateway de VPN sobre para o Azure Stack](azure-stack-vpn-gateway-settings.md). Este artigo ajudará você a entender:

* Tipos de gateway, tipos de VPN e os tipos de conexão.
* As sub-redes de gateway, gateways de rede local e outras configurações de recursos que você talvez queira considerar.

### <a name="deployment-tools"></a>Ferramentas de implantação

Você pode criar e configurar recursos usando uma ferramenta de configuração, como o portal do Azure. Posteriormente, você pode alternar para outra ferramenta como o PowerShell para configurar recursos adicionais ou modificar os recursos existentes, quando aplicável. Atualmente, não é possível configurar cada recurso e a configuração de recurso no portal do Azure. As instruções nos artigos para cada topologia de conexão especificam quando uma ferramenta de configuração específica é necessária.

## <a name="connection-topology-diagrams"></a>Diagramas de topologia de Conexão

Há diferentes configurações disponíveis para conexões de gateway VPN. Determine qual configuração melhor atende às suas necessidades. Nas seções a seguir, você pode exibir diagramas de topologia e informações sobre as seguintes conexões de gateway VPN:

* Modelo de implantação disponível
* Ferramentas de configuração disponíveis
* Links que levam você diretamente a um artigo, se disponível

Os diagramas e descrições nas seções a seguir podem ajudá-lo a selecionar uma topologia de conexão para corresponder aos seus requisitos. Os diagramas mostram as topologias de linha de base principais, mas é possível criar topologias mais complexas usando os diagramas como guia.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Site a site e multissite (túnel VPN IPsec/IKE)

### <a name="site-to-site"></a>Site a site

Um *site a site* (S2S) conexão de gateway VPN é uma conexão por túnel VPN IPsec/IKE (IKEv2). Esse tipo de conexão exige um dispositivo VPN que está localizado no local e é atribuído um endereço IP público. Este dispositivo não pode estar localizado atrás de NAT. As conexões S2S podem ser usadas para configurações entre instalações e híbridas.

![Exemplo de configuração de conexão de VPN site a site](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>Vários sites

Um *multissite* conexão é uma variação da conexão site a site. Você pode criar mais de uma conexão de VPN em seu gateway de rede virtual, normalmente se conectando a vários sites locais. Ao trabalhar com várias conexões, você deve usar um tipo de VPN baseado em rota (conhecido como gateway dinâmico ao trabalhar com redes virtuais clássicas). Como cada rede virtual pode ter apenas um gateway de VPN, todas as conexões por meio do gateway compartilham a largura de banda disponível.

![Exemplo de conexão Multissite do Gateway de VPN do Azure](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)

## <a name="gateway-skus"></a>SKUs de gateway

Quando você cria um gateway de rede virtual para o Azure Stack, você pode especificar o SKU do gateway que você deseja usar. Há suporte para os seguintes SKUs de gateway VPN:

* Basic
* Standard
* Alto Desempenho

Quando você seleciona um SKU, como o padrão de básico ou de alto desempenho de gateway maior sobre Standard ou básica, mais CPUs e largura de banda de rede são alocados para o gateway. Como resultado, o gateway pode dar suporte a maior taxa de transferência de rede para a rede virtual.

O Azure Stack não dá suporte para o gateway de ultradesempenho SKU, que é usado exclusivamente com a rota expressa.

Quando você seleciona o SKU, considere o seguinte:

* O Azure Stack não dá suporte a gateways baseados em política.
* Não há suporte para o Border Gateway Protocol (BGP) na SKU básica.
* Não há suporte para configurações coexistentes do gateway de VPN do ExpressRoute no Azure Stack.
* Conexões de gateway de VPN S2S ativa-ativa podem ser configuradas no SKU de alto desempenho somente.

## <a name="estimated-aggregate-throughput-by-sku"></a>Taxa de transferência agregada estimada por SKU

A tabela a seguir mostra os tipos de gateway e a produtividade agregada estimada por SKU de gateway:

|   | Taxa de transferência de Gateway de VPN *(1)* | Túneis IPsec máximo de Gateway de VPN *(2)* |
|-------|-------|-------|
|**SKU básica** ***(3)***    | 100 Mbps  | 20    |
|**SKU padrão**       | 100 Mbps  | 20    |
|**SKU de Alto Desempenho** | 200 Mbps    | 10    |

**Notas de tabela:**

*Observação (1)* -taxa de transferência VPN não é uma taxa de transferência garantida para conexões entre locais na Internet. É a medida de taxa de transferência máxima possível.  
*Observação (2)* -Max túneis é o total por implantação do Azure Stack para todas as assinaturas.  
*Observação (3)* -não há suporte para roteamento de BGP para a SKU básica.

>[!NOTE]
>Apenas uma conexão de VPN site a site pode ser criado entre duas implantações do Azure Stack. Isso é devido a uma limitação na plataforma que permite apenas uma única conexão de VPN para o mesmo endereço IP. Como o gateway de multilocatário, que usa um único IP público para todos os gateways VPN no sistema do Azure Stack, aproveita o Azure Stack pode haver apenas uma conexão VPN entre dois sistemas do Azure Stack. Essa limitação também se aplica à conexão de mais de uma conexão de VPN site a site com qualquer gateway VPN que usa um único endereço IP. O Azure Stack não permite mais de um recurso de gateway de rede local a ser criado usando o mesmo endereço IP.

## <a name="next-steps"></a>Próximas etapas

[Definições de configuração de gateway VPN para o Azure Stack](azure-stack-vpn-gateway-settings.md)
