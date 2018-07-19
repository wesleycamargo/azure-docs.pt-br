---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/04/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 0ff1e31e52c7db5c41f92cb9e4cb1a17f28dea6f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755479"
---
A tabela a seguir lista as informações de cota específicas às mensagens do Barramento de Serviço. Para obter informações sobre preços e outras cotas do Barramento de Serviço, consulte a visão geral [Preços do Barramento de Serviço](https://azure.microsoft.com/pricing/details/service-bus/) .

| Nome da Cota | Escopo | Observações | Valor |
| --- | --- | --- | --- | --- |
| Número máximo de namespaces básico/standard por assinatura do Azure |Namespace |As solicitações subsequentes por namespaces adicionais básico/standard são rejeitadas pelo portal. |100|
| Número máximo de namespaces premium por assinatura do Azure |Namespace |As solicitações subsequentes por namespaces adicionais premium são rejeitadas pelo portal. |10 |
| Tamanho do tópico/fila |Entidade |Definido na criação do tópico/fila. <br/><br/> Mensagens de entrada subsequentes são rejeitadas e uma exceção é recebida pelo código de chamada. |1, 2, 3, 4 GB ou 5 GB.<br /><br />No SKU Premium, bem como Standard com [particionamento](/azure/service-bus-messaging/service-bus-partitioning) habilitado, o tamanho máximo de fila/tópico é de 80 GB. |
| Número de conexões simultâneas em um namespace |Namespace |Solicitações subsequentes de conexões adicionais são rejeitadas e uma exceção é recebida pelo código de chamada. Operações REST não são consideradas conexões TCP simultâneas. |NetMessaging: 1.000<br /><br />AMQP: 5.000 |
| Número de solicitações de recebimento simultâneas em uma entidade de fila/tópico/assinatura |Entidade |Solicitações de recebimento subsequentes são rejeitadas e uma exceção é recebida pelo código de chamada. Essa cota aplica-se ao número combinado de operações de recebimento simultâneas em todas as assinaturas em um tópico. |5.000 |
| Número de tópicos/filas por namespace |Namespace |Solicitações subsequentes de criação de um novo tópico ou fila no namespace são rejeitadas. Consequentemente, se configuradas por meio do [Portal do Azure][Azure portal], uma mensagem de erro é gerada. Se chamado da API de gerenciamento, uma exceção é recebida pelo código de chamada. |10.000<br /><br />O número total de tópicos mais filas em um namespace de serviço deve ser menor ou igual a 10.000. |
| Número de [tópicos/filas](/azure/service-bus-messaging/service-bus-partitioning) particionados por namespace de serviço |Namespace |As solicitações subsequentes para a criação de um novo tópico ou fila particionado no namespace são rejeitadas. Consequentemente, se configuradas por meio do [Portal do Azure][Azure portal], uma mensagem de erro é gerada. Se chamado da API de gerenciamento, uma exceção **QuotaExceededException** é recebida pelo código de chamada. |Camadas básica e padrão - 100<br/><br/>Não há suporte para entidades particionadas na camada [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md).<br/><br />Cada fila ou tópico particionado conta para a cota de 10.000 entidades por namespace. |
| Tamanho máximo de qualquer caminho de entidade de mensagens: fila ou tópico |Entidade |- |260 caracteres |
| Tamanho máximo de qualquer nome de entidade de mensagens: namespace, assinatura ou regra de assinatura |Entidade |- |50 caracteres |
| Tamanho máximo de uma mensagem [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | Entidade |- | 128 |
| Tamanho da mensagem para uma entidade de fila/tópico/assinatura |Entidade |As mensagens de entrada que excederem essas cotas serão rejeitadas e uma exceção será recebida pelo código de chamada. |Tamanho máximo da mensagem: 256KB ([camada Standard](../articles/service-bus-messaging/service-bus-premium-messaging.md)) / 1 MB ([camada Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md)). <br /><br />Devido à sobrecarga do sistema, esse limite é menor do que esses valores.<br /><br />Tamanho máximo do cabeçalho: 64KB<br /><br />Número máximo de propriedades de cabeçalho no recipiente de propriedades: **byte/int.MaxValue**<br /><br />tamanho máximo da propriedade no recipiente de propriedades: nenhum limite explícito. Limitado pelo tamanho máximo do cabeçalho. |
| Tamanho de propriedade de mensagem para uma entidade de fila/tópico/assinatura |Entidade |Uma exceção **SerializationException** é gerada. |O tamanho máximo da propriedade da mensagem para cada propriedade é de 32 K. O tamanho cumulativo de todas as propriedades não pode exceder 64 K. Esse limite se aplica a todo o cabeçalho do [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), que possui propriedades de usuário, bem como propriedades do sistema (como [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label), [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid), e assim por diante). |
| Número de assinaturas por tópico |Entidade |As solicitações subsequentes para a criação de assinaturas adicionais para o tópico são rejeitadas. Como resultado, se configuradas por meio do portal, uma mensagem de erro é mostrada. Se chamado da API de gerenciamento, uma exceção é recebida pelo código de chamada. |2.000 |
| Número de filtros SQL por tópico |Entidade |As solicitações subsequentes de criação de filtros adicionais para o tópico são rejeitadas e uma exceção é recebida pelo código de chamada. |2.000 |
| Número de filtros de correlação por tópico |Entidade |As solicitações subsequentes de criação de filtros adicionais para o tópico são rejeitadas e uma exceção é recebida pelo código de chamada. |100.000 |
| Tamanho de filtros/ações SQL |Namespace |As solicitações subsequentes de criação de filtros adicionais são rejeitadas e uma exceção é recebida pelo código de chamada. |Comprimento máximo da cadeia de caracteres de condição de filtro: 1024 (1 K).<br /><br />Comprimento máximo da cadeia de caracteres de condição de função: 1024 (1 K).<br /><br />Número máximo de expressões por ação de regra: 32. |
| Número de regras [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) por namespace, fila ou tópico |Entidade, namespace |As solicitações subsequentes de criação de regras adicionais são rejeitadas e uma exceção é recebida pelo código de chamada. |Número máximo de regras: 12. <br /><br /> As regras configuradas em um namespace do Barramento de Serviço se aplicam a todas as filas e tópicos no namespace. |
| Número de mensagens por transação | Transação | As mensagens de entrada adicionais são rejeitadas e uma exceção indicando "Não é possível enviar mais de 100 mensagens em uma única transação" é recebida pelo código de chamada. | 100 <br /><br /> Para ambas as operações **Send()** e **SendAsync()**. |

[Azure portal]: https://portal.azure.com