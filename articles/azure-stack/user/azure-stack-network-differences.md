---
title: As diferenças e considerações de rede de pilha do Azure | Microsoft Docs
description: Saiba mais sobre as diferenças e considerações ao trabalhar com a rede na pilha do Azure.
services: azure-stack
keywords: ''
author: mattbriggs
manager: femila
ms.author: mabrigg
ms.date: 05/21/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: scottnap
ms.openlocfilehash: faff52ba5b5e2f0d573a67633d3a8411b2d7de74
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606419"
---
# <a name="considerations-for-azure-stack-networking"></a>Considerações de rede de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Rede de pilha do Azure tem muitos dos recursos fornecidos pela rede do Azure. No entanto, há algumas diferenças importantes que você deve compreender antes de implantar uma rede de pilha do Azure.

Este artigo fornece uma visão geral das considerações exclusivas para a rede de pilha do Azure e seus recursos. Para saber mais sobre as diferenças de alto nível entre a pilha do Azure e o Azure, consulte o [chave considerações](azure-stack-considerations.md) tópico.

## <a name="cheat-sheet-networking-differences"></a>Roteiro: diferenças de rede

|Serviço | Recurso | Azure (global) | Azure Stack |
| --- | --- | --- | --- |
| DNS | DNS de vários locatários | Com suporte| Ainda não tem suporte|
| |Registros DNS AAAA|Com suporte|Sem suporte|
| |Zonas do DNS por assinatura|100 (padrão)<br>Pode ser aumentado na solicitação.|100|
| |Conjuntos de registros do DNS por região|5000 (padrão)<br>Pode ser aumentado na solicitação.|5.000|
||Servidores de nome para a delegação de zona|O Azure fornece quatro servidores de nomes para cada zona de usuário (Locatário) que é criada.|A pilha do Azure fornece dois servidores de nome para cada região do usuário (Locatário) que é criado.|
| Rede virtual|Emparelhamento de rede virtual|Conecte duas redes virtuais na mesma região através da rede de backbone do Azure.|Ainda não tem suporte|
| |Endereços IPv6|Você pode atribuir um endereço IPv6 como parte do [configuração de Interface de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions).|Há suporte apenas para IPv4.|
|Gateways VPN|Gateway VPN ponto a Site|Com suporte|Ainda não tem suporte|
| |Gateway de Vnet para Vnet|Com suporte|Ainda não tem suporte|
| |SKUs de Gateway VPN|Suporte para Basic, GW1, GW2, GW3, padrão de alto desempenho, altíssimo desempenho. |Suporte para Basic, Standard e SKUs de alto desempenho.|
|Balanceador de carga|Endereços IP públicos de IPv6|Suporte para atribuir um endereço IP público do IPv6 a um balanceador de carga.|Há suporte apenas para IPv4.|
|Observador de Rede|Rede de locatário do Inspetor recursos de monitoramento de rede|Com suporte|Ainda não tem suporte|
|CDN|Perfis de rede de fornecimento de conteúdo|Com suporte|Ainda não tem suporte|
|Gateway de Aplicativo|Balanceamento de carga de camada 7|Com suporte|Ainda não tem suporte|
|Gerenciador de Tráfego|Rotear o tráfego de entrada para melhor desempenho dos aplicativos e a confiabilidade.|Com suporte|Ainda não tem suporte|
|ExpressRoute|Configure uma conexão rápida e privada para serviços de nuvem da Microsoft de suas instalações de infraestrutura ou a colocação do local.|Com suporte|Suporte para conexão a pilha do Azure a um circuito de rota expressa.|

## <a name="next-steps"></a>Próximas etapas

[DNS no Azure Stack](azure-stack-dns.md)