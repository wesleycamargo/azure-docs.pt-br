---
title: Noções básicas sobre recursos em aplicativos LUIS no Azure | Microsoft Docs
description: Saiba mais sobre os recursos que ajudam a melhorar o desempenho de um aplicativo LUIS. Os recursos incluem listas de frase e padrões para reconhecer expressões regulares.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/18/2018
ms.author: v-geberr
ms.openlocfilehash: 416fadaf52d7a71dcaab81aab3bf6099213e5af5
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35364979"
---
# <a name="phrase-list-features-in-luis"></a>Recursos de lista de frase no LUIS

No aprendizado de máquina, um *recurso* é um traço ou atributo específico de dados que seu sistema observa. 

Adicione recursos a um modelo de linguagem para fornecer dicas sobre como reconhecer a entrada que você deseja identificar ou classificar. Os recursos ajudam o LUIS a reconhecer intenções e entidades, mas não são intenções ou entidades em si. Em vez disso, os recursos podem fornecer exemplos de termos relacionados.  

## <a name="what-is-a-phrase-list-feature"></a>O que é um recurso de lista de frases?
Uma lista de frases inclui um grupo de valores (palavras ou frases) que pertencem à mesma classe e devem ser tratadas de maneira semelhante (por exemplo, nomes de cidades ou produtos). O que o LUIS aprende sobre um deles é aplicado automaticamente aos outros também. Isso não é uma [entidade de lista](luis-concept-entity-types.md#types-of-entities) (correspondências exatas de texto) de palavras correspondentes.

Uma lista de frase adiciona ao vocabulário do domínio de aplicativo como um segundo sinal ao LUIS sobre essas palavras.

## <a name="how-to-use-phrase-lists"></a>Como usar as listas de frase
Em um aplicativo de agente de viagem, crie uma lista de frases denominada "Cidades" contendo os valores de Londres, Paris e Cairo. Se você rotular um desses valores como uma entidade simples em uma [declaração de exemplo](luis-how-to-add-example-utterances.md#add-simple-entity-label) em uma intenção, o LUIS aprenderá a reconhecer os outros. 

Uma lista de frases pode ser intercambiável ou não. Uma lista de frases *intercambiável* serve para valores que são sinônimos, e uma lista de frases *não intercambiável* destina-se a valores que não são sinônimos, mas são semelhantes de outra maneira. 

## <a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>A lista de frases ajuda a identificar as entidades intercambiáveis simples
As listas de frase intercambiáveis são uma boa maneira de ajustar o desempenho do seu aplicativo LUIS. Se o seu aplicativo tiver problemas para prever declarações da intenção correta, ou reconhecer entidades, pense se a declaração contém palavras incomuns ou que podem ter significado ambíguo. Essas palavras são boas candidatas para inclusão em uma lista de frases.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Listas de frases ajudam a identificar intenções entendendo melhor o contexto
Use listas de frases para palavras raras, proprietárias e estrangeiras. Talvez o LUIS não reconheça palavras raras e proprietárias, bem como palavras estrangeiras (fora da cultura do aplicativo) e, portanto, elas devem ser adicionadas a uma lista de frases. Essa lista de frases deve ser marcada como não intercambiável, para indicar que o conjunto de palavras raras forma uma classe a qual o LUIS deve aprender a reconhecer, mas que não são sinônimos nem intercambiáveis.

Uma lista de frases não é uma instrução para o LUIS executar uma correspondência estrita ou sempre rotular todos os termos na lista de frases exatamente da mesma forma. Ela é simplesmente uma dica. Por exemplo, você poderia ter uma lista de frases que indica que "Patti" e "Selma" são nomes, mas o LUIS ainda pode usar as informações contextuais para reconhecer que eles significam algo diferente em "Fazer uma reserva de jantar para 2 no Patti's Diner" e "Localizar trajeto de automóvel até Selma, Geórgia". 

Adicionar uma lista de frases é uma alternativa à adição de mais declarações de exemplo para uma intenção. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>Quando usar listas de frase versus entidades de lista
Embora uma lista de frases e as entidades de lista possam afetar as declarações em todas as intenções, cada uma faz isso de maneira diferente. Use uma lista de frases para afetar a pontuação de previsão da intenção. Use uma entidade de lista para afetar a extração de entidade de uma correspondência exata do texto. 

### <a name="use-a-phrase-list"></a>Usar uma lista de frases
Com uma lista de frases, o LUIS ainda pode levar o contexto em consideração e generalizar para identificar os itens que são semelhantes, mas que não são uma correspondência exata, como itens em uma lista. Se você precisar que o seu aplicativo LUIS generalize e identifique novos itens em uma categoria, use uma lista de frases. 

Quando você quiser ser capaz de reconhecer novas instâncias de uma entidade, como o agendador de reunião que deve reconhecer os nomes de novos contatos, ou um aplicativo de inventário que deve reconhecer novos produtos, use outro tipo de entidade de aprendizado de máquina como uma entidade simples ou hierárquica. Em seguida, crie uma lista de palavras e frases que ajuda o LUIS a encontrar outras palavras semelhantes para a entidade. Essa lista orienta o LUIS a reconhecer exemplos da entidade adicionando outro significado ao valor dessas palavras. 

Listas de frase são como um vocabulário específico ao domínio que ajudam a melhorar a qualidade da compreensão de intenções e entidades. Um uso comum de uma lista de frases é para substantivos, como nomes de cidades. Um nome de cidade pode ter várias palavras, incluindo hifens ou apóstrofos.
 
### <a name="dont-use-a-phrase-list"></a>Não usar uma lista de frases 
Uma entidade de lista define explicitamente cada valor que uma entidade pode assumir, e identifica somente os valores com correspondência exata. Uma entidade de lista pode ser apropriada para um aplicativo no qual todas as instâncias de uma entidade são conhecidas e não mudam com frequência, como itens de alimento no cardápio de um restaurante que raramente são alterados. Se você precisar de uma correspondência exata do texto de uma entidade, não use uma lista de frases. 

## <a name="best-practices"></a>Práticas recomendadas
Conheça as [práticas recomendadas](luis-concept-best-practices.md).

## <a name="next-steps"></a>Próximas etapas

Veja [Adicionar recursos](luis-how-to-add-features.md) para saber como adicionar recursos ao seu aplicativo LUIS.