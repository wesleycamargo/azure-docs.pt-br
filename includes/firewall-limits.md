---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 7/30/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: f1fd32fc02bee47d229093680166e6a13a5ba721
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2018
ms.locfileid: "49458813"
---
| Recurso | Limite padrão |
| --- | --- |
| Dados processados |1000 TB/firewall/mês <sup>1</sup> |
|Regras|10k - todas os tipos de regras combinadas|
|Emparelhamento VNet|Para implementações de hub e spoke, máximo de 50 redes virtuais de spoke.|
|Emparelhamento global|Sem suporte. Você deve ter pelo menos uma implantação do firewall por região.|
|Número máximo de portas em uma única regra de rede|15<br>Um intervalo de portas (por exemplo: 2-10) é contado como dois.


<sup>1</sup> Contate o suporte do Azure caso precise aumentar esses limites.
