---
title: Adicionar intenções a aplicativos do LUIS
titleSuffix: Azure Cognitive Services
description: Adicione intenções ao seu aplicativo do LUIS para identificar grupos de perguntas ou comandos que têm as mesmas intenções.
services: cognitive-services
author: diberry
manager: cgronlun
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 9b7207a16a89e48ad64b2dbc48a5293d0cf8aa25
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47036075"
---
# <a name="manage-intents"></a>Gerenciar intenções 
Adicionar [intenções](luis-concept-intent.md) ao seu aplicativo do LUIS para identificar grupos de perguntas ou comandos que têm as mesmas intenções. 

As intenções são gerenciadas na seção **Build** na barra de ferramentas superior. Adicione e gerencie suas intenções da página **Intenções**, disponível no painel esquerdo. 

O procedimento a seguir demonstra como adicionar a intenção "Bookflight" no aplicativo TravelAgent.

## <a name="add-intent"></a>Adicionar intenção

1. Abra o aplicativo (por exemplo, TravelAgent) clicando em seu nome na página **Meus Aplicativos** e clique em **Intenções** no painel esquerdo. 
2. Na página **Intenções**, clique em **Criar nova intenção**.

3. Na caixa de diálogo **Criar nova intenção**, digite o nome da intenção "BookFlight" e clique em **Concluído**.

    ![Adicionar intenção](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    Na página de detalhes de intenções da intenção recém-adicionada, [adicione declarações](#add-an-utterance-on-intent-page).

## <a name="rename-intent"></a>Renomear intenção

1. Na página **Intenção**, clique no ícone de Renomear em ![Renomear intenção](./media/luis-how-to-add-intents/Rename-Intent-btn.png) ao lado do nome da intenção. 

2. Na página **Intenção**, o nome da intenção atual é mostrado na caixa de diálogo. Edite o nome da intenção e pressione Enter. O novo nome é salvo e exibido na página de intenções.

    ![Editar intenção](./media/luis-how-to-add-intents/EditIntent-dialogbox.png)

## <a name="delete-intent"></a>Excluir intenção
Ao excluir uma intenção diferente de None, é possível adicionar todas as declarações à intenção None. Isso será útil se você precisar mover as declarações em vez de excluí-las.   

1. Na página **Intenção**, clique no botão **Excluir Intenção** à direita do nome da intenção. 

    ![Botão Excluir Intenção](./media/luis-how-to-add-intents/DeleteIntent.png)

2. Clique no botão "OK" na caixa de diálogo de confirmação.

<!--
    TBD: waiting for confirmation about which delete dialog is going to be in //BUILD

    ![Delete Intent Dialog](./media/luis-how-to-add-intents/DeleteIntent-Confirmation.png)
-->


## <a name="add-an-utterance-on-intent-page"></a>Adicionar uma declaração na página de intenções

Na página de intenções, digite uma declaração relevante esperada dos usuários, como `book 2 adult business tickets to Paris tomorrow on Air France` na caixa de texto abaixo do nome da intenção e pressione Enter. 
 
>[!NOTE]
>O LUIS converte todas as declarações para letras minúsculas.

![Captura de tela da página Detalhes de intenções, com o enunciado realçado](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

Declarações são adicionadas à lista de declarações para a intenção atual. Depois que uma declaração é adicionada, [rotule as entidades](luis-how-to-add-example-utterances.md) dentro das declarações e [treine](luis-how-to-train.md) seu aplicativo. 

## <a name="create-a-pattern-from-an-utterance"></a>Criar um padrão de uma declaração
Veja [Adicionar padrão de declaração existente na página de intenção ou entidade](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="edit-an-utterance-on-intent-page"></a>Editar uma declaração na página de intenções

Para editar um enunciado, selecione o botão de reticências (***...***) na extremidade direita da linha para essa expressão e, em seguida, selecione **Editar**. Modifique o texto e pressione Enter no teclado.

![Captura de tela da página de detalhes de Intenções, com o botão de reticências realçado](./media/luis-how-to-add-intents/edit-utterance.png) 

## <a name="reassign-utterances-on-intent-page"></a>Reatribuir declarações na página de intenções
Você pode alterar a intenção de uma ou mais declarações reatribuindo-as para outra intenção. 

Para reatribuir uma única declaração para uma intenção diferente, na extremidade direita da linha da declaração, selecione o nome da intenção correta na coluna **Intenção rotulada**. A declaração é removida da lista de declarações da intenção atual. 

![Captura de tela da página de intenções BookFlight com a intenção de uma declaração na coluna de intenções Rotulada selecionada](./media/luis-how-to-add-intents/reassign-1-utterance.png)

Para alterar a intenção de várias declarações, marque as caixas de seleção à esquerda das declarações e, em seguida, selecione **Reatribuir intenção**. Selecione a intenção correta na lista.

![Captura de tela da página de intenções BookFlight com uma declaração marcada e o botão de intenções Reatribuir realçado](./media/luis-how-to-add-intents/delete-several-utterances.png) 

## <a name="delete-utterances-on-intent-page"></a>Excluir declarações na página de intenções

Para excluir um enunciado, selecione o botão de reticências (***...***) na extremidade direita da linha para essa expressão e, em seguida, selecione **Excluir**. A declaração é removida da lista e do aplicativo LUIS.

![Captura de tela da página de detalhes de intenções, com a opção Excluir realçada](./media/luis-how-to-add-intents/delete-utterance-ddl.png)

Para excluir várias declarações:

1. Marque as caixas de seleção à esquerda das declarações e selecione **Excluir declarações**. 

    ![Captura de tela da página de detalhes de intenções com as declarações marcadas e o botão Excluir declarações realçado](./media/luis-how-to-add-intents/delete-several-utterances.png)

2. Selecione **Concluído** na caixa de diálogo **Excluir declarações?**.

## <a name="search-in-utterances-on-intent-page"></a>Pesquisar em declarações na página de intenções
Você pode procurar enunciados que contêm texto (palavras ou frases) na lista de enunciados de intenção. Por exemplo, você pode perceber um erro que envolve uma palavra específica e deseja localizar todos os exemplos que incluem essa palavra específica. 

1. Selecione o ícone de lupa na barra de ferramentas.

    ![Captura de tela da página de intenções, com um ícone de pesquisa de lupa realçado](./media/luis-how-to-add-intents/magnifying-glass.png)

2. Será exibida uma caixa de texto de pesquisa. Digite a palavra ou frase na caixa de pesquisa no canto superior direito da lista de declarações. As declarações listam as atualizações, para exibirem somente as que incluem o texto da pesquisa. 

    ![Captura de tela da página de intenções, com a caixa de texto de pesquisa realçada](./media/luis-how-to-add-intents/search-textbox.png)

    Para cancelar a pesquisa e restaurar a lista completa de declarações, exclua o texto de pesquisa que você digitou. Para fechar a caixa de texto de pesquisa, selecione o ícone de lupa na barra de ferramentas novamente.

## <a name="prediction-discrepancy-errors-on-intent-page"></a>Erros de discrepância de previsão na página de intenções
Uma declaração em uma intenção pode ter uma discrepância entre a intenção selecionada e a pontuação de previsão. O LUIS indica essa discrepância com uma caixa vermelha ao redor da pontuação. 

![Captura de tela da página de intenções BookFlight, com a pontuação de discrepância de previsão realçada](./media/luis-how-to-add-intents/score-discrepancy.png) 

## <a name="filter-by-intent-prediction-discrepancy-errors-on-intent-page"></a>Filtrar por erros de discrepância de previsão de intenções na página de intenções
Para filtrar a lista de declarações para apenas declarações com uma discrepância de previsão de intenções, alterne de **Mostrar tudo** para **Somente erros** na barra de ferramentas. 

## <a name="filter-by-entity-type-on-intent-page"></a>Filtrar por tipo de entidade na página de intenções
Use o menu suspenso **Filtros de entidade** na barra de ferramentas para filtrar as declarações por entidade. 

![Captura de tela da página de intenções, com o filtro de tipo de entidade realçado](./media/luis-how-to-add-intents/filter-by-entities.png) 

Para remover o filtro, marque a caixa de filtro azul com essa palavra ou frase na barra de ferramentas.  
<!-- TBD: waiting for ux fix - bug in ux of prebuit entity number -- when filtering by it, it doesn't show the list -->

## <a name="switch-to-token-view-on-intent-page"></a>Alterne para o modo de token na página de intenções
Ativar/desativar **Exibição de Tokens** para exibir os tokens em vez dos nomes do tipo de entidade. No teclado, você também pode usar **Control+E** para alternar o modo de exibição. 

![Captura de tela da intenção BookFlight com a Exibição de Tokens realçada](./media/luis-how-to-add-intents/toggle-tokens-view.png)

## <a name="train-your-app-after-changing-model-with-intents"></a>Treinar seu aplicativo após alterar o modelo com intenções
Depois de adicionar, editar ou remover intenções, [treine](luis-how-to-train.md) e [publique](luis-how-to-publish-app.md) seu aplicativo para que as suas alterações afetem as consultas de ponto de extremidade. 

## <a name="next-steps"></a>Próximas etapas

Depois de adicionar intenções ao seu aplicativo, a próxima tarefa é começar a adicionar [declarações de exemplo](luis-how-to-add-example-utterances.md) para as intenções adicionadas. 
