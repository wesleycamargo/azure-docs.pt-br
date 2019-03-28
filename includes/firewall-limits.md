---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 3/25/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 368a7cf1d372d79f062affe9b206d070afe5c2f7
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58505937"
---
| Recurso | Limite padrão |
| --- | --- |
| Taxa de transferência de dados |30 Gbps<sup>1</sup> |
|Regras|10.000, regra tipos combinados.|
|Tamanho mínimo de AzureFirewallSubnet |/ 26|
|Intervalo de portas em regras de rede e de aplicativo|0-64.000. Estamos trabalhando para flexibilizar essa limitação.|
|Tabela de rotas|Por padrão, AzureFirewallSubnet tem uma rota 0.0.0.0/0 com o valor de NextHopType definido como **Internet**.<br><br>Se você habilitar o túnel forçado para o local por meio do Gateway de VPN ou ExpressRoute, você precisa configurar uma rota definida pelo usuário de 0.0.0.0/0 (UDR) com o conjunto de valores de NextHopType como a Internet e associá-la ao seu AzureFirewallSubnet explicitamente. Isso substitui um gateway padrão de potencial anúncio de BGP para sua rede local. Se sua organização exige que o túnel forçado para o Firewall do Azure direcionar o tráfego de gateway padrão voltar por meio de sua rede local, contate o suporte. Podemos colocar sua assinatura para garantir que o firewall necessário conectividade com a Internet é mantida.|

<sup>1</sup>caso você precise aumentar esses limites, entre em contato com o suporte do Azure.
