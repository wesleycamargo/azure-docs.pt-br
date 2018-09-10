---
title: Esquema de evento de Registro de Contêiner de Grade de Eventos do Azure
description: Descreve as propriedades fornecidas para eventos de Registro de Contêiner com a Grade de Eventos do Azure
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 08/13/2018
ms.author: tomfitz
ms.openlocfilehash: d18a6718e4c29f3d04639644dc752b0733f15ba8
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2018
ms.locfileid: "42141201"
---
# <a name="azure-event-grid-event-schema-for-container-registry"></a>Esquema de evento de Grade de Eventos do Azure para Registro de Contêiner

Este artigo apresenta as propriedades e o esquema para eventos de Registro de Contêiner. Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](event-schema.md).

## <a name="available-event-types"></a>Tipos de evento disponíveis

Armazenamento de blob emite os seguintes tipos de evento:

| Tipo de evento | DESCRIÇÃO |
| ---------- | ----------- |
| Microsoft.ContainerRegistry.ImagePushed | Gerado quando é efetuado o push de uma imagem. |
| Microsoft.ContainerRegistry.ImageDeleted | Gerado quando uma imagem é excluída. |

## <a name="example-event"></a>Exemplo de evento

O exemplo a seguir mostra o esquema de um evento de efetuar push de uma imagem: 

```json
[{
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld:v1",
  "eventType": "ImagePushed",
  "eventTime": "2018-04-25T21:39:47.6549614Z",
  "data": {
    "id": "31c51664-e5bd-416a-a5df-e5206bc47ed0",
    "timestamp": "2018-04-25T21:39:47.276585742Z",
    "action": "push",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 3023,
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "length": 3023,
      "repository": "aci-helloworld",
      "tag": "v1"
    },
    "request": {
      "id": "7c66f28b-de19-40a4-821c-6f5f6c0003a4",
      "host": "demo.azurecr.io",
      "method": "PUT",
      "useragent": "docker/18.03.0-ce go/go1.9.4 git-commit/0520e24 os/windows arch/amd64 UpstreamClient(Docker-Client/18.03.0-ce \\\\(windows\\\\))"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

O esquema para um evento de exclusão de imagem é semelhante:

```json
[{
  "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld",
  "eventType": "ImageDeleted",
  "eventTime": "2018-04-26T17:56:01.8211268Z",
  "data": {
    "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
    "timestamp": "2018-04-26T17:56:00.996603117Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "repository": "aci-helloworld"
    },
    "request": {
      "id": "aeda5b99-4197-409f-b8a8-ff539edb7de2",
      "host": "demo.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | Tipo | DESCRIÇÃO |
| -------- | ---- | ----------- |
| topic | string | Caminho de recurso completo para a origem do evento. Esse campo não é gravável. Grade de Eventos fornece esse valor. |
| subject | string | Caminho definido pelo fornecedor para o assunto do evento. |
| eventType | string | Um dos tipos de evento registrados para a origem do evento. |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| ID | string | Identificador exclusivo do evento. |
| data | objeto | Dados de eventos do armazenamento de blob. |
| dataVersion | string | A versão do esquema do objeto de dados. O fornecedor define a versão do esquema. |
| metadataVersion | string | A versão do esquema do metadados de evento. Grade de Eventos define o esquema de propriedades de nível superior. Grade de Eventos fornece esse valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | DESCRIÇÃO |
| -------- | ---- | ----------- |
| ID | string | A ID do evento. |
| timestamp | string | A hora em que o evento ocorreu. |
| ação | string | A ação que abrange o evento fornecido. |
| destino | objeto | O destino do evento. |
| solicitação | objeto | A solicitação que gerou o evento. |

O objeto de destino tem as seguintes propriedades:

| Propriedade | Tipo | DESCRIÇÃO |
| -------- | ---- | ----------- |
| mediaType | string | O tipo MIME do objeto referenciado. |
| tamanho | inteiro | O número de bytes do conteúdo. Mesmo que o campo de Comprimento. |
| digest | string | O resumo da mensagem, conforme definido pela Especificação API HTTP do Registry V2. |
| length | inteiro | O número de bytes do conteúdo. O mesmo que o campo Tamanho. |
| repository | string | Nome do repositório. |
| marca | string | O nome da marca. |

O objeto solicitado tem as seguintes propriedades:

| Propriedade | Tipo | DESCRIÇÃO |
| -------- | ---- | ----------- |
| ID | string | A ID da solicitação que iniciou o evento. |
| addr | string | O IP ou nome de host e, possivelmente, a porta da conexão do cliente que iniciou o evento. Esse valor é o RemoteAddr da solicitação http padrão. |
| host | string | O nome de host acessível externamente da instância de registro, conforme especificado pelo cabeçalho do host http em solicitações de entrada. |
| estático | string | O método de solicitação que gerou o evento. |
| useragent | string | O cabeçalho do agente de usuário da solicitação. |

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
