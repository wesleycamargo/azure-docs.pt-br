---
title: "Criar uma função no Portal do Azure | Microsoft Docs"
description: "Crie sua primeira Função do Azure, um aplicativo sem servidor, em menos de dois minutos."
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
ms.date: 02/02/2017
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: ab0b218a99ab3ff98edfa075eabbd3eb2c2bd1d4
ms.openlocfilehash: 996fc80ff926117dc12180efe1949b3dbeba3f91


---
# <a name="create-a-function-from-the-azure-portal"></a>Criar uma função no portal do Azure
## <a name="overview"></a>Visão geral
O Azure Functions consiste em uma experiência de computação sob demanda controlada por eventos que estende a plataforma de aplicativos existente do Azure com recursos para implementar código disparado por eventos que ocorrem em outros serviços do Azure, produtos de SaaS e sistemas locais. Com o Azure Functions, seus aplicativos são dimensionados sob demanda e você paga apenas pelos recursos que consome. O Azure Functions permite que você crie unidades de código agendadas ou disparadas, que serão implementadas em várias linguagens de programação. Para saber mais sobre o Azure Functions, veja a [Visão geral do Azure Functions](functions-overview.md).

Este tópico mostra como usar o portal do Azure para criar uma Azure Function "hello world" simples do Node.js que é invocada por um gatilho HTTP. Antes de criar uma função no portal do Azure, você deve criar um aplicativo de funções explicitamente no Serviço de Aplicativo do Azure. Para que o aplicativo de funções seja criado para você automaticamente, confira [os outros tutoriais de início rápido do Azure Functions](functions-create-first-azure-function.md), que é uma experiência de início rápido mais simples e inclui vídeo.

## <a name="create-a-function-app"></a>Criar um aplicativo de funções
Um aplicativo de função hospeda a execução de suas funções no Azure. Se você ainda não tiver uma conta do Azure, confira a experiência [Experimentar Funções](https://functions.azure.com/try) ou [criar uma conta do Azure gratuita](https://azure.microsoft.com/free/). Siga estas etapas para criar um aplicativo de funções no portal do Azure.

1. Vá para o [portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.
2. Clique em **+Novo** > **Computação** > **Aplicativo de Funções**, selecione sua **Assinatura**, digite um **Nome do aplicativo** exclusivo que identifica seu aplicativo de funções e especifique as seguintes configurações:
   
   * **[Grupo de Recursos](../azure-resource-manager/resource-group-overview.md)**: selecione **Criar novo** e digite um nome para seu novo grupo de recursos. Você também pode escolher um grupo de recursos existente; no entanto, é possível que você não consiga criar um Plano do Serviço de Aplicativo com base no consumo para seu aplicativo de funções.
   * **[Plano de hospedagem](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)**, que pode ser um dos seguintes: 
     * **Plano de consumo**: plano padrão para o Azure Functions. Ao escolher um plano de consumo, você também deve escolher o **Local** e definir a **Alocação de Memória** (em MB). Para saber como a alocação de memória afeta os custos, confira [Preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions/). 
     * **Plano do Serviço de Aplicativo**: esse plano exige que você crie um **plano/local para o Serviço de Aplicativo** ou selecione um existente. Essas configurações determinam o [local, os recursos, o custo e os recursos de computação](https://azure.microsoft.com/pricing/details/app-service/) associados ao seu aplicativo.  
   * **Conta de armazenamento**: cada aplicativo de funções requer uma conta de armazenamento. Você pode usar uma conta de armazenamento existente ou criar uma nova. 
     
     ![Criar um aplicativo de funções no portal do Azure](./media/functions-create-first-azure-function-azure-portal/function-app-create-flow.png)

    Observe que você deve inserir um **Nome de aplicativo** válido, que pode conter apenas letras, números e hifens. Sublinhado (**_**) não é um caractere permitido.

3. Clique em **Criar** para provisionar e implantar o novo aplicativo de funções.  

Agora que o aplicativo de funções está provisionado, você pode criar sua primeira função.

## <a name="create-a-function"></a>Criar uma função
Estas etapas criam uma função no guia de início rápido do Azure Functions.

1. Na guia **Início Rápido**, clique em **WebHook + API** e em **JavaScript**; em seguida, clique em **Criar uma função**. Uma nova função predefinida do Node.js é criada. 
   
    ![](./media/functions-create-first-azure-function-azure-portal/function-app-quickstart-node-webhook.png)

2. (Opcional) Neste ponto no início rápido, é possível fazer um tour rápido pelos recursos do Azure Functions no portal.    Depois de concluir ou ignorar o tour, teste sua nova função usando o gatilho HTTP.

## <a name="test-the-function"></a>Testar a função
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Feb17_HO1-->


