---
title: Habilidade de pesquisa cognitiva da Análise de IMagem (Azure Search) | Microsoft Docs
description: Extraia o texto semântico por meio de análise de imagem usando a habilidade de cognitivas de análise de Imagens em um pipeline de enriquecimento do Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 7f7d447edd0d73084a46aeff81f27b3ab0f072d3
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286227"
---
#   <a name="image-analysis-cognitive-skill"></a>Habilidade cognitiva de Análise de Imagens

A habilidade **Análise de Imagens** extrai um conjunto avançado de recursos visuais com base no conteúdo da imagem. Por exemplo, é possível gerar uma legenda de uma imagem, criar marcas ou identificar celebridades e pontos de referência.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Vision.ImageAnalysisSkill 

## <a name="skill-parameters"></a>Parâmetros de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas.

| Nome do parâmetro     | DESCRIÇÃO |
|--------------------|-------------|
| defaultLanguageCode   |  Uma cadeia de caracteres que indica o idioma para retornar. O serviço retorna resultados de reconhecimento no idioma especificado. Se este parâmetro não for especificado, o valor padrão é “en”. <br/><br/>Idiomas com suporte: <br/>*en* - inglês (padrão) <br/> *zh* - chinês Simplificado|
|visualFeatures |   Uma matriz de cadeias de caracteres que indica os tipos de recurso visuais para retornar. Tipos de recurso válido visuais:  <ul><li> *categorias* - classifica o conteúdo da imagem de acordo com uma taxonomia definida na documentação [Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy).</li><li> *marcas* - marca a imagem com uma lista detalhada das palavras relacionadas ao conteúdo da imagem.</li><li>*Descrição* - descreve o conteúdo da imagem com uma frase em inglês completa.</li><li>*Faces* -detecta se as faces estão presentes. Se estiverem, gera coordenadas de sexo e idade.</li><li> *ImageType* - detecta se a imagem é clip-art ou um desenho de linha.</li><li>   *Cor* -determina a cor de ênfase, cor dominante e se uma imagem é preta e branco.</li><li>*Adultos* - detecta se a imagem pe de natureza pornográfica (descreve nudez ou um ato de sexo). Conteúdo que sugere sexo também detectado.</li></ul> Nomes de recursos visuais diferenciam maiusculas de minúsculas.|
| detalhes   | Uma matriz de cadeias de caracteres que indica qual domínio específico de detalhes retornar. Tipos de recurso válido visuais: <ul><li>*Celebridades* - identifica se há celebridades detectadas na imagem.</li><li>*Pontos de referência* - identifica se há pontos de referência detectados na imagem.</li></ul>
 |

## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada      | DESCRIÇÃO                                          |
|---------------|------------------------------------------------------|
| image         | Tipo complexo. Atualmente só funciona com o campo "/document/normalized_images" produzido pelo indexador de BLOBs do Microsoft Azure quando ```imageAction``` é definido como ```generateNormalizedImages```. Para obter mais informações, confira [este exemplo](#sample-output).|



##  <a name="sample-definition"></a>Definição de exemplo

```json
{
    "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
    "context": "/document/normalized_images/*",
    "visualFeatures": [
        "Tags",
        "Faces",
        "Categories",
        "Adult",
        "Description",
        "ImageType",
        "Color"
    ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "image",
            "source": "/document/normalized_images/*"
        }
    ],
    "outputs": [
        {
            "name": "categories",
            "targetName": "myCategories"
        },
        {
            "name": "tags",
            "targetName": "myTags"
        },
        {
            "name": "description",
            "targetName": "myDescription"
        },
        {
            "name": "faces",
            "targetName": "myFaces"
        },
        {
            "name": "imageType",
            "targetName": "myImageType"
        },
        {
            "name": "color",
            "targetName": "myColor"
        },
        {
            "name": "adult",
            "targetName": "myAdultCategory"
        }
    ]
}
```

##  <a name="sample-input"></a>Entrada de exemplo

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "url": "https://storagesample.blob.core.windows.net/sample-container/image.jpg"
            }
        }
    ]
}
```


##  <a name="sample-output"></a>Saída de exemplo

```json
{
    "values": [
      {
        "recordId": "1",
            "data": {
                "categories": [
           {
                        "name": "abstract_",
                        "score": 0.00390625
                    },
                    {
                "name": "people_",
                        "score": 0.83984375,
                "detail": {
                            "celebrities": [
                                {
                                    "name": "Satya Nadella",
                                    "faceBoundingBox": {
                                        "left": 597,
                                        "top": 162,
                                        "width": 248,
                                        "height": 248
                                    },
                                    "confidence": 0.999028444
                                }
                            ],
                            "landmarks": [
                                {
                                    "name": "Forbidden City",
                                    "confidence": 0.9978346
                                }
                            ]
                        }
                    }
                ],
                "adult": {
                    "isAdultContent": false,
                    "isRacyContent": false,
                    "adultScore": 0.0934349000453949,
                    "racyScore": 0.068613491952419281
                },
                "tags": [
                    {
                        "name": "person",
                        "confidence": 0.98979085683822632
                    },
                    {
                        "name": "man",
                        "confidence": 0.94493889808654785
                    },
                    {
                        "name": "outdoor",
                        "confidence": 0.938492476940155
                    },
                    {
                        "name": "window",
                        "confidence": 0.89513939619064331
                    }
                ],
                "description": {
                    "tags": [
                        "person",
                        "man",
                        "outdoor",
                        "window",
                        "glasses"
                    ],
                    "captions": [
                        {
                            "text": "Satya Nadella sitting on a bench",
                            "confidence": 0.48293603002174407
                        }
                    ]
                },
                "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
                "metadata": {
                    "width": 1500,
                    "height": 1000,
                    "format": "Jpeg"
                },
                "faces": [
                    {
                        "age": 44,
                        "gender": "Male",
                    "faceBoundingBox": {
                            "left": 593,
                            "top": 160,
                            "width": 250,
                            "height": 250
                        }
                    }
                ],
                "color": {
                    "dominantColorForeground": "Brown",
                    "dominantColorBackground": "Brown",
                    "dominantColors": [
                        "Brown",
                        "Black"
                    ],
                    "accentColor": "873B59",
                    "isBWImg": false
                    },
                "imageType": {
                    "clipArtType": 0,
                    "lineDrawingType": 0
                }
           }
      }
    ]
}
```


## <a name="error-cases"></a>Casos de erro
Nos casos de erro a seguir, nenhum elemento é extraído.

| Código do Erro | DESCRIÇÃO |
|------------|-------------|
| NotSupportedLanguage | O idioma fornecido não tem suporte. |
| InvalidImageUrl | A URL da imagem está incorretamente formatada ou não está acessível.|
| InvalidImageFormat | Os dados de entrada não são uma imagem válida. |
| InvalidImageSize | A imagem de entrada é muito grande. |
| NotSupportedVisualFeature  | O tipo de destino especificado não é válido. |
| NotSupportedImage | Imagem sem suporte, por exemplo, pornografia infantil. |
| InvalidDetails | Modelo de domínio específico sem suporte. |

## <a name="see-also"></a>Consulte também

+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Criar Indexador (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
