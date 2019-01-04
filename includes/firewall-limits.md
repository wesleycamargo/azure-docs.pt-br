---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 12/14/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 7d905550114bb76a0a091146b3972bab4a652022
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53429917"
---
| Recurso | Limite padrão |
| --- | --- |
| Dados processados |1000 TB/firewall/mês <sup>1</sup> |
|Regras|10k - todas os tipos de regras combinadas|
|Emparelhamento global|Sem suporte. Você deve ter pelo menos uma implantação do firewall por região.|
|Número máximo de portas em uma única regra de rede|15<br>Um intervalo de portas (por exemplo: 2 - 10) é contado como dois.
|Tamanho mínimo de AzureFirewallSubnet |/ 26|
|Intervalo de portas em regras de rede e de aplicativo|0-64.000. Estamos trabalhando para flexibilizar essa limitação.|
|


<sup>1</sup> Contate o suporte do Azure caso precise aumentar esses limites.
