---
title: Declarações sugeridas de rótulo com o LUIS | Microsoft Docs
description: Use o Reconhecimento vocal (LUIS) para rotular declarações sugeridas e ajudar a melhorar o aprendizado de máquina ativo.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/08/2017
ms.author: v-geberr
ms.openlocfilehash: 91a2fe738743d359677392bfb79aac885702b440
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35367016"
---
# <a name="review-endpoint-utterances"></a>Examinar declarações de ponto de extremidade

O recurso inovador do LUIS é o [conceito](luis-concept-review-endpoint-utterances.md) de aprendizado ativo. Depois que seu LUIS tiver consultas de ponto de extremidade, ele usa o aprendizado ativo para melhorar a qualidade dos resultados. No processo de aprendizado ativo, o LUIS examina todas as declarações de ponto de extremidade e seleciona as declarações de que não tem certeza. Se você rotular essas declarações, treiná-las e publicá-las, o LUIS identificará as declarações com mais precisão. 

## <a name="filter-utterances"></a>Filtrar declarações
1. Abra o aplicativo (por exemplo, TravelAgent) selecionando o nome na página **Meus Aplicativos** e selecione **Compilar** na barra superior.

2. Em **Melhorar o desempenho do aplicativo**, selecione **Examinar declarações de ponto de extremidade**.

    ![Revisar declarações](./media/label-suggested-utterances/review.png)

3. Na página **Examinar declarações de ponto de extremidade**, clique na caixa de texto **Filtrar lista por intenção ou entidade**. Essa lista suspensa inclui todas as intenções em **INTENÇÕES** e todas as entidades em **ENTIDADES**.

    ![Filtro de declarações](./media/label-suggested-utterances/filter.png)

4. Selecione uma categoria (intenções ou entidades) na lista suspensa e examine as declarações.

    ![Declarações de intenção](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Rotular entidades
O LUIS substitui tokens de entidade (palavras) por nomes de entidade realçados em azul. Se uma declaração tem entidades sem rótulo, rotule-as na declaração. 

1. Selecione as palavras na declaração. 

2. Selecione uma entidade na lista.

    ![Rotular entidade](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Alinhar declaração única

Cada declaração tem uma intenção sugerida exibida na coluna **Intenção alinhada**. 

1. Se você concordar com essa sugestão, selecione-a na marca de seleção.

    ![Manter intenção alinhada](./media/label-suggested-utterances/align-intent-check.png)

2. Se você não concordar com a sugestão, selecione a intenção correta na lista suspensa de intenções alinhadas, e marque a caixa de seleção à direita da intenção alinhada. 

    ![Alinhar intenção](./media/label-suggested-utterances/align-intent.png)

3. Depois de marcar a caixa de seleção, a declaração é removida da lista. 

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

Para testar como o desempenho melhora depois que você rotula as declarações sugeridas, você poderá acessar o console de teste selecionando **Teste** no painel superior. Para obter instruções sobre como testar seu aplicativo usando o console de teste, consulte [Treinar e testar seu aplicativo](Train-Test.md).
