---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 11/26/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 8b236bc1f0089b89aca90e7c69e9b445b01a374b
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52439948"
---
| Recurso | Limite padrão |
| --- | --- |
| Dados processados |1000 TB/firewall/mês <sup>1</sup> |
|Regras|10k - todas os tipos de regras combinadas|
|Emparelhamento global|Sem suporte. Você deve ter pelo menos uma implantação do firewall por região.|
|Número máximo de portas em uma única regra de rede|15<br>Um intervalo de portas (por exemplo: 2-10) é contado como dois.
|Tamanho mínimo de AzureFirewallSubnet |/ 26


<sup>1</sup> Contate o suporte do Azure caso precise aumentar esses limites.
