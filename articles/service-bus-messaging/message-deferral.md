---
title: "Adiamento de mensagens do Barramento de Serviço do Azure | Microsoft Docs"
description: "Adiar a entrega de mensagens do Barramento de Serviço"
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: sethm
ms.openlocfilehash: bece2be88a020610dfd3d22f15f7d276d99bb153
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="message-deferral"></a>Adiamento de mensagens

Quando um cliente de fila ou assinatura recebe uma mensagem que está disposto a processar, mas para a qual o processamento não é possível no momento devido a circunstâncias especiais dentro do aplicativo, ele tem a opção de “adiar” a recuperação da mensagem para um ponto posterior. A mensagem permanece na fila ou assinatura, mas é reservada.

O adiamento é um recurso criado especificamente para cenários de processamento de fluxo de trabalho. As estruturas de fluxo de trabalho podem exigir que determinadas operações sejam processadas em uma ordem específica e talvez precisem adiar o processamento de algumas mensagens recebidas até o trabalho anterior prescrito informado por outras mensagens ter sido concluído.

Um exemplo ilustrativo simples disso é uma sequência de processamento de ordem em que uma notificação de pagamento de um provedor de pagamento externo aparece em um sistema antes de a ordem de compra correspondente ter sido propagada da frente da loja para o sistema de atendimento. Nesse caso, o sistema de atendimento pode adiar o processamento da notificação de pagamento até que haja um pedido ao qual associá-la. Em cenários de reunião, em que mensagens de origens diferentes movem o fluxo de trabalho adiante, a ordem de execução em tempo real pode realmente estar correta, mas as mensagens refletindo os resultados podem chegar fora de ordem.

Por fim, o adiamento ajuda na reordenação das mensagens da ordem de chegada em uma ordem na qual elas possam ser processadas, deixando essas mensagens com segurança no repositório de mensagens para as quais o processamento precisa ser adiado.

## <a name="message-deferral-apis"></a>APIs de adiamento de mensagens

A API é [BrokeredMessage.Defer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) ou [BrokeredMessage.DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) no cliente do .NET Framework, [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) no cliente do .NET Standard e **mesageReceiver.defer** ou **messageReceiver.deferSync** no cliente Java. 

As mensagens adiadas permanecem na fila principal junto com todas as outras mensagens ativas (ao contrário de mensagens mortas que residem em uma subfila), mas elas não podem mais ser recebidas usando as funções Receive/ReceiveAsync regulares. Mensagens adiadas podem ser descobertas por meio da [procura de mensagens](message-browsing.md) se um aplicativo perder o controle delas.

Para recuperar uma mensagem adiada, seu proprietário é responsável por memorizar o [NúmerodeSequência](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) conforme ele a adia. Todo destinatário que souber um número de sequência de uma mensagem adiada pode receber depois a mensagem explicitamente com `Receive(sequenceNumber)`.

Se uma mensagem não puder ser processada porque um recurso específico para lidar com essa mensagem está temporariamente indisponível, mas o processamento de mensagem não deve ser suspenso sumariamente, uma maneira de colocar essa mensagem de lado por alguns minutos é lembrar o **NúmerodeSequência** em uma [mensagem agendada](message-sequencing.md) para ser postada em alguns minutos, e recuperar a mensagem adiada novamente quando a mensagem agendada chegar. Observe que se um manipulador de mensagens depender de um banco de dados para todas as operações e o banco de dados estiver temporariamente indisponível, ele não deverá usar o adiamento, mas suspender o recebimento de mensagens completamente até o banco de dados estar disponível novamente.

O adiamento de mensagens não tem impacto na expiração da mensagem, o que significa que mensagens adiadas ainda expiraram no horário agendado inicialmente e, em seguida, são movidas para a fila de mensagens mortas, se estiverem configuradas para isso.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os seguintes tópicos:

* [Conceitos fundamentais do barramento de serviço](service-bus-fundamentals-hybrid-solutions.md)
* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)