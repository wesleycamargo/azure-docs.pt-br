---
title: "Criar um webhook ou Função do Azure da API | Microsoft Docs"
description: "Use os Azure Functions para criar uma função que é invocada por um WebHook ou chamada à API."
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
ms.date: 11/29/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 44e397c7521ba8f0ba11893c364f51177561bee4
ms.openlocfilehash: a74fc30480068788f33df092594119253df9487b


---
# <a name="create-a-webhook-or-api-azure-function"></a>Criar um webhook ou um Azure Function de API
O Azure Functions é uma experiência de computação sob demanda controlada por evento que permite que você crie unidades de código agendadas ou disparadas, que serão implementadas em diversas linguagens de programação. Para saber mais sobre o Azure Functions, veja a [Visão geral do Azure Functions](functions-overview.md).

Este tópico mostra como criar uma função do Node.js que é invocada por um webhook do GitHub. A nova função é criada com base em um modelo predefinido no portal do Azure Functions. Você também pode assistir a um vídeo curto para ver como essas etapas são executadas no portal.

## <a name="watch-the-video"></a>Assista ao vídeo
O vídeo a seguir mostra como executar as etapas básicas deste tutorial 

>[!VÍDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-a-Web-Hook-or-API-Azure-Function/player]
>
>

## <a name="create-a-webhook-triggered-function-from-the-template"></a>Criar uma função disparada por webhook por meio do modelo
Um aplicativo de função hospeda a execução de suas funções no Azure. Se você ainda não tiver uma conta do Azure, confira a experiência [Experimentar Funções](https://functions.azure.com/try) ou [criar uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

1. Vá para o [portal do Azure Functions](https://functions.azure.com/signin) e entre com sua conta do Azure.

2. Se você tiver um aplicativo de funções existente para usar, selecione-o em **Seus aplicativos de função** e clique em **Abrir**. Para criar um aplicativo de funções, digite um **Nome** exclusivo para ele ou aceite o que foi gerado, selecione sua **Região** preferencial e clique em **Criar + introdução**. 

3. No aplicativo de função, clique em **+ Nova Função** > **Webhook do GitHub - Nó** > **Criar**. Essa etapa cria uma função com um nome padrão de acordo com o modelo especificado. 
   
    ![Criar uma função disparada pelo webhook do GitHub](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook.png) 

4. Em **Desenvolver**, observe a função express.js de exemplo na janela **Código**. Essa função recebe uma solicitação do GitHub de um webhook de comentário do problema, registra o texto do problema e envia uma resposta ao webhook como `New GitHub comment: <Your issue comment text>`.

    ![Examinar o código de função](./media/functions-create-a-web-hook-or-api-function/functions-new-webhook-in-portal.png) 

1. Copie a **URL de Função** e os valores do **Segredo do GitHub**. Você precisará desses valores ao criar o webhook no GitHub. 

2. Clique em **Testar**, observe o corpo JSON predefinido de um comentário do problema no **Corpo da solicitação** e clique em **Executar**. 

    ![Testar função webhook no portal](./media/functions-create-a-web-hook-or-api-function/functions-test-webhook-in-portal.png)
   
    > [!NOTE]
    > Você sempre poderá testar uma nova função baseada em modelo direitamente na guia **Desenvolver** fornecendo todos os dados JSON de corpo esperados e clicando no botão **Executar**. Nesse caso, o modelo tem um corpo predefinido para um comentário do problema. 

Em seguida, você criará o webhook real no repositório GitHub.

## <a name="configure-the-webhook"></a>Configurar o webhook
1. No GitHub, navegue até um repositório de sua propriedade. Você também pode usar todos os repositórios que você tenha bifurcado.
 
2. Clique em **Configurações** > **Webhooks e serviços** > **Adicionar webhook**.
   
    ![Adicionar um webhook do GitHub](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook-2.png)   
3. Cole a URL e o segredo da função em **URL de Carga** e no **Segredo** e clique em **Deixe-me selecionar eventos individuais**, selecione **Emitir comentário e clique em Adicionar webhook** e clique em **Adicionar webhook**.
   
    ![Definir o segredo e a URL do webhook](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook-3.png) 

Neste momento, o webhook do GitHub é configurado para disparar a função quando um novo comentário do problema é adicionado.  
Agora é hora de testá-lo.

## <a name="test-the-function"></a>Testar a função
1. No repositório do GitHub, abra a guia **Problemas** em uma nova janela do navegador, clique em **Novo Problema**, digite um título e clique em **Enviar novo problema**. Também é possível abrir um problema existente.

2. No problema, digite um comentário e clique em **Comentar**. Neste momento, é possível voltar para o novo webhook no GitHub e, em **Entregas Recentes**, ver se uma solicitação de webhook foi enviada e se o corpo da resposta é `New GitHub comment: <Your issue comment text>`.

3. Novamente no portal do Functions, role para baixo até os logs e veja se a função foi disparada e se o valor `New GitHub comment: <Your issue comment text>` foi gravado nos logs de transmissão.

## <a name="next-steps"></a>Próximas etapas
Veja estes tópicos para obter mais informações sobre o Azure Functions.

* [Referência do desenvolvedor do Azure Functions](functions-reference.md)  
   Referência do programador para codificação de funções.
* [Testando o Azure Functions](functions-test-a-function.md)  
   Descreve várias ferramentas e técnicas para testar suas funções.
* [Como escalar o Azure Functions](functions-scale.md)  
  Discute os planos de serviço disponíveis com o Azure Functions, incluindo o plano de hospedagem de consumo e como escolher o plano certo.  

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Dec16_HO2-->


