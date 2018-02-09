---
title: "Publicar evento para tópico personalizado de Grade de Eventos do Azure"
description: "Descreve como publicar um evento para um tópico personalizado para a Grade de Eventos do Azure"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 43dcdf9ab0fee5f7e61ecdc42aaf40430e272d92
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Publicar para tópico personalizado para Grade de Eventos do Azure

Este artigo descreve como publicar um evento para um tópico personalizado. Ele mostra o formato dos dados de postagem e evento. O [Contrato de Nível de Serviço (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) só se aplica às publicações que correspondem ao formato esperado.

## <a name="endpoint"></a>Ponto de extremidade

Ao enviar o HTTP POST para um tópico personalizado, use o formato URI: `https://<topic-endpoint>?api-version=2018-01-01`.

Por exemplo, um URI válido é: `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`.

Para obter o ponto de extremidade para um tópico personalizado com CLI do Azure, use:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Para obter o ponto de extremidade para um tópico personalizado com Azure PowerShell, use:

```powershell
(Get-AzureRmEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Cabeçalho

Na solicitação, inclua um valor de cabeçalho chamado `aeg-sas-key` que contém uma chave para autenticação.

Por exemplo, um valor de cabeçalho válido é `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`.

Para obter a chave para um tópico personalizado com CLI do Azure, use:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Para obter a chave para um tópico personalizado com PowerShell, use:

```powershell
(Get-AzureRmEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Dados de evento

Para tópicos personalizados, os dados de nível superior contêm os mesmos campos do que os eventos definidos pelo recurso padrão. Uma dessas propriedades é uma propriedade de dados que contém propriedades exclusivas para o tópico personalizado. Como publicador de eventos, você deve determinar as propriedades para esse objeto de dados. Use o esquema a seguir:

```json
[
  {
    "id": string,    
    "eventType": string,
    "subject": string,
    "eventTime": string-in-date-time-format,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string
  }
]
```

Para obter uma descrição dessas propriedades, consulte [esquema de evento de Grade de Eventos do Azure](event-schema.md).

Por exemplo, um esquema de dados de evento válido é:

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0"
}]
```

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução ao roteamento de eventos personalizados, consulte [Criar e encaminhar eventos personalizados com CLI do Azure e a Grade de Eventos](custom-event-quickstart.md) ou [Criar e encaminhar eventos personalizados com Azure PowerShell e a Grade de Eventos](custom-event-quickstart-powershell.md).
* Para saber mais sobre a chave de autenticação, confira [Event Grid security and authentication](security-authentication.md) (Segurança e autenticação da Grade de Eventos).
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
