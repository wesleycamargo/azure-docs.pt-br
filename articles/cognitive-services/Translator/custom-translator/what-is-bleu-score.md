---
title: O que é uma pontuação no BLEU? - Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: O BLEU é uma medida das diferenças entre uma tradução automática e uma ou mais traduções de referência criadas pelo homem da mesma sentença fonte. O algoritmo BLEU compara frases consecutivas da tradução automática com as frases consecutivas encontradas na tradução de referência e conta o número de correspondências, de maneira ponderada.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.topic: article
ms.date: 11/13/2018
ms.author: v-rada
ms.openlocfilehash: 887eaeac5d74aafd9839495939a2079b288738e4
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626679"
---
# <a name="what-is-a-bleu-score"></a>O que é uma pontuação no BLEU?

[BLEU (Understudy de Avaliação Bilíngue)](https://en.wikipedia.org/wiki/BLEU) é uma medida das diferenças entre uma tradução automática e uma ou mais traduções de referência criadas por humanos da mesma sentença fonte.

## <a name="scoring-process"></a>Processo de pontuação

O algoritmo BLEU compara frases consecutivas da tradução automática com as frases consecutivas encontradas na tradução de referência e conta o número de correspondências, de maneira ponderada. Essas correspondências são independentes de posição. Um maior grau de correspondência indica um maior grau de similaridade com a tradução de referência e maior pontuação. Inteligibilidade e correção gramatical não são levadas em conta.

## <a name="how-bleu-works"></a>Como funciona o BLEU?

A força do BLEU é que ele se correlaciona bem com o julgamento humano, calculando a média dos erros individuais de julgamento de sentença sobre um corpus de teste, em vez de tentar elaborar o julgamento humano exato para cada sentença.

É uma discussão mais ampla de pontuações BLEU [aqui](https://youtu.be/-UqDljMymMg).

Os resultados do BLEU dependem fortemente da amplitude de seu domínio, da consistência dos dados de teste com os dados de treinamento e ajuste e da quantidade de dados que você tem disponível para treinar. Se seus modelos foram treinados em um domínio restrito e seus dados de treinamento são consistentes com seus dados de teste, você pode esperar uma alta pontuação no BLEU.

>[!NOTE]
>Uma comparação entre as pontuações BLEU só é justificável quando BLEU resultados são comparados com o mesmo conjunto de teste, o mesmo par de idioma e o mesmo mecanismo MT. Uma pontuação de BLEU de um conjunto de testes diferente é obrigada a ser diferente.
