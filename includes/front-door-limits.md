---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 9/17/2018
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: e3fa5616518675d8475937ec63afdd8e1742e8c6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553216"
---
| Recurso | Limite padrão |
| --- | --- |
| Recursos de serviço de porta da frente do Azure por assinatura | 100 |
| Hosts de front-end, que inclui os domínios personalizados por recurso | 100 |
| Regras de roteamento por recurso | 100 |
| Pools de back-end por recurso | 50 |
| Back-ends por pool de back-end | 100 |
| Padrões de caminho para corresponder a uma regra de roteamento | 25 |
| Regras de firewall do aplicativo Web personalizadas por política | 10 |
| Política do firewall do aplicativo Web por recurso | 100 |

### <a name="timeout-values"></a>Valores de tempo limite
#### <a name="client-to-front-door"></a>Cliente para o Front Door
- O Front Door tem um tempo limite de conexão TCP ocioso de 61 segundos.

#### <a name="front-door-to-application-back-end"></a>Porta da frente para back-end do aplicativo
- Se a resposta é uma resposta em partes, uma resposta 200 é retornado se ou quando a primeira parte é recebida.
- Depois que a solicitação HTTP é encaminhada para o back-end, porta da frente aguarda 30 segundos para o primeiro pacote de back-end. Em seguida, ele retorna um erro 503 ao cliente.
- Depois que o primeiro pacote é recebido do back-end, porta da frente aguarda 30 segundos em um tempo limite de ociosidade. Em seguida, ele retorna um erro 503 ao cliente.
- Porta da frente para o tempo limite da sessão TCP back-end é de 30 minutos.

### <a name="upload-and-download-data-limit"></a>Carregar e baixar o limite de dados

|  | Com codificação (CTE) de transferência em partes | Sem agrupamento de HTTP |
| ---- | ------- | ------- |
| **Baixar** | Não há nenhum limite no tamanho do download. | Não há nenhum limite no tamanho do download. |
| **Upload** |  Não há nenhum limite desde que cada carregamento CTE for inferior a 28,6 MB. | O tamanho não pode ser maior do que 28,6. MB. |
