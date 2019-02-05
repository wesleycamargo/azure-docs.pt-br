---
title: Como usar a extração de frase-chave na API REST de Análise de Texto (Serviços Cognitivos da Microsoft no Azure) | Microsoft Docs
description: Como extrair as frases chave usando a API REST de Análise de Texto nos Serviços Cognitivos da Microsoft no Azure neste tutorial passo a passo.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 09/12/2018
ms.author: heidist
ms.openlocfilehash: bbca745da1fe657c1316d9e4e5fbeeeabfa5e1ef
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216737"
---
# <a name="example-how-to-extract-key-phrases-in-text-analytics"></a>Exemplo: Como extrair frases-chave em Análise de Texto

A [API de Extração de Frases-chave](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) avalia o texto desestruturado e para cada documento JSON, retorna uma lista de frases-chave. 

Esse recurso é útil se você precisar identificar rapidamente os principais pontos de estratégias em uma coleção de documentos. Por exemplo, para o texto de entrada dado "A comida estava deliciosa e a equipe era maravilhosa", o serviço retorna os principais pontos de discussão: "comida" e "equipe maravilhosa".

Atualmente, a Extração de Frases-chave dá tem suporte em inglês, alemão, espanhol e japonês. Os idiomas estão em versão prévia. Para obter mais informações, consulte [Linguagens com suporte](../text-analytics-supported-languages.md).

> [!TIP]
> A Análise de Texto também fornece uma imagem de contêiner do Docker baseado em Linux para extração de frases-chave, para que você possa [instalar e executar o contêiner de Análise de Texto](text-analytics-how-to-install-containers.md) perto de seus dados.

## <a name="preparation"></a>Preparação

A extração de frase-chave funciona melhor quando você concede maiores partes de texto para trabalhar. Isso é o oposto da análise de sentimento, que executa melhor em blocos menores de texto. Para obter os melhores resultados de ambas as operações, considere a reestruturação de entradas adequadamente.

Você deve ter documentos JSON neste formato: id, texto, idioma

O tamanho do documento deve ter menos de 5.000 caracteres por documento e você pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo da solicitação. O exemplo a seguir é uma ilustração do conteúdo que você pode enviar para extração de frase-chave.

```
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },                
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```    
    
## <a name="step-1-structure-the-request"></a>Etapa 1: Estruturar a solicitação

Detalhes sobre a definição de solicitação podem ser encontrados em [Como chamar a API de Análise de Texto](text-analytics-how-to-call-api.md). Os seguintes pontos são redeclarados para conveniência:

+ Criar uma solicitação de **Postagem**. Examine a documentação da API para esta solicitação: [API de Frases-Chave](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)

+ Defina o ponto de extremidade HTTP para extração de frases-chave, usando um recurso de análise de texto no Azure ou um instanciado [contêiner de Análise de Texto](text-analytics-how-to-install-containers.md). Deve incluir o recurso `/keyPhrases`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases`

+ Defina um cabeçalho de solicitação para incluir a chave de acesso para operações de Análises de Texto do Azure Machine Learning. Para obter mais informações, consulte [Como localizar pontos de extremidade e chaves de acesso](text-analytics-how-to-access-key.md).

+ No corpo da solicitação, forneça a coleção de documentos JSON preparada para esta análise

> [!Tip]
> Use o [Postman](text-analytics-how-to-call-api.md) ou abra o **console de teste da API** na [documentação](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) para estruturar uma solicitação e POSTAR no serviço.

## <a name="step-2-post-the-request"></a>Etapa 2: Postar a solicitação

A análise é executada após o recebimento da solicitação. O serviço aceita até 100 solicitações por minuto. Cada solicitação pode ter um máximo de 1 MB.

Lembre-se de que o serviço é sem estado. Nenhum dado é armazenado em sua conta. Os resultados são retornados imediatamente na resposta.

## <a name="step-3-view-results"></a>Etapa 3: Exibir resultados

Todas as solicitações POST retornam uma resposta formatada em JSON com as IDs e as propriedades detectadas.

A saída é retornada imediatamente. Você pode transmitir os resultados para um aplicativo que aceita JSON ou salvar a saída em um arquivo no sistema local e, em seguida, importá-lo para um aplicativo que permite que você classifique, pesquise e manipule os dados.

Um exemplo de saída para extração de frase-chave é mostrado a seguir:

```
    "documents": [
        {
            "keyPhrases": [
                "year",
                "trail",
                "trip",
                "views"
            ],
            "id": "1"
        },
        {
            "keyPhrases": [
                "marked trails",
                "Worst hike",
                "goners"
            ],
            "id": "2"
        },
        {
            "keyPhrases": [
                "trail",
                "small children",
                "family"
            ],
            "id": "3"
        },
        {
            "keyPhrases": [
                "spectacular views",
                "trail",
                "area"
            ],
            "id": "4"
        },
        {
            "keyPhrases": [
                "places",
                "beautiful views",
                "favorite trail"
            ],
            "id": "5"
        }
```

Conforme observado, o analisador localiza e descarta as palavras não essenciais e mantém termos ou frases únicas para serem o assunto ou o objeto de uma sentença. 

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu os conceitos e fluxo de trabalho de extração de frase-chave usando a análise de texto em Serviços Cognitivos. Em resumo:

+ [API de extração de frase-chave](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) está disponível para os idiomas selecionados.
+ Documentos JSON no corpo da solicitação incluem um código de idioma, texto e id.
+ Solicitação POST é um `/keyphrases` ponto de extremidade, usando uma [chave de acesso personalizada e um ponto de extremidade](text-analytics-how-to-access-key.md) que é válido para sua assinatura.
+ Saída de resposta, que consiste em palavras e frases chave para cada ID do documento, pode ser transmitida para qualquer aplicativo que aceita JSON, incluindo o Excel e Power BI, para citar alguns.

## <a name="see-also"></a>Consulte também 

 [Visão geral da Análise de Texto](../overview.md)  
 [Perguntas frequentes (FAQ)](../text-analytics-resource-faq.md)</br>
 [Página de produto de Análise de Texto do Azure Machine Learning](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [API de Análise de Texto do Azure Machine Learning](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
