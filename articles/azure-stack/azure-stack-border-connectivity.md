---
title: "Considerações de integração de rede de conectividade para sistemas de pilha do Azure integradas da borda | Microsoft Docs"
description: "Saiba o que você pode fazer para planejar a conectividade de rede do datacenter borda com vários nós do Azure pilha."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: c1a5496f3ab9a625d7d97c3096ae89100b7c5592
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="border-connectivity"></a>Conectividade de borda 
Planejamento de integração da rede é um pré-requisito importante para implantação de sistemas de pilha do Azure integradas com êxito, operação e gerenciamento. Planejamento de conectividade com a borda começa, escolha se deseja ou não usar o roteamento dinâmico com o protocolo de gateway de borda (BGP). Isso requer a atribuição de um número de sistema autônomo de BGP de 16 bits (público ou privado) ou usando o roteamento estático, em que uma rota estática padrão é atribuída para os dispositivos de borda.

> [!IMPORTANT]
> A parte superior de switches de rack (TOR) exigem uplinks de camada 3 com IPs de ponto a ponto (/ 30 redes) configurado em interfaces físicas. Não há suporte para usar uplinks de camada 2 com comutadores TOR com suporte a operações de pilha do Azure. 

## <a name="bgp-routing"></a>Roteamento de BGP
Usar um protocolo de roteamento dinâmico como o BGP garante que o sistema sempre está ciente das alterações de rede e facilita a administração. 

Conforme mostrado no diagrama a seguir, o IP privado de publicidade espaço no comutador TOR é restrito com o uso de uma lista de prefixos. As listas de prefixo nega as sub-redes IP privadas e aplicá-lo como um mapa de rota na conexão entre a borda e TOR.

O Software de carga balanceador SLB () em execução na solução de pilha do Azure correspondentes aos dispositivos TOR para anunciar dinamicamente os endereços VIP.

Para garantir que o tráfego do usuário imediatamente e transparente à recuperação de falha, o VPC ou MLAG configurada entre os dispositivos TOR permite o uso de link do multi-chassi agregação para hosts e HSRP ou VRRP fornece redundância para as redes IP de rede.

![Roteamento de BGP](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>roteamento estático
Usar rotas estáticas adiciona a configuração mais fixa para a borda e dispositivos TOR. Isso requer análise completa antes de qualquer alteração. Problemas causados por um erro de configuração podem levar mais tempo para reversão dependendo das alterações feitas. Não é o método de roteamento recomendado, mas ele tem suporte.

Para integrar a pilha do Azure em seu ambiente de rede usando esse método, o dispositivo de borda deve ser configurado com rotas estáticas apontando para os dispositivos TOR para o tráfego destinado à rede externa, VIPs públicos.

Os dispositivos TOR devem ser configurados com uma rota estática padrão enviar todo o tráfego para os dispositivos de borda. Uma exceção de tráfego para essa regra é privados espaço que será bloqueado, usando uma lista de controle de acesso aplicadas em TOR a conexão de borda.

Todo o restante deve ser o mesmo que o primeiro método. O roteamento dinâmico do BGP ainda será usado em rack porque ele é uma ferramenta essencial para o SLB e outros componentes e não pode ser desabilitado ou removido.

![roteamento estático](media/azure-stack-border-connectivity/static-routing.png)

## <a name="transparent-proxy"></a>Proxy transparente
Se seu datacenter requer que todo o tráfego para usar um proxy, você deve configurar um *proxy transparente* para processar todo o tráfego do rack para tratá-la de acordo com a política, separar o tráfego entre as zonas em sua rede.

> [!IMPORTANT]
> A solução de pilha do Azure não oferece suporte a proxies da web normal.  

Um proxy transparente (também conhecido como uma interceptação, embutido ou proxy forçado) intercepta comunicação normal na camada de rede sem a necessidade de qualquer configuração especial do cliente. Os clientes não precisam estar atento a existência do proxy.

![Proxy transparente](media/azure-stack-border-connectivity/transparent-proxy.png)

## <a name="next-steps"></a>Próximas etapas
[Integração do DNS](azure-stack-integrate-dns.md)