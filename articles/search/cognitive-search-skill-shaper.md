---
title: Habilidade de pesquisa cognitiva do Shaper (Azure Search) | Microsoft Docs
description: Extrair metadados e informações estruturadas de dados não estruturados e formatá-los como um tipo complexo em um pipeline de enriquecimento do Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 286e1f9d6f6ae09d98aa87b447df7a7524642a1f
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45728990"
---
#   <a name="shaper-cognitive-skill"></a>Habilidades cognitivas do Shaper

A habilidade **Shaper** cria um tipo complexo para dar suporte a campos compostos (também conhecido como campos de várias partes). Um campo de tipo complexo tem várias partes, mas é tratado como um único item em um índice do Azure Search. Exemplos de campos consolidados úteis nos cenários de pesquisa incluem combinar um nome e sobrenome em um campo único, cidade e estado em um campo único ou nome e data de nascimento em m um campo único para estabelecer a identidade exclusiva.

A habilidade de Shaper permite que você crie uma estrutura essencialmente, defina o nome dos membros de estrutura e atribuir valores a cada membro.

Por padrão, essa técnica oferece suporte a objetos que estão um nível de profundidade. Para objetos mais complexos, é possível encadear várias etapas do Shaper.

Em resposta, o nome de saída é sempre "saída". Internamente, o pipeline pode mapear um nome diferente, como "analyzedText" nos exemplos a seguir "saída", mas a habilidade do Shaper em si retorna "saída" na resposta. Isso pode ser importante se você estiver depurando documentos enriquecidos e observar a discrepância de nomenclatura, ou se você criar uma habilidade personalizada e estruturação de resposta por conta própria.

> [!NOTE]
> Pesquisa Cognitiva está na visualização pública. A execução do conjunto de qualificações e a extração e normalização de imagem são oferecidas gratuitamente. Em um momento posterior, os preços desses recursos serão anunciados. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="sample-1-complex-types"></a>Amostra 1: tipos complexos

Considere um cenário onde você deseja criar uma estrutura chamada *analyzedText* que tem dois membros: *texto* e *sentimento*, respectivamente. No Azure Search, um campo de pesquisado de várias parte é chamado um *tipo complexo* e ainda não tem suporte fora da caixa. Nesta visualização, uma habilidade do Shaper pode ser usada para gerar campos de um tipo complexo no índice. 

O exemplo a seguir fornece os nomes do membro como a entrada. A estrutura de saída (campo complexo no Azure Search) é especificada por meio de *targetName*. 


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
      "targetName": analyzedText"
    }
  ]
}
```

### <a name="sample-input"></a>Entrada de exemplo
Um documento JSON fornece entrada utilizável para esta habilidade Shaper poderia ser:

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


### <a name="sample-output"></a>Saída de exemplo
A habilidade de Shaper gera um novo elemento chamado *analyzedText* com os elementos combinados de *texto* e *sentimento*. 

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

## <a name="sample-2-input-consolidation"></a>Exemplo 2: consolidação de entrada

Em outro exemplo, imagine que em diferentes estágios do processamento de pipeline, você extraiu o título de um livro e títulos de capítulo em diferentes páginas do livro. Agora você pode criar uma única estrutura composta por essas várias entradas.

A definição de habilidade Shaper para esse cenário pode parecer com o exemplo a seguir:

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
            "source": "/document/content/pages/*/chapterTitles/*"
        }
    ],
    "outputs": [
        {
            "output": "titlesAndChapters",
            "targetName": "analyzedText"
        }
    ]
}
```

### <a name="sample-output"></a>Saída de exemplo
Nesse caso, o Shaper mescla todos os títulos de capítulo para criar uma matriz. 

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

## <a name="see-also"></a>Consulte também

+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)

