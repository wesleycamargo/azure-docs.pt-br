---
title: Criar um novo aplicativo com LUIS | Microsoft Docs
description: Criar e gerenciar seus aplicativos na página da Web do Serviço Inteligente de Reconhecimento Vocal (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/17/2018
ms.author: diberry
ms.openlocfilehash: 3adeecd4a4e2040a92689b7c92be9630c9a0d93b
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225407"
---
# <a name="create-an-app"></a>Criar um aplicativo
Você pode criar um novo aplicativo de maneiras diferentes: 

* [Inicie](#create-new-app) com um aplicativo vazio e crie intenções, declarações e entidades.
* [Inicie](#create-new-app) com um aplicativo vazio e adicione um [domínio predefinido](luis-how-to-use-prebuilt-domains.md).
* [Importe um aplicativo do LUIS](#import-new-app) de um arquivo JSON que já contém intenções, declarações e entidades.

## <a name="what-is-an-app"></a>O que é um aplicativo
O aplicativo contém [versões](luis-how-to-manage-versions.md) do seu modelo, bem como os [colaboradores](luis-how-to-collaborate.md) para o aplicativo. Quando você cria o aplicativo, você seleciona a cultura ([idioma](luis-supported-languages.md)) que **não pode ser alterada posteriormente**. 

A versão padrão de um novo aplicativo é "0.1." 

Você pode criar e gerenciar seus aplicativos na página **Meus Aplicativos**. Você sempre pode acessar essa página selecionando **Meus Aplicativos** na barra de navegação superior do site do [LUIS](luis-reference-regions.md). 

[![](media/luis-create-new-app/apps-list.png "Captura de tela da lista de aplicativos")](media/luis-create-new-app/apps-list.png#lightbox)

## <a name="create-new-app"></a>Criar novo aplicativo

1. Na página **Meus Aplicativos**, selecione **Criar novo aplicativo**.
2. Na caixa de diálogo, dê um nome ao seu aplicativo "TravelAgent".

    ![Caixa de diálogo Criar novo aplicativo](./media/luis-create-new-app/create-app.png)

3. Escolha a cultura do aplicativo (para aplicativo TravelAgent, escolha inglês) e, em seguida, selecione **Concluído**. 

    >[!NOTE]
    >A cultura não poderá ser alterada depois que o aplicativo for criado. 

## <a name="import-new-app"></a>Importar novos aplicativos
Você pode definir o nome (no máximo 50 caracteres), a versão (máximo de 10 caracteres) e a descrição de um aplicativo no arquivo JSON. Exemplos de arquivos do aplicativo JSON estão disponíveis em [exemplos de LUIS](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/Examples-BookFlight).

1. Na página **Meus Aplicativos**, selecione **Importar novo aplicativo**.
2. Na caixa de diálogo **Importar novo aplicativo**, selecione o arquivo JSON definindo o aplicativo do LUIS.

    ![Caixa de diálogo Importar um novo aplicativo](./media/luis-create-new-app/import-app.png)

## <a name="export-app"></a>Exportar aplicativo
1. Na página **Meus Aplicativos** selecione as reticências (***...***) no final da linha do aplicativo.

    [![](media/luis-create-new-app/apps-list.png "Captura de tela da caixa de diálogo pop-up de ações por aplicativo")](media/luis-create-new-app/three-dots.png#lightbox)

2. Selecione **Exportar aplicativo** no menu. 

## <a name="rename-app"></a>Renomear aplicativo

1. Na página **Meus Aplicativos** selecione as reticências (***...***) no final da linha do aplicativo. 
2. Selecione **Renomear** no menu.
3. Digite o novo nome do aplicativo e selecione **Concluído**.

## <a name="delete-app"></a>Excluir aplicativo

> [!CAUTION]
> Você está excluindo o aplicativo para todos os colaboradores e o proprietário. [Exporte](#export-app) o aplicativo antes de excluí-lo. 

1. Na página **Meus Aplicativos** selecione as reticências (***...***) no final da linha do aplicativo. 
2. Selecione **Excluir** no menu.
3. Selecione **Ok** na janela de confirmação.

## <a name="export-endpoint-logs"></a>Exportar logs do ponto de extremidade
Os logs contêm a consulta, a hora UTC e resposta JSON do LUIS.

1. Na página **Meus Aplicativos** selecione as reticências (***...***) no final da linha do aplicativo. 
2. Selecione **Exportar logs do ponto de extremidade** no menu.

```
Query,UTC DateTime,Response
text i'm driving and will be 30 minutes late to the meeting,02/13/2018 15:18:43,"{""query"":""text I'm driving and will be 30 minutes late to the meeting"",""intents"":[{""intent"":""None"",""score"":0.111048922},{""intent"":""SendMessage"",""score"":0.987501}],""entities"":[{""entity"":""i ' m driving and will be 30 minutes late to the meeting"",""type"":""Message"",""startIndex"":5,""endIndex"":58,""score"":0.162995353}]}"
```

## <a name="next-steps"></a>Próximas etapas

Sua primeira tarefa no aplicativo é [adicionar intenções](luis-how-to-add-intents.md).