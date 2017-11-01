---
title: "Webhooks de Registro de Contêiner do Azure"
description: "Saiba como usar webhooks para disparar eventos quando ocorrerem determinadas ações em seus repositórios de registro."
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acr, azure-container-registry
keywords: "Docker, Contêineres, ACR"
ms.assetid: 
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2017
ms.author: nepeters
ms.openlocfilehash: 5a9dab91aafb92f944b473f05144242143e36477
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2017
---
# <a name="using-azure-container-registry-webhooks"></a>Como usar webhooks do Registro de Contêiner do Azure

Um registro de contêiner do Azure armazena e gerencia imagens de contêiner privadas do Docker, de forma semelhante a como o Docker Hub armazena imagens públicas do Docker. Você usar webhooks para disparar eventos quando certas ações ocorrem em um dos repositórios do registro. Webhooks podem responder a eventos no nível do registro ou podem ter o escopo reduzido para a marca de um repositório específico.

Para obter mais informações e conceitos, confira [Sobre o Registro de Contêiner do Azure](./container-registry-intro.md).

## <a name="prerequisites"></a>Pré-requisitos

- Registro gerenciado de contêiner do Azure – crie um registro de contêiner gerenciado em sua assinatura do Azure. Por exemplo, use o [Portal do Azure](container-registry-get-started-portal.md) ou a [CLI do Azure](container-registry-get-started-azure-cli.md).
- CLI do Docker – para configurar o computador local como um host do Docker e acessar os comandos da CLI do Docker, instale o [Mecanismo do Docker](https://docs.docker.com/engine/installation/).

## <a name="create-webhook-azure-portal"></a>Criar webhook no portal do Azure

1. Faça logon no [portal do Azure](https://portal.azure.com) e navegue até o registro no qual você deseja criar webhooks.

2. Na folha do contêiner, selecione **Webhooks** em **SERVIÇOS**.

3. Selecione **Adicionar** na barra de ferramentas da folha do webhook.

4. Preencha o formulário *Criar webhook* com as seguintes informações:

| Valor | Descrição |
|---|---|
| Nome | O nome que você deseja dar ao webhook. Ele pode conter somente caracteres minúsculos e números e deve ter de 5 a 50 caracteres de extensão. |
| URI de serviço | O URI para que o webhook deveria enviar notificações POST. |
| Cabeçalhos personalizados | Cabeçalhos que você deseja passar junto com a solicitação POST. Eles devem estar no formato "chave: valor". |
| Ações de gatilho | Ações que disparam o webhook. Webhooks no momento podem ser acionados por push de imagem e/ou ações de exclusão. |
| Status | O status do webhook depois que ele é criado. Ele é habilitado por padrão. |
| Escopo | O escopo no qual o webhook funciona. Por padrão, o escopo é para todos os eventos no registro. Pode ser especificado para um repositório ou uma marca usando o formato "repositório:marca". |

Formulário de webhook de exemplo:

![Interface do usuário de criação de webhook ACR no portal do Azure](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>Criar webhook na CLI do Azure

Para criar um webhook usando a CLI do Azure, use o comando [az acr webhook create](/cli/azure/acr/webhook#create).

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Testar webhook

### <a name="azure-portal"></a>Portal do Azure

Antes de usar o webhook em ações de exclusão e de push de imagem de contêiner, você pode testá-lo com o botão **Ping**. O ping envia uma solicitação POST genérica para o ponto de extremidade especificado e registra a resposta. Isso pode ajudá-lo a verificar se você configurou corretamente o webhook.

1. Selecione o webhook que você deseja testar.
2. Na barra de ferramentas superior, selecione **Ping**.
3. Verifique a resposta do ponto de extremidade na coluna **STATUS HTTP**.

![Interface do usuário de criação de webhook ACR no portal do Azure](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>CLI do Azure

Para testar um webhook ACR com a CLI do Azure, use o comando [az acr webhook ping](/cli/azure/acr/webhook#ping).

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
