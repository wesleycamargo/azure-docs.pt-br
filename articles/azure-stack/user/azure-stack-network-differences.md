---
title: O Azure Stack diferenças e considerações de rede | Microsoft Docs
description: Saiba mais sobre as diferenças e considerações ao trabalhar com a rede no Azure Stack.
services: azure-stack
keywords: ''
author: mattbriggs
manager: femila
ms.date: 10/22/2018
ms.topic: article
ms.service: azure-stack
ms.author: mabrigg
ms.reviewer: scottnap
ms.openlocfilehash: de98387b0c7d5eb3c5ca99f9aa31619397e2aadf
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49944571"
---
# <a name="considerations-for-azure-stack-networking"></a>Considerações sobre a rede do Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Rede do Azure Stack tem muitos dos recursos fornecidos pelo sistema de rede do Azure. No entanto, há algumas diferenças importantes que você deve compreender antes de implantar uma rede do Azure Stack.

Este artigo fornece uma visão geral das considerações exclusivas para a rede do Azure Stack e seus recursos. Para saber mais sobre as diferenças de alto nível entre o Azure Stack e o Azure, consulte a [considerações da chave](azure-stack-considerations.md) artigo.

## <a name="cheat-sheet-networking-differences"></a>Folha de dados: diferenças de rede

| Serviço | Recurso | (Global) do Azure | Azure Stack |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| DNS | DNS multilocatário | Com suporte | Ainda não tem suporte |
|  | Registros DNS AAAA | Com suporte | Sem suporte |
|  | Zonas DNS por assinatura | 100 (padrão)<br>Pode ser aumentado na solicitação. | 100 |
|  | Conjuntos de registros DNS por zona | 5000 (padrão)<br>Pode ser aumentado na solicitação. | 5.000 |
|  | Servidores de nomes para a delegação de zona | O Azure fornece quatro servidores de nomes para cada zona de usuário (Locatário) que é criada. | O Azure Stack fornece dois servidores de nomes para cada região do usuário (Locatário) que é criado. |
| Rede Virtual | Emparelhamento de rede virtual | Conecte duas redes virtuais na mesma região por meio da rede de backbone do Azure. | Ainda não tem suporte |
|  | Endereços IPv6 | Você pode atribuir um endereço IPv6 como parte do [configuração do adaptador de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions). | Há suporte apenas para IPv4. |
|  | Plano de proteção contra DDoS | Com suporte | Ainda não tem suporte. |
|  | Configurações de IP do conjunto de dimensionamento | Com suporte | Ainda não tem suporte. |
|  | Serviços de acesso privado (sub-rede) | Com suporte | Ainda não tem suporte. |
|  | Pontos de extremidade de serviço | Suporte para conexão de (sem Internet) interno para serviços do Azure. | Ainda não tem suporte. |
| Há suporte apenas para IPv4. | Políticas do Ponto de Extremidade do Serviço | Com suporte | Ainda não tem suporte. |
|  | Túneis de serviço | Com suporte | Ainda não tem suporte.  |
| Grupos de segurança de rede | Regras de segurança aumentadas | Com suporte | Ainda não tem suporte. |
|  | Regras de segurança efetivas | Com suporte | Ainda não tem suporte. |
|  | Grupos de segurança do aplicativo | Com suporte | Ainda não tem suporte. |
| Gateways de Rede Virtual | Gateway VPN ponto a Site | Com suporte | Ainda não tem suporte. |
|  | Gateway de rede virtual para rede virtual | Com suporte | Ainda não tem suporte. |
|  | Tipo de Gateway de rede virtual | Azure dá suporte a VPN<br> ExpressRoute <br> Net Hyper | O Azure Stack oferece suporte a apenas o tipo de VPN no momento. |
|  | SKUs de Gateway VPN | Suporte para Basic, GW1, GW2, GW3, padrão de alto desempenho, excepcionalmente alto desempenho. | Suporte para Basic, Standard e SKUs de alto desempenho. |
|  | Tipo de VPN | Azure suporta baseado em políticas e com base em rota. | O Azure Stack dá suporte a rota com base apenas. |
|  | Configurações BGP | O Azure suporta a configuração de endereço de emparelhamento via protocolo BGP e peso do par. | Endereço de emparelhamento via protocolo BGP e peso do par são configurados automaticamente no Azure Stack. Não há nenhuma maneira para o usuário definir essas configurações com seus próprios valores. |
|  | Site de Gateway padrão | O Azure suporta a configuração de um site padrão para o túnel forçado. | Ainda não tem suporte. |
|  | Redimensionamento do gateway | O Azure suporta o redimensionamento do gateway após a implantação. | Redimensionamento não suportado. |
|  | Configuração ativo/ativo | Com suporte | Ainda não tem suporte. |
|  | Políticas de IPSec/IKE | Do Azure dá suporte a IPSec política configurações personalizadas. | Ainda não tem suporte. |
|  | UsePolicyBasedTrafficSelectors | O Azure suporta usando seletores de tráfego baseado em políticas com conexões de gateway de baseado em rota. | Ainda não tem suporte. |
| Balanceador de carga | SKU | Básica e balanceadores de carga padrão têm suporte | Há suporte para apenas o Load Balancer básico.  Não há suporte para a propriedade SKU. |
|  | Zonas | Há suporte para zonas de disponibilidade. | Ainda não tem suporte |
|  | Suporte a regras NAT de entrada para pontos de extremidade de serviço | O Azure suporta especificando pontos de extremidade de serviço para regras de NAT de entrada. | O Azure Stack ainda não dá suporte pontos de extremidade de serviço, portanto, elas não podem ser especificadas. |
|  | Protocolo | Dá suporte ao Azure especificando GRE ou ESP. | Não há suporte para a classe de protocolo no Azure Stack. |
| Endereço IP público | Versão do endereço IP público | Azure dá suporte a IPv6 e IPv4 | Há suporte apenas para IPv4. |
| Interface de rede | Obter tabela efetiva de rotas | Com suporte | Ainda não tem suporte. |
|  | Obter as ACLs em vigor | Com suporte | Ainda não tem suporte. |
|  | Habilitar Rede acelerada | Com suporte | Ainda não tem suporte. |
|  | encaminhamento IP | Desabilitado por padrão.  Pode ser habilitado. | Ativar/desativar essa configuração não é suportado.  Em por padrão. |
|  | Várias configurações de IP por interface | Com suporte | Ainda não tem suporte. |
|  | Grupos de segurança do aplicativo | Com suporte | Ainda não tem suporte. |
|  | Rótulo de nome DNS interno | Com suporte | Ainda não tem suporte. |
|  | Versão do endereço IP privado | Há suporte para IPv6 e IPv4. | Há suporte apenas para IPv4. |
|  | Configuração de IP primário | Com suporte. Identifica a configuração de IP primário na interface. | Ainda não tem suporte. |
| Observador de Rede | Recursos de monitoramento de rede de locatário de observador de rede | Com suporte | Ainda não tem suporte. |
| CDN | Perfis de rede de distribuição de conteúdo | Com suporte | Ainda não tem suporte. |
| Gateway de Aplicativo | Balanceamento de carga de camada 7 | Com suporte | Ainda não tem suporte. |
| Gerenciador de Tráfego | Rotear o tráfego de entrada para desempenho ideal do aplicativo e a confiabilidade. | Com suporte | Ainda não tem suporte. |
| ExpressRoute | Configure uma conexão privada, rápida aos serviços de nuvem da Microsoft de suas instalações de infraestrutura ou a colocação do local. | Com suporte | Suporte para conexão do Azure Stack a um circuito do Expressroute. |

## <a name="next-steps"></a>Próximas etapas

[DNS no Azure Stack](azure-stack-dns.md)