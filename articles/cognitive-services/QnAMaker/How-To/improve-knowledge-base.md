---
title: Melhorar a base de dados de conhecimento – QnA Maker
titleSuffix: Azure Cognitive Services
description: ''
author: diberry
manager: cgronlun
displayName: active learning, suggestion, dialog prompt, train api, feedback loop, autolearn, auto-learn, user setting, service setting, services setting
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/29/2019
ms.author: diberry
ms.openlocfilehash: 639b665926f54387dfdc6e837c15c8d6d28df925
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755757"
---
# <a name="use-active-learning-to-improve-knowledge-base"></a>Usar o aprendizado ativo para melhorar a base de conhecimento

O aprendizado ativo permite melhorar a qualidade da sua base de dados de Conhecimento, sugerindo perguntas alternativas, com base em envios de usuário, para o par de pergunta e resposta. Você examina essas sugestões, adicionando-as às perguntas existentes ou rejeitando-as. 

Sua base de dados de conhecimento não é alterada automaticamente. Você precisa aceitar as sugestões para que qualquer alteração entre em vigor. Essas sugestões adicionam perguntas, mas não alteram nem removem perguntas existentes.

## <a name="active-learning"></a>Aprendizado ativo

O QnA Maker aprende novas variações de pergunta com comentários implícitos e explícitos.
 
* Comentários implícitos – o classificador compreende quando uma pergunta do usuário tem várias respostas com pontuações muito parecidas e considera isso como comentários. 
* Comentários explícitos – quando várias respostas com pouca variação nas pontuações são retornadas da base de conhecimento, o aplicativo cliente solicita ao usuário qual pergunta é a pergunta correta. Os comentários explícitos do usuário são enviados para o QnA Maker com a API de treinamento. 

Qualquer um dos métodos fornece ao classificador consultas semelhantes são clusterizadas.

Quando consultas semelhantes são clusterizadas, o QnA Maker sugere as perguntas baseadas no usuário ao designer de base de dados de conhecimento que, por sua vez, deve aceitá-las ou rejeitá-las.

## <a name="how-active-learning-works"></a>Como o aprendizado ativo funciona

O aprendizado ativo é disparado com base nas pontuações das primeiras melhores respostas retornadas pelos QnA Maker para qualquer consulta. Se as diferenças de pontuação residem dentro de um pequeno intervalo, a consulta é considerada uma possível _sugestão_ para cada uma das possíveis respostas. 

Todas as sugestões são clusterizadas por similaridade e as principais sugestões de perguntas alternativas são exibidas com base na frequência das consultas específicas por usuários finais. O aprendizado ativo fornece as melhores sugestões possíveis em casos em que os pontos de extremidade estão obtendo uma quantidade e variedade razoáveis de consultas de uso.

## <a name="upgrade-version-to-use-active-learning"></a>Atualizar a versão para usar o aprendizado ativo

O aprendizado ativo é compatível com a versão de tempo de execução 4.4.0 e superior. Se sua base de dados de conhecimento foi criada em uma versão anterior, [atualize seu tempo de execução](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates) para usar esse recurso. 

## <a name="best-practices"></a>Práticas recomendadas

Para as práticas recomendadas ao usar o aprendizado ativo, veja [Práticas recomendadas](../Concepts/best-practices.md#active-learning).

## <a name="score-proximity-between-knowledge-base-questions"></a>Proximidade da pontuação entre as perguntas da base de dados de conhecimento

Quando a pontuação de uma pergunta tem um alto grau de confiabilidade, tal como 80%, o intervalo de pontuações que são consideradas para aprendizado ativo é amplo, dentro de aproximadamente 10%. Conforme a pontuação de confiança diminui, por exemplo, para 40%, o intervalo de pontuações diminui também, para dentro de aproximadamente 4%. 

O algoritmo para determinar a proximidade não é um cálculo simples. Os intervalos nos exemplos anteriores não devem ser corrigidos, mas devem ser usados como um guia para entender o impacto apenas do algoritmo.

## <a name="turn-on-active-learning"></a>Ativar o aprendizado ativo

O aprendizado ativo está desativado por padrão. Ative-o para ver sugestões de perguntas. 

1. Para desativar o aprendizado ativo, clique em seu **Nome** e vá para [**Configurações de Serviço**](https://www.qnamaker.ai/UserSettings) no portal do QnA Maker no canto superior direito.  

    ![Na página de configurações de serviço, ativar o aprendizado ativo](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Localize o serviço QnA Maker e, em seguida, ative o **aprendizado ativo**. 

    [![Na página de configurações de serviço, ativar o aprendizado ativo](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    Uma vez que o **aprendizado ativo** for habilitado, o conhecimento sugere novas perguntas em intervalos regulares, com base em perguntas enviadas por usuários. Você pode desabilitar o **aprendizado ativo** desativando a configuração novamente.

## <a name="add-active-learning-suggestion-to-knowledge-base"></a>Adicionar a sugestão de aprendizado ativo à base de dados de conhecimento

1. Para ver as sugestões de perguntas, na página **Editar** da base de dados de conhecimento, selecione **Mostrar sugestões**. 

    [![Na página de configurações de serviço, ativar/desativar o botão Mostrar Sugestões](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtrar a base de dados de conhecimento com os pares de pergunta e resposta para mostrar apenas sugestões, selecionando **Filtrar por Sugestões**.

    [![Na página de configurações do serviço, filtrar por sugestões para ver apenas esses pares de pergunta/resposta](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1.  Cada seção de pergunta com sugestões mostra as novas perguntas com uma marca de seleção, `✔`, para aceitar a pergunta, ou então um `x` para rejeitar as sugestões. Selecione a marca de seleção para adicionar a pergunta. 

    [![Na página de configurações de serviço, ativar o aprendizado ativo](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Você pode adicionar ou excluir _todas as sugestões_ selecionando **Adicionar todas** ou **Rejeitar todas**.

1. Selecione **Salvar e Treinar** para salvar as alterações à base de dados de conhecimento.


## <a name="determine-best-choice-when-several-questions-have-similar-scores"></a>Determinar a melhor escolha quando várias perguntas têm pontuações semelhantes

Quando a pontuação de uma pergunta for muito próxima à de outras perguntas, o desenvolvedor do aplicativo cliente poderá escolher solicitar esclarecimentos.

### <a name="use-the-top-property-in-the-generateanswer-request"></a>Usar a propriedade superior na solicitação GenerateAnswer

Ao enviar uma pergunta para o QnA Maker a fim de obter uma resposta, parte do corpo JSON permite retornar mais de uma das primeiras respostas:

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

Quando o aplicativo cliente (por exemplo, um chat bot) recebe a resposta, as três primeiras perguntas são retornadas:

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

### <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Acompanhamento do aplicativo cliente quando perguntas têm pontuações semelhantes

O aplicativo cliente exibe todas as perguntas com uma opção para o usuário selecionar a pergunta que melhor representa suas intenções. 

Após o usuário selecionar uma das perguntas existentes. Os comentários do usuário são enviados para a API [Train](http://www.aka.ms/activelearningsamplebot) do QnA Maker continuar o loop de comentários de aprendizado ativo. 

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

Saiba mais sobre como usar o aprendizado ativo com um [exemplo em C# de bot do Azure](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-activelearning-bot)

## <a name="next-steps"></a>Próximas etapas
 
> [!div class="nextstepaction"]
> [Usar a API de QnA Maker](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
