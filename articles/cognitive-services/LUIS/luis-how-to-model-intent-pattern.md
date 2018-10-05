---
title: Adicionar modelos de padrão em vez de mais enunciados em aplicativos LUIS
titleSuffix: Azure Cognitive Services
description: Saiba como adicionar modelos padrão em aplicativos LUIS (Reconhecimento Vocal) para aumentar a precisão da previsão.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: c97092385cdc28ba6e4d916ae8039f065f97d31d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47030805"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Como adicionar Padrões para aumentar a precisão da previsão
Depois que um aplicativo LUIS receber declarações do ponto de extremidade, use o [conceito](luis-concept-patterns.md) de Padrões para aumentar a precisão da previsão de declarações que revelam um padrão na ordem e na escolha de palavras. Os Padrões usam [entidades](luis-concept-entity-types.md), e a funções que elas têm de extrair dados usando uma sintaxe de padrão específica. 

## <a name="add-template-utterance-to-create-pattern"></a>Adicionar declaração modelo para criar o padrão
1. Abra o aplicativo selecionando seu nome na página **Meus Aplicativos** e, em seguida, selecione **Padrões** no painel esquerdo, sob **Melhorar o desempenho do aplicativo**.

    ![Captura de tela da lista de Padrões](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Selecione a intenção correta para o padrão. 

    ![Selecionar a intenção](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. Na caixa de texto do modelo, digite a declaração modelo e selecione Enter. Quando quiser inserir o nome da entidade, use a sintaxe de entidade de padrão correta. Comece a sintaxe de entidade com `{`. A lista de entidades será exibida. Selecione a entidade correta e, depois, Enter. 

    ![Captura de tela de entidade do padrão](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Se a entidade incluir uma função, indique a função com um único dois-pontos, `:`, após o nome da entidade, como `{Location:Origin}`. A lista de funções das entidades é exibida em uma lista. Selecione a função e, depois, selecione Enter. 

    ![Captura de tela da entidade com a função](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Depois de selecionar a entidade correta, termine de inserir o padrão e, em seguida, selecione Enter. Quando você terminar de inserir padrões, [treine](luis-how-to-train.md) seu aplicativo.

    ![Captura de tela do padrão inserido com os dois tipos de entidades](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="search-patterns"></a>Padrões de pesquisa
A pesquisa permite que você encontre padrões que contenham um determinado texto.  

1. Selecione o ícone de lupa.

    ![Captura de tela da página Padrões com o ícone da ferramenta de pesquisa realçado](./media/luis-how-to-model-intent-pattern/search-icon.png)

    Digite o texto de pesquisa na caixa de pesquisa, que está no canto superior direito da lista de padrões, e selecione Enter. A lista de padrões é atualizada para exibir apenas os padrões, incluindo o texto de pesquisa.

    ![Captura de tela da página Padrões com o texto de pesquisa, na caixa de pesquisa, realçado](./media/luis-how-to-model-intent-pattern/search-text.png)

    Para cancelar a pesquisa e restaurar a lista completa de padrões, exclua o texto de pesquisa que você digitou.

<!-- TBD: should I be able to click on the magnifying glass again to close the search box? It doesn't reset the list. -->

## <a name="edit-a-pattern"></a>Editar um padrão
1. Para editar um padrão, selecione o botão de reticências (***...***) no lado direito da linha daquele padrão e, em seguida, selecione **Editar**. 

    ![Captura de tela do item de menu Editar na linha do padrão](./media/luis-how-to-model-intent-pattern/patterns-three-dots.png) 

2. Insira as alterações na caixa de texto. Quando terminar, selecione enter. Ao terminar de editar os padrões, [treine](luis-how-to-train.md) seu aplicativo.

    ![Captura de tela da edição do padrão](./media/luis-how-to-model-intent-pattern/edit-pattern.png)

## <a name="reassign-individual-pattern-to-different-intent"></a>Reatribuir padrão individual a uma intenção diferente

Para reatribuir um único padrão a uma intenção diferente, selecione a caixa de listagem de intenções à direita do texto padrão e selecione uma intenção diferente.

![Captura de tela de reatribuição de padrão individual a uma intenção diferente](./media/luis-how-to-model-intent-pattern/reassign-individual-pattern.png)

## <a name="reassign-several-patterns-to-different-intent"></a>Reatribuir vários padrões a uma intenção diferente

Para reatribuir vários padrões a uma intenção diferente, marque a caixa de seleção à esquerda de cada padrão ou marque a caixa de seleção superior. A opção **Reatribuir intenção** é exibida na barra de ferramentas. Selecione a intenção correta para os padrões. 

![Captura de tela de reatribuição de vários padrões a uma intenção diferente](./media/luis-how-to-model-intent-pattern/reassign-many-patterns.png)

## <a name="delete-a-single-pattern"></a>Excluir um único padrão

1. Para excluir um padrão, selecione o botão de reticências (***...***) no lado direito da linha daquele padrão e, em seguida, selecione **Excluir**. 

    ![Captura de tela de Excluir declaração](./media/luis-how-to-model-intent-pattern/patterns-three-dots-ddl.png)

2. Selecione **OK** para confirmar a exclusão.

    ![Captura de tela de Excluir confirmação](./media/luis-how-to-model-intent-pattern/confirm-delete.png)

## <a name="delete-several-patterns"></a>Excluir vários padrões

1. Para excluir vários padrões, marque a caixa de seleção à esquerda de cada padrão ou marque a caixa de seleção superior. A opção **Excluir padrões** é exibida na barra de ferramentas. Selecione **Excluir padrões**.  

    ![Captura de tela da exclusão de vários padrões](./media/luis-how-to-model-intent-pattern/delete-many-patterns.png)

2. O diálogo de confirmação **Excluir padrões** é exibido. Selecione **OK** para concluir a exclusão.

    ![Captura de tela da exclusão de vários padrões](./media/luis-how-to-model-intent-pattern/delete-many-patterns-confirmation.png)

## <a name="filter-pattern-list-by-entity"></a>Filtrar a lista de padrões por entidade

Para filtrar a lista de padrões por uma entidade específica, selecione **Filtros de entidade** na barra de ferramentas acima dos padrões. 

![Captura de tela da filtragem de padrões por entidade](./media/luis-how-to-model-intent-pattern/filter-entities-1.png)

Após a aplicação do filtro, o nome da entidade é exibido abaixo da barra de ferramentas. 

## <a name="filter-pattern-list-by-intent"></a>Filtrar a lista de padrões por intenção

Para filtrar a lista de padrões por uma intenção específica, selecione **Filtros de intenção** na barra de ferramentas acima dos padrões. 

![Captura de tela da filtragem de padrões por intenção](./media/luis-how-to-model-intent-pattern/filter-intents-1.png)

Após a aplicação do filtro, o nome da intenção é exibido abaixo da barra de ferramentas. 

## <a name="remove-entity-or-intent-filter"></a>Remover o filtro de entidade ou intenção
Após a filtragem da lista de padrões, o nome da entidade ou da intenção aparecerá abaixo da barra de ferramentas. Para remover o filtro, selecione o nome.

![Captura de tela da filtragem de padrões por entidade](./media/luis-how-to-model-intent-pattern/filter-entities-2.png)

O filtro é removido e todos os padrões aparecem. 

## <a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>Adicionar padrão de declaração existente na página de intenção ou de entidade
Você pode criar um padrão de uma declaração existente na página **Intenção** ou **Entidade**. Todas as declarações em qualquer página de intenção ou entidade são exibidas em uma lista, na qual a coluna à direita fornece acesso às opções no nível da declaração, como **Editar**, **Excluir** e **Adicionar como padrão**.

1. Na linha selecionada do enunciado, selecione o botão de reticências (***...***) à direita do enunciado e selecione **Adicionar como padrão**.

    [![](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png "Captura de tela da tabela de declarações com Adicionar padrão realçado no menu de opções")](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png)

2. Modificar o padrão de acordo com as [regras de sintaxe](luis-concept-patterns.md#pattern-syntax). Se a declaração selecionada for rotulada com entidades, essas entidades já estarão no padrão com a sintaxe correta.

    ![Captura de tela da filtragem de padrões por entidade](./media/luis-how-to-model-intent-pattern/confirm-patterns-modal.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Treinar seu aplicativo após alterar o modelo com os padrões
Depois de adicionar, editar, remover ou reatribuir um padrão, [treine](luis-how-to-train.md) e [publique](luis-how-to-publish-app.md) seu aplicativo para que as suas alterações afetem as consultas de ponto de extremidade. 

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar um padrão de](luis-tutorial-pattern.md) com pattern.any e funções.
* Saiba como [treinar](luis-how-to-train.md) seu aplicativo.