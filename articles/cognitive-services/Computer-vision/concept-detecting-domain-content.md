---
title: Detectar conteúdo específico do domínio - Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Saiba como especificar um domínio de categorização de imagem para retornar informações mais detalhadas sobre uma imagem.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 50942634bd50974453c242d1980db9fc589bd47e
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53579202"
---
# <a name="detecting-domain-specific-content"></a>Detectar conteúdo específico de domínio

Além da marcação e da categorização de nível superior, o Computer Vision também oferece suporte a informações especializadas (ou de domínio específico). Informações especializadas podem ser implementadas como um método autônomo ou com a categorização de nível superior. Elas funcionam como um meio de refinar ainda mais a taxonomia de 86 categorias por meio da adição de modelos específicos a um domínio.

Há duas opções para usar os modelos específicos a um domínio:

* Análise de escopo  
  Analise apenas um modelo escolhido, invocando uma chamada HTTP POST. Se você souber qual modelo deseja usar, especifique o nome do modelo. Você só obtém informações relevantes para esse modelo. Por exemplo, você pode usar essa opção para procurar somente o reconhecimento de celebridades. A resposta contém uma lista de possíveis correspondências de celebridades, acompanhadas por suas pontuações de confiança.
* Análise aprimorada  
  Análise para fornecer detalhes adicionais relacionados às categorias da taxonomia de 86 categorias. Essa opção está disponível para uso em aplicativos em que os usuários desejam obter uma análise de imagem genérica, além dos detalhes de um ou mais modelos específicos a um domínio. Quando esse método é invocado, o classificador da taxonomia de 86 categorias é chamado primeiro. Se alguma das categorias corresponder à de modelos conhecidos ou correspondentes, uma segunda passagem de invocações de classificador será exibida. Por exemplo, se o parâmetro `details` da chamada HTTP POST for definido como "todos" ou incluir "celebridades", o método chamará o classificador de celebridade depois que o classificador de 86 categorias for chamado. Se a imagem for classificada como `people_` ou uma subcategoria dessa categoria, o classificador de celebridade será chamado.

## <a name="listing-domain-specific-models"></a>Listagem de modelos específicos de domínio

Você pode listar os modelos específicos de domínio compatíveis com a visão do computador. Atualmente, o Computer Vision suporta os seguintes modelos específicos de domínio para detectar conteúdo específico de domínio:

| NOME | DESCRIÇÃO |
|------|-------------|
| Celebridades | Reconhecimento de celebridades, compatível com imagens classificadas na categoria `people_` |
| Pontos de referência | Reconhecimento de pontos de referência, com suporte para imagens classificado na `outdoor_` ou `building_` categorias |

### <a name="domain-model-list-example"></a>Exemplo de lista de modelos de domínio

A seguinte resposta JSON lista os modelos específicos de domínio suportados pelo Computer Vision.

```json
{
    "models": [
        {
            "name": "celebrities",
            "categories": ["people_", "人_", "pessoas_", "gente_"]
        },
        {
            "name": "landmarks",
            "categories": ["outdoor_", "户外_", "屋外_", "aoarlivre_", "alairelibre_",
                "building_", "建筑_", "建物_", "edifício_"]
        }
    ]
}
```

## <a name="next-steps"></a>Próximas etapas

Conheça os conceitos [categorizar imagens](concept-categorizing-images.md).