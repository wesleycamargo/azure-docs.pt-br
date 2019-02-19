---
title: Análise de sentimento usando a Análise de Texto dos Serviços Cognitivos do Azure | Microsoft Docs
description: Saiba como detectar sentimento usando a API REST de Análise de Texto.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: add284a3a001d5bc2e756f9ad4a2a3b9550b212c
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56242117"
---
# <a name="example-how-to-detect-sentiment-with-text-analytics"></a>Exemplo: Como detectar o sentimento com Análise de Texto

A [API de análise de sentimento](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) avalia a entrada de texto e retorna uma pontuação de sensibilidade para cada documento, variando de 0 (negativo) a 1 (positivo).

Esse recurso é útil para detectar o sentimento positivo e negativo em fóruns de discussão, mídia social e revisões de cliente. O conteúdo é fornecido por você, modelos e dados de treinamento são fornecidos pelo serviço.

Atualmente, a análise de sentimento suporta inglês, alemão, espanhol e francês. Os idiomas estão em versão prévia. Para obter mais informações, consulte [Linguagens com suporte](../text-analytics-supported-languages.md).

> [!TIP]
> A Análise de Texto também fornece um Docker baseados em Linux imagem de contêiner para análise de sentimentos, para que você possa [instalar e executar o contêiner de análise de texto](text-analytics-how-to-install-containers.md) perto de seus dados.

## <a name="concepts"></a>Conceitos

Análise de texto usa um algoritmo de classificação de aprendizado de máquina para gerar uma pontuação de sensibilidade entre 0 e 1. As pontuações mais próximas de 1 indicam sentimento positivo, enquanto as classificações mais próximas de 0 indicam sentimento negativo.” O modelo é classificação com um amplo corpo de texto com associações de sensibilidade. Atualmente, não é possível fornecer seus próprios dados de treinamento. O modelo usa uma combinação de técnicas durante a análise de texto, incluindo o processamento de texto, análise de parte de fala, posicionamento do word e associações do word. Para obter mais informações sobre o algoritmo, consulte [Apresentando Análise de Texto do Azure Machine Learning](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

A análise de sentimento é executada em todo o documento, em vez de extrair o sentimento para uma entidade específica no texto. Na prática, há uma tendência de precisão de pontuação para melhorar quando documentos contêm uma ou duas frases em vez de um grande bloco de texto. Durante a fase de avaliação objetividade, o modelo determina se um documento como um todo é objetivo ou contém sentimento. Um documento que é principalmente objetivo não faz progresso para a frase de detecção de sentimento, resultando em uma pontuação.50, com nenhum processamento adicional. Para continuar no pipeline de documentos, a próxima fase gera uma pontuação acima ou abaixo de.50, dependendo do grau de sensibilidade detectado no documento.

## <a name="preparation"></a>Preparação

Análise de sentimento produz um resultado de qualidade superior quando você concede partes menores de texto para trabalhar. Este é o oposto da extração de frase-chave que executa melhor em grandes blocos de texto. Para obter os melhores resultados de ambas as operações, considere a reestruturação de entradas adequadamente.

Você deve ter documentos JSON neste formato: id, texto, idioma

O tamanho do documento deve ter menos de 5.000 caracteres por documento e você pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo da solicitação. Este é um exemplo de conteúdo que você pode enviar para a detecção de sentimento.

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

+ Criar uma solicitação de **Postagem**. Examine a documentação da API para esta solicitação: [API de Análise de Sentimento](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9)

+ Defina o ponto de extremidade HTTP para a análise de sentimentos, usando um recurso de análise de texto no Azure ou um instanciado [contêiner de Análise de Texto](text-analytics-how-to-install-containers.md). Deve incluir o recurso `/sentiment`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment`

+ Defina um cabeçalho de solicitação para incluir a chave de acesso para operações de Análises de Texto do Azure Machine Learning. Para obter mais informações, consulte [Como localizar pontos de extremidade e chaves de acesso](text-analytics-how-to-access-key.md).

+ No corpo da solicitação, forneça a coleção de documentos JSON preparada para esta análise

> [!Tip]
> Use o [Postman](text-analytics-how-to-call-api.md) ou abra o **console de teste da API** na [documentação](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) para estruturar uma solicitação e POSTAR no serviço.

## <a name="step-2-post-the-request"></a>Etapa 2: Postar a solicitação

A análise é executada após o recebimento da solicitação. O serviço aceita até 100 solicitações por minuto. Cada solicitação pode ter um máximo de 1 MB.

Lembre-se de que o serviço é sem estado. Nenhum dado é armazenado em sua conta. Os resultados são retornados imediatamente na resposta.


## <a name="step-3-view-results"></a>Etapa 3: Exibir resultados

O analisador de sentimento classifica texto como predominantemente positivo ou negativo, atribuindo uma pontuação no intervalo de 0 a 1. Valores próximos 0,5 são neutros ou indeterminados. Uma pontuação de 0,5 indica neutralidade. Quando uma cadeia de caracteres não pode ser analisada para sentimento ou não tem nenhum sentimento, a pontuação é sempre 0,5 exatamente. Por exemplo, se você passar uma cadeia de caracteres espanhol com um código de idioma inglês, a pontuação é 0,5.

A saída é retornada imediatamente. Você pode transmitir os resultados para um aplicativo que aceita JSON ou salvar a saída em um arquivo no sistema local e, em seguida, importá-lo para um aplicativo que permite que você classifique, pesquise e manipule os dados.

O exemplo a seguir mostra a resposta para o conjunto de documentos neste artigo.

```
{
    "documents": [
        {
            "score": 0.9999237060546875,
            "id": "1"
        },
        {
            "score": 0.0000540316104888916,
            "id": "2"
        },
        {
            "score": 0.99990355968475342,
            "id": "3"
        },
        {
            "score": 0.980544924736023,
            "id": "4"
        },
        {
            "score": 0.99996328353881836,
            "id": "5"
        }
    ],
    "errors": []
}
```

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu os conceitos e fluxo de trabalho para detecção de idioma usando a análise de texto em Serviços Cognitivos. Em resumo:

+ [API de análise de sentimento](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) está disponível para os idiomas selecionados.
+ Documentos JSON no corpo da solicitação incluem um código de idioma, texto e id.
+ A solicitação POST é um `/sentiment` ponto de extremidade, usando uma [chave de acesso personalizada e um ponto de extremidade](text-analytics-how-to-access-key.md) que é válido para sua assinatura.
+ Saída de resposta, que pontuação de sentimento para cada ID do documento, pode ser transmitida para qualquer aplicativo que aceita JSON, incluindo o Excel e Power BI, para citar alguns.

## <a name="see-also"></a>Consulte também 

 [Visão geral da Análise de Texto](../overview.md)  
 [Perguntas frequentes (FAQ)](../text-analytics-resource-faq.md)</br>
 [Página de produto de Análise de Texto do Azure Machine Learning](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Extraia frases-chave](text-analytics-how-to-keyword-extraction.md)
