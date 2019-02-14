---
title: Criar, publicar e responder no QnA Maker
titleSuffix: Azure Cognitive Services
description: Este tutorial baseado no portal guiará você pela criação e publicação programáticas de uma base de dados de conhecimento e pela resposta a uma pergunta com a base de dados de conhecimento.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.technology: qna-maker
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: diberry
ms.openlocfilehash: b78c19d4fa6b2e5b69e5d7f46cea51d943b808fa
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882141"
---
# <a name="tutorial-create-a-knowledge-base-then-answer-question-via-the-qna-maker-portal"></a>Tutorial: Criar uma base de dados de conhecimento e responder perguntas usando o portal do QnA Maker

Este tutorial orienta você pela criação e publicação de uma base de dados de conhecimento e pela resposta a uma pergunta com a base de dados de conhecimento.

Neste tutorial, você aprenderá como: 

> [!div class="checklist"]
* Criar uma base de dados de conhecimento no portal do QnA Maker
* Revisar, salvar e treinar a base de dados de conhecimento
* Publicar a base de dados de conhecimento
* Usar cURL para consultar a base de dados de conhecimento

> [!NOTE] 
> A versão programática deste tutorial está disponível com uma solução completa [ **Azure-Samples/cognitive-services-qnamaker-csharp** no repositório do GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md) existente. 

## <a name="create-a-knowledge-base"></a>Como criar uma base de dados de conhecimento 

1. Entre no portal do [QnA Maker](https://www.qnamaker.ai). 

1. Selecione **Criar uma base de dados de conhecimento** no menu superior.

    ![Etapa 1 do processo de criação da base de dados de conhecimento](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. Ignore a primeira etapa, pois você usará o serviço QnA Maker existente. 

1. Na próxima etapa, selecione as configurações existentes:  

    |Configuração|Finalidade|
    |--|--|
    |ID do Diretório do Microsoft Azure|Sua _Id do Diretório do Microsoft Azure_ está associada à conta usada para entrar no portal do Azure e no portal do QnA Maker. |
    |Nome da assinatura do Azure|Sua conta de cobrança em que o recurso QnA Maker foi criado.|
    |Serviço QnA do Azure|O recurso existente do QnA Maker.|

    ![Etapa 2 do processo de criação da base de dados de conhecimento](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. Na próxima etapa, insira o nome da base de dados de conhecimento, `My Tutorial kb`.

    ![Etapa 3 do processo de criação da base de dados de conhecimento](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. Na próxima etapa, preencha a base de dados de conhecimento com as seguintes configurações:  

    |Nome da configuração|Valor da configuração|Finalidade|
    |--|--|--|
    |URL|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |O conteúdo das Perguntas frequentes nessa URL é formatado com um ponto de interrogação seguido de uma resposta. O QnA Maker pode interpretar esse formato para extrair perguntas e as respostas associadas.|
    |Arquivo |_não usado neste tutorial_|Isso carrega arquivos para perguntas e respostas. |
    |Personalidade do bate-papo|O amigo|Isso oferece uma personalidade casual e amigável para perguntas e respostas comuns. Você pode editar essas perguntas e respostas mais tarde. |

    ![Etapa 4 do processo de criação da base de dados de conhecimento](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. Selecione **Criar sua KB** para concluir o processo de criação.

    ![Etapa 5 do processo de criação da base de dados de conhecimento](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-kb-save-and-train"></a>Revisar a base de dados de conhecimento, salvar e treinar

1. Examine as perguntas e respostas. A primeira página é de perguntas e respostas da URL. 

    ![Salvar e treinar](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. Selecione a última página de perguntas e respostas na parte inferior da tabela. A página mostra as perguntas e respostas da Personalidade do bate-papo. 

1. Na barra de ferramentas acima da lista de perguntas e respostas, selecione o ícone de metadados. Isso mostra as marcas de metadados para cada pergunta e resposta. As perguntas de bate-papo têm os metadados **editorial: bate-papo** já definidos. Esses metadados são retornados para o aplicativo cliente com a resposta selecionada. O aplicativo cliente, como um chatbot, pode usar esses metadados filtrados para determinar outros processamentos ou interações com o usuário.

    ![Exibir marcas de Metadados](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. Selecione **Salvar e treinar** na barra de menus superior.

## <a name="publish-to-get-kb-endpoints"></a>Publicar para obter pontos de extremidade da base de dados de conhecimento

Selecione o botão **Publicar** no menu superior. Quando você estiver na página de publicação, selecione **Publicar**, ao lado do botão **Cancelar**.

![Publicar](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

Depois que a base de dados de conhecimento é publicada, o ponto de extremidade é exibido

![Publicar configurações de ponto de extremidade da página](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

## <a name="use-curl-to-query-for-an-faq-answer"></a>Usar cURL para consultar uma resposta de Perguntas frequentes

1. Selecione a guia **cURL**. 

    ![Comando de cURL](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. Copie o texto da guia **cURL** e execute em um terminal ou linha de comando habilitado para cURL. O valor do cabeçalho de autorização inclui o texto `Endpoint ` com um espaço à direita e, em seguida, a chave.

1. Substitua `<Your question>` por `How large can my KB be?`. Isso parece a pergunta `How large a knowledge base can I create?`, mas não exatamente. O QnA Maker aplica processamento de linguagem natural para determinar se as duas perguntas são iguais.     

1. Execute o comando CURL e receba a resposta JSON, incluindo a pontuação e a resposta. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   581  100   543  100    38    418     29  0:00:01  0:00:01 --:--:--   447{
      "answers": [
        {
          "questions": [
            "How large a knowledge base can I create?"
          ],
          "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
          "score": 42.81,
          "id": 2,
          "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
          "metadata": []
        }
      ]
    }
    
    ```

    O QnA Maker tem alguma certeza, com pontuação de 42,81%.  

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Usar cURL para consultar uma resposta de bate-papo

1. No terminal habilitado para cURL, substitua `How large can my KB be?` por uma instrução de fim de conversa do bot com o usuário, como `Thank you`.   

1. Execute o comando CURL e receba a resposta JSON, incluindo a pontuação e a resposta. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   525  100   501  100    24    525     25 --:--:-- --:--:-- --:--:--   550{
      "answers": [
        {
          "questions": [
            "Thank you",
            "Thanks",
            "Thnx",
            "Kthx",
            "I appreciate it",
            "Thank you so much",
            "I thank you",
            "My sincere thank"
          ],
          "answer": "You're very welcome.",
          "score": 100.0,
          "id": 109,
          "source": "qna_chitchat_the_friend.tsv",
          "metadata": [
            {
              "name": "editorial",
              "value": "chitchat"
            }
          ]
        }
      ]
    }
   
    ```

    Como a pergunta `Thank you` correspondeu exatamente a uma pergunta do bate-papo, o QnA Maker está completamente seguro com a pontuação de 100. O QnA Maker também retornou todas as perguntas relacionadas e a propriedade de metadados que contém as informações de marca de metadados de Bate-papo.  

## <a name="use-curl-to-query-for-the-default-answer"></a>Usar cURL para consultar a resposta padrão

Todas as perguntas que não tenham resposta com grau de confiança do QnA Maker recebem a resposta padrão. Esta resposta é configurada no portal do Azure. 

1. No terminal habilitado para cURL, substitua `Thank you` por `x`. 

1. Execute o comando CURL e receba a resposta JSON, incluindo a pontuação e a resposta. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   186  100   170  100    16    272     25 --:--:-- --:--:-- --:--:--   297{
      "answers": [
        {
          "questions": [],
          "answer": "No good match found in KB.",
          "score": 0.0,
          "id": -1,
          "metadata": []
        }
      ]
    }
    ```
    
    O QnA Maker retornou uma pontuação igual a 0, o que significa nenhuma confiança, mas também retornou a resposta padrão. 

## <a name="next-steps"></a>Próximas etapas

Consulte as [Fontes de dados com suporte](../Concepts/data-sources-supported.md) para obter mais informações sobre os formatos de arquivo com suporte. 

Saiba mais sobre [personalidades](../Concepts/best-practices.md#chit-chat) de bate-papo.

Para obter mais informações sobre a resposta padrão, consulte [Nenhuma correspondência encontrada](../Concepts/confidence-score.md#no-match-found). 

> [!div class="nextstepaction"]
> [Conceitos da base de dados de conhecimento](../Concepts/knowledge-base.md)