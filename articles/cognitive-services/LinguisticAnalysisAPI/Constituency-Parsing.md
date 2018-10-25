---
title: Análise de clientes – API de Análise Linguística
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre como a análise de clientes, também conhecida como "análise de estrutura de frase", identifica as frases no texto.
services: cognitive-services
author: RichardSunMS
manager: cgronlun
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: conceptual
ms.date: 03/21/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 89832f2d936a08df8b6f9e846c3dd4a5665c06a4
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238617"
---
# <a name="constituency-parsing"></a>Análise de clientes

> [!IMPORTANT]
> A versão prévia da Análise Linguística foi encerrada em 9 de agosto de 2018. Recomendamos usar os [módulos de Análise de Texto do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) para análise e processamento de texto.

O objetivo da análise de clientes (também conhecida como "análise de estrutura de frase") é identificar as frases no texto.
Isso pode ser útil ao extrair informações de texto.
Os clientes talvez queiram localizar nomes de recurso ou frase-chave de um grande corpo de texto e para ver os modificadores e as ações ao redor de cada frase.

## <a name="phrases"></a>Frases

Para uma linguista, uma *frase* é mais do que apenas uma sequência de palavras.
Para ser uma frase, um grupo de palavras tem que executar uma função específica no período.
Esse grupo de palavras pode ser movido junto ou substituído como um todo, e a frase deve permanecer fluente e gramática.

Considere a frase

> Quero encontrar um automóvel híbrida novo com Bluetooth.

Essa frase contém locução nominal: "um automóvel híbrida novo com Bluetooth".
Como sabemos que se trata de uma frase?
Podemos reescrever o período (um pouco poeticamente) movendo essa frase inteira para a frente:

> Um automóvel híbrido novo com Bluetooth quero encontrar.

Ou podemos substituir essa frase com uma frase de frase e significado semelhantes, como "um carro novo especial":

> Quero encontrar um carro novo especial.

Se, em vez disso, escolhemos um subconjunto diferente de palavras, essas tarefas de substituição resultariam em sentenças estranhas ou ilegíveis.
Considere o que acontece quando movemos "encontrar um novo" para a frente:

> Encontrar um novo eu quero para automóvel híbrido com Bluetooth.

Os resultados são muito difíceis de ler e entender.

O objetivo de um analisador é localizar todas essas frases.
Curiosamente, em linguagem natural, as frases tendem a estar aninhadas dentro de outra.
Uma representação natural dessas frase é uma árvore, como o seguinte:

![Árvore](./Images/tree.png)

Nesta árvore, as ramificações marcadas como "NP" são frases nominais.
Há várias dessas frases: *Eu*, *um automóvel híbrido novo*, *Bluetooth*, e *um automóvel híbrido novo com Bluetooth*.

## <a name="phrase-types"></a>Tipos de frase (locuções)

| Rótulo | DESCRIÇÃO | Exemplo |
|-------|-------------|---------|
|ADJP   | Locução adjetiva | "tão grosseiro" |
|ADVP   | Locução adverbial | "limpar através de" |
|CONJP  | Locução conjuntiva | “bem como” |
|FRAG   | Fragmento, usado para entradas fragmentadas ou incompletas | “Altamente recomendado...” |
|INTJ   | Interjeição | "Viva" |
|LST    | Marcador de lista, incluindo pontuação | "#4)" |
|NAC    | Não constituinte, usado para indicar o escopo de uma frase não constituinte |  "e por um bom negócio" em "você fazer as coisas e por um bom negócio" |
|NP | Frase substantivada | "uma panqueca de batata saborosa" |
|NX | Usada em determinadas NPs complexas para marcar o início| |
|PP | Locução adverbial de lugar| "na piscina" |
|PRN    | Entre parênteses| "(assim chamado)" |
|PRT    | Afixos| "de" em "copiados de" |
|QP | Locução adverbial de quantidade (ou seja, medida/quantidade complexa) em uma frase| "em torno de US$ 75" |
|RRC    | Oração relativa reduzida.| "ainda não resolvidos" em "muitos problemas ainda não resolvidos" |
|S  | Frase ou oração. | "Esta é uma frase".
|SBAR   | Oração subordinada, geralmente introduzida por uma conjunção subordinada | "assim que saí" em "Eu procurei em volta assim que saí."|
|SBARQ  | Pergunta direta introduzida por uma palavra ou frase de pergunta | "Qual foi o objetivo?" |
|SINV   | Frase declarativa invertida | "Em nenhum momento tinham eles conhecimento." (Observe como o sujeito normal "eles" foi movidos para após o verbo "tinham") |
|SQ | Pergunta Sim/Não invertida ou oração principal de uma pergunta | "Eles conseguiram o carro?" |
|UCP    | Ao contrário de oração coordenada| "pequeno e com bugs" (Observe como um adjetivo e uma frase preposicional são unidas com "e")|
|VP | Oração coordenada | "chove na floresta" |
|WHADJP | Locução adjetiva | "quão confortável" |
|WHADVP | Adjunto adverbial| "quando" |
|WHNP   | Frase substantivada| "qual batata", "quanta sopa"|
|WHPP   | Locução prepositiva| "em que país"|
|X  | Desconhecido, incerto ou inquebrável.| primeiro "a" em "a... a sopa" |


## <a name="specification"></a>Especificação

As árvores aqui usam as expressões de S do [Treebank Universidade](https://catalog.ldc.upenn.edu/ldc99t42).
