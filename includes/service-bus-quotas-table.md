---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 12/13/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7add8c10fd3224b9c287ea4cc672191157f56a09
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58124364"
---
A tabela a seguir lista informações sobre cotas específicas para mensagens do barramento de serviço do Azure. Para obter informações sobre preços e outras cotas do barramento de serviço, consulte [preços do barramento de serviço](https://azure.microsoft.com/pricing/details/service-bus/).

| Nome da cota | Escopo | Observações | Valor |
| --- | --- | --- | --- |
| Número máximo de namespaces básico ou Standard por assinatura do Azure |Namespace |As solicitações subsequentes por namespaces básico ou Standard adicionais são rejeitadas pelo portal do Azure. |100|
| Número máximo de namespaces Premium por assinatura do Azure |Namespace |As solicitações subsequentes para os namespaces adicionais Premium são rejeitadas pelo portal. |25 |
| Tamanho da fila ou tópico |Entidade |Definido na criação da fila ou tópico. <br/><br/> Mensagens de entrada subsequentes são rejeitadas e uma exceção é recebida pelo código de chamada. |1, 2, 3, 4 GB ou 5 GB.<br /><br />O SKU Premium e o SKU Standard com [particionamento](/azure/service-bus-messaging/service-bus-partitioning) habilitado, o tamanho máximo de fila ou tópico é 80 GB. |
| Número de conexões simultâneas em um namespace |Namespace |Solicitações subsequentes de conexões adicionais são rejeitadas e uma exceção é recebida pelo código de chamada. Operações REST não contam para conexões TCP simultâneas. |NetMessaging: 1,000.<br /><br />AMQP: 5,000. |
| Número de recebimento simultâneas solicitações em uma entidade de fila, tópico ou assinatura |Entidade |Recebimento subsequentes solicitações são rejeitadas e uma exceção é recebida pelo código de chamada. Essa cota aplica-se ao número combinado de operações de recebimento simultâneas em todas as assinaturas em um tópico. |5.000 |
| Número de tópicos ou filas por namespace |Namespace |Solicitações subsequentes de criação de um novo tópico ou fila no namespace são rejeitadas. Consequentemente, se configuradas por meio do [Portal do Azure][Azure portal], uma mensagem de erro é gerada. Se chamado da API de gerenciamento, uma exceção é recebida pelo código de chamada. |1.000 para a camada básica ou Standard. O número total de tópicos e filas em um namespace deve ser menor ou igual a 1.000. <br/><br/>Para a camada Premium, 1.000 por unidade de mensagens (MU). Limite máximo é 4.000. |
| Número de [tópicos ou filas particionados](/azure/service-bus-messaging/service-bus-partitioning) por namespace |Namespace |As solicitações subsequentes para a criação de um novo tópico ou fila particionado no namespace são rejeitadas. Consequentemente, se configuradas por meio do [Portal do Azure][Azure portal], uma mensagem de erro é gerada. Se chamado da API de gerenciamento, a exceção **QuotaExceededException** é recebida pelo código de chamada. |Básica e as camadas padrão: 100.<br/><br/>Entidades particionadas não têm suporte nas [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) camada.<br/><br />Cada fila ou tópico particionado conta como a cota de 1.000 entidades por namespace. |
| Tamanho máximo de qualquer caminho de entidade de mensagens: fila ou tópico |Entidade |- |260 caracteres. |
| Tamanho máximo de qualquer nome de entidade de mensagens: namespace, assinatura ou regra de assinatura |Entidade |- |50 caracteres. |
| Tamanho máximo de uma [mensagem ID](/dotnet/api/microsoft.azure.servicebus.message.messageid) | Entidade |- | 128 |
| Tamanho máximo de uma mensagem [ID de sessão](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | Entidade |- | 128 |
| Tamanho de mensagem para uma entidade de fila, tópico ou assinatura |Entidade |Mensagens de entrada que excederem essas cotas serão rejeitadas e uma exceção é recebida pelo código de chamada. |Tamanho máximo da mensagem: 256 KB para [camada Standard](../articles/service-bus-messaging/service-bus-premium-messaging.md), 1 MB para [camada Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />Devido à sobrecarga do sistema, esse limite é menor do que esses valores.<br /><br />Tamanho máximo do cabeçalho: 64 KB.<br /><br />Número máximo de propriedades de cabeçalho no recipiente de propriedades: **byte/int. MaxValue**.<br /><br />Tamanho máximo da propriedade no recipiente de propriedades: Nenhum limite explícito. Limitado pelo tamanho máximo do cabeçalho. |
| Tamanho de propriedade de mensagem para uma entidade de fila, tópico ou assinatura |Entidade | A exceção **SerializationException** é gerado. |Tamanho máximo da mensagem da propriedade para cada propriedade é 32.000. Tamanho cumulativo de todas as propriedades não pode exceder 64.000. Esse limite se aplica ao cabeçalho inteiro do [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), que tem as propriedades de usuário e propriedades do sistema, tais como [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [rótulo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)e [ MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid). |
| Número de assinaturas por tópico |Entidade |As solicitações subsequentes para a criação de assinaturas adicionais para o tópico são rejeitadas. Como resultado, se configuradas por meio do portal, uma mensagem de erro é mostrada. Se chamado da API de gerenciamento, uma exceção é recebida pelo código de chamada. |Camada Standard: Cada assinatura conta com a cota de 1.000 entidades, ou seja, filas, tópicos e assinaturas, por namespace. <br/> <br/> Camada Premium: 2,000. |
| Número de filtros SQL por tópico |Entidade |Solicitações subsequentes de criação de filtros adicionais para o tópico serão rejeitadas e uma exceção é recebida pelo código de chamada. |2.000 |
| Número de filtros de correlação por tópico |Entidade |Solicitações subsequentes de criação de filtros adicionais para o tópico serão rejeitadas e uma exceção é recebida pelo código de chamada. |100.000 |
| Tamanho de filtros SQL ou ações |Namespace |Solicitações subsequentes de criação de filtros adicionais são rejeitadas e uma exceção é recebida pelo código de chamada. |Comprimento máximo da cadeia de caracteres de condição de filtro: 1.024 (1 K).<br /><br />Comprimento máximo da cadeia de caracteres de condição de função: 1.024 (1 K).<br /><br />Número máximo de expressões por ação de regra: 32. |
| Número de regras [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) por namespace, fila ou tópico |Entidade, namespace |Solicitações subsequentes para a criação de regras adicionais são rejeitadas e uma exceção é recebida pelo código de chamada. |Número máximo de regras: 12. <br /><br /> As regras configuradas em um namespace do Barramento de Serviço se aplicam a todas as filas e tópicos no namespace. |
| Número de mensagens por transação | Transação | Mensagens de entrada adicionais serão rejeitadas e uma exceção indicando "não é possível enviar mais de 100 mensagens em uma única transação" é recebida pelo código de chamada. | 100 <br /><br /> Para ambas as operações **Send()** e **SendAsync()**. |

[Azure portal]: https://portal.azure.com