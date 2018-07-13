---
title: Como testar uma base de dados de conhecimento - QnA Maker - Serviços Cognitivos do Azure | Microsoft Docs
description: Teste a base de dados de conhecimento antes de publicá-la.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: cffb63666edab25e1b3b0739d0e0f2f828600f3a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364256"
---
# <a name="test-your-knowledge-base"></a>Testar a base de dados de conhecimento

Testar a base de conhecimento do QnA Maker é uma parte importante de um processo iterativo para melhorar a precisão das respostas que estão sendo retornadas. É possível testar a base de dados de conhecimento por meio de uma interface de chat avançada que também permite fazer edições.

## <a name="test-answer-matching"></a>Testar correspondência de resposta

1.  Acesse a base de dados de conhecimento, selecionando o nome na página **Minhas bases de dados de conhecimento**.
2.  Para acessar o painel deslizante de Teste, selecione **Testar** no painel superior do aplicativo.

    ![Teste de acesso](../media/qnamaker-how-to-test-kb/access-test.png)

3.  Insira uma consulta na caixa de texto e selecione Enter.

4.  A resposta da melhor correspondência da base de dados de conhecimento é retornada como a resposta.

## <a name="clear-test-panel"></a>Limpar painel de teste

Para limpar todas as consultas de teste inseridas e seus resultados do console de teste, selecione **Iniciar de novo** no canto superior esquerdo do Painel de teste.

## <a name="close-test-panel"></a>Fechar painel de teste

Para fechar o painel de **Teste**, selecione o botão Testar novamente. Enquanto o painel de Teste estiver aberto, não será possível editar o conteúdo da Base de Dados de Conhecimento.

## <a name="inspect-score"></a>Inspecionar pontuação

Inspecione detalhes do resultado do teste no painel Inspecionar.

1.  Com o painel deslizante de Teste aberto, selecione **Inspecionar** para obter mais detalhes sobre essa resposta.

    ![Inspecione as respostas](../media/qnamaker-how-to-test-kb/inspect.png)

2.  O painel de Inspeção é exibido. O painel inclui a principal intenção de pontuação e as entidades identificadas. O painel mostra o resultado da declaração selecionada.

## <a name="correct-the-top-scoring-answer"></a>Corrigir a resposta de pontuação máxima

Se a resposta de pontuação mais alta estiver incorreta, selecione a resposta correta na lista e selecione **Salvar e Treinar**.

![Teste de acesso](../media/qnamaker-how-to-test-kb/choose-answer.png)

## <a name="add-alternate-questions"></a>Adicionar perguntas alternativas

É possível adicionar formas alternativas de uma pergunta a uma determinada resposta. Digite as respostas alternativas na caixa de texto e clique em entrar para adicioná-las. Selecione **Salvar e Treinar** para armazenar as atualizações.

![Teste de acesso](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

## <a name="add-a-new-answer"></a>Adicionar uma nova resposta

Você poderá adicionar uma nova resposta, se alguma das respostas existentes que foram correspondidas estiver incorreta ou a resposta não existir na base de dados de conhecimento (nenhuma boa correspondência encontrada na base de dados de conhecimento). Insira a nova resposta à pergunta atual na caixa de texto e pressione Enter para adicioná-la. 

Selecione **Salvar e Treinar** para persistir essa resposta. Um novo par de perguntas e respostas foi adicionado à base de dados de conhecimento.

![Teste de acesso](../media/qnamaker-how-to-test-kb/add-answer.png)

> [!NOTE]
> Todas as edições da base de dados de conhecimento somente serão salvas quando você pressionar o botão **Salvar e Treinar**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Publicar uma base de dados de conhecimento](./publish-knowledge-base.md)
