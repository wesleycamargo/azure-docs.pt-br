---
title: Detecção de mensagens duplicadas do Barramento de Serviço do Azure | Microsoft Docs
description: Detectar mensagens duplicadas do Barramento de Serviço
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: spelluru
ms.openlocfilehash: 5241020b1db3797891ae13da54cc9225bbd4619b
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741260"
---
# <a name="duplicate-detection"></a>Detecção de duplicidade

Se um aplicativo falhar devido a um erro fatal imediatamente após enviar uma mensagem, e a instância do aplicativo reiniciado erroneamente acreditar que a entrega da mensagem anterior não ocorreu, um envio subsequente fará com que a mesma mensagem apareça no sistema duas vezes.

Também é possível ocorrer um erro no nível do cliente ou da rede um momento antes e para uma mensagem enviada ser confirmada na fila, com a confirmação não retornando de forma bem sucedida para o cliente. Este cenário deixa o cliente em dúvida quanto ao resultado da operação de envio.

A detecção duplicada elimina a dúvida dessas situações, permitindo que o remetente reenvie a mesma mensagem, e a fila ou o tópico descarta qualquer cópia duplicada.

Habilitar detecção de duplicidades ajuda a controlar a *MessageId* controlada pelo aplicativo de todas as mensagens enviadas para uma fila ou tópico durante um intervalo especificado. Se qualquer nova mensagem for enviada com *MessageId* que foi registrada durante a janela de tempo, a mensagem será relatada como aceita (a operação de envio será com êxito), mas a mensagem enviada recentemente será imediatamente ignorada e descartada. Nenhuma outra parte da mensagem além de *MessageId* é considerada.

O controle de aplicativos do identificador é essencial porque somente isso permite que o aplicativo vincule *MessageId* a um contexto de processo de negócios, a partir do qual ele poderá ser previsivelmente reconstruído quando ocorrer uma falha.

Para um processo de negócios em que várias mensagens são enviadas no decorrer de tratamento de algum contexto de aplicativo, a *MessageId* pode ser uma composição do identificador de contexto do nível do aplicativo, como um número de ordem de compra e o assunto da mensagem, por exemplo, **12345.2017/pagamento**.

A *MessageId* sempre pode ser algum GUID, mas a ancoragem o identificador para o processo de negócios produz a repetição previsível, o que é desejado para aproveitar o recurso de detecção de duplicidades com eficiência.

## <a name="enable-duplicate-detection"></a>Habilitar detecção de duplicidade

No portal, o recurso é ativado durante a criação de entidade com a caixa de seleção **Habilitar detecção de duplicidade**, que está desativado por padrão. A configuração para criar novos tópicos é equivalente.

![][1]

> [!IMPORTANT]
> Você não pode ativar / desativar a detecção de duplicados após a criação da fila. Você só pode fazer isso no momento da criação da fila. 

Programaticamente, você pode definir o sinalizador com a propriedade [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) na API do .NET de estrutura completa. Com a API do Azure Resource Manager, o valor é definido com a propriedade [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values).

O histórico de tempo de detecção duplicado é padronizado para 30 segundos para filas e tópicos, com um valor máximo de sete dias. Você pode alterar essa configuração na janela de propriedades de fila e tópico no Portal do Azure.

![][2]

Programaticamente, você pode configurar o tamanho da janela de detecção de duplicidades durante o qual as IDs de mensagem são mantidas, usando a propriedade [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) com a API do .NET Framework completa. Com a API do Azure Resource Manager, o valor é definido com a propriedade [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values).

Observe que habilitar a detecção de duplicidades e o tamanho da janela afeta diretamente a taxa de transferência da fila (e do tópico), uma vez que todas as IDs de mensagem registradas devem corresponder ao identificador de mensagem recém-enviado.

Manter a janela pequena significa que menos IDs de mensagem devem ser retidos e correspondidos e a taxa de transferência é menos afetada. Para entidades de alta taxa de transferência que exigem a detecção de duplicidades, você deve manter a janela o menor possível.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os seguintes tópicos:

* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
