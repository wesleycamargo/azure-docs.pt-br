---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: victorh
ms.openlocfilehash: 3d66d825306c5183bdd8d8e611d98904eef2022a
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52439961"
---
| Recurso | Limite padrão | Observação |
| --- | --- | --- |
| Gateway de Aplicativo |1000 por assinatura | |
| Configuração do IP de front-end |2 |1 pública e 1 privada |
| Portas de front-end |40 | |
| Pool de endereços de back-end |40 | |
| Servidores de back-end por pool |1.200 | |
| Ouvintes HTTP |40 | |
| Regras de balanceamento de carga HTTP |400 |número de ouvintes HTTP * n |
| Configurações HTTP de back-end |40 | |
| Instâncias por gateway |75 | |
| Certificados SSL |40 |1 por ouvintes HTTP |
| Certificados de autenticação |40 | |
| Tempo limite mínimo de solicitação |1 segundo | |
| Tempo limite máximo de solicitação |24 horas | |
| Número de sites |40 |1 por ouvintes HTTP |
| Mapas de URL por ouvinte |1 | |
| Regras com base em caminho máximo por mapa de URL|100|
| Configurações de redirecionamento |40| |
| Conexões WebSocket Concorrentes |5.000| |
|Tamanho máximo da URL|8000|
| Tamanho de carregamento de arquivo máximo Padrão |2 GB | |
| Tamanho de carregamento de arquivo máximo WAF |Gateways de WAF médio, 100 MB<br>Gateways de WAF grandes - 500 MB| |
|Limite de tamanho de corpo de WAF (sem arquivos)|128 KB|
