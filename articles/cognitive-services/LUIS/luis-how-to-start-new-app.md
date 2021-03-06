---
title: Criar um novo aplicativo
titleSuffix: Language Understanding - Azure Cognitive Services
description: Criar e gerenciar seus aplicativos na página da Web do Serviço Inteligente de Reconhecimento Vocal (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/28/2019
ms.author: diberry
ms.openlocfilehash: 72c4f23f47e0a2c6d9a96dbbe36716bc3ab665f1
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891422"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Criar um aplicativo LUIS no portal do LUIS
Há algumas maneiras de criar um aplicativo do LUIS. Você pode criar um aplicativo do LUIS no portal do [LUIS](https://www.luis.ai) ou por meio das [APIs](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) de criação do LUIS.

## <a name="using-the-luis-portal"></a>Usando o portal do LUIS
Você pode criar um aplicativo no portal do LUIS de várias maneiras:

* Inicie com um aplicativo vazio e crie intenções, declarações e entidades.
* Inicie com um aplicativo vazio e adicione um [domínio predefinido](luis-how-to-use-prebuilt-domains.md).
* Importe um aplicativo do LUIS de um arquivo JSON que já contém intenções, declarações e entidades.

## <a name="using-the-authoring-apis"></a>Usando as APIs de criação
Você pode criar um aplicativo com as APIs de criação de várias maneiras:

* [Inicie](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) com um aplicativo vazio e crie intenções, declarações e entidades.
* [Comece](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/59104e515aca2f0b48c76be5) com um domínio pré-criado.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

## <a name="create-new-app-in-luis"></a>Criar aplicativo no LUIS

1. Na página **Meus Aplicativos**, selecione **Criar novo aplicativo**.

    ![Lista de aplicativos LUIS](./media/luis-create-new-app/apps-list.png)


2. Na caixa de diálogo, dê um nome ao seu aplicativo "TravelAgent".

    ![Caixa de diálogo Criar novo aplicativo](./media/luis-create-new-app/create-app.png)

3. Escolha a cultura do aplicativo (para aplicativo TravelAgent, escolha inglês) e, em seguida, selecione **Concluído**. 

    > [!NOTE]
    > A cultura não poderá ser alterada depois que o aplicativo for criado. 

## <a name="import-an-app-from-file"></a>Importar um aplicativo de arquivo

1. Na página **Meus Aplicativos**, selecione **Importar novo aplicativo**.
1. Na caixa de diálogo pop-up, selecione um arquivo JSON de aplicativo válido e, em seguida, selecione **feito**.

### <a name="import-errors"></a>Erros de importação

Possíveis erros são: 

* Um aplicativo com esse nome já existe. Reimporte o aplicativo e defina as **nome opcional** para um novo nome. 

## <a name="export-app"></a>Exportar aplicativo

1. Na página **Meus Aplicativos**, selecione **Importar novo aplicativo**.
1. Na caixa de diálogo **Importar novo aplicativo**, selecione o arquivo JSON definindo o aplicativo do LUIS.

## <a name="delete-app"></a>Excluir aplicativo

1. Na página **Meus Aplicativos**, selecione as reticências (...) no final da linha do aplicativo.
1. Selecione **Excluir** no menu.
1. Selecione **Ok** na janela de confirmação.

## <a name="next-steps"></a>Próximas etapas

Sua primeira tarefa no aplicativo é [adicionar intenções](luis-how-to-add-intents.md).
