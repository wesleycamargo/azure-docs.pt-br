---
title: Habilidade de pesquisa cognitiva de Reconhecimento de Entidade Nomeada (Azure Search) | Microsoft Docs
description: Extrai entidades nomeadas para a pessoa, local e a organização de texto em um pipeline de pesquisa cognitivas do Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 653a4675d546432eea8478ba6203be1df71ec4f4
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45731386"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Habilidade cognitiva de Reconhecimento de Entidade Nomeada

A habilidade do **Reconhecimento de Entidade Nomeada** extrai entidades nomeadas de texto. Entidades disponíveis incluem os tipos `person`, `location`, e `organization`.

> [!NOTE]
> Pesquisa Cognitiva está na visualização pública. A execução do Skillset e a extração e normalização de imagens são oferecidas gratuitamente. Em um momento posterior, os preços desses recursos serão anunciado. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="skill-parameters"></a>Parâmetros de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas.

| Nome do parâmetro     | DESCRIÇÃO |
|--------------------|-------------|
| Categorias    | Matriz de categorias que devem ser extraídas.  Tipos possíveis de categoria: `"Person"`, `"Location"`, `"Organization"`. Se nenhuma categoria for fornecida, todos os tipos são retornados.|
|defaultLanguageCode |  Código de idioma do texto de entrada. Há suporte para vários idiomas: `ar, cs, da, de, en, es, fi, fr, he, hu, it, ko, pt-br, pt`|
| minimumPrecision  | Um número entre 0 e 1. Se a precisão for menor do que esse valor, a entidade não é retornada. O padrão é 0.|

## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada      | DESCRIÇÃO                   |
|---------------|-------------------------------|
| languageCode  | Opcional. O padrão é `"en"`.  |
| text          | O texto para analisar.          |

## <a name="skill-outputs"></a>Saídas de habilidades

| Nome de saída     | DESCRIÇÃO                   |
|---------------|-------------------------------|
| pessoas      | Uma matriz de cadeias de caracteres onde cada cadeia de caracteres representa o nome de uma pessoa. |
| Locais  | Uma matriz de cadeias de caracteres onde cada cadeia de caracteres representa um local. |
| organizações  | Uma matriz de cadeias de caracteres onde cada cadeia de caracteres representa uma organização. |
| entidades | Uma matriz de tipos complexos. Cada tipo complexo inclui os seguintes campos: <ul><li>categoria (`"person"`, `"organization"`, ou `"location"`)</li> <li>valor (nome de entidade real)</li><li>deslocamento (o local onde ele foi encontrado no texto)</li><li>confiança (um valor entre 0 e 1 que representa essa confiança de que o valor é uma entidade real)</li></ul> |

##  <a name="sample-definition"></a>Definição de exemplo

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
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
        "data":
           {
             "text": "This is a the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia… Ana Smith is provided as a reference.",
             "languageCode": "en"
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
      "data" : 
      {
        "persons": [ "Joe Romero", "Ana Smith"],
        "locations": ["Seattle"],
        "organizations":["Microsoft Corporation"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 45,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 59,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Seattle",
            "offset": 5,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft Corporation",
            "offset": 120,
            "confidence": 0.99
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Casos de erro
Se você especificar um código de idioma sem suporte ou se o conteúdo não corresponde ao idioma especificado, um erro é retornado e nenhuma entidade é extraída.

## <a name="see-also"></a>Consulte também

+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)