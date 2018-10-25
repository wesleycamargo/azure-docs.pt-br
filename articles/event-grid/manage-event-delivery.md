---
title: Mensagens mortas e nova tentativa de políticas para as assinaturas da Grade de Eventos do Azure
description: Descreve como personalizar opções de entrega de eventos para a Grade de Eventos. Definir um destino de inatividade e especificar o tempo de entrega novamente.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: fcf3ecaff6e8ba1421496a96d01428946cf8ab8e
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077775"
---
# <a name="dead-letter-and-retry-policies"></a>Mensagens mortas e tentar novas políticas

Ao criar uma assinatura de evento, você pode personalizar as configurações para entrega de eventos. Este artigo mostra como configurar um local de mensagens mortas e personalizar as configurações de repetição. Para obter informações sobre esses recursos, consulte [Entrega e repetição de mensagens da Grade de Eventos](delivery-and-retry.md).

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-dead-letter-location"></a>Defina o local de mensagens mortas

Para definir um local de mensagens mortas, é necessário ter uma conta de armazenamento para armazenar eventos que não podem ser entregues a um ponto de extremidade. O script a seguir obtém a ID de recurso de uma conta de armazenamento existente e cria uma assinatura de evento que usa um contêiner na conta de armazenamento para o ponto de extremidade de mensagens mortas.

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

storagename=demostorage
containername=testcontainer

storageid=$(az storage account show --name $storagename --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Para desativar a colocação em fila de mensagens mortas, execute novamente o comando para criar a assinatura de evento, mas não forneça um valor para `deadletter-endpoint`. Você não precisa excluir a assinatura do evento.

## <a name="set-retry-policy"></a>Definir a política de repetição

Ao criar uma assinatura de Grade de Eventos, você pode definir valores de por quanto tempo a Grade de Eventos deve tentar entregar o evento. Por padrão, a Grade de Eventos tenta por 24 horas (1440 minutos) e tenta um máximo de 30 vezes. Você pode definir esses valores para a sua assinatura de grade de eventos. O valor de tempo de vida do evento deve ser um inteiro de 1 a 1440. O valor de tentativas de entrega máximo deve ser um inteiro de 1 a 30.

Não é possível configurar um [plano de tentativas](delivery-and-retry.md#retry-schedule-and-duration).

Para definir o evento de vida útil para um valor diferente de 1440 minutos, use:

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

Para definir o máximo de novas tentativas com um valor diferente de 30, use:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Se você definir `event-ttl` e `max-deliver-attempts`, a Grade de Eventos usa o primeiro a expirar para novas tentativas.

## <a name="next-steps"></a>Próximas etapas

* Para um aplicativo de exemplo que usa um aplicativo de função do Azure para processar eventos de mensagens mortas, consulte [Exemplos de Mensagens Mortas de Grade de Eventos do Azure para .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* Para obter informações sobre a entrega de eventos e repetições, [Entrega e repetição de mensagens da Grade de Eventos](delivery-and-retry.md).
* Para ver uma introdução à Grade de Eventos, confira [About Event Grid](overview.md) (Sobre a Grade de Eventos).
* Para começar a usar rapidamente a Grade de Eventos, confira [Create and route custom events with Azure Event Grid](custom-event-quickstart.md) (Criar e rotear eventos personalizados com a Grade de Eventos do Azure).
