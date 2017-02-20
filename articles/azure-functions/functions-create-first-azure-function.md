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
ms.date: 02/09/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 7743bfb98552f7fa2334d8daa6a6f6935969f393
ms.openlocfilehash: d76630e0be4a021720d88e6d7e64cf2258f84266


---
# <a name="create-your-first-azure-function"></a>Criar sua primeira Função do Azure
## <a name="overview"></a>Visão geral
O Azure Functions consiste em uma experiência de computação sob demanda controlada por eventos que estende a plataforma de aplicativos existente do Azure com recursos para implementar código disparado por eventos que ocorrem em outros serviços do Azure, produtos de SaaS e sistemas locais. Com o Azure Functions, seus aplicativos são dimensionados sob demanda e você paga apenas pelos recursos que consome. O Azure Functions permite que você crie unidades de código agendadas ou disparadas, que serão implementadas em várias linguagens de programação. Para saber mais sobre o Azure Functions, veja a [Visão geral do Azure Functions](functions-overview.md).

Este tópico mostra como usar o início rápido do Azure Functions no portal para criar uma função "hello world" simples do JavaScript que é invocada por um gatilho HTTP. Você também pode assistir a um vídeo curto para ver como essas etapas são executadas no portal.

## <a name="watch-the-video"></a>Assista ao vídeo
O vídeo a seguir mostra como executar as etapas básicas deste tutorial. 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 
> 

## <a name="create-a-function-from-the-quickstart"></a>Criar uma função do início rápido
Um aplicativo de função hospeda a execução de suas funções no Azure. Execute estas etapas para criar um novo aplicativo de funções com a nova função. O novo aplicativo de funções é criado com uma configuração padrão. Para obter um exemplo de como criar explicitamente seu aplicativo de funções, confira [outros tutoriais de início rápido do Azure Functions](functions-create-first-azure-function-azure-portal.md).

Antes de criar sua primeira função, você precisará ter uma conta ativa do Azure. Se você ainda não tiver uma conta do Azure, [há contas gratuitas disponíveis](https://azure.microsoft.com/free/).

1. Vá para o [portal do Azure Functions](https://functions.azure.com/signin) e entre com sua conta do Azure.
2. Digite um **Nome** exclusivo para seu novo aplicativo de função ou aceite o que foi gerado automaticamente, selecione sua **Região** preferencial e clique em **Criar + introdução**. Observe que você deve inserir um nome válido, que pode conter apenas letras, números e hifens. Sublinhado (**_**) não é um caractere permitido.
3. Na guia **Início Rápido**, clique em **WebHook + API** e em **JavaScript**; em seguida, clique em **Criar uma função**. É criada uma nova função de JavaScript predefinida. 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)
4. (Opcional) Neste ponto no início rápido, é possível fazer um tour rápido pelos recursos do Azure Functions no portal. Depois de concluir ou ignorar o tour, teste sua nova função usando o gatilho HTTP.

## <a name="test-the-function"></a>Testar a função
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Feb17_HO2-->


