---
title: Intenções predefinidas
titleSuffix: Azure Cognitive Services
description: O LUIS inclui um conjunto de intenções predefinidas para adicionar rapidamente cenários comuns de usuário de conversação.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: abdbd0672fbb29f3936e4bda6f71e41c0f287329
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221208"
---
# <a name="add-prebuilt-intents-for-common-intents"></a>Adicionar intenções predefinidas para intenções comuns 

O LUIS inclui um conjunto de intenções predefinidas dos domínios predefinidos para adicionar rapidamente intenções e enunciados comuns. Essa é uma maneira rápida e fácil de adicionar habilidades ao aplicativo cliente de conversação sem a necessidade de projetar os modelos para essas habilidades. 

## <a name="add-a-prebuilt-intent"></a>Adicionar uma intenção predefinida

1. Na página **Meus aplicativos**, selecione o aplicativo. Isso abre o aplicativo na seção **Compilar** do aplicativo. 

1. Na página **Intenções**, selecione **Adicionar intenção predefinida** na barra de ferramentas acima da lista de tentativas. 

1. Selecione a intenção **Utilities.Cancel** na caixa de diálogo pop-up. 

    ![Adicionar intenção predefinida](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. Selecione o botão **Concluído**.

## <a name="train-and-test"></a>Treinar e testar o aplicativo

1. Depois que a intenção for adicionada, treine o aplicativo selecionando **Treinar** na barra de ferramentas superior direita. 

1. Testar a nova intenção, selecionando **Teste** na barra de ferramentas à direita. 

1. Na caixa de texto, insira as declarações para cancelar:

    |Enunciado do teste|Pontuação de previsão|
    |--|:--|
    |Desejo cancelar meu voo.|0.67|
    |Cancelar a compra.|0.52|
    |Cancelar a reunião.|0.56|

    ![Testar intenção predefinida](./media/luis-prebuilt-intents/test.png)

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Entidades predefinidas](./luis-prebuilt-entities.md)
