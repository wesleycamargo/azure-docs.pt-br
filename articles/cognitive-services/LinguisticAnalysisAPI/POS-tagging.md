---
title: Marcação de Parte da Fala ‒ API de Análise Linguística
description: Saiba como a Marcação de Parte da Fala na API de Análise Linguística identifica a categoria ou parte da fala de cada palavra do texto.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 09/27/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 0269397b0f8da66d2bafecfb427ba705fdfff001
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60394482"
---
# <a name="part-of-speech-tagging"></a>Marcação de Parte da Fala

> [!IMPORTANT]
> A versão prévia da Análise Linguística foi encerrada em 9 de agosto de 2018. Recomendamos usar os [módulos de Análise de Texto do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) para análise e processamento de texto.

## <a name="background-and-motivation"></a>Motivação e Segundo Plano

Uma vez que um texto tenha sido separado em frases e tokens, a próxima etapa da análise é identificar a categoria ou a parte da fala de cada palavra.
Isso inclui categorias como *substantivo* (geralmente representando pessoas, lugares, coisas, ideias, etc.) e *verbo* (geralmente representando ações, mudanças de estado, etc.). Para algumas palavras, a parte da fala não é ambígua (por exemplo, *pântano* é apenas um substantivo), mas para muitas outras, pode ser diferente.
*Painel* pode ser uma pintura sobre tela (ou, um quadro com mensagem publicitária), mas também pode-se dizer um “painel de discussões”.

## <a name="list-of-part-of-speech-tags"></a>Lista de marcas de Parte da Fala

| Marca | DESCRIÇÃO | Palavras de exemplo |
|-----|-------------|---------------|
| $ | dólar | $ |
| \`\` | aspas de abertura | \` \`\` |
| '' | aspas de fechamento | ' '' |
| ( | parêntese de abertura | ( [ { |
| ) | parêntese de fechando | ) ] } |
| , | vírgula | , |
| -- | traço | -- |
| . | terminador de sentença | . ! ? |
| : | dois-pontos ou reticências | : ; ... |
| CC | conjunção, coordenação | e mas ou ainda|
| CD | numeral, cardinal | nove 20 1980 '96 |
| DT | determinantes |um o uma todos outros nenhum|
| EX | existencial lá | ali |
| FW | palavra estrangeira | enfant terrible hoi polloi je ne sais quoi |
| IN | preposição ou conjunção de subordinação| em dentro se sobre se |
| JJ | adjetivo ou numeral ordinal | nono bonito abominável multimodal |
| JJR | adjetivo, comparativo | melhor mais rápido mais barato |
| JJS | adjetivo, superlativo | o melhor mais rápido mais barato |
| LS | lista item marcador | (a) (b) 1 2 A B A. B. |
| MD | Modal | poder dever desejar poderia |
| NN | substantivo, comum, singular ou coletivo | batata dinheiro calçado |
| NNP | substantivo, próprio, singular | Kennedy Roosevelt Chicago Weehauken |
| NNPS | substantivo, próprio, plural | Arbustos Springfields |
| NNS | substantivo, comum, plural | peças ratos campos |
| PDT | pré-determinantes | todos ambos meio muitos bem tal claro isso |
| POS | marcador genitivo | ' 's |
| PRP | pronome, pessoal | ela ele tu eu nós eles elas você |
| PRP$ | pronome, possessivo | dela dele seu meu nosso deles seus |
| RB | advérbio | clinicamente somente |
| RBR | advérbio, comparativo | mais, mais sombrio, maior, mais grave, mais severo, mais duro, mais saudável, mais pesado, mais elevado, mais tarde, mais magro, mais comprido, menos perfeitamente, menor, mais solitário, mais longo, mais alto, mais baixo... |
| RBS | advérbio, superlativo | o melhor, o maior, o mais brusco, o mais breve, o mais distante, antes, o mais afastado, o mais difícil, o mais sincero, o mais alto, o mais largo, pouco, mínimo, o mais próximo, o mais apertado |
| RP | Afixos | in anti retro sobre |
| SYM | símbolo | % & |
| ATÉ | "para" como preposição ou marcador infinitivo | para |
| UH | interjeição | ah viva puxa olá |
| VB | verbo, forma | dar atribuir voar |
| VBD | verbo, passado | deu atribuiu voou |
| VBG | verbo, particípio presente ou gerúndio | dando atribuindo voando |
| VBN | verbo, particípio passado | dado atribuído voado |
| VBP | verbo, presente do indicativo, não 3ª pessoa do singular | dou atribuo voo |
| VBZ | verbo, presente do indicativo, 3ª pessoa do singular | dá atribui voa |
| WDT | Determinante WH | que qual quais |
| WP | Pronome WH | que quem |
| WP$ | Pronome WH, possessivo | cujo |
| WRB | Advérbio WH | como porém sempre que onde |

## <a name="specification"></a>Especificação

Quanto à geração de tokens, confiamos na especificação do [Penn Treebank](https://catalog.ldc.upenn.edu/LDC99T42).
