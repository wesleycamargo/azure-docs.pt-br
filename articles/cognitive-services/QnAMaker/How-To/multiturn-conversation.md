---
title: Ativar várias conversas
titleSuffix: Azure Cognitive Services
description: Usar prompts e contexto para gerenciar o vários ativa, conhecido como várias por sua vez, para o bot de uma pergunta para outro. Vários por sua vez é a capacidade de ter um back e para trás em que o contexto da pergunta anterior influencia a próxima pergunta e resposta de conversa.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: d94b527f1ad84d2b34a1708fd31eed273f8c363a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074454"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Prompts de acompanhamento de uso para criar várias sequências de uma conversa

Usar prompts de acompanhamento e o contexto para gerenciar o vários ativa, conhecido como _ativar várias_, para o bot de uma pergunta para outro.

Aprenda com o [demonstração em vídeo](https://aka.ms/multiturnexample).

## <a name="what-is-a-multi-turn-conversation"></a>O que é uma conversa com vários por sua vez?

Alguns tipos de conversa não podem ser concluídos em uma única rodada. Quando você projeta suas conversas de (bot de bate-papo) do aplicativo cliente, um usuário pode fazer uma pergunta que precisa ser filtrada ou refinados para determinar a resposta correta. Esse fluxo por meio de perguntas é possível, apresentando o usuário com **acompanhamento solicita**.

Quando o usuário faz a pergunta, o QnA Maker retorna a resposta _e_ quaisquer prompts de acompanhamento. Isso permite que você apresente as perguntas de acompanhamento como opções. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Conversa de turno vários de exemplo com o bot de bate-papo

Um chat bot gerencia a conversa, pergunta a pergunta, com o usuário para determinar a resposta final.

![Dentro do fluxo de conversação, gerencie o estado da conversa em um sistema de caixa de diálogo de ativar várias fornecendo avisos dentro das respostas apresentados como opções para continuar a conversa.](../media/conversational-context/conversation-in-bot.png)

Na imagem anterior, a pergunta do usuário precisa ser refinado antes de retornar a resposta. Na base de dados de Conhecimento, a pergunta (n º 1), tem quatro prompts de acompanhamento, apresentadas no bot bate-papo como quatro opções (2). 

Quando o usuário seleciona uma opção (3), a lista próxima de refinar escolhas (4) é apresentada. Isso pode continuar (5) até que a resposta correta e final (6) é determinada.

Você precisará alterar seu aplicativo cliente para gerenciar a conversa contextual.

## <a name="create-a-multi-turn-conversation-from-documents-structure"></a>Criar uma conversa com vários por sua vez de estrutura do documento
Quando você cria uma base de Conhecimento, você verá uma caixa de seleção opcional para habilitar a ativar a extração. Se você selecionar essa opção, quando você importa um documento, a conversa com vários por sua vez pode implícita da estrutura. Se existir nessa estrutura, o QnA Maker cria os pares de QnA prompt acompanhamento para você. Estrutura de folheio de vários só pode ser inferida de DOCX, PDF ou URLs de arquivos. 

A imagem a seguir de um Microsoft Surface [arquivo PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf) destina-se a ser usado como um manual. 

![! [Se você importar um documento, contextual conversa pode ser implícita da estrutura. Se essa estrutura, QnA Maker criará os pares de QnA prompt acompanhamento para você, como parte da importação do documento.] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

Ao importar o documento PDF, QnA Maker determina prompts de acompanhamento da estrutura para criar o fluxo de conversação. 

![! [Ao importar o documento PDF, QnA Maker determina prompts de acompanhamento da estrutura para criar o fluxo de conversação. ](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

## <a name="filter-questions-and-answers-by-context"></a>Perguntas e respostas de filtro pelo contexto

1. Reduza os pares de perguntas e respostas exibidos apenas com conversas contextuais. Selecione **as opções de exibição**, em seguida, selecione **Mostrar contexto (visualização)**. A lista estará vazia até que você adicione o primeiro par de pergunta e resposta com um prompt de acompanhamento. 

    ![Filtrar pergunta e resposta pares por conversas contextuais](../media/conversational-context/filter-question-and-answers-by-context.png)

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>Adicionar novo par de QnA como prompt de acompanhamento

1. Selecione **QnA adicionar par**. 
1. Insira o novo texto de pergunta `Give feedback.` com uma resposta de `What kind of feedback do you have?`.

1. No **resposta** coluna para essa pergunta, selecione **prompt de acompanhamento de adicionar**. 
1. O **prompt de acompanhamento** caixa de diálogo pop-up permite que você procure uma pergunta existente ou insira uma nova pergunta. Neste procedimento, digite o texto `Feedback on an QnA Maker service`, para o **exibir texto**. 
1. Verifique **somente contexto**. O **somente contexto** opção indica que esse texto de usuário será compreendido _somente_ se fornecido em resposta à pergunta anterior. Para este cenário, o texto de aviso não faz sentido como uma pergunta autônomo, ele só faz sentido no contexto do pergunta anterior.
1. No **Link para responder** texto, digite a resposta, `How would you rate QnA Maker?`.
1. Selecione **criar novo** , em seguida, selecione **salvar**. 

    ![Criar novo QnA prompt](../media/conversational-context/create-child-prompt-from-parent.png)

    Isso criou um novo par de perguntas e respostas e vinculados a pergunta selecionada como um prompt de acompanhamento. O **contexto** coluna, ambas as perguntas, indicar uma relação de prompt de acompanhamento. 

    ![! [A coluna de contexto, ambas as perguntas, indica uma relação de prompt de acompanhamento.] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Selecione **prompt de acompanhamento de Add** para o `Give feedback` pergunta para adicionar outro prompt de acompanhamento. 
1. Crie uma nova pergunta, inserindo `Feedback on an existing feature`, com a resposta `Which feature would you like to give feedback on?`.  

1.  Verifique **somente contexto**. O **somente contexto** opção indica que esse texto de usuário será compreendido _somente_ se fornecido em resposta à pergunta anterior. Para este cenário, o texto de aviso não faz sentido como uma pergunta autônomo, ele só faz sentido no contexto do pergunta anterior.
1.  Clique em **Salvar**. 

    Isso criou uma nova pergunta e vinculadas a pergunta como uma pergunta de prompt de acompanhamento para o `Give feedback` pergunta.
    
    Neste ponto, a pergunta superior tem dois prompts de acompanhamento gostou à pergunta anterior, `Give feedback`.

    ![! [Neste ponto, a pergunta superior tem dois prompts de acompanhamento gostou à pergunta anterior, 'Enviar comentários']. (.. / media/conversational-context/all-child-prompts-created.png)](../media/conversational-context/all-child-prompts-created.png#lightbox)

1. Selecione **salvar e treinar** para treinar a base de dados de conhecimento com novas perguntas. 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>Adicionar o par de QnA existente como o prompt de acompanhamento

1. Se você deseja vincular um par de QnA existente como um prompt de acompanhamento, selecione a linha para o par de pergunta e resposta.
1. Selecione **prompt de acompanhamento de adicionar** nessa linha.
1. Na caixa de diálogo pop-up, insira o texto da pergunta na caixa de pesquisa. Todas as correspondências são retornadas. Selecione a pergunta que você deseja como o acompanhamento e verificar **somente contexto**, em seguida, selecione **salvar**. 

    Depois que a unidade organizacional adicionou o prompt de acompanhamento, lembre-se de selecionar **salvar e treinar**.
  
## <a name="add-metadata-to-follow-up-prompts"></a>Adicionar metadados aos prompts de acompanhamento 

Na base de dados de Conhecimento, quando um par de perguntas e respostas que esteja vinculado à solicita de acompanhamento, os filtros de metadados são aplicados primeiro e o acompanhamento é retornado.

1. Para os dois pares de QnA acompanhamento, adicione metadados para cada um:

    |Pergunta|Adicionar metadados|
    |--|--|
    |`Feedback on an QnA Maker service`|"Recurso": "all"|
    |`Feedback on an existing feature`|"Feature":"one"|
    
    ![Adicionar metadados ao prompt de acompanhamento para que ele pode ser filtrado na resposta de conversa do serviço](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Salve e treinar. 

    Quando você envia a pergunta `Give feedback` com o filtro de metadados `Feature` com um valor de `all`, apenas o par de QnA com que os metadados será retornado. Os dois pares de QnA não são retornados porque ambos não correspondem ao filtro. 

## <a name="test-the-qna-set-to-get-all-the-follow-up-prompts"></a>Solicita que o QnA definida para obter todos os o acompanhamento de teste

Quando o teste a pergunta com acompanhamento solicitar que no **teste** painel, a resposta inclui os prompts de acompanhamento.

![Ao testar a pergunta no painel de teste, a resposta inclui os prompts de acompanhamento.](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>Solicitação JSON para retornar a resposta inicial e as solicitações de acompanhamento

Use a esvaziar `context` objeto para solicitar a resposta à pergunta do usuário e incluir prompts de acompanhamento. 

```JSON
{
  "question": "accounts and signing in",
  "top": 30,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="json-response-to-return-initial-answer-and-follow-up-prompts"></a>Resposta JSON para retornar a resposta inicial e as solicitações de acompanhamento

A seção anterior solicitou uma resposta e os prompts de acompanhamento para `Accounts and signing in`. A resposta inclui as informações de aviso, localizadas em `answers[0].context`, incluir o texto a ser exibido ao usuário. 

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 86.96,
            "id": 37,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 38,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 39,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 40,
                        "qna": null,
                        "displayText": "Sign out"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 32.27,
            "id": 38,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
            "score": 27.0,
            "id": 40,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ],
    "debugInfo": null
}
```

O `prompts` matriz fornece o texto em de `displayText` propriedade e o `qnaId` valor, portanto, você pode mostrar essas respostas como as opções exibidas Avançar no fluxo de conversa. 

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>Solicitação JSON para retornar a resposta não inicial e as solicitações de acompanhamento

Preencher o `context` objeto para incluir o contexto anterior.

Na solicitação JSON a seguir, a pergunta atual é `Use Windows Hello to sign in` e a pergunta anterior era `Accounts and signing in`. 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 30,
  "userId": "Default",
  "isTest": false,
  "qnaId": 39,
  "context": {
    "previousQnAId": 37,
    "previousUserQuery": "accounts and signing in"
  }
}
``` 

##  <a name="json-response-to-return-non-initial-answer-and-follow-up-prompts"></a>Resposta JSON para retornar a resposta não inicial e as solicitações de acompanhamento

O QnA Maker _GenerateAnswer_ resposta JSON inclui os prompts de acompanhamento na `context` propriedade do primeiro item no `answers` objeto:

```JSON
{
    "answers": [
        {
            "questions": [
                "Give feedback"
            ],
            "answer": "What kind of feedback do you have?",
            "score": 100.0,
            "id": 288,
            "source": "Editorial",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 291,
                        "qna": null,
                        "displayText": "Feedback on an QnA Maker service"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 292,
                        "qna": null,
                        "displayText": "Feedback on an existing feature"
                    }
                ]
            }
        }
    ]
}
```

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>Exibindo prompts e enviar o contexto no aplicativo cliente 

O aplicativo cliente exibe todas as perguntas com uma opção para o usuário exibir os avisos como botões ou ações sugeridas.
O aplicativo cliente, em seguida, armazena a consulta atual de par e o usuário do QnA como contexto a ser passado com a próxima consulta de usuário. 

Use esta [exemplo de estrutura do Bot](https://aka.ms/qnamakermultiturnsample) para ver a caixa de diálogo de ativar várias trabalho-ponta em um bot do QnA Maker.


## <a name="prompt-order-supported-in-api"></a>Ordem de prompt com suporte na API

A ordem de prompt, retornado na resposta JSON, há suporte para edição por apenas a API. 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre conversas contextuais do [exemplo de caixa de diálogo](https://aka.ms/qnamakermultiturnsample) ou aprender mais [bot conceitual de design para ativar várias conversas](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Migrar uma base de Conhecimento](../Tutorials/migrate-knowledge-base.md)
