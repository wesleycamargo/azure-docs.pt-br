---
title: "Criar sua primeira Função do Azure | Microsoft Docs"
description: "Crie sua primeira Função do Azure, um aplicativo sem servidor, em menos de dois minutos."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 4a1669e7-233e-4ea2-9b83-b8624f2dbe59
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/08/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e446766c0f3d19a8cce585d708e8e7a809593408


---
# <a name="create-your-first-azure-function"></a>Criar sua primeira Função do Azure
## <a name="overview"></a>Visão geral
O Azure Functions consiste em uma experiência de computação sob demanda controlada por eventos que estende a plataforma de aplicativos existente do Azure com recursos para implementar código disparado por eventos que ocorrem em outros serviços do Azure, produtos de SaaS e sistemas locais. Com as Funções do Azure, seus aplicativos são dimensionados sob demanda e você paga apenas pelos recursos que consome. O Azure Functions permite que você crie unidades de código agendadas ou disparadas, que serão implementadas em uma variedade de linguagens de programação. Para saber mais sobre o Azure Functions, confira a [Visão geral do Azure Functions](functions-overview.md).

Este tópico mostra como usar o início rápido do Azure Functions no portal para criar uma função "hello world" simples do Node.js que é invocada por um gatilho HTTP. Você também pode assistir a um vídeo curto para ver como essas etapas são executadas no portal.

## <a name="watch-the-video"></a>Assista ao vídeo
O vídeo a seguir mostra como executar as etapas básicas deste tutorial. 

> [!VÍDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 
> 

## <a name="create-a-function-from-the-quickstart"></a>Criar uma função do início rápido
Um aplicativo de função hospeda a execução de suas funções no Azure. Siga estas etapas para criar um novo aplicativo de função, bem como a nova função. O novo aplicativo de funções é criado com uma configuração padrão. Para obter um exemplo de como criar explicitamente seu aplicativo de funções, confira [outros tutoriais de início rápido do Azure Functions](functions-create-first-azure-function-azure-portal.md).

Antes de criar sua primeira função, você precisará ter uma conta ativa do Azure. Se você ainda não tiver uma conta do Azure, [há contas gratuitas disponíveis](https://azure.microsoft.com/free/).

1. Vá para o [portal do Azure Functions](https://functions.azure.com/signin) e entre com sua conta do Azure.
2. Digite um **Nome** exclusivo para seu novo aplicativo de função ou aceite o que foi gerado, selecione sua **Região** preferencial e clique em **Criar + introdução**. 
3. Na guia **Início Rápido**, clique em **WebHook + API** e em **JavaScript**; em seguida, clique em **Criar uma função**. Uma nova função predefinida do Node.js é criada. 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)
4. (Opcional) Neste ponto no início rápido, é possível fazer um tour rápido pelos recursos do Azure Functions no portal.    Depois que você tiver concluído ou ignorado o tour, poderá testar sua nova função usando o gatilho HTTP.

## <a name="test-the-function"></a>Testar a função
Como os inícios rápidos do Azure Functions contêm código funcional, você poderá testar imediatamente sua nova função.

1. Na guia **Desenvolver**, examine a janela **Código** e observe que esse código Node.js espera uma solicitação HTTP com um valor *name* passado no corpo da mensagem ou em uma cadeia de consulta. Quando a função é executada, esse valor é retornado na mensagem de resposta.
   
    ![](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)
2. Role para baixo até a caixa de texto **Corpo da solicitação**, altere o valor da propriedade *name* para seu nome e clique em **Executar**. Você verá que a execução é disparada por uma solicitação HTTP de teste, as informações são gravadas em logs de streaming e a resposta "hello" é exibida na **Saída**. 
3. Para disparar a execução da mesma função de outra janela ou guia, copie o valor de **URL da Função** da guia **Desenvolver** e cole-o na barra de endereços de um navegador, anexe o valor da cadeia de consulta `&name=yourname` e pressione enter. As mesmas informações são gravadas nos logs e o navegador exibe a resposta "hello" como antes.

## <a name="next-steps"></a>Próximas etapas
Este início rápido demonstra uma execução muito simples de uma função básica disparada por HTTP. Veja estes tópicos para saber mais sobre como aproveitar o poder do Azure Functions em seus aplicativos.

* [Referência do desenvolvedor do Azure Functions](functions-reference.md)  
   Referência do programador para codificação de funções e definição de gatilhos e de associações.
* [Testando o Azure Functions](functions-test-a-function.md)  
   Descreve várias ferramentas e técnicas para testar suas funções.
* [Como escalar o Azure Functions](functions-scale.md)  
   Discute os planos de serviço disponíveis com o Azure Functions, incluindo o plano de serviço Dinâmico, e como escolher o plano certo. 
* [O que é o Serviço de Aplicativo do Azure?](../app-service/app-service-value-prop-what-is.md)  
   O Azure Functions utiliza a plataforma Serviço de Aplicativo do Azure para a funcionalidade básica, como implantações, variáveis de ambiente e diagnóstico. 

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO2-->


