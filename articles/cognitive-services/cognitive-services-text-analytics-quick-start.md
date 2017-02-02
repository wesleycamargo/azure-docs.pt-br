---
title: "Guia de início rápido: APIs de Análise de Texto do Machine Learning | Microsoft Docs"
description: "Análise de Texto do Aprendizado de Máquina do Azure - Guia de Início Rápido"
services: cognitive-services
documentationcenter: 
author: onewth
manager: jhubbard
editor: cgronlun
ms.assetid: e8b9e98c-40e7-4425-ae16-d1eaa7d2f837
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: onewth
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 663b99c2491bebf49f950605152604b040ade070


---
# <a name="getting-started-with-the-text-analytics-apis-to-detect-sentiment-key-phrases-topics-and-language"></a>Introdução às APIs de Análise de Texto para detectar o sentimento, senhas, tópicos e idioma
<a name="HOLTop"></a>

Este documento descreve como integrar seu serviço ou aplicativo para usar as [APIs de Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759711).
Você pode usar essas APIs para detectar o sentimento, senhas, tópicos e idioma de seu texto. [Clique aqui para ver uma demonstração interativa da experiência.](//go.microsoft.com/fwlink/?LinkID=759712)

Confira as [definições da API](//go.microsoft.com/fwlink/?LinkID=759346) para obter a documentação técnica das APIs.

Este guia destina-se à versão 2 das APIs. Para obter detalhes sobre a versão 1 das APIs, [confira este documento](../machine-learning/machine-learning-apps-text-analytics.md).

No final deste tutorial, você será capaz de detectar de forma programática:

* **Sentimento** - o texto é positivo ou negativo?
* **Frases-chave** - o que as pessoas estão discutindo em um único artigo?
* **Tópicos** - o que as pessoas estão discutindo em muitos artigos?
* **Idiomas** - em que idioma o texto está escrito?

Observe que essa API cobra uma transação por documento enviado. Por exemplo, se você solicitar o sentimento de 1000 documentos em uma única chamada, ocorrerá a dedução de 1000 transações.

<a name="Overview"></a>

## <a name="general-overview"></a>Visão geral
Este documento é um guia passo a passo. Nosso objetivo é apresentar as etapas necessárias para treinar um modelo e indicar recursos que lhe permitirão colocá-lo em produção. Esse exercício levará cerca de 30 minutos.

Para essas tarefas, você necessita de um editor e precisa chamar os pontos de extremidade RESTful na linguagem de sua escolha.

Vamos começar!

## <a name="task-1---signing-up-for-the-text-analytics-apis"></a>Tarefa 1 - inscrever-se para as APIs de Análise de Texto
Nessa tarefa, você se inscreverá para obter o serviço de análise de texto.

1. Navegue até **Serviços Cognitivos** no [Portal do Azure](//go.microsoft.com/fwlink/?LinkId=761108) e verifique se a **Análise de Texto** está selecionada como o ‘tipo de API’.
2. Escolha um plano. Você pode escolher a **camada gratuita para 5.000 transações/mês**. Por ser um plano gratuito, você não será cobrado pelo uso do serviço. Você precisará fazer logon em sua assinatura do Azure. 
3. Preencha os outros campos e crie sua conta.
4. Após inscrever-se para obter a Análise de Texto, localize a **Chave de API**. Copie a chave primária, pois você precisará dela ao usar os serviços de API.

## <a name="task-2---detect-sentiment-key-phrases-and-languages"></a>Tarefa 2 - detectar sentimento, frases-chave e idiomas
É fácil detectar o sentimento, as frases-chave e os idiomas no texto. Você obterá de forma programática os mesmos resultados que a [experiência de demonstração](//go.microsoft.com/fwlink/?LinkID=759712) retornar.

> [!TIP]
> Para a análise do sentimento, recomendamos que você divida o texto em sentenças. Isso geralmente leva a uma maior precisão em previsões de sentimento.
> 
> 

Observe que os idiomas com suporte são os seguintes:

| Recurso | Códigos de idioma com suporte |
|:--- |:--- |
| Sentimento |`en` (Inglês), `es` (Espanhol), `fr` (Francês), `pt` (Português) |
| Frases principais |`en` (Inglês), `es` (Espanhol), `de` (Alemão), `ja` (Japonês) |

1. Você precisará definir os cabeçalhos da seguinte maneira. Observe que, atualmente, JSON é o único formato de entrada aceito para as APIs. Não há suporte para XML.
   
        Ocp-Apim-Subscription-Key: <your API key>
        Content-Type: application/json
        Accept: application/json
2. Em seguida, formate as linhas de entrada em JSON. O formato é o mesmo para sentimento, frases-chave e idioma. Observe que cada ID deve ser exclusiva e será a ID retornada pelo sistema. O tamanho máximo de um único documento que pode ser enviado é 10 KB, e o tamanho máximo total da entrada enviada é de 1 MB. Podem ser enviados no máximo 1.000 documentos em uma chamada. A limitação da taxa existe em uma taxa de 100 chamadas por minuto, portanto, recomendamos que você envie grandes quantidades de documentos em uma única chamada. O idioma é um parâmetro opcional que deve ser especificado se estiver analisando um texto que não está em inglês. Veja abaixo um exemplo de entrada, onde o parâmetro opcional `language` para análise de sentimento ou a extração de expressão chave está incluído:
   
        {
            "documents": [
                {
                    "language": "en",
                    "id": "1",
                    "text": "First document"
                },
                ...
                {
                    "language": "en",
                    "id": "100",
                    "text": "Final document"
                }
            ]
        }
3. Faça uma chamada **POST** para o sistema para obter o sentimento, as frases-chave e o idioma de entrada. As URLs serão semelhantes ao seguinte:
   
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages
4. Essa chamada retornará uma resposta formatada em JSON com as IDs e as propriedades detectadas. Um exemplo da saída de sentimento é mostrado abaixo (com detalhes de erro excluídos). No caso do sentimento, será retornada uma pontuação entre 0 e 1 para cada documento:
   
        // Sentiment response
        {
              "documents": [
                {
                    "id": "1",
                    "score": "0.934"
                },
                ...
                {
                    "id": "100",
                    "score": "0.002"
                },
            ]
        }
   
        // Key phrases response
        {
              "documents": [
                {
                    "id": "1",
                    "keyPhrases": ["key phrase 1", ..., "key phrase n"]
                },
                ...
                {
                    "id": "100",
                    "keyPhrases": ["key phrase 1", ..., "key phrase n"]
                },
            ]
        }
   
        // Languages response
        {
              "documents": [
                {
                    "id": "1",
                    "detectedLanguages": [
                        {
                            "name": "English",
                            "iso6391Name": "en",
                            "score": "1"
                        }
                    ]
                },
                ...
                {
                    "id": "100",
                    "detectedLanguages": [
                        {
                            "name": "French",
                            "iso6391Name": "fr",
                            "score": "0.985"
                        }
                    ]
                }
            ]
        }

## <a name="task-3---detect-topics-in-a-corpus-of-text"></a>Tarefa 3 - detectar tópicos no corpo de texto
Essa é uma API recém-lançada que retorna os principais tópicos detectados para obter uma lista dos registros de texto enviados. Um tópico é identificado com uma senha, que pode ser uma ou mais palavras relacionadas. A API é projetada para funcionar bem com textos curto escritos por humanos, como revisões e comentários do usuário.

Essa API requer o envio de **,no mínimo, 100 registros de texto** , mas foi projetada para detectar os tópicos em centenas ou milhares de registros. Quaisquer registros que não estejam em inglês ou tenham menos de três palavras serão descartados e, assim, não serão atribuídos aos tópicos. Para a detecção dos tópicos, o tamanho máximo de um único documento que pode ser enviado é de 30 KB e o tamanho máximo total da entrada enviada é de 30 MB. A detecção do tópico tem uma taxa limitada a cinco envios a cada 5 minutos.

Há mais dois parâmetros de entrada **opcionais** que podem ajudar a melhorar a qualidade dos resultados:

* **Palavras irrelevantes.**   Essas palavras e suas formas aproximadas (por exemplo, plurais) serão excluídas do pipeline inteiro de detecção de tópicos. Use esse recurso para palavras comuns (por exemplo, "problema", "erro" e "usuário" podem ser opções apropriadas para reclamações do cliente sobre o software). Cada cadeia de caracteres deve ser uma única palavra.
* **Frases irrelevantes** - essas frases serão excluídas da lista de tópicos retornados. Use esse recurso para excluir os tópicos genéricos que você não deseja ver nos resultados. Por exemplo, "Microsoft" e "Azure" seriam opções adequadas para os tópicos a excluir. As cadeias de caracteres podem conter várias palavras.

Siga estas etapas para detectar os tópicos no texto.

1. Formate a entrada em JSON. Dessa vez, você pode definir palavras irrelevantes e frases irrelevantes.
   
        {
            "documents": [
                {
                    "id": "1",
                    "text": "First document"
                },
                ...
                {
                    "id": "100",
                    "text": "Final document"
                }
            ],
            "stopWords": [
                "issue", "error", "user"
            ],
            "stopPhrases": [
                "Microsoft", "Azure"
            ]
        }
2. Usando os mesmos cabeçalhos definidos na Tarefa 2, faça uma chamada **POST** para o ponto de extremidade de tópicos:
   
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/topics
3. Isso retornará um `operation-location` como o cabeçalho na resposta, no qual o valor é a URL para consultar os tópicos resultantes:
   
        'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'
4. Confira periodicamente o `operation-location` retornado com uma solicitação **GET** . O intervalo recomendado é uma vez por minuto.
   
        GET https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>
5. O ponto de extremidade retornará uma resposta, incluindo `{"status": "notstarted"}` antes do processamento, `{"status": "running"}` durante o processamento e `{"status": "succeeded"}` com a saída após a conclusão. Em seguida, você pode consumir a saída, que deverá estar no seguinte formato (observe que detalhes como formato de erro e datas foram excluídos deste exemplo):
   
        {
            "status": "succeeded",
            "operationProcessingResult": {
                  "topics": [
                    {
                        "id": "8b89dd7e-de2b-4a48-94c0-8e7844265196"
                        "score": "5"
                        "keyPhrase": "first topic name"
                    },
                    ...
                    {
                        "id": "359ed9cb-f793-4168-9cde-cd63d24e0d6d"
                        "score": "3"
                        "keyPhrase": "final topic name"
                    }
                ],
                  "topicAssignments": [
                    {
                        "topicId": "8b89dd7e-de2b-4a48-94c0-8e7844265196",
                        "documentId": "1",
                        "distance": "0.354"
                    },
                    ...
                    {
                        "topicId": "359ed9cb-f793-4168-9cde-cd63d24e0d6d",
                        "documentId": "55",
                        "distance": "0.758"
                    },            
                ]
            }
        }

Observe que a resposta bem-sucedida para os tópicos a partir do ponto de extremidade `operations` terá o esquema a seguir:

    {
            "topics" : [{
                "id" : "string",
                "score" : "number",
                "keyPhrase" : "string"
            }],
            "topicAssignments" : [{
                "documentId" : "string",
                "topicId" : "string",
                "distance" : "number"
            }],
            "errors" : [{
                "id" : "string",
                "message" : "string"
            }]
        }

Explicações de cada parte da resposta são as seguintes:

**tópicos**

| Chave | Descrição |
|:--- |:--- |
| ID |Um identificador exclusivo para cada tópico. |
| para seu app&#39;s |Contagem de documentos atribuídos ao tópico. |
| keyPhrase |Uma palavra ou frase que resume o tópico. |

**topicAssignments**

| Chave | Descrição |
|:--- |:--- |
| documentId |Identificador do documento. É igual à ID incluída na entrada. |
| topicId |A ID do tópico à qual o documento foi atribuído. |
| distância |Pontuação de afiliação do documento ao tópico entre 0 e 1. Quanto menor a pontuação de distância, mais forte é a afiliação do tópico. |

**erros**

| Chave | Descrição |
|:--- |:--- |
| ID |O identificador exclusivo do documento de entrada a que o erro se refere. |
| message |Mensagem de erro. |

## <a name="next-steps"></a>Próximas etapas
Parabéns! Você concluiu o uso da análise de texto em seus dados. Agora convém examinar o uso de uma ferramenta como o [Power BI](//powerbi.microsoft.com) para visualizar os dados e para automatizar as percepções para fornecer uma exibição em tempo real dos dados de texto.

Para ver como os recursos da Análise de Texto, como o sentimento, podem ser usados como parte de um bot, consulte o exemplo [Bot Emocional](http://docs.botframework.com/en-us/bot-intelligence/language/#example-emotional-bot) no site do Bot Framework.




<!--HONumber=Nov16_HO3-->


