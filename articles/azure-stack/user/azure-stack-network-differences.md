---
title: "Rede do Azure de pilha: As diferenças e considerações"
description: "Saiba mais sobre as diferenças e considerações ao trabalhar com a rede na pilha do Azure."
services: azure-stack
keywords: 
author: ScottNapolitan
ms.author: victorh
ms.date: 9/25/2017
ms.topic: article
ms.service: azure-stack
ms.openlocfilehash: 3c72c58e63335f1cb440811e283bd742b8124161
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2017
---
# <a name="considerations-for-azure-stack-networking"></a>Considerações de rede de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

A rede na pilha do Azure fornece muitos dos recursos que você encontrar no Azure, com algumas diferenças que você deve compreender antes de iniciar a implantação.


Este artigo fornece uma visão geral das considerações de rede e seus recursos no Azure pilha exclusivas. Para saber mais sobre as diferenças de alto nível entre a pilha do Azure e o Azure, consulte o [chave considerações](azure-stack-considerations.md) tópico.


## <a name="cheat-sheet-networking-differences"></a>Roteiro: diferenças de rede

|O Barramento de | Recurso | Azure (global) | Azure Stack |
| --- | --- | --- | --- |
| DNS | DNS de vários locatários | Suportado| Ainda não tem suporte|
| |Registros DNS AAAA|Suportado|Sem suporte|
| |Zonas do DNS por assinatura|100 (padrão)<br>Pode ser aumentado na solicitação.|100|
| |Conjuntos de registros do DNS por região|5000 (padrão)<br>Pode ser aumentado na solicitação.|5.000|
||Servidores de nome para a delegação de zona|Para cada zona de usuário (Locatário) que é criada, o Azure fornece quatro servidores de nome.|A pilha do Azure fornece dois servidores de nome para cada região do usuário (Locatário) que é criado.|
| Rede virtual|Emparelhamento de rede virtual|Conecte duas redes virtuais na mesma região através da rede de backbone do Azure.|Ainda não tem suporte|
| |Endereços IPv6|Você pode atribuir um endereço IPv6 como parte do [configuração de Interface de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions).|Há suporte apenas para IPv4.|
|Gateways VPN|Gateway VPN ponto a Site|Suportado|Ainda não tem suporte|
| |Gateway de Vnet para Vnet|Suportado|Ainda não tem suporte|
| |SKUs de Gateway VPN|Suporte para Basic, GW1, GW2, GW3, padrão de alto desempenho, altíssimo desempenho. |Suporte para Basic, Standard e SKUs de alto desempenho.|
|Balanceador de carga|Endereços IP públicos de IPv6|Suporte para atribuir um endereço IP público do IPv6 a um balanceador de carga.|Há suporte apenas para IPv4.|
|Observador de Rede|Rede de locatário do Inspetor recursos de monitoramento de rede|Suportado|Ainda não tem suporte|
|CDN|Perfis de rede de fornecimento de conteúdo|Suportado|Ainda não tem suporte|
|Gateway de Aplicativo|Balanceamento de carga de camada 7|Suportado|Ainda não tem suporte|
|Gerenciador de Tráfego|Rotear o tráfego de entrada para melhor desempenho dos aplicativos e a confiabilidade.|Suportado|Ainda não tem suporte|
|ExpressRoute|Configure uma conexão rápida e privada para serviços de nuvem da Microsoft de suas instalações de infraestrutura ou a colocação do local.|Suportado|Suporte para conexão a pilha do Azure a um circuito de rota expressa.|

## <a name="next-steps"></a>Próximas etapas

[DNS no Azure Stack](azure-stack-dns.md)
