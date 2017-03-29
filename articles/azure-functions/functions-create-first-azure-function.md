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
ms.date: 03/14/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: a797910e286cd2aacf5a8aa6c509e2b0f5f60276
ms.lasthandoff: 03/17/2017


---
# <a name="create-your-first-azure-function"></a>Criar sua primeira Função do Azure

Este tópico mostra como usar o início rápido do Azure Functions no portal para criar uma função "olá, mundo" simples, invocada por uma solicitação HTTP. Para saber mais sobre o Azure Functions, veja a [Visão geral do Azure Functions](functions-overview.md).

Antes de começar, você deve ter uma conta do Azure. [Contas gratuitas](https://azure.microsoft.com/free/) estão disponíveis. Você também pode [experimentar o Azure Functions](https://azure.microsoft.com/try/app-service/functions/) sem precisar se registrar no Azure.

## <a name="create-a-function-from-the-portal-quickstart"></a>Criar uma função no início rápido do portal

1. Vá para o [portal do Azure Functions](https://functions.azure.com/signin) e conecte-se usando sua conta do Azure. 
 
2. Digite um **Nome** exclusivo para seu novo aplicativo de função ou aceite o que foi gerado automaticamente, selecione sua **Região** preferencial e clique em **Criar + introdução**. Um nome válido só pode conter letras, números e hifens. Sublinhado (**_**) não é um caractere permitido.

3. Na guia **Início Rápido**, clique em **Webhook + API**, escolha um idioma para a função e clique em **Criar uma função**. Uma nova função predefinida será criada no idioma escolhido. 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

4. (Opcional) Neste ponto no início rápido, é possível fazer um tour rápido pelos recursos do Azure Functions no portal. Após concluir ou ignorar o tour, você pode testar a nova função enviando uma solicitação HTTP.

## <a name="test-the-function"></a>Testar a função
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="watch-the-video"></a>Assista ao vídeo
O vídeo a seguir mostra como executar as etapas básicas deste tutorial. 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 


## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


