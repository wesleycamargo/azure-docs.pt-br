---
title: Examinar as declarações de usuário
titleSuffix: Language Understanding - Azure Cognitive Services
description: Aprendizado ativo captura consultas de ponto de extremidade e seleciona as declarações de ponto de extremidade do usuário que não saibam. Você examinar essas declarações para selecionar a intenção e marcação de entidades para essas declarações do mundo de leitura. Aceitar essas alterações em suas declarações de exemplo, em seguida, treinar e publicar. Em seguida, LUIS identifica declarações com mais precisão.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/25/2019
ms.author: diberry
ms.openlocfilehash: 8fac360682ef11c438cdec333fac21d6f8cfc117
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791039"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>Como revisar as declarações de ponto de extremidade no portal do LUIS para aprendizado ativo

[Aprendizado ativo](luis-concept-review-endpoint-utterances.md) captura consultas de ponto de extremidade e seleciona as declarações de ponto de extremidade do usuário que não saibam. Você examinar essas declarações para selecionar a intenção e marcação de entidades para essas declarações do mundo de leitura. Aceitar essas alterações em suas declarações de exemplo, em seguida, treinar e publicar. Em seguida, LUIS identifica declarações com mais precisão.


## <a name="enable-active-learning"></a>Habilitar o aprendizado ativo

Para habilitar o aprendizado ativo, registrar consultas de usuário. Isso é feito definindo a [consulta de ponto de extremidade](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) com o `log=true` querystring parâmetro e valor.

## <a name="disable-active-learning"></a>Desabilitar o aprendizado ativo

Para desabilitar o aprendizado ativo, não faça logon em consultas de usuário. Isso é feito definindo a [consulta de ponto de extremidade](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) com o `log=false` querystring parâmetro e valor.

## <a name="filter-utterances"></a>Filtrar declarações

1. Abra o aplicativo (por exemplo, TravelAgent) selecionando o nome na página **Meus Aplicativos** e selecione **Compilar** na barra superior.

1. Em **Melhorar o desempenho do aplicativo**, selecione **Examinar declarações de ponto de extremidade**.

1. Na página **Examinar declarações de ponto de extremidade**, clique na caixa de texto **Filtrar lista por intenção ou entidade**. Essa lista suspensa inclui todas as intenções em **INTENÇÕES** e todas as entidades em **ENTIDADES**.

    ![Filtro de declarações](./media/label-suggested-utterances/filter.png)

1. Selecione uma categoria (intenções ou entidades) na lista suspensa e examine as declarações.

    ![Declarações de intenção](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Rotular entidades
O LUIS substitui tokens de entidade (palavras) por nomes de entidade realçados em azul. Se uma declaração tem entidades sem rótulo, rotule-as na declaração. 

1. Selecione as palavras na declaração. 

1. Selecione uma entidade na lista.

    ![Rotular entidade](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Alinhar declaração única

Cada declaração tem uma intenção sugerida exibida na coluna **Intenção alinhada**. 

1. Se você concordar com essa sugestão, selecione-a na marca de seleção.

    ![Manter intenção alinhada](./media/label-suggested-utterances/align-intent-check.png)

1. Se você não concordar com a sugestão, selecione a intenção correta na lista suspensa de intenções alinhadas, e marque a caixa de seleção à direita da intenção alinhada. 

    ![Alinhar intenção](./media/label-suggested-utterances/align-intent.png)

1. Depois de marcar a caixa de seleção, a declaração é removida da lista. 

## <a name="align-several-utterances"></a>Alinhar várias declarações

Para alinhar várias declarações, marque a caixa à esquerda das declarações e selecione o botão **Adicionar selecionados**. 

![Alinhar vários](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>Verificar intenção alinhada

Você pode verificar se a declaração foi alinhado com a intenção correta na página **Intenções**, selecione o nome da intenção e revise as declarações. A declaração **Examinar declarações de ponto de extremidade** está na lista.

## <a name="delete-utterance"></a>Excluir a declaração

Cada declaração pode ser excluída da lista de revisão. Depois de excluída, ela não aparecerá na lista novamente. Isso será verdadeiro mesmo se o usuário inserir a mesma declaração do ponto de extremidade. 

Se você não tiver certeza se deve excluir a declaração, mova-a para a intenção None ou crie uma nova intenção, como "diversos" e mova a declaração para essa intenção. 

## <a name="delete-several-utterances"></a>Excluir várias declarações

Para excluir várias declarações, selecione cada item e selecione a lixeira à direita do botão **Adicionar selecionados**.

![Excluir vários](./media/label-suggested-utterances/delete-several.png)


## <a name="next-steps"></a>Próximas etapas

Para testar como o desempenho melhora depois que você rotula as declarações sugeridas, você poderá acessar o console de teste selecionando **Teste** no painel superior. Para obter instruções sobre como testar seu aplicativo usando o console de teste, consulte [Treinar e testar seu aplicativo](luis-interactive-test.md).
