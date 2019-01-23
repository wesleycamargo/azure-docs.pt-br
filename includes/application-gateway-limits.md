---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: victorh
ms.openlocfilehash: 66dea07a1ff725c6707b19bc6ebdc5563f1b158b
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54211798"
---
| Recurso | Limite padrão | Observação |
| --- | --- | --- |
| Gateway de Aplicativo |1000 por assinatura | |
| Configuração de IP de front-end |2 |1 pública e 1 privada |
| Portas de front-end |100<sup>1</sup> | |
| Pools de endereços de back-end |100<sup>1</sup> | |
| Servidores de back-end por pool |1.200 | |
| Ouvintes HTTP |100<sup>1</sup> | |
| Regras de balanceamento de carga HTTP |100<sup>1</sup> | |
| Configurações de HTTP de back-end |100<sup>1</sup> | |
| Instâncias por gateway |32 | |
| Certificados SSL |100<sup>1</sup> |1 por ouvintes HTTP |
| Certificados de autenticação |100 | |
| Certificados raiz confiáveis |100 | |
| Tempo limite mínimo de solicitação |1 segundo | |
| Tempo limite máximo de solicitação |24 horas | |
| Número de sites |100<sup>1</sup> |1 por ouvintes HTTP |
| Mapas de URL por ouvinte |1 | |
| Regras com base em caminho máximo por mapa de URL|100||
| Configurações de redirecionamento |100<sup>1</sup>| |
| Conexões WebSocket Simultâneas |5.000| |
| Tamanho máximo da URL|8000||
| Tamanho de carregamento de arquivo máximo Padrão |2 GB | |
| Tamanho de carregamento de arquivo máximo WAF |Gateways de WAF médio, 100 MB<br>Gateways de WAF grandes - 500 MB| |
| Limite de tamanho de corpo de WAF (sem arquivos)|128 KB||

<sup>1</sup> No caso de SKUs habilitadas para WAF, é recomendável limitar o número de recursos a 40 para otimizar o desempenho.
