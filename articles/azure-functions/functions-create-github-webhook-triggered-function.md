---
title: Criar uma função no Azure disparada por um webhook do GitHub | Microsoft Docs
description: Usar o Azure Functions para criar uma função sem servidor que é invocada por um webHook do GitHub.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: 36ef34b8-3729-4940-86d2-cb8e176fcc06
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/28/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 05ad567e407a6506222acdb66ab38c4cfab76e4b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2018
---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>Criar uma função disparada pelo webhook do GitHub

Saiba como criar uma função que é disparada por uma solicitação de webhook HTTP com um conteúdo específico do GitHub.

![Função disparada pelo webhook do GitHub no Portal do Azure](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>pré-requisitos

+ Uma conta do GitHub com pelo menos um projeto.
+ Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-azure-function-app"></a>Criar um Aplicativo de funções do Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Aplicativo de funções criado com êxito.](./media/functions-create-first-azure-function/function-app-create-success.png)

Em seguida, crie uma nova função no novo aplicativo de funções.

<a name="create-function"></a>

## <a name="create-a-github-webhook-triggered-function"></a>Criar uma função disparada pelo webhook do GitHub

1. Expanda seu aplicativo de funções e clique no botão **+** ao lado de **Functions**. Se essa for a primeira função em seu aplicativo de funções, selecione **Função personalizada**. Exibe o conjunto completo de modelos de função.

    ![Página de início rápido de funções no portal do Azure](./media/functions-create-github-webhook-triggered-function/add-first-function.png)

2. No campo de pesquisa, digite `github` e depois escolha a linguagem desejada para o modelo de gatilho de webhook do GitHub. 

     ![Escolha o modelo de gatilho webhook do GitHub](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger.png) 

2. Digite um **Nome** para sua função, depois selecione **Criar**. 

     ![Configurar a função disparada pelo webhook do GitHub no portal do Azure](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger-2.png) 

3. Na sua nova função, clique em **</> Obter URL de função** e depois copie e salve os valores. Faça o mesmo para **</> Get GitHub secret**. Você usa esses valores ao criar o webhook no GitHub.

    ![Examinar o código de função](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png)

Em seguida, você cria o webhook no repositório GitHub.

## <a name="configure-the-webhook"></a>Configurar o webhook

1. No GitHub, navegue até um repositório de sua propriedade. Você também pode usar qualquer repositório que você tenha bifurcado. Se você precisar bifurcar um repositório, use <https://github.com/Azure-Samples/functions-quickstart>.

2. Escolha **Configurações** > **Opções** e verifique se a opção **Problemas** está habilitada em **Recursos**.

   ![Habilitar Problemas](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook.png)

1. Em **Configurações**, escolha **Webhooks** > **Adicionar webhook**.

    ![Adicionar um webhook do GitHub](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

1. Use as configurações especificadas na tabela a seguir, depois clique em **Adicionar webhook**:

    ![Definir o segredo e a URL do webhook](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

| Configuração | Valor sugerido | DESCRIÇÃO |
|---|---|---|
| **URL do conteúdo** | Valor copiado | Usar o valor retornado por **</> Obter URL de função**. |
| **Tipo de conteúdo** | aplicativo/json | A função espera um conteúdo JSON. |
| **Segredo**   | Valor copiado | Use o valor retornado por **</> Obter segredo do GitHub**. |
| Gatilhos de evento | Deixe-me selecionar eventos individuais | Queremos disparar apenas em eventos de comentário do problema.  |
| | Comentário do problema |  |

Neste momento, o webhook é configurado para disparar sua função quando um novo comentário do problema é adicionado.

## <a name="test-the-function"></a>Testar a função

1. No seu repositório GitHub, abra a guia **Problemas** em uma nova janela do navegador.

1. Na nova janela, clique em **Novo Problema**, digite um título e clique em **Enviar novo problema**.

1. No problema, digite um comentário e clique em **Comentar**.

    ![Adicione um comentário de problema do GitHub.](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-add-comment.png)

1. Volte para o portal e exiba os logs. Você deve ver uma entrada de rastreamento com o novo texto de comentário.

     ![Exiba o texto do comentário nos logs.](./media/functions-create-github-webhook-triggered-function/function-app-view-logs.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Você criou uma função que é disparada quando uma solicitação é recebida de um webhook do GitHub.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para saber mais sobre gatilhos do webhook, veja [Associações HTTP e de webhook do Azure Functions](functions-bindings-http-webhook.md).
