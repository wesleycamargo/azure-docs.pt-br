---
title: Como usar a detecção de idioma na API REST de Análise de Texto (Serviços Cognitivos da Microsoft no Azure) | Microsoft Docs
description: Como detectar o idioma usando a API REST de Análise de Texto nos Serviços Cognitivos da Microsoft no Azure neste tutorial passo a passo.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: sample
ms.date: 09/12/2018
ms.author: heidist
ms.openlocfilehash: 460dfb168894d28d5fbc5e5585a6054917127931
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633557"
---
# <a name="example-how-to-detect-language-in-text-analytics"></a>Exemplo: como detectar idiomas na Análise de Texto

A [API de Detecção de Idioma](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) avalia o texto de entrada e para cada documento e retorna os identificadores de idioma com uma pontuação que indica a intensidade da análise. A Análise de Texto reconhece até 120 idiomas.

Esse recurso é útil para conteúdo armazena esse texto arbitrário de coleção, onde o idioma é desconhecido. Você pode analisar os resultados dessa análise para determinar qual idioma é usado no documento de entrada. A resposta também retorna uma pontuação que reflete a confiança do modelo (um valor entre 0 e 1).

> [!TIP]
> A Análise de Texto também fornece um Docker baseado em imagem de contêiner do Linux para detecção de idioma, para que você possa [instalar e executar o contêiner de análise de texto](text-analytics-how-to-install-containers.md) perto de seus dados.

## <a name="preparation"></a>Preparação

Você deve ter documentos JSON neste formato: id, texto

O tamanho do documento deve ter menos de 5.000 caracteres por documento e você pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo da solicitação. Este é um exemplo de conteúdo que você pode enviar para a detecção de idioma.

   ```
    {
        "documents": [
            {
                "id": "1",
                "text": "This document is in English."
            },
            {
                "id": "2",
                "text": "Este documento está en inglés."
            },
            {
                "id": "3",
                "text": "Ce document est en anglais."
            },
            {
                "id": "4",
                "text": "本文件为英文"
            },                
            {
                "id": "5",
                "text": "Этот документ находится на английском языке."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Etapa 1: Estruturar a solicitação

Detalhes sobre a definição de solicitação podem ser encontrados em [Como chamar a API de Análise de Texto](text-analytics-how-to-call-api.md). Os seguintes pontos são redeclarados para conveniência:

+ Criar uma solicitação de **Postagem**. Examine a documentação da API para esta solicitação: [API de Detecção de Idioma](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)

+ Defina o ponto de extremidade HTTP para a detecção de idioma usando um recurso de análise de texto no Azure ou um [contêiner de Análise de Texto](text-analytics-how-to-install-containers.md) instanciado. Deve incluir o recurso `/languages`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages`

+ Defina um cabeçalho de solicitação para incluir a chave de acesso para operações de Análises de Texto do Azure Machine Learning. Para obter mais informações, consulte [Como localizar pontos de extremidade e chaves de acesso](text-analytics-how-to-access-key.md).

+ No corpo da solicitação, forneça a coleção de documentos JSON preparada para esta análise

> [!Tip]
> Use o [Postman](text-analytics-how-to-call-api.md) ou abra o **console de teste da API** na [documentação](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) para estruturar uma solicitação e POSTAR no serviço.

## <a name="step-2-post-the-request"></a>Etapa 2: postar a solicitação

A análise é executada após o recebimento da solicitação. O serviço aceita até 100 solicitações por minuto. Cada solicitação pode ter um máximo de 1 MB.

Lembre-se de que o serviço é sem estado. Nenhum dado é armazenado em sua conta. Os resultados são retornados imediatamente na resposta.


## <a name="step-3-view-results"></a>Etapa 3: exibir resultados

Todas as solicitações POST retornam uma resposta formatada em JSON com as IDs e as propriedades detectadas.

A saída é retornada imediatamente. Você pode transmitir os resultados para um aplicativo que aceita JSON ou salvar a saída em um arquivo no sistema local e, em seguida, importá-lo para um aplicativo que permite que você classifique, pesquise e manipule os dados.

Resultados para o exemplo de solicitação devem parecer com o JSON a seguir. Observe que se trata de um documento com vários itens. A saída é em inglês. Identificadores de idioma incluem um nome amigável e um código de idioma no formato [ISO 639-1](https://www.iso.org/standard/22109.html).

Uma pontuação positiva de 1.0 expressa o nível mais alto de confiança possível da análise.



```
{
    "documents": [
        {
            "id": "1",
            "detectedLanguages": [
                {
                    "name": "English",
                    "iso6391Name": "en",
                    "score": 1
                }
            ]
        },
        {
            "id": "2",
            "detectedLanguages": [
                {
                    "name": "Spanish",
                    "iso6391Name": "es",
                    "score": 1
                }
            ]
        },
        {
            "id": "3",
            "detectedLanguages": [
                {
                    "name": "French",
                    "iso6391Name": "fr",
                    "score": 1
                }
            ]
        },
        {
            "id": "4",
            "detectedLanguages": [
                {
                    "name": "Chinese_Simplified",
                    "iso6391Name": "zh_chs",
                    "score": 1
                }
            ]
        },
        {
            "id": "5",
            "detectedLanguages": [
                {
                    "name": "Russian",
                    "iso6391Name": "ru",
                    "score": 1
                }
            ]
        }
    ],
```

### <a name="ambiguous-content"></a>Conteúdo ambíguo

Se o analisador puder analisar a entrada (por exemplo, suponha que você enviou um bloco de texto que consiste exclusivamente algarismos arábicos), ele retornará `(Unknown)`.

```
    {
      "id": "5",
      "detectedLanguages": [
        {
          "name": "(Unknown)",
          "iso6391Name": "(Unknown)",
          "score": "NaN"
        }
      ]
```
### <a name="mixed-language-content"></a>Conteúdo de idioma misto

O conteúdo de idioma misto dentro do mesmo documento retorna o idioma com a representação maior no conteúdo, mas com uma classificação positiva inferior, refletindo a intensidade marginal dessa avaliação. No exemplo a seguir, a entrada é uma combinação de francês, espanhol e inglês. O analisador de contagens de caracteres em cada segmento determina o idioma predominante.

**Input**

```
{
  "documents": [
    {
      "id": "1",
      "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
    }
  ]
}
```

**Saída**

A saída resultante consiste no idioma predominante, com uma pontuação de menor que 1.0, que indica um nível mais fraco de confiança.

```
{
  "documents": [
    {
      "id": "1",
      "detectedLanguages": [
        {
          "name": "Spanish",
          "iso6391Name": "es",
          "score": 0.9375
        }
      ]
    }
  ],
  "errors": []
}
```

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu os conceitos e fluxo de trabalho para detecção de idioma usando a análise de texto em Serviços Cognitivos. A seguir está um lembrete rápido dos recursos explicados e demonstrados anteriormente:

+ [A API de detecção de idioma](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) está disponível para 120 idiomas.
+ Documentos JSON no corpo da solicitação incluem uma id e texto.
+ A solicitação POST é um `/languages` ponto de extremidade, usando uma [chave de acesso personalizada e um ponto de extremidade](text-analytics-how-to-access-key.md) que é válido para sua assinatura.
+ Saída de resposta, que consiste de identificadores de idioma para cada ID do documento, pode ser transmitida para qualquer aplicativo que aceita JSON, incluindo o Excel e Power BI, para citar alguns.

## <a name="see-also"></a>Consulte também 

 [Visão geral da Análise de Texto](../overview.md)  
 [Perguntas frequentes (FAQ)](../text-analytics-resource-faq.md)</br>
 [Página de produto de Análise de Texto do Azure Machine Learning](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Analisar sentimento](text-analytics-how-to-sentiment-analysis.md)
