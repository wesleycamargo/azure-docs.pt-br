---
title: Como gerenciar grandes conjuntos de tópicos no Azure Event Grid e publicar eventos para eles usando o Event Domains
description: Mostra como criar e gerenciar tópicos no Azure Event Grid e publicar eventos para eles usando o Event Domains.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: d6da1ee603c85556693b145ba17d1e0cd0dfabd7
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51034533"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Gerenciar tópicos e publicar eventos usando o Event Domains

Este artigo mostra como:

* Criar um domínio de grade de eventos
* Assinar tópicos
* Listar chaves
* Publicar eventos em um domínio

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Crie um domínio de evento

A criação de um domínio de evento pode ser feita por meio da extensão `eventgrid` para [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Depois de criar um domínio, você poderá usá-lo para gerenciar grandes conjuntos de tópicos.

```azurecli-interactive
# if you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name>
  -l <location>
```

A criação bem-sucedida retornará o seguinte:

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

Observe os `endpoint` e `id`, pois eles serão necessários para gerenciar o domínio e publicar eventos.

## <a name="create-topics-and-subscriptions"></a>Criar tópicos e assinaturas

O serviço Event Grid cria e gerencia automaticamente o tópico correspondente em um domínio com base na chamada para criar uma inscrição de evento para um tópico de domínio. Não há uma etapa separada para criar um tópico em um domínio. Da mesma forma, quando a última assinatura de evento de um tópico é excluída, o tópico também é excluído.

Assinar um tópico em um domínio é o mesmo que assinar qualquer outro recurso do Azure:

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/<my-topic>" \
  --endpoint https://contoso.azurewebsites.net/api/f1?code=code
```

O ID do recurso fornecido é o mesmo ID retornado ao criar o domínio anteriormente. Para especificar o tópico no qual você deseja se inscrever, adicione `/topics/<my-topic>` ao final do ID do recurso.

Para criar uma assinatura de evento de escopo de domínio que receba todos os eventos no domínio, forneça o domínio como `resource-id` sem especificar nenhum tópico, por exemplo, `/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>`.

Se você precisar de um endpoint de teste para assinar seus eventos, poderá sempre implantar um aplicativo da Web [pré-criado](https://github.com/Azure-Samples/azure-event-grid-viewer) que exiba os eventos de entrada. Você pode enviar seus eventos para o website de teste em `https://<your-site-name>.azurewebsites.net/api/updates`.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

As permissões definidas para um tópico são armazenadas no Azure Active Directory e devem ser excluídas explicitamente. A exclusão de uma assinatura de evento não revoga o acesso de usuários para criar assinaturas de eventos se eles tiverem acesso de gravação em um tópico.

## <a name="manage-access-to-topics"></a>Gerenciar o acesso aos tópicos

O gerenciamento de acesso aos tópicos é feito por meio da [atribuição de função](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli). A atribuição de função usa a verificação de acesso com base na função para limitar as operações nos recursos do Azure a usuários autorizados em um determinado escopo.

O Event Grid tem duas funções internas que você pode usar para designar acesso de usuários específicos a vários tópicos em um domínio. Essas funções são `EventGrid EventSubscription Contributor (Preview)`, o que permite a criação e exclusão de assinaturas, e `EventGrid EventSubscription Reader (Preview)`, que permite apenas a listagem de assinaturas de eventos.

O comando a seguir limitaria `alice@contoso.com` a criar e excluir assinaturas de eventos apenas no tópico `foo`:

```azurecli-interactive
az role assignment create --assignee alice@contoso.com --role "EventGrid EventSubscription Contributor (Preview)" --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/foo
```

Veja [segurança e autenticação da Grade de Eventos](./security-authentication.md) para saber mais sobre:

* Controle de acesso de gerenciamento
* Tipos de operação
* Criando definições de função customizadas

## <a name="publish-events-to-an-event-grid-domain"></a>Publicar eventos em um domínio da grade de eventos

Publicar eventos em um domínio é o mesmo que [publicar em um tópico personalizado](./post-to-custom-topic.md). A única diferença é que você precisa especificar o tópico para o qual deseja que cada evento seja direcionado. A seguinte matriz de eventos resultaria em evento com `"id": "1111"`para o tópico`foo`, enquanto o evento com `"id": "2222"`seria enviado para o tópico`bar`:

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

Para obter as chaves para um domínio, use:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

Em seguida, use seu método favorito de fazer um HTTP POST para publicar seus eventos no seu domínio da grade de eventos.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre conceitos de alto nível em Domínios de Eventos e por que são úteis, consulte a visão geral conceitual de [Domínios de Eventos](./event-domains.md).