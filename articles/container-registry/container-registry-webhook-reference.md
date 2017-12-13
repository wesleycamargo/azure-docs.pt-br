---
title: "Referência de esquema de webhook do Registro de Contêiner do Azure"
description: "O webhook solicita a referência da carga JSON para o Registro de Contêiner do Azure."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 12/02/2017
ms.author: marsma
ms.openlocfilehash: 84f0277a7b1a5bd7dfe2178f78f34140b1dd2642
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2017
---
# <a name="azure-container-registry-webhook-reference"></a>Referência de webhook do Registro de Contêiner do Azure

Você pode [configurar webhooks](container-registry-webhook.md) para o registro de contêiner que geram eventos quando certas ações são executadas em relação a ela. Por exemplo, você pode habilitar webhooks são disparados na imagem de contêiner `push` e operações `delete`. Quando um webhook é disparado, o Registro de Contêiner do Azure emite uma solicitação HTTPS ou HTTP contendo informações sobre o evento para um ponto de extremidade que você especificou. Seu ponto de extremidade pode então processar o webhook e agir em conformidade.

As seções a seguir detalham o esquema das solicitações de webhook geradas por eventos com suporte. As seções de evento contêm o esquema de carga para o tipo de evento, um exemplo de carga de solicitação e um ou mais exemplos de comandos que disparam o webhook.

Para obter informações sobre a configuração de webhooks para o seu registro de contêiner do Azure, consulte [Usando Webhooks do Registro de Contêiner do Azure](container-registry-webhook.md).

## <a name="webhook-requests"></a>Solicitações de webhook

### <a name="http-request"></a>Solicitação HTTP

Um webhook disparado faz uma solicitação HTTP `POST` para o ponto de extremidade da URL que você especificou quando configurou o webhook.

### <a name="http-headers"></a>Cabeçalhos HTTP

As solicitações de webhook incluem um `Content-Type` de `application/json` se você não especificou um cabeçalho personalizado `Content-Type` para o seu webhook.

Nenhum outro cabeçalho é adicionado à solicitação além dos cabeçalhos personalizados que podem ter sido especificado para o webhook.

## <a name="push-event"></a>Enviar evento por push

Webhook disparado quando uma imagem de contêiner é enviada por push para um repositório.

### <a name="push-event-payload"></a>Enviar carga do evento por push

|Elemento|Tipo|Descrição|
|-------------|----------|-----------|
|`id`|Cadeia de caracteres|A ID do evento do webhook.|
|`timestamp`|Datetime|A hora em que o evento do webhook foi disparado.|
|`action`|Cadeia de caracteres|A ação que disparou o evento do webhook.|
|[destino](#target)|Tipo complexo|O destino do evento que desencadeou o evento do webhook.|
|[solicitação](#request)|Tipo complexo|A solicitação que gerou o evento do webhook.|

### <a name="target"></a>destino

|Elemento|Tipo|Descrição|
|------------------|----------|-----------|
|`mediaType`|Cadeia de caracteres|O tipo MIME do objeto referenciado.|
|`size`|Int32|O número de bytes do conteúdo. Mesmo que o campo de Comprimento.|
|`digest`|Cadeia de caracteres|O resumo da mensagem, conforme definido pela Especificação API HTTP do Registry V2.|
|`length`|Int32|O número de bytes do conteúdo. O mesmo que o campo Tamanho.|
|`repository`|Cadeia de caracteres|Nome do repositório.|
|`tag`|Cadeia de caracteres|O nome da marca de imagem.|

### <a name="request"></a>solicitação

|Elemento|Tipo|Descrição|
|------------------|----------|-----------|
|`id`|Cadeia de caracteres|A ID da solicitação que iniciou o evento.|
|`host`|Cadeia de caracteres|O nome de host acessível externamente da instância de registro, conforme especificado pelo cabeçalho do host HTTP em solicitações de entrada.|
|`method`|Cadeia de caracteres|O método de solicitação que gerou o evento.|
|`useragent`|Cadeia de caracteres|O cabeçalho do agente de usuário da solicitação.|

### <a name="payload-example-push-event"></a>Exemplo de carga: enviar evento por push

```JSON
{
  "id": "cb8c3971-9adc-488b-bdd8-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:80f0d5c8786bb9e621a45ece0db56d11cdc624ad20da9fe62e9d25490f331d7d",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-86cd-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

Exemplo do comando [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) que dispara o **envio por push** do webhook do evento:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="delete-event"></a>Excluir evento

Webhook é disparado quando um repositório ou manifesto é excluído. Não é disparado quando uma marca é excluída.

### <a name="delete-event-payload"></a>Excluir carga do evento

|Elemento|Tipo|Descrição|
|-------------|----------|-----------|
|`id`|Cadeia de caracteres|A ID do evento do webhook.|
|`timestamp`|Datetime|A hora em que o evento do webhook foi disparado.|
|`action`|Cadeia de caracteres|A ação que disparou o evento do webhook.|
|[destino](#delete_target)|Tipo complexo|O destino do evento que desencadeou o evento do webhook.|
|[solicitação](#delete_request)|Tipo complexo|A solicitação que gerou o evento do webhook.|

### <a name="delete_target"></a> destino

|Elemento|Tipo|Descrição|
|------------------|----------|-----------|
|`mediaType`|Cadeia de caracteres|O tipo MIME do objeto referenciado.|
|`digest`|Cadeia de caracteres|O resumo da mensagem, conforme definido pela Especificação API HTTP do Registry V2.|
|`repository`|Cadeia de caracteres|Nome do repositório.|

### <a name="delete_request"></a> solicitação

|Elemento|Tipo|Descrição|
|------------------|----------|-----------|
|`id`|Cadeia de caracteres|A ID da solicitação que iniciou o evento.|
|`host`|Cadeia de caracteres|O nome de host acessível externamente da instância de registro, conforme especificado pelo cabeçalho do host HTTP em solicitações de entrada.|
|`method`|Cadeia de caracteres|O método de solicitação que gerou o evento.|
|`useragent`|Cadeia de caracteres|O cabeçalho do agente de usuário da solicitação.|

### <a name="payload-example-delete-event"></a>Exemplo de carga: excluir evento

```JSON
{
    "id": "afc359ce-df7f-4e32-bdde-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:80f0d5c8786bb9e621a45ece0db56d11cdc624ad20da9fe62e9d25490f331d7d",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-807f-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

Exemplo de comandos [CLI 2.0 do Azure](/cli/azure/acr) que disparam um evento **excluir** do webhook:

```azurecli
# Delete repository
az acr repository delete -n MyRegistry --repository MyRepository

# Delete manifest
az acr repository delete -n MyRegistry --repository MyRepository --tag MyTag --manifest
```

## <a name="next-steps"></a>Próximas etapas

[Como usar webhooks do Registro de Contêiner do Azure](container-registry-webhook.md)