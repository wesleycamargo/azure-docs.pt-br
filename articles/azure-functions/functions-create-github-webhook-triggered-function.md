---
title: "Criar uma função no Azure disparada por um webhook do GitHub | Microsoft Docs"
description: "Usar o Azure Functions para criar uma função sem servidor que é invocada por um webHook do GitHub."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 36ef34b8-3729-4940-86d2-cb8e176fcc06
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/18/2017
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: d4354546f3342d65353a86a4cec7d02547ab92e7
ms.lasthandoff: 04/22/2017


---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>Criar uma função disparada pelo webhook do GitHub

Saiba como criar uma função que é disparada por um webhook do GitHub. 

![Criar um aplicativo de funções no portal do Azure](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

Este tópico requer os recursos criados no tópico [Criar sua primeira função no portal do Azure](functions-create-first-azure-function.md).

Você também precisa de uma conta do GitHub. Você pode [inscrever-se para uma conta gratuita do GitHub](https://github.com/join), se você ainda não tiver um. 

Deve levar menos de cinco minutos para concluir todas as etapas neste tópico.

## <a name="find-your-function-app"></a>Localize seu aplicativo de funções    

1. Faça logon no [Portal do Azure](https://portal.azure.com/). 

2. Na barra de pesquisa na parte superior do portal, digite o nome do seu aplicativo de funções e selecione-o na lista.

## <a name="create-function"></a>Criar uma função disparada pelo webhook do GitHub

1. Em seu aplicativo de funções, clique no botão **+** ao lado de **Funções**, clique no modelo **GitHubWebHook** para o idioma desejado e clique em **Criar**.
   
    ![Criar uma função disparada pelo webhook do GitHub no portal do Azure.](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger.png) 

2. Clique em **</> Get function URL**, em seguida, copie e salve os valores. Faça o mesmo para **</> Get GitHub secret**. Você usa esses valores ao criar o webhook no GitHub. 

    ![Examinar o código de função](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png) 
         
Em seguida, você cria o webhook no repositório GitHub. 

## <a name="configure-the-webhook"></a>Configurar o webhook
1. No GitHub, navegue até um repositório de sua propriedade. Você também pode usar qualquer repositório que você tenha bifurcado.
 
2. Clique em **Configurações**, em seguida, clique em **Webhooks**, e **Adicionar webhook**.
   
    ![Adicionar um webhook do GitHub](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

3. Cole a URL e o segredo em sua função **carga URL** e **segredo** e selecione **application/json** para **tipo de conteúdo**.

4. Clique em **deixe-me selecionar eventos individuais**, selecione **emitir comentário**e clique em **adicionar webhook**.
   
    ![Definir o segredo e a URL do webhook](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

Neste momento, o webhook é configurado para disparar sua função quando um novo comentário do problema é adicionado. 

## <a name="test-the-function"></a>Testar a função
1. No seu repositório GitHub, abra a guia **Problemas** em uma nova janela do navegador.

2. Na nova janela, clique em **novo problema**, digite um título e clique em **enviar novo problema**. 

2. No problema, digite um comentário e clique em **Comentar**. 

3. Na outra janela GitHub, clique em **Editar** ao lado de seu novo webhook, role para baixo até **Entregas recente** e verifique se uma solicitação webhook foi processada pela sua função. 
 
    ![Definir o segredo e a URL do webhook](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-triggered.png)

   A resposta da função deve conter `New GitHub comment: <Your issue comment text>`.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


