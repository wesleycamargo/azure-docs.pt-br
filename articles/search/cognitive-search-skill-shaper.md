---
title: Habilidade de Formatador da pesquisa cognitiva – Azure Search
description: Extrair metadados e informações estruturadas de dados não estruturados e formatá-los como um tipo complexo em um pipeline de enriquecimento do Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 5267f81c9886e2d1d8d62c134156aedb3b2b8763
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023703"
---
#   <a name="shaper-cognitive-skill"></a>Habilidades cognitivas do Shaper

O **Shaper** habilidade consolida várias entradas em um [tipo complexo](search-howto-complex-data-types.md) que pode ser referenciado posteriormente no pipeline enriquecimento. A habilidade **Formatador** permite basicamente que você crie uma estrutura, defina o nome dos membros dessa estrutura e atribua valores a cada membro. Campos consolidados útil em cenários de pesquisa com exemplos de combinação de um nome e sobrenome em uma única estrutura, cidade e estado em uma única estrutura, ou o nome e data de nascimento em uma única estrutura para estabelecer a identidade exclusiva.

A versão da API determina a profundidade de formatação que você pode atingir. 

| Versão da API | Modelagem de comportamentos | 
|-------------|-------------------|
| Versão de 2019-05-06-preview da API REST (não há suporte para SDK do .NET) | Objetos complexos, vários níveis de profundidade, em uma **Shaper** definição de habilidades. |
| 2019-05-06 * * (geralmente disponível), 2017-11-11-Preview| Objetos complexos, um nível de profundidade. Uma forma de vários nível requer várias etapas shaper encadear.|

A versão prévia **Shaper** habilidade, ilustrada na [cenário 3](#nested-complex-types), adiciona um novo opcional *ContextodeOrigem* propriedade à entrada. O *fonte* e *ContextodeOrigem* propriedades são mutuamente exclusivas. Se a entrada está no contexto da habilidade, basta usar *fonte*. Se a entrada for em uma *diferentes* contexto que o contexto de habilidade, use o *ContextodeOrigem*. O *ContextodeOrigem* exige que você definir uma entrada aninhada com o elemento específico que está sendo tratado como a origem. 

Na resposta, para todas as versões de API, o nome de saída será sempre "saído". Internamente, o pipeline pode mapear um nome diferente, como "analyzedText", conforme mostrado nos exemplos a seguir, mas o **Shaper** habilidade em si retorna "output" na resposta. Isso pode ser importante se você estiver depurando documentos enriquecidos e observar a discrepância de nomenclatura, ou se você criar uma habilidade personalizada e estruturação de resposta por conta própria.

> [!NOTE]
> O **Shaper** habilidade não está associada a uma API de serviços Cognitivos e você não será cobrado para utilizá-lo. No entanto, você ainda deverá [anexar um recurso dos Serviços Cognitivos](cognitive-search-attach-cognitive-services.md) para substituir a opção de recurso **Gratuito** que limita você a um pequeno número de enriquecimentos por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>Cenário 1: tipos complexos

Considere um cenário onde você deseja criar uma estrutura chamada *analyzedText* que tem dois membros: *texto* e *sentimento*, respectivamente. Em um índice de Azure Search, um campo pesquisável com várias parte é chamado um *tipo complexo* e geralmente é criado quando a fonte de dados tem uma estrutura complexa correspondente que é mapeada para ele.

No entanto, outra abordagem para a criação de tipos complexos é por meio de **Shaper** habilidade. Incluindo essa habilidade em um conjunto de qualificações, as operações de memória durante o processamento de conjunto de qualificações podem produzir as formas de dados com estruturas aninhadas, que podem ser mapeadas para um tipo complexo em seu índice. 

A definição de habilidade de exemplo a seguir fornece o membro nomes como entrada. 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-index"></a>Índice de exemplo

Um conjunto de qualificações é invocado por um indexador, e um indexador requer um índice. Uma representação de campo complexa em seu índice pode parecer com o exemplo a seguir. 

```json

    "name": "my-index",
    "fields": [
        {   "name": "myId", "type": "Edm.String", "key": true, "filterable": true   },
        {   "name": "analyzedText", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "text",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
          {
                    "name": "sentiment",
                    "type": "Edm.Double",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
```

### <a name="skill-input"></a>Entrada de habilidades

Um documento JSON de entrada fornecendo a entrada utilizável para este **Shaper** habilidade poderia ser:

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="skill-output"></a>Saída de habilidades

A habilidade **Formatador** gera um novo elemento chamado *analyzedText* com os elementos combinados de *texto* e *sentimento*. Essa saída é compatível com o esquema de índice. Ele será importado e indexado no índice de Azure Search.

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="scenario-2-input-consolidation"></a>Cenário 2: consolidação de entrada

Em outro exemplo, imagine que em diferentes estágios do processamento de pipeline, você extraiu o título de um livro e títulos de capítulo em diferentes páginas do livro. Agora você pode criar uma única estrutura composta por essas várias entradas.

O **Shaper** definição de habilidades para esse cenário pode parecer com o exemplo a seguir:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*/title"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Saída de habilidades
Nesse caso, o **Shaper** mescla todos os títulos de capítulos para criar uma única matriz. 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Cenário 3: consolidação de entrada de contextos aninhados

> [!NOTE]
> Aninhado estruturas que têm suportadas na api-version = 2019-05-06-Preview pode ser usado em uma [repositório de dados de Conhecimento](knowledge-store-concept-intro.md) ou em um índice de Azure Search.

Imagine que você tem o título, capítulos e conteúdo de um livro e executou entidade frases de reconhecimento e a chave no conteúdo e agora precisa agregar resultados com as habilidades diferentes em uma única forma com o nome do capítulo, entidades e frases-chave.

O **Shaper** definição de habilidades para esse cenário pode parecer com o exemplo a seguir:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
        }

    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Saída de habilidades
Nesse caso, o **Shaper** cria um tipo complexo. Essa estrutura existe na memória. Se você deseja salvá-lo em um repositório de dados de Conhecimento, você deve criar uma projeção em seu conjunto de qualificações que definem as características de armazenamento.

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>Consulte também

+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Como usar tipos complexos](search-howto-complex-data-types.md)
+ [Visão geral do armazenamento de dados de Conhecimento](knowledge-store-concept-intro.md)
+ [Como começar com conhecimento Store](knowledge-store-howto.md)