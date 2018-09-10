---
title: Como usar a vinculação de entidade na API REST de Análise de Texto (Serviços Cognitivos da Microsoft no Azure) | Microsoft Docs
description: Saiba como identificar e resolver entidades usando a API REST de Análise de Texto nos Serviços Cognitivos da Microsoft no Azure neste tutorial passo a passo.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 5/02/2018
ms.author: ashmaka
ms.openlocfilehash: 55bec1a0223b70749a97a30e2da92ef15128038c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363795"
---
# <a name="how-to-identify-linked-entities-in-text-analytics-preview"></a>Como identificar entidades vinculadas na Análise de Texto (Versão prévia)

A [API de Vinculação de Entidade](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) usa texto não estruturado e, para cada documento JSON, retorna uma lista de entidades com ambiguidade removida com links para mais informações na Web (Wikipédia e Bing). 

## <a name="entity-linking-vs-named-entity-recognition"></a>Vinculação de Identidade versus Reconhecimento de Entidade Nomeada

No processamento de linguagem natural, os conceitos de vinculação de entidade e reconhecimento de entidade nomeada (NER) podem ser facilmente confundidos. Na versão prévia do ponto de extremidade `entities` da Análise de Texto, somente a vinculação de entidade tem suporte.

Vinculação de entidade é a capacidade de identificar e resolver a ambiguidade da identidade de uma entidade encontrada no texto (por exemplo, determinar se o "Marte" está sendo usado como o planeta ou como o Deus romano da guerra). Esse processo exige a presença de uma base de conhecimento a qual as entidades reconhecidas são vinculadas. A Wikipédia é usada como a base de conhecimento para o ponto de extremidade `entities` da Análise de Texto.

### <a name="language-support"></a>Suporte ao idioma

O uso da vinculação de entidade em vários idiomas exige o uso de uma base de conhecimento correspondente em cada idioma. Para vinculação de entidade na Análise de Texto, isso significa que cada idioma com suporte do ponto de extremidade `entities` será vinculado ao corpus da Wikipédia correspondente nesse idioma. Como o tamanho do corpora varia entre as linguagens, espera-se que a funcionalidade de vinculação de entidade também variará.


## <a name="preparation"></a>Preparação

Você deve ter documentos JSON neste formato: id, texto, idioma

Para conferir os idiomas com suporte no momento, consulte [esta lista](../text-analytics-supported-languages.md).

O tamanho do documento deve ter menos de 5.000 caracteres por documento e você pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo da solicitação. O exemplo a seguir é uma ilustração do conteúdo que você pode enviar para a extremidade de vinculação de entidade.

```
{"documents": [{"id": "1",
                "language": "en",
                "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."
                },
               {"id": "2",
                "language": "en",
                "text": "The Seattle Seahawks won the Super Bowl in 2014."
                }
               ]
}
```    
    
## <a name="step-1-structure-the-request"></a>Etapa 1: Estruturar a solicitação

Detalhes sobre a definição de solicitação podem ser encontrados em [Como chamar a API de Análise de Texto](text-analytics-how-to-call-api.md). Os seguintes pontos são redeclarados para conveniência:

+ Criar uma solicitação de **Postagem**. Examine a documentação da API para esta solicitação: [API de Vinculação de Entidade](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)

+ Defina o ponto de extremidade HTTP para a extração da frase-chave. Deve incluir o recurso `/entities`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/entities`

+ Defina um cabeçalho de solicitação para incluir a chave de acesso para operações de Análise de Texto. Para obter mais informações, consulte [Como localizar pontos de extremidade e chaves de acesso](text-analytics-how-to-access-key.md).

+ No corpo da solicitação, forneça a coleção de documentos JSON preparada para esta análise

> [!Tip]
> Use o [Postman](text-analytics-how-to-call-api.md) ou abra o **console de teste da API** na [documentação](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) para estruturar uma solicitação e POSTAR no serviço.

## <a name="step-2-post-the-request"></a>Etapa 2: postar a solicitação

A análise é executada após o recebimento da solicitação. O serviço aceita até 100 solicitações por minuto. Cada solicitação pode ter um máximo de 1 MB.

Lembre-se de que o serviço é sem estado. Nenhum dado é armazenado em sua conta. Os resultados são retornados imediatamente na resposta.

## <a name="step-3-view-results"></a>Etapa 3: exibir resultados

Todas as solicitações POST retornam uma resposta formatada em JSON com as IDs e as propriedades detectadas.

A saída é retornada imediatamente. Você pode transmitir os resultados para um aplicativo que aceita JSON ou salvar a saída em um arquivo no sistema local e, em seguida, importá-lo para um aplicativo que permite que você classifique, pesquise e manipule os dados.

Um exemplo de saída da vinculação de entidade é mostrado a seguir:

```
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "name": "Xbox One",
                    "matches": [
                        {
                            "text": "XBox One",
                            "offset": 23,
                            "length": 8
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Xbox One",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Xbox_One",
                    "bingId": "446bb4df-4999-4243-84c0-74e0f6c60e75"
                },
                {
                    "name": "Ultra-high-definition television",
                    "matches": [
                        {
                            "text": "4K",
                            "offset": 63,
                            "length": 2
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Ultra-high-definition television",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Ultra-high-definition_television",
                    "bingId": "7ee02026-b6ec-878b-f4de-f0bc7b0ab8c4"
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "name": "2013 Seattle Seahawks season",
                    "matches": [
                        {
                            "text": "Seattle Seahawks",
                            "offset": 4,
                            "length": 16
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "2013 Seattle Seahawks season",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/2013_Seattle_Seahawks_season",
                    "bingId": "eb637865-4722-4eca-be9e-0ac0c376d361"
                }
            ]
        }
    ],
    "errors": []
}
```

Quando disponível, a resposta inclui a ID da Wikipédia, URL da Wikipédia e ID do Bing para cada entidade detectada. Elas podem ser usadas para melhorar ainda mais seu aplicativo com informações sobre a entidade vinculada.


## <a name="summary"></a>Resumo

Neste artigo, você aprendeu os conceitos e fluxo de trabalho para detecção de idioma usando a Análise de Texto em Serviços Cognitivos. Em resumo:

+ [API de Vinculação de Entidade](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) está disponível para os idiomas selecionados.
+ Documentos JSON no corpo da solicitação incluem um código de idioma, texto e id.
+ A solicitação POST é um `/entities` ponto de extremidade, usando uma [chave de acesso personalizada e um ponto de extremidade](text-analytics-how-to-access-key.md) que é válido para sua assinatura.
+ A saída da resposta, composta por entidades vinculadas (incluindo pontuações de confiança, deslocamentos e links da Web, para cada ID de documento) pode ser usada em qualquer aplicativo

## <a name="see-also"></a>Consulte também 

 [Visão geral da Análise de Texto](../overview.md)  
 [Perguntas frequentes (FAQ)](../text-analytics-resource-faq.md)</br>
 [Página de produto de análise de texto](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [API de Análise de Texto](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
