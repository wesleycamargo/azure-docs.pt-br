---
title: "Criar sua primeira função no Portal do Azure | Microsoft Docs"
description: Bem-vindo ao Azure. Crie sua primeira Azure Function no portal do Azure.
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/10/2017
ms.author: glenga
ms.custom: welcome-email
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 7542280ca6dbe1a8d110155e521228d675c0d994
ms.lasthandoff: 04/12/2017


---
# <a name="create-your-first-function-in-the-azure-portal"></a>Criar sua primeira função no portal do Azure

Este tópico mostra como usar as Azure Functions para criar uma função “olá, mundo” que é invocada por uma solicitação HTTP. Antes de criar uma função no Portal do Azure, é necessário criar um aplicativo de funções no Serviço de Aplicativo do Azure para hospedar a execução sem servidor da função.

Para concluir este início rápido, é necessário ter uma conta do Azure. [Contas gratuitas](https://azure.microsoft.com/free/) estão disponíveis. Você também pode [experimentar o Azure Functions](https://azure.microsoft.com/try/app-service/functions/) sem precisar se registrar no Azure.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Para obter mais informações, consulte [Criar um aplicativo de funções no portal do Azure](functions-create-function-app-portal.md).

## <a name="create-a-function"></a>Criar uma função
Estas etapas criam uma função no novo aplicativo de funções usando o início rápido do Azure Functions.

1. Clique no botão **Novo**, depois em **WebHook + API**, escolha linguagem de programação para a função e clique em **Criar uma função**. Uma função é criada na linguagem escolhida por você usando o modelo da função HTTP disparada.  
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

Depois que a função é criada, você pode testá-la enviando uma solicitação HTTP.

## <a name="test-the-function"></a>Testar a função

Já que os modelos de função contêm código de trabalho, você pode testar imediatamente a sua nova função diretamente no portal.

1. Em seu aplicativo de funções, clique na nova função e examine o código do modelo. Observe que a função espera uma solicitação HTTP com um valor *name* passado no corpo da mensagem ou em uma cadeia de consulta. Quando a função é executada, esse valor é retornado na mensagem de resposta. O exemplo abaixo é uma função JavaScript.
   
2. Clique em **Executar** para executar a função. Você vê que a execução é disparada por uma solicitação HTTP de teste, as informações são gravadas em logs e a resposta "hello..." é exibida na **Saída** na guia **Teste**.
 
    ![](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

3. Na caixa de texto **Corpo da solicitação**, altere o valor da propriedade *name* para seu nome e clique em **Executar** novamente. Neste momento, a resposta no **Saída** contém seu nome.   

4. Para disparar a execução da mesma função de uma ferramenta de teste HTTP ou de outra janela de navegador, clique em **</> Obter URL da função**, copie a URL da solicitação e cole-a na ferramenta ou na barra de endereços do navegador. Acrescente o valor de cadeia de caracteres de consulta `&name=yourname` à URL e execute a solicitação. As mesmas informações são gravadas nos logs e a mesma cadeia de caracteres está contida no corpo da mensagem de resposta.

    ![](./media/functions-create-first-azure-function/function-app-browser-testing.png)


## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


