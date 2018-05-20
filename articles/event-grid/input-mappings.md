---
title: Mapear o campo personalizado para o esquema de Grade de Eventos do Azure
description: Descreve como converter seu esquema personalizado para o esquema de Grade de Eventos do Azure.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 05/09/2018
ms.author: tomfitz
ms.openlocfilehash: 1e809f83b43c32031b66c8f470575da6e9fcdc56
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/11/2018
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Mapear campos personalizados para o esquema de Grade de Eventos

Se seus dados de evento não coincidem com o esperado [esquema de Grade de Eventos](event-schema.md), você ainda pode usar a Grade de Eventos para eventos de rota para os assinantes. Este artigo descreve como mapear seu esquema para o esquema de Grade de Eventos.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Esquema de evento original

Vamos supor que você tenha um aplicativo que envia eventos no seguinte formato:

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

Embora esse formato não corresponda ao esquema requerido, a Grade de Eventos permite que você mapeie os campos para o esquema. Ou, você pode receber os valores no esquema original.

## <a name="create-custom-topic-with-mapped-fields"></a>Criar tópico personalizado com campos mapeados

Ao criar um tópico personalizado, especifique como mapear campos de seu evento original para o esquema de grade de eventos. Há três propriedades que você usa para personalizar o mapeamento:

* O parâmetro `--input-schema` especifica o tipo de esquema. As opções disponíveis são *cloudeventv01schema*, *customeventschema*, e *eventgridschema*. O valor padrão é eventgridschema. Ao criar mapeamento personalizado entre seu esquema e o esquema de Grade de Eventos, use customeventschema. Quando os eventos estão no esquema de CloudEvents, use cloudeventv01schema.

* O parâmetro `--input-mapping-default-values` especifica valores padrão para campos no esquema de Grade de Eventos. Você pode definir valores padrão para *assunto*, *eventtype*, e *dataversion*. Normalmente, você usa esse parâmetro quando seu esquema personalizado não inclui um campo que corresponde a um desses três campos. Por exemplo, você pode especificar que dataversion é sempre definido como **1.0**.

* O parâmetro `--input-mapping-fields` mapeia os campos de seu esquema para o esquema de grade de eventos. Você especifica valores em pares chave/valor separados por espaços. Para o nome da chave, use o nome do campo de grade de eventos. O valor, use o nome do seu campo. Você pode usar nomes de chave para *id*, *tópico*, *eventtime*, *assunto*, *eventtype*e *dataversion*.

O exemplo a seguir cria um tópico personalizado com alguns campos padrão e mapeados:

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

## <a name="subscribe-to-event-grid-topic"></a>Inscrever-se para o tópico de grade de eventos

Ao inscrever-se para o tópico personalizado, você pode especificar o esquema que você deseja usar para receber os eventos. Você usa o parâmetro `--event-delivery-schema` e o define como *cloudeventv01schema*, *eventgridschema*, ou *inputeventschema*. O valor padrão é eventgridschema.

Os exemplos nesta seção usam um armazenamento de fila para o manipulador de eventos. Para obter mais informações, confira [Encaminhar eventos personalizados para o Armazenamento de Filas do Azure](custom-event-to-queue-storage.md).

O exemplo a seguir assina um tópico de grade de eventos e usa o esquema de grade de evento padrão:

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub1 \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

O exemplo a seguir usa o esquema de entrada do evento:

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub2 \
  --event-delivery-schema inputeventschema \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

## <a name="publish-event-to-topic"></a>Publicar evento para tópico

Agora você está pronto para enviar um evento para o tópico personalizado e ver o resultado do mapeamento. O script a seguir para publicar um evento de [esquema de exemplo](#original-event-schema):

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/mapeventfields.json)'")

curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Agora, examine o armazenamento de fila. As duas assinaturas entregaram eventos em esquemas diferentes.

A primeira assinatura usou o esquema de Grade de Eventos. O formato do evento fornecido é:

```json
{
  "Id": "016b3d68-881f-4ea3-8a9c-ed9246582abe",
  "EventTime": "2018-05-01T20:00:25.2606434Z",
  "EventType": "Created",
  "DataVersion": "1.0",
  "MetadataVersion": "1",
  "Topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "Subject": "DefaultSubject",
  "Data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": { "someDataField1": "SomeDataFieldValue" } 
  }
}
```

Esses campos contêm os mapeamentos do tópico personalizado. **myEventTypeField** é mapeado para **EventType**. Os valores padrão para **DataVersion** e **Assunto** são usados. O objeto **dados** contém os campos de esquema do evento original.

A segunda assinatura usou o esquema de evento de entrada. O formato do evento fornecido é:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": { "someDataField1": "SomeDataFieldValue" }
}
```

Observe que os campos originais foram entregues.

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre a entrega de eventos e repetições, [Entrega e repetição de mensagens da Grade de Eventos](delivery-and-retry.md).
* Para ver uma introdução à Grade de Eventos, confira [About Event Grid](overview.md) (Sobre a Grade de Eventos).
* Para começar a usar rapidamente a Grade de Eventos, confira [Create and route custom events with Azure Event Grid](custom-event-quickstart.md) (Criar e rotear eventos personalizados com a Grade de Eventos do Azure).
