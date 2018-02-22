---
title: "Arquivo de inclusão"
description: "Arquivo de inclusão"
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/12/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 0e89371720dd8bce1009ede922b2b5cd86a61363
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2018
---
A tabela a seguir lista as informações de cota específicas às mensagens do Barramento de Serviço. Para obter informações sobre preços e outras cotas do Barramento de Serviço, consulte a visão geral [Preços do Barramento de Serviço](https://azure.microsoft.com/pricing/details/service-bus/) .

| Nome da Cota | Escopo | type | Comportamento quando excedido | Valor |
| --- | --- | --- | --- | --- |
| Número máximo de namespaces básico/standard por assinatura do Azure |Namespace |estático |As solicitações subsequentes por namespaces adicionais básico/standard são rejeitadas pelo portal. |100|
| Número máximo de namespaces premium por assinatura do Azure |Namespace |estático |As solicitações subsequentes por namespaces adicionais premium são rejeitadas pelo portal. |10 |
| Tamanho do tópico/fila |Entidade |Definido na criação do tópico/fila. |Mensagens de entrada são rejeitadas e uma exceção é recebida pelo código de chamada. |1, 2, 3, 4 ou 5 GB.<br /><br />Se [particionamento](../articles/service-bus-messaging/service-bus-partitioning.md) for habilitado, o tamanho máximo do tópico/fila será de 80 GB. |
| Número de conexões simultâneas em um namespace |Namespace |estático |Solicitações subsequentes de conexões adicionais são rejeitadas e uma exceção é recebida pelo código de chamada. Operações REST não são consideradas conexões TCP simultâneas. |NetMessaging: 1.000<br /><br />AMQP: 5.000 |
| Número de solicitações de recebimento simultâneas em uma entidade de fila/tópico/assinatura |Entidade |estático |Solicitações de recebimento subsequentes são rejeitadas e uma exceção é recebida pelo código de chamada. Essa cota aplica-se ao número combinado de operações de recebimento simultâneas em todas as assinaturas em um tópico. |5.000 |
| Número de tópicos/filas por namespace de serviço |Todo o sistema |estático |Solicitações subsequentes de criação de um novo tópico ou fila no namespace de serviço são rejeitadas. Consequentemente, se configuradas por meio do [Portal do Azure][Azure portal], uma mensagem de erro é gerada. Se chamado da API de gerenciamento, uma exceção é recebida pelo código de chamada. |10.000<br /><br />O número total de tópicos mais filas em um namespace de serviço deve ser menor ou igual a 10.000.<br/>Isso não é aplicável para Premium, pois todas as entidades são particionadas. |
| Número de tópicos/filas particionados por namespace de serviço |Todo o sistema |estático |As solicitações subsequentes para a criação de um novo tópico ou fila particionado no namespace de serviço são rejeitadas. Consequentemente, se configuradas por meio do [Portal do Azure][Azure portal], uma mensagem de erro é gerada. Se chamado da API de gerenciamento, uma exceção **QuotaExceededException** é recebida pelo código de chamada. |Camadas básica e padrão - 100<br />[Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) -1.000 (por unidade do sistema de mensagens)<br/><br />Cada fila ou tópico particionado conta para a cota de 10.000 entidades por namespace. |
| Tamanho máximo de qualquer caminho de entidade de mensagens: fila ou tópico |Entidade |estático |- |260 caracteres |
| Tamanho máximo de qualquer nome de entidade de mensagens: namespace, assinatura ou regra de assinatura |Entidade |estático |- |50 caracteres |
| Tamanho da mensagem para uma entidade de fila/tópico/assinatura |Todo o sistema |estático |As mensagens de entrada que excederem essas cotas serão rejeitadas e uma exceção será recebida pelo código de chamada. |Tamanho máximo da mensagem: 256KB ([Camada padrão](../articles/service-bus-messaging/service-bus-premium-messaging.md))/1MB ([Camada premium](../articles/service-bus-messaging/service-bus-premium-messaging.md)). <br /><br />**Observação** devido à sobrecarga do sistema, esse limite geralmente é um pouco menor.<br /><br />Tamanho máximo do cabeçalho: 64KB<br /><br />Número máximo de propriedades de cabeçalho no recipiente de propriedades: **byte/int.MaxValue**<br /><br />tamanho máximo da propriedade no recipiente de propriedades: nenhum limite explícito. Limitado pelo tamanho máximo do cabeçalho. |
| Tamanho de propriedade de mensagem para uma entidade de fila/tópico/assinatura |Todo o sistema |estático |Uma exceção **SerializationException** é gerada. |O tamanho máximo de propriedade de mensagem para cada propriedade é de 32 K. O tamanho cumulativo de todas as propriedades não pode exceder 64 K. Isso se aplica a todo o cabeçalho de [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), que tem as duas propriedades de usuário, bem como propriedades do sistema (como [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label), [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid) e assim por diante). |
| Número de assinaturas por tópico |Todo o sistema |estático |As solicitações subsequentes para a criação de assinaturas adicionais para o tópico são rejeitadas. Como resultado, se configuradas por meio do portal, uma mensagem de erro é mostrada. Se chamado da API de gerenciamento, uma exceção é recebida pelo código de chamada. |2.000 |
| Número de filtros SQL por tópico |Todo o sistema |estático |As solicitações subsequentes de criação de filtros adicionais para o tópico são rejeitadas e uma exceção é recebida pelo código de chamada. |2.000 |
| Número de filtros de correlação por tópico |Todo o sistema |estático |As solicitações subsequentes de criação de filtros adicionais para o tópico são rejeitadas e uma exceção é recebida pelo código de chamada. |100.000 |
| Tamanho de filtros/ações SQL |Todo o sistema |estático |As solicitações subsequentes de criação de filtros adicionais são rejeitadas e uma exceção é recebida pelo código de chamada. |Comprimento máximo da cadeia de caracteres de condição de filtro: 1024 (1 K).<br /><br />Comprimento máximo da cadeia de caracteres de ação de regra: 1024 (1 K).<br /><br />Número máximo de expressões por ação de regra: 32. |
| Número de regras [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) por namespace, fila ou tópico |Entidade, namespace |estático |As solicitações subsequentes de criação de regras adicionais são rejeitadas e uma exceção é recebida pelo código de chamada. |Número máximo de regras: 12. <br /><br /> As regras configuradas em um namespace do Barramento de Serviço se aplicam a todas as filas e tópicos no namespace. |
| Número de mensagens por transação | Todo o sistema | estático | As mensagens de entrada adicionais são rejeitadas e uma exceção indicando "Não é possível enviar mais de 100 mensagens em uma única transação" é recebida pelo código de chamada. | 100 <br /><br /> Para ambas as operações **Send()** e **SendAsync()**. |

[Azure portal]: https://portal.azure.com