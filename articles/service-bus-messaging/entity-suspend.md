---
title: Entidades de mensagens suspensas do Barramento de Serviço do Azure | Microsoft Docs
description: Suspenda e reative as entidades de mensagens do Barramento de Serviço do Azure.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 8feee92e9105259bddf45114ac48a54347266cd7
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845992"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Suspender e reativar as entidades de mensagens (desabilitar)

Filas, tópicos e assinaturas podem ser suspensos temporariamente. A suspensão coloca a entidade em um estado desabilitado em que todas as mensagens são mantidas no armazenamento. No entanto, as mensagens não podem ser removidas ou adicionadas e as respectivas operações de protocolo geram erros.

A suspensão de uma entidade normalmente é feita por razões administrativas urgentes. Um cenário é ter implantado um destinatário com falha que recebe as mensagens da fila, falha no processamento e ainda completa as mensagens de forma incorreta e as remove. Se esse comportamento é diagnosticado, a fila pode ser desabilitada para recebimentos até que o código corrigido seja implantado e ainda mais a perda de dados causada pelo código com falha possa ser evitada.

Uma suspensão ou reativação pode ser executada pelo usuário ou pelo sistema. O sistema suspende entidades apenas por razões administrativas graves como atingir a limite de gastos de assinatura. As entidades desabilitadas pelo sistema não podem ser reativadas pelo usuário, mas serão restauradas quando a causa a suspensão tiver sido resolvida.

No portal, a seção **Propriedades** da respectiva entidade permite alterar o estado, a captura de tela a seguir mostra a alternância para uma fila:

![][1]

O portal permite apenas desabilitar completamente filas. Você também pode desabilitar as operações de envio e recebimento separadamente usando as APIs de Barramento de Serviço [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) no SDK do .NET Framework, ou com um modelo do Azure Resource Manager por meio da CLI do Azure ou o Azure PowerShell.

## <a name="suspension-states"></a>Estados de suspensão

Os estados que podem ser definidos para uma fila são:

-   **Ativo**: a fila está ativa.
-   **Desabilitado**: a fila está suspensa.
-   **SendDisabled**: a fila está parcialmente suspensa, com o recebimento sendo permitido.
-   **ReceiveDisabled**: a fila está parcialmente suspensa, com o envio sendo permitido.

Para assinaturas e tópicos, é possível definir apenas **Active** e **Disabled**.

A enumeração [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) também define um conjunto de estados de transição que podem ser definidos apenas pelo sistema. O comando do PowerShell para desabilitar uma fila é mostrado no exemplo a seguir. O comando de reativação é equivalente, definindo `Status` como **Active**.

```powershell
$q = Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os seguintes tópicos:

* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

