---
title: Mensagens mortas e nova tentativa de políticas para as assinaturas da Grade de Eventos do Azure
description: Descreve como personalizar opções de entrega de eventos para a Grade de Eventos. Definir um destino de inatividade e especificar o tempo de entrega novamente.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/06/2019
ms.author: spelluru
ms.openlocfilehash: 8660f09c41cf6226f2ffb173508d37c260522b80
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54474230"
---
# <a name="dead-letter-and-retry-policies"></a>Mensagens mortas e tentar novas políticas

Ao criar uma assinatura de evento, você pode personalizar as configurações para entrega de eventos. Este artigo mostra como configurar um local de mensagens mortas e personalizar as configurações de repetição. Para obter informações sobre esses recursos, consulte [Entrega e repetição de mensagens da Grade de Eventos](delivery-and-retry.md).

## <a name="install-preview-feature"></a>Instalar versão prévia do recurso

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-dead-letter-location"></a>Defina o local de mensagens mortas

Para definir um local de mensagens mortas, é necessário ter uma conta de armazenamento para armazenar eventos que não podem ser entregues a um ponto de extremidade. Os exemplos obtêm a ID de recurso de uma conta de armazenamento existente. Eles criam uma assinatura de evento que usa um contêiner na conta de armazenamento para o ponto de extremidade de inatividade.

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

containername=testcontainer

topicid=$(az eventgrid topic show --name demoTopic -g gridResourceGroup --query id --output tsv)
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Para desativar a colocação em fila de mensagens mortas, execute novamente o comando para criar a assinatura de evento, mas não forneça um valor para `deadletter-endpoint`. Você não precisa excluir a assinatura do evento.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

$containername = "testcontainer"

$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzureRmStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

Para desativar a colocação em fila de mensagens mortas, execute novamente o comando para criar a assinatura de evento, mas não forneça um valor para `DeadLetterEndpoint`. Você não precisa excluir a assinatura do evento.

## <a name="set-retry-policy"></a>Definir a política de repetição

Ao criar uma assinatura de Grade de Eventos, você pode definir valores de por quanto tempo a Grade de Eventos deve tentar entregar o evento. Por padrão, a Grade de Eventos tenta por 24 horas (1440 minutos) ou 30 vezes. Você pode definir esses valores para a sua assinatura de grade de eventos. O valor de tempo de vida do evento deve ser um inteiro de 1 a 1440. O valor para máximo de tentativas deve ser um inteiro de 1 a 30.

Não é possível configurar um [plano de tentativas](delivery-and-retry.md#retry-schedule-and-duration).

### <a name="azure-cli"></a>CLI do Azure

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

Para definir o máximo de tentativas com um valor diferente de 30, use:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Se você definir ambos `event-ttl` e `max-deliver-attempts`, a Grade de Eventos usa o primeiro para expirar, a fim de determinar quando parar a entrega de eventos.

### <a name="powershell"></a>PowerShell

Para definir o evento de vida útil para um valor diferente de 1440 minutos, use:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

Para definir o máximo de tentativas com um valor diferente de 30, use:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

Se você definir ambos `EventTtl` e `MaxDeliveryAttempt`, a Grade de Eventos usa o primeiro para expirar, a fim de determinar quando parar a entrega de eventos.

## <a name="next-steps"></a>Próximas etapas

* Para um aplicativo de exemplo que usa um aplicativo de função do Azure para processar eventos de mensagens mortas, consulte [Exemplos de Mensagens Mortas de Grade de Eventos do Azure para .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* Para obter informações sobre a entrega de eventos e repetições, [Entrega e repetição de mensagens da Grade de Eventos](delivery-and-retry.md).
* Para ver uma introdução à Grade de Eventos, confira [About Event Grid](overview.md) (Sobre a Grade de Eventos).
* Para começar a usar rapidamente a Grade de Eventos, confira [Create and route custom events with Azure Event Grid](custom-event-quickstart.md) (Criar e rotear eventos personalizados com a Grade de Eventos do Azure).
