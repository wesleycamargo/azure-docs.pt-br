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
ms.openlocfilehash: 0a4a66543b323b1dbe56158851dafcb1dfe695f0
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47185547"
---
| Recurso | Limite padrão |
| --- | --- |
| Dados processados |1000 TB/firewall/mês <sup>1</sup> |
|Regras|regras de aplicativo 10k, regras de rede 10k|
|Emparelhamento VNet|Para implementações de hub e spoke, máximo de 50 redes virtuais de spoke.|
|Emparelhamento global|Sem suporte. Você deve ter pelo menos uma implantação do firewall por região.|
|Número máximo de portas em uma única regra de rede|15<br>Um intervalo de portas (por exemplo: 2-10) é contado como dois.


<sup>1</sup> Contate o suporte do Azure caso precise aumentar esses limites.
