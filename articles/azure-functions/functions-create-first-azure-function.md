---
title: "Criar sua primeira função no Portal do Azure | Microsoft Docs"
description: "Aprenda a criar sua primeira Função do Azure para a execução sem servidor usando o Portal do Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/21/2018
ms.author: glenga
ms.custom: mvc, devcenter
experiment: 
ms.openlocfilehash: 82f87447a2176dffd55dcf71155c389398f7d64c
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Criar sua primeira função no portal do Azure

O Azure Functions lhe permite executar seu código em um ambiente [sem servidor](https://azure.microsoft.com/overview/serverless-computing/) sem que seja preciso primeiro criar uma VM ou publicar um aplicativo Web. Neste tópico, aprenda a usar o Functions para criar uma função "Olá, Mundo" no Portal do Azure.

![Criar um aplicativo de funções no portal do Azure](./media/functions-create-first-azure-function/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Entre no portal do Azure no endereço <http://portal.azure.com> com sua conta do Azure.

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

Você deve ter um aplicativo de funções para hospedar a execução de suas funções. Um aplicativo de funções permite a você agrupar funções como uma unidade lógica para facilitar o gerenciamento, implantação e compartilhamento de recursos. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

Em seguida, crie uma nova função no novo aplicativo de funções.

## <a name="create-function"></a>Criar uma função disparada por HTTP

1. Expanda seu novo aplicativo de funções, depois clique no botão **+** ao lado de **Functions**.

2.  Na página **Início rápido**, selecione **WebHook + API**, **Escolha uma linguagem** para sua função e clique em **Criar esta função**. 
   
    ![Início rápido de funções no Portal do Azure.](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

Uma função é criada na linguagem escolhida por você usando o modelo de uma função disparada por HTTP. Este tópico mostra uma função de script C# no portal, mas você pode criar uma função em qualquer [linguagem suportada](supported-languages.md). 

Agora você pode executar a nova função enviando uma solicitação HTTP.

## <a name="test-the-function"></a>Testar a função

1. Em sua nova função, clique em **</> Obter URL da função** na parte superior direita, selecione **padrão (Tecla de função)** e depois clique em **Copiar**. 

    ![Copiar a URL da função do Portal do Azure](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. Cole a URL de função na barra de endereços do navegador. Adicione o valor da cadeia de caracteres de consulta `&name=<yourname>` ao final desta URL e pressione `Enter` em seu teclado para executar a solicitação. Você deverá ver a resposta retornada pela função exibida no navegador.  

    O exemplo a seguir mostra a resposta no navegador Edge (outros navegadores podem incluir XML exibido):

    ![Resposta da função no navegador.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    A URL da solicitação inclui uma chave que é necessária, por padrão, para acessar sua função via HTTP.   

3. Quando a função é executada, informações de rastreamento são gravadas nos logs. Para ver a saída do rastreamento da execução anterior, volte para sua função no portal e clique na seta na parte inferior da tela para expandir os **Logs**. 

   ![Visualizador de log de função no Portal do Azure.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Você criou um aplicativo de funções com uma função simples disparada por HTTP.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obter mais informações, consulte [Associações HTTP e de webhook do Azure Functions](functions-bindings-http-webhook.md).



