---
title: Recursos
titleSuffix: Language Understanding - Azure Cognitive Services
description: Adicione recursos a um modelo de linguagem para fornecer dicas sobre como reconhecer a entrada que você deseja identificar ou classificar.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: 2d6f7e2fd332e1687db1564befeb6f531045c5dd
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993052"
---
# <a name="phrase-list-features-in-your-luis-app"></a>Recurso de lista de frases em seu aplicativo LUIS

No aprendizado de máquina, um *recurso* é um traço ou atributo específico de dados que seu sistema observa. 

Adicione recursos a um modelo de linguagem para fornecer dicas sobre como reconhecer a entrada que você deseja identificar ou classificar. Os recursos ajudam o LUIS a reconhecer intenções e entidades, mas não são intenções ou entidades em si. Em vez disso, os recursos podem fornecer exemplos de termos relacionados.  

## <a name="what-is-a-phrase-list-feature"></a>O que é um recurso de lista de frases?
Uma lista de frases inclui um grupo de valores (palavras ou frases) que pertencem à mesma classe e devem ser tratadas de maneira semelhante (por exemplo, nomes de cidades ou produtos). O que o LUIS aprende sobre um deles é aplicado automaticamente aos outros também. Essa lista não é uma [entidade de lista](luis-concept-entity-types.md#types-of-entities) (correspondências exatas de texto) de palavras correspondentes.

Uma lista de frase adiciona ao vocabulário do domínio de aplicativo como um segundo sinal ao LUIS sobre essas palavras.

## <a name="phrase-lists-help-all-models"></a>Listas de frases ajudam todos os modelos

As listas de frases não são vinculadas a uma intenção ou entidade específica, mas são adicionadas como um boost em todos os modelos. Sua finalidade é melhorar a detecção de intenções e a classificação de entidades.

## <a name="how-to-use-phrase-lists"></a>Como usar as listas de frase
No [tutorial de entidade simples](luis-quickstart-primary-and-secondary-data.md) do aplicativo de Recursos Humanos, o aplicativo usa a lista de frases **Trabalho** para tipos de trabalho, como programador, carpinteiro e secretária. Se você rotular um desses valores como uma entidade de aprendizado de máquina, o LUIS aprenderá a reconhecer os outros. 

Uma lista de frases pode ser intercambiável ou não. Uma lista de frases *intercambiáveis* serve para valores que são sinônimos, e uma lista de frases *não intercambiáveis* destina-se a uma lista de vocabulário específica de um aplicativo. Conforme expande a lista de frases do vocabulário do aplicativo, você pode descobrir que alguns termos têm muitas formas (sinônimos). Divida esses termos em outra lista de frases intercambiáveis. 

|Tipo de lista|Finalidade|
|--|--|
|Intercambiável|Sinônimos ou palavras que, quando trocadas por outra palavra da lista, têm a mesma intenção e extração de entidade.|
|Não intercambiável|Vocabulário do aplicativo, específico de seu aplicativo, mais do que outras palavras do idioma de modo geral.|

Listas de frases não só ajudam com a detecção de entidade, mas também com a classificação intenção, em que o uso de termos não intercambiáveis faz sentido, como ao adicionar palavras de vocabulário que não são conhecidas no idioma inglês.


<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="phrase-lists-help-identify-simple-interchangeable-entities"></a>Listas de frases ajudam a identificar entidades intercambiáveis simples
Listas de frases intercambiáveis são uma boa maneira de ajustar o desempenho do aplicativo do LUIS. Se o seu aplicativo tiver problemas para prever declarações da intenção correta, ou reconhecer entidades, pense se a declaração contém palavras incomuns ou que podem ter significado ambíguo. Essas palavras são boas candidatas para inclusão em uma lista de frases.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Listas de frases ajudam a identificar intenções entendendo melhor o contexto
Uma lista de frases não é uma instrução para o LUIS executar uma correspondência estrita ou sempre rotular todos os termos na lista de frases exatamente da mesma forma. Ela é simplesmente uma dica. Por exemplo, você poderia ter uma lista de frases que indica que "Patti" e "Selma" são nomes, mas o LUIS ainda pode usar as informações contextuais para reconhecer que eles significam algo diferente em "Fazer uma reserva de jantar para 2 no Patti's Diner" e "Localizar trajeto de automóvel até Selma, Geórgia". 

Adicionar uma lista de frases é uma alternativa à adição de mais declarações de exemplo para uma intenção. 

## <a name="an-interchangeable-phrase-list"></a>Uma lista de frases intercambiáveis
Use uma lista de frases intercambiáveis quando a lista de palavras ou fases criar uma classe ou um grupo. Um exemplo é uma lista de meses como "janeiro", "fevereiro", "março", ou de nomes como "João", "Maria", "Pedro".  Essas listas são intercambiáveis em que o enunciado seria rotulado com a mesma intenção ou entidade, se uma palavra diferente na lista de frases fosse usada. Por exemplo, se "mostrar o calendário de janeiro" tiver a mesma intenção que "mostrar o calendário de fevereiro", as palavras devem estar em uma lista intercambiável. 

## <a name="a-non-interchangeable-phrase-list"></a>Uma lista de frases não intercambiáveis
Use uma lista de frases não intercambiáveis para palavras ou frases não sinônimos que podem ser agrupadas no domínio. 

Como um exemplo, use uma lista de frases não intercambiáveis para palavras raras, proprietárias e estrangeiras. O LUIS pode ser incapaz de reconhecer palavras raras e proprietárias, assim como palavras estrangeiras (fora da cultura do aplicativo). A configuração não intercambiável indica que o conjunto de palavras raras forma uma classe que o LUIS deve aprender a reconhecer, mas elas não são sinônimos ou intercambiáveis entre si.

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>Quando usar listas de frase versus entidades de lista
Embora uma lista de frases e as entidades de lista possam afetar as declarações em todas as intenções, cada uma faz isso de maneira diferente. Use uma lista de frases para afetar a pontuação de previsão da intenção. Use uma entidade de lista para afetar a extração de entidade de uma correspondência exata do texto. 

### <a name="use-a-phrase-list"></a>Usar uma lista de frases
Com uma lista de frases, o LUIS ainda pode levar o contexto em consideração e generalizar para identificar os itens que são semelhantes, mas que não são uma correspondência exata, como itens em uma lista. Se você precisar que o seu aplicativo LUIS generalize e identifique novos itens em uma categoria, use uma lista de frases. 

Quando você quiser ser capaz de reconhecer novas instâncias de uma entidade, como o agendador de reunião que deve reconhecer os nomes de novos contatos, ou um aplicativo de inventário que deve reconhecer novos produtos, use outro tipo de entidade de aprendizado de máquina como uma entidade simples ou hierárquica. Em seguida, crie uma lista de palavras e frases que ajuda o LUIS a encontrar outras palavras semelhantes para a entidade. Essa lista orienta o LUIS a reconhecer exemplos da entidade adicionando outro significado ao valor dessas palavras. 

Listas de frase são como um vocabulário específico ao domínio que ajudam a melhorar a qualidade da compreensão de intenções e entidades. Um uso comum de uma lista de frases é para substantivos, como nomes de cidades. Um nome de cidade pode ter várias palavras, incluindo hifens ou apóstrofos.
 
### <a name="dont-use-a-phrase-list"></a>Não usar uma lista de frases 
Uma entidade de lista define explicitamente cada valor que uma entidade pode assumir, e identifica somente os valores com correspondência exata. Uma entidade de lista pode ser apropriada para um aplicativo no qual todas as instâncias de uma entidade são conhecidas e não são alteradas com frequência. Exemplos são os itens do menu de um restaurante que muda com pouca frequência. Se você precisar de uma correspondência exata do texto de uma entidade, não use uma lista de frases. 

## <a name="best-practices"></a>Práticas recomendadas
Conheça as [práticas recomendadas](luis-concept-best-practices.md).

## <a name="next-steps"></a>Próximas etapas

Veja [Adicionar recursos](luis-how-to-add-features.md) para saber como adicionar recursos ao seu aplicativo LUIS.
