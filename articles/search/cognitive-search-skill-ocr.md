---
title: Habilidade de OCR da pesquisa cognitiva – Azure Search
description: Extraia o texto de arquivos de imagem usando o OCR (reconhecimento óptico de caracteres) em um pipeline de enriquecimento do Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 097fd93955a4ca3fd96ae6452fa3b503b029ffc3
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53313217"
---
# <a name="ocr-cognitive-skill"></a>Habilidades cognitivas OCR

A habilidade **OCR** extrai o texto de arquivos de imagem. Formatos de arquivo com suporte incluem:

+ .JPEG
+ .JPG
+ .PNG
+ .BMP
+ .GIF

> [!NOTE]
> A partir de 21 de dezembro de 2018, você poderá associar um recurso de Serviços Cognitivos a um conjunto de habilidades do Azure Search. Isso nos permitirá começar a cobrar pela execução do conjunto de habilidades. Nessa data, também começaremos a cobrar pela extração de imagens como parte do estágio de decodificação de documentos. A extração de texto de documentos continuará sendo oferecida sem custo adicional.
>
> A execução das habilidades internas será cobrada conforme o [preço pago conforme o uso dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) existente. O preço da extração de imagens será cobrado conforme o preço da versão prévia, descrito na [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400). Saiba [mais](cognitive-search-attach-cognitive-services.md).
>
>  A habilidade de OCR é mapeada para a seguinte funcionalidade de serviços cognitivos: Quando textExtractionAlgorithm é definido como "handwritten", a funcionalidade ["RecognizeText"](../cognitive-services/computer-vision/quickstarts-sdk/csharp-hand-text-sdk.md) é usada.
>  Quando textExtractionAlgorithm é definido como "printed", a funcionalidade ["OCR"](../cognitive-services/computer-vision/concept-extracting-text-ocr.md) é usada para idiomas diferentes do inglês. Para o inglês, a nova funcionalidade ["Reconhecimento de Texto"](../cognitive-services/computer-vision/concept-recognizing-text.md) para texto impresso é usada.

## <a name="skill-parameters"></a>Parâmetros de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas.

| Nome do parâmetro     | DESCRIÇÃO |
|--------------------|-------------|
| detectOrientation | Habilita a detecção automática da orientação da imagem. <br/> Valores válidos: verdadeiro / falso.|
|defaultLanguageCode | <p>  Código de idioma do texto de entrada. As linguagens com suporte incluem: <br/> zh-Hans (Chinês Simplificado) <br/> zh-Hant (Chinês Tradicional) <br/>cs (tcheco) <br/>da (dinamarquês) <br/>nl (holandês) <br/>en (em inglês) <br/>fi (finlandês)  <br/>fr (francês) <br/>  de (alemão) <br/>el (Grego) <br/> hu (húngaro) <br/> it (Italiano) <br/>  ja (Japonês) <br/> ko (Coreano) <br/> nb (Norueguês) <br/>   pl (Polonês) <br/> pt (Português) <br/>  ru (Russo) <br/>  es (Espanhol) <br/>  sv (Sueco) <br/>  tr (Turco) <br/> ar (Árabe) <br/> ro (Romeno) <br/> sr-Cyrl (Cirílico sérvio) <br/> SR-Latn (Latim sérvio) <br/>  SK (Eslovaco). <br/>  unk (desconhecido) <br/><br/> Se o código de idioma for especificado ou nulo, o idioma é detectado automaticamente. </p> |
| textExtractionAlgorithm | "impresso" ou "manuscrito". O algoritmo de OCR de reconhecimento de texto "manuscrito" está atualmente em visualização e tem suporte apenas em inglês. |

## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada      | DESCRIÇÃO                                          |
|---------------|------------------------------------------------------|
| image         | Tipo complexo. Atualmente só funciona com o campo "/document/normalized_images" produzido pelo indexador de BLOBs do Microsoft Azure quando ```imageAction``` é definido como ```generateNormalizedImages```. Para obter mais informações, confira [este exemplo](#sample-output).|


## <a name="skill-outputs"></a>Saídas de habilidades
| Nome de saída     | DESCRIÇÃO                   |
|---------------|-------------------------------|
| text          | Texto sem formatação extraído da imagem.   |
| layoutText    | Tipo complexo que descreve o texto extraído, bem como o local onde o texto foi encontrado.|


## <a name="sample-definition"></a>Definição de exemplo

```json
{
    "skills": [
      {
        "description": "Extracts text (plain and structured) from image.",
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
        "defaultLanguageCode": null,
        "detectOrientation": true,
        "inputs": [
          {
            "name": "image",
            "source": "/document/normalized_images/*"
          }
        ],
        "outputs": [
          {
            "name": "text",
            "targetName": "myText"
          },
          {
            "name": "layoutText",
            "targetName": "myLayoutText"
          }
        ]
      }
    ]
 }
```
<a name="sample-output"></a>

## <a name="sample-text-and-layouttext-output"></a>Exemplo de saída de texto e layoutText

```json
{
  "text": "Hello World. -John",
  "layoutText":
  {
    "language" : "en",
    "text" : "Hello World. -John",
    "lines" : [
      {
        "boundingBox":
        [ {"x":10, "y":10}, {"x":50, "y":10}, {"x":50, "y":30},{"x":10, "y":30}],
        "text":"Hello World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ],
    "words": [
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"Hello"
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ]
  }
}
```

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Exemplo: Mesclar o texto extraído de imagens incorporadas com o conteúdo do documento.

Um caso de uso comum para o Text Merger é a capacidade de mesclar a representação textual de imagens (texto de uma habilidade OCR ou a legenda de uma imagem) no campo de conteúdo de um documento. 

O conjunto de qualificações do exemplo a seguir cria um campo *merged_text* para conter o conteúdo textual do documento, bem como o texto processado para OCR de cada uma das imagens inseridas nesse documento. 

#### <a name="request-body-syntax"></a>Sintaxe de Corpo da Solicitação
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
        "name": "OCR skill",
        "description": "Extract text (plain and structured) from image.",
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
        "defaultLanguageCode": "en",
        "detectOrientation": true,
        "inputs": [
          {
            "name": "image",
            "source": "/document/normalized_images/*"
          }
        ],
        "outputs": [
          {
            "name": "text"
          }
        ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
O exemplo de conjunto de qualificações acima presume que existe um campo de imagens normalizado. Para gerar esse campo, defina a configuração *imageAction* na definição do indexador para *generateNormalizedImages* conforme mostrado abaixo:

```json
{  
   //...rest of your indexer definition goes here ... 
  "parameters":{  
      "configuration":{  
         "dataToExtract":"contentAndMetadata",
         "imageAction":"generateNormalizedImages"
      }
   }
}
```

## <a name="see-also"></a>Consulte também
+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
+ [Habilidade de TextMerger](cognitive-search-skill-textmerger.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Criar Indexador (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
