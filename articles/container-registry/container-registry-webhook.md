---
title: "Webhooks de Registro de Contêiner do Azure"
description: "Saiba como usar webhooks para disparar eventos quando ocorrerem determinadas ações em seus repositórios de registro."
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 12/02/2017
ms.author: nepeters
ms.openlocfilehash: 915f90fd5d969d5544d56e5bec754b799f349015
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="using-azure-container-registry-webhooks"></a>Como usar webhooks do Registro de Contêiner do Azure

Um registro de contêiner do Azure armazena e gerencia imagens de contêiner privadas do Docker, de forma semelhante a como o Docker Hub armazena imagens públicas do Docker. É possível usar webhooks para disparar eventos quando certas ações ocorrem em um dos repositórios do Registro. Webhooks podem responder a eventos no nível do registro ou podem ter o escopo reduzido para a marca de um repositório específico.

Para obter detalhes sobre solicitações de webhook, consulte [Azure Container Registry webhook schema reference](container-registry-webhook-reference.md) (Referência de esquema de webhook do Registro de Contêiner do Azure).

## <a name="prerequisites"></a>pré-requisitos

* Registro de Contêiner do Azure – Crie um Registro de contêiner em sua assinatura do Azure. Por exemplo, use o [Portal do Azure](container-registry-get-started-portal.md) ou a [CLI do Azure](container-registry-get-started-azure-cli.md).
* CLI do Docker – para configurar o computador local como um host do Docker e acessar os comandos da CLI do Docker, instale o [Mecanismo do Docker](https://docs.docker.com/engine/installation/).

## <a name="create-webhook-azure-portal"></a>Criar webhook no portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com)
1. Navegue até o Registro de contêiner no qual você deseja criar um webhook.
1. Em **SERVIÇOS**, selecione **Webhooks**.
1. Selecione **Adicionar** na barra de ferramentas do webhook.
1. Preencha o formulário *Criar webhook* com as seguintes informações:

| Valor | DESCRIÇÃO |
|---|---|
| NOME | O nome que você deseja dar ao webhook. Ele pode conter somente caracteres minúsculos e números e deve ter de 5 a 50 caracteres de extensão. |
| URI de serviço | O URI para que o webhook deveria enviar notificações POST. |
| Cabeçalhos personalizados | Cabeçalhos que você deseja passar junto com a solicitação POST. Eles devem estar no formato "chave: valor". |
| Ações de gatilho | Ações que disparam o webhook. Webhooks no momento podem ser acionados por push de imagem e/ou ações de exclusão. |
| Status | O status do webhook depois que ele é criado. Ele é habilitado por padrão. |
| Escopo | O escopo no qual o webhook funciona. Por padrão, o escopo é para todos os eventos no registro. Pode ser especificado para um repositório ou uma marca usando o formato "repositório:marca". |

Formulário de webhook de exemplo:

![Interface do usuário de criação de webhook ACR no portal do Azure](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>Criar webhook na CLI do Azure

Para criar um webhook usando a CLI do Azure, use o comando [az acr webhook create](/cli/azure/acr/webhook#az_acr_webhook_create).

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Testar webhook

### <a name="azure-portal"></a>Portal do Azure

Antes de usar o webhook em ações de exclusão e de push de imagem de contêiner, você pode testá-lo com o botão **Ping**. O ping envia uma solicitação POST genérica para o ponto de extremidade especificado e registra a resposta. Usar o recurso de ping pode ajudá-lo a verificar se você configurou corretamente o webhook.

1. Selecione o webhook que você deseja testar.
2. Na barra de ferramentas superior, selecione **Ping**.
3. Verifique a resposta do ponto de extremidade na coluna **STATUS HTTP**.

![Interface do usuário de criação de webhook ACR no portal do Azure](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>CLI do Azure

Para testar um webhook ACR com a CLI do Azure, use o comando [az acr webhook ping](/cli/azure/acr/webhook#az_acr_webhook_ping).

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Para ver os resultados, use o comando [az acr webhook list-events](/cli/azure/acr/webhook#list-events).

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Excluir webhook

### <a name="azure-portal"></a>Portal do Azure

Cada webhook pode ser excluído selecionando-se o webhook e, em seguida, o botão **Excluir** no Portal do Azure.

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Próximas etapas

[Referência de esquema de webhook do Registro de Contêiner do Azure](container-registry-webhook-reference.md)