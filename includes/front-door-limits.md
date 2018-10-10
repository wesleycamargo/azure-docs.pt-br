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
ms.openlocfilehash: f0c2d1501b9aa19dec8c4ad157e004a57e0e5070
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006462"
---
| Recurso | Limite padrão |
| --- | --- |
| Recursos do Front Door por assinatura | 100 |
| Hosts de front-end incluindo domínios personalizados por recurso | 100 |
| Regras de roteamento por recurso | 100 |
| Pools de back-end por recurso | 50 |
| Back-ends por pool de back-end | 100 |
| Padrões de caminho para corresponder a uma regra de roteamento | 25 |
| Regras de firewall do aplicativo Web personalizadas por política | 10 |
| Política do firewall do aplicativo Web por recurso | 100 |

### <a name="timeout-values"></a>Valores de tempo limite
#### <a name="client-to-front-door"></a>Cliente para o Front Door
- O Front Door tem um tempo limite de conexão TCP ocioso de 61 segundos.
#### <a name="front-door-to-application-backend"></a>Front Door para o back-end do aplicativo
- Se a resposta for uma resposta em partes, será retornado 200 se/quando a primeira parte for recebida.
- Depois que a solicitação HTTP for encaminhada para o back-end, o Front Door aguardará 30 segundos pelo primeiro pacote do back-end, antes de retornar um erro 503 para o cliente.
- Após o recebimento do primeiro pacote do back-end, o Front Door aguarda 30 segundos (tempo limite ocioso) antes de retornar um erro 503 ao cliente.
- O tempo limite da sessão do Front Door para o TCP de back-end é de 30 minutos.

### <a name="upload--download-data-limit"></a>Carregar/baixar limite de dados

|  | Com CTE (Codificação de transferência em bloco) | Sem agrupamento de HTTP |
| ---- | ------- | ------- |
| **Baixar** | Não há nenhum limite para o tamanho do download | Não há nenhum limite para o tamanho do download |
| **Upload** |  Não há limite, desde que cada upload de CTE seja inferior a 28,6 MB | O tamanho não pode ser maior do que 28,6 MB. |
