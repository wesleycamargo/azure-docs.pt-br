---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 5/3/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 8709d4d903bd31ff94d04ec61e226857e4190407
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150356"
---
| Resource | Limite padrão |
| --- | --- |
| Taxa de transferência de dados |30 Gbps<sup>1</sup> |
|Regras|10.000, regra tipos combinados.|
|Tamanho mínimo de AzureFirewallSubnet |/ 26|
|Intervalo de portas em regras de rede e de aplicativo|0-64.000. Estamos trabalhando para flexibilizar essa limitação.|
|Tabela de rotas|Por padrão, AzureFirewallSubnet tem uma rota 0.0.0.0/0 com o valor de NextHopType definido como **Internet**.<br><br>Firewall do Azure deve ter conectividade direta com a Internet. Se seu AzureFirewallSubnet aprende uma rota padrão à sua rede local por meio do BGP, você deve substituí-lo com um UDR 0.0.0.0/0 com o **NextHopType** o valor definido como **Internet** manter direto Conectividade com a Internet. Por padrão, o Firewall do Azure não dá suporte a túnel forçado para uma rede local.<br><br>No entanto, se sua configuração exigir um túnel forçado para uma rede local, a Microsoft oferecerá suporte-lo caso a caso. Contate o suporte para que possamos examinar seu caso. Se aceita, vamos lista branca de sua assinatura e verifique se que a conectividade de Internet de firewall necessárias é mantida.|

<sup>1</sup>caso você precise aumentar esses limites, entre em contato com o suporte do Azure.
