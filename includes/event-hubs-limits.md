---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 02/26/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 9d6b54027adcf2b12c6ca4081a11208a31f620e8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59803209"
---
A tabela a seguir relaciona as cotas e limites específicos para os [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/). Para saber mais sobre os preços dos Hubs de Eventos, veja os [preços dos Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

| Limite | Escopo | Observações | Valor |
| --- | --- | --- | --- |
| Número de namespaces de Hubs de Eventos do Azure por assinatura |Assinatura |- |100 |
| Número de hubs de eventos por namespace |Namespace |Solicitações subsequentes para a criação de um novo hub de eventos são rejeitadas. |10 |
| O número de partições por hub de eventos |Entidade |- |32 |
| Número de grupos de consumidores por hub de eventos |Entidade |- |20 |
| Número de conexões AMQP por namespace |Namespace |Solicitações subsequentes de conexões adicionais são rejeitadas e uma exceção é recebida pelo código de chamada. |5.000 |
| Tamanho máximo de eventos de Hubs de Eventos|Entidade |- |1 MB |
| Tamanho máximo do nome de um hub de eventos |Entidade |- |50 caracteres |
| Número de destinatários sem época por grupo de consumidores |Entidade |- |5 |
| Período de retenção máximo dos dados do evento |Entidade |- |Um a sete dias |
| Unidades de produtividade máxima |Namespace |Exceder o limite de unidade de produtividade faz com que seus dados para ser limitadas e gera uma [exceção de servidor ocupado](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Para solicitar um número maior de unidades de taxa de transferência para uma camada Standard, arquivo de um [solicitação de suporte](/azure/azure-supportability/how-to-create-azure-support-request). As [unidades de produtividade adicionais](../articles/event-hubs/event-hubs-auto-inflate.md) estão disponíveis em blocos de 20, em uma base de compra garantida. |20 |
| Número de regras de autorização por namespace |Namespace|Solicitações subsequentes de criação de regra de autorização são rejeitadas.|12 |
