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
ms.date: 03/15/2017
ms.author: glenga
ms.custom: welcome-email
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: 6bb15dfd0e2c40b99d06ef2d695e3ba7ca78e91b
ms.lasthandoff: 03/22/2017


---
# <a name="create-your-first-function-in-the-azure-portal"></a>Criar sua primeira função no portal do Azure

Este tópico mostra como criar uma Azure Function simples “olá, mundo” que é invocada por uma solicitação HTTP. Antes de criar uma função no portal do Azure, é necessário criar um aplicativo de funções no Serviço de Aplicativo do Azure para hospedar a execução da função.

Para concluir este início rápido, é necessário ter uma conta do Azure. [Contas gratuitas](https://azure.microsoft.com/free/) estão disponíveis. Você também pode [experimentar o Azure Functions](https://azure.microsoft.com/try/app-service/functions/) sem precisar se registrar no Azure.

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Para obter mais informações, consulte [Criar um aplicativo de funções no portal do Azure](functions-create-function-app-portal.md).

## <a name="create-a-function"></a>Criar uma função
Estas etapas criam uma função no novo aplicativo de funções usando o início rápido do Azure Functions.

1. Na guia **Início Rápido**, clique em **Webhook + API**, escolha um idioma para a função e clique em **Criar uma função**. Uma nova função predefinida será criada no idioma escolhido.  
   
    ![](./media/functions-create-first-azure-function-azure-portal/function-app-quickstart-node-webhook.png)

4. (Opcional) Neste ponto no início rápido, é possível fazer um tour rápido pelos recursos do Azure Functions no portal. Depois de concluir ou ignorar o tour, teste a nova função enviando uma solicitação HTTP.


## <a name="test-the-function"></a>Testar a função
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


