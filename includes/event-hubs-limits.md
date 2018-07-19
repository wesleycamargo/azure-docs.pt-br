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
ms.openlocfilehash: ab4c5b98ed9f6fcc8c271797db2d81dcc7ec4449
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755458"
---
A tabela a seguir relaciona as cotas e limites específicos para os [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/). Para saber mais sobre os preços dos Hubs de Eventos, veja os [preços dos Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

| Limite | Escopo | Observações | Valor |
| --- | --- | --- | --- | --- |
| Número de hubs de eventos por namespace |Namespace |As solicitações seguintes para a criação de um novo hub de eventos serão rejeitadas. |10 |
| O número de partições por hub de eventos |Entidade |- |32 |
| Número de grupos de consumidores por hub de eventos |Entidade |- |20 |
| Número de conexões AMQP por namespace |Namespace |As solicitações subsequentes de conexões adicionais serão rejeitadas e uma exceção é recebida pelo código de chamada. |5.000 |
| Tamanho máximo de eventos de Hubs de Eventos|Entidade |- |256 KB |
| Tamanho máximo do nome de um hub de eventos |Entidade |- |50 caracteres |
| Número de destinatários sem época por grupo de consumidores |Entidade |- |5 |
| Período de retenção máximo dos dados do evento |Entidade |- |Um a sete dias |
| Unidades de produtividade máxima |Namespace |Exceder o limite de unidades de produtividade causará a limitação dos seus dados e gerará um **[ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)**. Você pode solicitar um número maior de unidades de produtividade para uma camada Standard ao preencher uma [solicitação de suporte](/azure/azure-supportability/how-to-create-azure-support-request). As [unidades de produtividade adicionais](../articles/event-hubs/event-hubs-auto-inflate.md) estão disponíveis em blocos de 20, em uma base de compra garantida. |20 |
| Número de regras de autorização por namespace |Namespace|As solicitações subsequentes de criação de regra de autorização serão rejeitadas.|12 |
