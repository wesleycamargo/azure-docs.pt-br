---
title: Como editar uma base de dados de conhecimento - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Como editar uma base de dados de conhecimento
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: eaa65bf3d257399fceadaa42f0d9ddbbf8afe234
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364255"
---
# <a name="edit-a-knowledge-base"></a>Editar uma base de dados de conhecimento

O QnA Maker permite que você gerencie o conteúdo de sua base de dados de conhecimento, fornecendo uma experiência de edição fácil de usar.

## <a name="edit-your-knowledge-base-content"></a>Editar o conteúdo da base de conhecimento

1.  Selecione **Minhas bases de dados de conhecimento** na barra de navegação superior. 

    Você pode ver todos os serviços que você criou ou compartilhou classificados em ordem decrescente da data da **última modificação**.

    ![Minhas Bases de Dados de Conhecimento](../media/qnamaker-how-to-edit-kb/my-kbs.png)

2. Selecione uma base de dados de conhecimento específica para fazer edições nela.

3. Quando você terminar de fazer alterações à base de dados de conhecimento, clique em **Salvar e treinar** no canto superior direito da página para manter as alterações.    

    ![Salvar e Treinar](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!NOTE]
    Deixe a página antes de clicar em Salvar e treinar não persistirá as alterações.

## <a name="add-a-qna-pair"></a>Adicionar um par de QnA

Selecione **Adicionar par QnA** para adicionar uma nova linha à tabela de base de dados de conhecimento.

![Adicionar par QnA](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Excluir um par de QnA

Para excluir um QnA, clique no ícone **excluir** à direita da linha QnA.

![Excluir par de QnA](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Adicionar perguntas alternativas

Adicione perguntas alternativas para um par de QnA existente para melhorar a probabilidade de uma correspondência para uma consulta de usuário.

![Adicionar Perguntas Alternativas](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>Adicionar metadados


Adicionar pares de metadados, selecionando o ícone de filtro

![Adicionar Metadados](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Salve e treine periodicamente a base de dados de conhecimento depois de fazer edições para evitar a perda de alterações.

## <a name="manage-large-knowledge-bases"></a>Gerenciar grandes bases de conhecimento

1. Os QnAs são **agrupados** pela fonte de dados da qual eles foram extraídos. Você pode expandir ou recolher a fonte de dados.
2. Você pode **pesquisar** a base de dados de conhecimento digitando na caixa de texto na parte superior da tabela da Base de Dados de Conhecimento. Clique em Enter para pesquisar o conteúdo de pergunta, resposta ou metadados. Clique no ícone X para remover o filtro de pesquisa.
3. **Paginação** permite que você gerencie grandes bases de dados de conhecimento

    ![Pesquisar, Paginar, Agrupar](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Colaborar em uma base de dados de conhecimento](./collaborate-knowledge-base.md)
