---
title: 'Exemplo: chamar a API da Pesquisa Visual Computacional'
titlesuffix: Azure Cognitive Services
description: Saiba como chamar a API da Pesquisa Visual Computacional usando o REST nos Serviços Cognitivos do Azure.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: sample
ms.date: 01/20/2017
ms.author: kefre
ms.openlocfilehash: e8297fbe59ebe2dea9caf112ebea4517447cf9e0
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45981738"
---
# <a name="example-how-to-call-the-computer-vision-api"></a>Exemplo: como chamar a API da Pesquisa Visual Computacional

Este guia demonstra como chamar a API da Pesquisa Visual Computacional usando o REST. Os exemplos são escritos em C# usando a biblioteca de clientes da API da Pesquisa Visual Computacional e como chamadas HTTP POST/GET. Nosso foco será:

-   Como obter "Tags", "Descrição" e "Categorias".
-   Como obter informações "específicas a um domínio" (celebridades).

### <a name="Prerequisites">Pré-requisitos</a> 
URL da imagem ou do caminho para a imagem armazenada localmente.
  * Métodos de entrada compatíveis: binário da imagem bruta na forma de um aplicativo/fluxo de octeto ou uma URL da imagem
  * Formatos de imagem compatíveis: JPEG, PNG, GIF, BMP
  * Tamanho do arquivo de imagem: inferior a 4 MB
  * Dimensão da imagem: superior a 50 x 50 pixels
  
Nos exemplos abaixo, os seguintes recursos são demonstrados:

1. Análise de uma imagem e obtenção de uma matriz de tags e um descrição retornada.
2. Análise de uma imagem com um modelo específico a um domínio (especificamente, modelo de "celebridades") e obtenção do resultado correspondente no reajuste do JSON.

Os recursos são divididos em:

  * **Opção um:** análise com escopo – analisar apenas um modelo dado
  * **Opção dois:** análise avançada – analisar para fornecer mais detalhes com a [taxonomia de 86 categorias](../Category-Taxonomy.md)
  
### <a name="Step1">Etapa 1: autorizar a chamada à API</a> 
Toda chamada à API da Pesquisa Visual Computacional requer uma chave de assinatura. Essa chave precisa ser passada por um parâmetro de cadeia de caracteres de consulta ou especificada no cabeçalho da solicitação. 

Para obter uma chave de assinatura, consulte [Como obter chaves de assinatura](../Vision-API-How-to-Topics/HowToSubscribe.md
).

**1.** Passando a chave de assinatura por meio de uma cadeia de consulta, veja abaixo como um exemplo da API da Pesquisa Visual Computacional:

```https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>```

**2.** A passagem da chave de assinatura também pode ser especificada no cabeçalho da solicitação HTTP:

```ocp-apim-subscription-key: <Your subscription key>```

**3.** Ao usar uma biblioteca de clientes, a chave de assinatura é passada por meio do construtor de VisionServiceClient:

```var visionClient = new VisionServiceClient(“Your subscriptionKey”);```

### <a name="Step2">Etapa 2: Carregar uma imagem para o serviço API da Pesquisa Visual Computacional e retornar as tags, descrições e celebridades</a>
A maneira básica de realizar a chamada à API da Pesquisa Visual Computacional é carregar uma imagem diretamente. Isso é feito enviando uma solicitação "POST" com o tipo de conteúdo application/octet-stream junto com os dados lidos da imagem. Para "Tags" e "Descrição", este método de upload será o mesmo para todas as chamadas à API da Pesquisa Visual Computacional. A única diferença será os parâmetros de consulta especificados pelo usuário. 

Veja como obter "Tags" e "Descrição" para uma determinada imagem:

**Opção um:** obter a lista das "Tags" e uma "Descrição"
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```
```
using Microsoft.ProjectOxford.Vision;
using Microsoft.ProjectOxford.Vision.Contract;
using System.IO;

AnalysisResult analysisResult;
var features = new VisualFeature[] { VisualFeature.Tags, VisualFeature.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.AnalyzeImageAsync(fs, features);
}
```
**Opção dois:** obter apenas a lista de "Tags" ou apenas a lista de "Descrição":

###### <a name="tags-only"></a>Apenas tags:
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/tag&subscription-key=<Your subscription key>
var analysisResult = await visionClient.GetTagsAsync("http://contoso.com/example.jpg");
```

###### <a name="description-only"></a>Apenas descrição:
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/describe&subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.DescribeAsync(fs);
}
```
### <a name="here-is-how-to-get-domain-specific-analysis-in-our-case-for-celebrities"></a>Veja como obter a análise específica a um domínio (em seu caso, para celebridades).

**Opção um:** análise com escopo – analisar apenas um modelo dado
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```
Para essa opção, todos os outros parâmetros de consulta {visualFeatures, details} não são válidos. Se desejar ver todos os modelos com suporte, use: 
```
GET https://westus.api.cognitive.microsoft.com/vision/v2.0/models 
var models = await visionClient.ListModelsAsync();
```
**Opção dois:** análise avançada – analisar para fornecer mais detalhes com a [taxonomia de 86 categorias](../Category-Taxonomy.md)

Para aplicativos nos quais você deseja obter análise de imagem genérica, além de detalhes de um ou mais modelos específicos a um domínio, expandimos a API v1 com o parâmetro de consulta de modelos.
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?details=celebrities
```
Quando esse método é invocado, chamaremos o classificador de 86 categorias primeiro. Se qualquer uma das categorias corresponder àquelas de um modelo conhecido/correspondente, uma segunda passagem de invocações do classificador ocorrerá. Por exemplo, se "details=all" ou "details" incluir "celebridades", chamaremos o modelo de celebridades após o classificador de 86 categorias ser chamado, e o resultado incluirá a pessoa da categoria. Isso aumentará a latência dos usuários interessados em celebridades, em comparação com a Opção um.

Todos os parâmetros de consulta v1 se comportarão da mesma maneira nesse caso.  Se visualFeatures=categories não for especificado, será habilitado implicitamente.

### <a name="Step3">Etapa 3: Recuperando e compreendendo a saída JSON para analyze&visualFeatures=Tags, Description</a>

Aqui está um exemplo:
```
  {
    “tags”: [
    {
    "name": "outdoor",
        "score": 0.976
    },
    {
    "name": "bird",
        "score": 0.95
    }
            ],
    “description”: 
    {
    "tags": [
    "outdoor",
    "bird"
            ],
    "captions": [
    {
    "text”: “partridge in a pear tree”,
            “confidence”: 0.96
    }
                ]
    }
  }
```
Campo   | Tipo  | Conteúdo
------|------|------|
Marcas    | objeto    | Objeto de nível superior para matriz de tags
tags[].Name | string    | Palavra-chave do classificador de tags
tags[].Score    | número    | Pontuação de confiança, entre 0 e 1.
Descrição  | objeto   | Objeto de nível superior de uma descrição.
description.tags[] |    string  | Lista de tags.  Se houver confiança insuficiente na capacidade de produzir uma legenda, talvez as tags sejam as únicas informações disponíveis para o chamador.
description.captions[].text | string    | Uma frase que descreve a imagem.
description.captions[].confidence   | número    | Confiança da frase.

### <a name="Step4">Etapa 4: Recuperando e compreendendo a saída JSON de modelos específicos a um domínio</a>

**Opção um:** análise com escopo – analisar apenas um modelo dado

A saída será uma matriz de tags, como este exemplo:
```
  { 
    "result": [ 
      { 
    "name": "golden retriever", 
    "score": 0.98
    },
    { 
    "name": "Labrador retriever", 
    "score": 0.78
    }
              ]
  }
```

**Opção dois:** análise avançada – analisar para fornecer mais detalhes com a taxonomia de 86 categorias

Para modelos específicos a um domínio que usam a Opção dois (análise avançada), o tipo de retorno das categorias é ampliado. Há um exemplo a seguir:
```
  {
    "requestId": "87e44580-925a-49c8-b661-d1c54d1b83b5",
    "metadata":     {
      "width": 640,
      "height": 430,
      "format": "Jpeg"
                    },
    "result": {
      "celebrities": 
      [
        {
        "name": "Richard Nixon",
        "faceRectangle": {
          "left": 107,
          "top": 98,
          "width": 165,
          "height": 165
                         },
        "confidence": 0.9999827
        }
      ]
  }
```

O campo de categorias é uma lista de uma ou mais das [86 categorias](../Category-Taxonomy.md) na taxonomia original. Observe também que as categorias que terminam com um sublinhado corresponderão à categoria e seus filhos (por exemplo, people_ assim como people_group, para o modelo de celebridades).

Campo   | Tipo  | Conteúdo
------|------|------|
Categorias | objeto | Objeto de nível superior
categories[].name    | string   | Nome da taxonomia de 86 categorias
categories[].score  | número    | Pontuação de confiança, entre 0 e 1
categories[].detail  | object?      | Objeto de detalhes opcional

Observe que, se várias categorias forem correspondentes (por exemplo, o classificador de 86 categorias retorna uma pontuação para people_ e people_young se model=celebrities), os detalhes serão anexados à correspondência de nível mais geral (people_, nesse exemplo).

### <a name="Errors">Respostas de erros</a>
Essas são idênticas a vision.analyze, com o erro adicional do erro NotSupportedModel (HTTP 400), que pode ser retornado nos cenários Opção um e Opção dois. Para a Opção dois (Análise avançada), se qualquer um dos modelos especificados nos detalhes não for reconhecido, a API retornará um NotSupportedModel, mesmo se um ou mais deles forem válidos.  Os usuários podem chamar listModels para descobrir para quais modelos há suporte.

### <a name="Summary">Resumo</a>

Essas são as funcionalidades básicas da API da Pesquisa Visual Computacional: a maneira como você pode carregar imagens e recuperar metadados valiosos em troca.

Para usar a API REST, acesse [Referência da API da Pesquisa Visual Computacional](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
 
