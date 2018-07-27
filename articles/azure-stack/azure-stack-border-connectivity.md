---
title: Considerações de integração de rede de conectividade para sistemas integrados do Azure Stack da borda | Microsoft Docs
description: Saiba o que você pode fazer para planejar a conectividade de rede de borda do datacenter com vários nós do Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 260c58ad9099a4532c8a6558cfcf5c13f0fc8d52
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282001"
---
# <a name="border-connectivity"></a>Conectividade de borda 
Planejamento de integração de rede é um pré-requisito importante para a implantação de sistemas integrados do Azure Stack, operação e gerenciamento com êxito. O planejamento de conectividade com a borda começa escolhendo se deseja ou não usar o roteamento dinâmico com o protocolo de gateway de borda (BGP). Isso requer a atribuição de um número de sistema autônomo de BGP de 16 bits (público ou privado) ou usando o roteamento estático, em que uma rota estática padrão é atribuída para os dispositivos de borda.

> [!IMPORTANT]
> A parte superior de comutadores de rack (TOR) exigem uplinks de camada 3 com IPs de ponto a ponto (/ 30 redes) configurado nas interfaces físicas. Não há suporte para usar uplinks de camada 2 com comutadores TOR oferecem suporte a operações do Azure Stack. 

## <a name="bgp-routing"></a>Roteamento de BGP
Usar um protocolo de roteamento dinâmico como o BGP garante que o sistema sempre está ciente das alterações de rede e facilita a administração. 

Conforme mostrado no diagrama a seguir, anúncio de IP privado espaço no comutador TOR é restringido usando uma lista de prefixos. As listas de prefixo nega as sub-redes IP privadas e aplicá-lo como um mapa de rota sobre a conexão entre o TOR e a borda.

O Software SLB balanceador de carga () em execução dentro da solução do Azure Stack de mesmo nível para os dispositivos TOR para que ele pode anunciar dinamicamente os endereços de VIP.

Para garantir que o tráfego do usuário imediatamente e transparente se recuperar de falha, o VPC ou MLAG configuradas entre os dispositivos do TOR permite o uso de link de chassi a agregação para o HSRP e VRRP fornece ou hosts de rede redundância para as redes IP.

![Roteamento de BGP](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>Roteamento estático
Roteamento estático requer configuração adicional para os dispositivos de borda. Ele exige mais uma intervenção manual e gerenciamento, bem como análise completa antes de qualquer alteração e problemas causados por um erro de configuração pode levar mais tempo para reversão dependendo das alterações feitas. Não é o método de roteamento recomendado, mas ele tem suporte.

Para integrar o Azure Stack em seu ambiente de rede usando o roteamento estático, todos os links físicos quatro entre a borda e o dispositivo TOR deve estar conectados e alta disponibilidade não pode ser garantida devido a estática como funciona o roteamento.

O dispositivo de borda deve ser configurado com rotas estáticas que aponta para os dispositivos TOR P2P para o tráfego destinado à rede externa ou VIPs públicos e a rede de infraestrutura. Ele requer rotas estáticas na rede BMC para a implantação. Os clientes podem optar por deixar rotas estáticas na borda para acessar alguns recursos que residem na rede BMC.  Adicionar rotas estáticas para *comutador infra-estrutura* e *alternar gerenciamento* redes é opcional.

Os dispositivos TOR vêm configurados com uma rota estática padrão enviar todo o tráfego para os dispositivos de borda. É a única exceção de tráfego para a regra padrão para o espaço privado, o que está bloqueado usando uma lista de controle de acesso aplicada TOR para conexão de borda.

Roteamento estático só se aplica os uplinks entre os comutadores TOR e a borda. Roteamento dinâmico do BGP é usado dentro do rack, porque ele é uma ferramenta essencial para o SLB e outros componentes e não pode ser desabilitado ou removido.

![Roteamento estático](media/azure-stack-border-connectivity/static-routing.png)

## <a name="transparent-proxy"></a>Proxy transparente
Se seu data center exige que todo o tráfego para usar um proxy, você deve configurar uma *proxy transparente* para processar todo o tráfego de rack para tratá-la de acordo com a política, separar o tráfego entre as zonas em sua rede.

> [!IMPORTANT]
> A solução do Azure Stack não dá suporte a proxies da web normal.  

Um proxy transparente (também conhecido como um interceptar, embutido ou proxy forçado) intercepta comunicação normal na camada de rede sem a necessidade de qualquer configuração especial do cliente. Os clientes não precisam estar atento a existência do proxy.

![Proxy transparente](media/azure-stack-border-connectivity/transparent-proxy.png)

## <a name="next-steps"></a>Próximas etapas
[Integração do DNS](azure-stack-integrate-dns.md)