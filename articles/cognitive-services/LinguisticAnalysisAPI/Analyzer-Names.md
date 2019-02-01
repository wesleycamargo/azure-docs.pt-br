---
title: Estrutura de nomenclatura do analisador – API de Análise Linguística
titlesuffix: Azure Cognitive Services
description: Saiba como a estrutura de nomenclatura do Analisador da API de Análise Linguística permite flexibilidade e precisão.
services: cognitive-services
author: RichardSunMS
manager: cgronlun
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 03/23/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 997dcdd6ce99815a79006b40b87568e573c63ff3
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221667"
---
# <a name="analyzer-names"></a>Nomes do analisador

> [!IMPORTANT]
> A versão prévia da Análise Linguística foi encerrada em 9 de agosto de 2018. Recomendamos usar os [módulos de Análise de Texto do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) para análise e processamento de texto.

Usamos uma estrutura de nomeação relativamente complicada para analisadores para permitir flexibilidade em analisadores e precisão no reconhecimento do que significa que um nome.
Nomes de analisador consistem em quatro partes: uma ID, um tipo, uma especificação e uma implementação.
A função de cada componente está definida abaixo.

## <a name="id"></a>ID
Primeiro, um analisador tem uma ID exclusiva; um GUID.
Esses GUIDs devem alterar raramente, mas são a única maneira de descrever exclusivamente um analisador específico.

## <a name="kind"></a>Tipo
Em seguida, cada analisador é um **tipo**.
Isso define em termos muito amplos o tipo de análise retornado e deve definir exclusivamente a estrutura de dados usada para representar essa análise.
Atualmente, há três tipos distintos:
 - [Tokens](Sentences-and-Tokens.md)
 - [Marcas de POS](Pos-Tagging.md)
 - [Árvore de clientes](constituency-parsing.md)

## <a name="specification"></a>Especificação
Dentro de um determinado tipo, no entanto, diferentes especialistas podem não concordar em como um fenômeno específico deve ser analisado.
Ao contrário de linguagens de programação, não há uma definição clara e exata de como isso deve ser feito.

Por exemplo, imagine que estamos tentando encontrar os tokens na frase em inglês "He didn’t go."
Em particular, considere a cadeia de caracteres "didn’t".
Uma interpretação possível é que isso deve ser dividido em dois tokens: "did" e "not".
Em seguida, a frase alternativa "He did not go" teria o mesmo conjunto de tokens.
Outra possibilidade é dizer que ele deve ser dividido nos tokens "did" e "n’t".
O último token não seria normalmente considerado uma palavra, mas essa abordagem retém mais informações sobre a cadeia de caracteres de superfície que, às vezes, pode ser útil.
Ou talvez esse contração deve ser considerada uma única palavra.

Independentemente da escolha feita, essa escolha deve ser feita de forma consistente.
Essa é exatamente a função de uma **especificação**: decidir o que deve ser uma representação correta.

Saídas de analisador só podem ser comparadas para dados que conformam a mesma especificação.

## <a name="implementation"></a>Implementação

Geralmente, há vários modelos que tentam obter os mesmos resultados, mas com diferentes características de desempenho.
Um modelo pode ser mais rápido porém menos preciso; outro pode fazer uma compensação diferente.

A parte **implementação** de um nome de analisador é usada para identificar esse tipo de informação, para que os usuários possam selecionar o analisador mais adequado às suas necessidades.
