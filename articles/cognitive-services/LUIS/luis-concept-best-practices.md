---
title: Noções básicas sobre melhores práticas do LUIS – Azure | Microsoft Docs
description: Conheça as melhores práticas do LUIS para obter os melhores resultados.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: 6c4e4c7acb5083e758aa7c6b94e2464a85ae8e9e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37951100"
---
# <a name="best-practices"></a>Práticas recomendadas
Use o processo de criação de aplicativos para criar seu aplicativo LUIS. 

* Criar modelo de linguagem
* Adicione alguns exemplos de declaração de treinamento (10-15 por intenção)
* Publicar 
* Testar do ponto de extremidade 
* Adicionar recursos

Quando seu aplicativo for [publicado](luis-how-to-publish-app.md), use o ciclo de criação de adicionar recursos, publicar e testar do ponto de extremidade. Não comece o próximo ciclo de criação adicionando mais declarações de exemplo. Isso não permite que o LUIS aprenda seu modelo com declarações de usuário do mundo real. 

Para o LUIS ser eficiente em seu trabalho de aprendizado, não expanda as declarações até que o conjunto atual de declarações de exemplo e de ponto de extremidade esteja retornando pontuações confiantes e de alta previsão. Melhore as pontuações usando aprendizado ativo, [padrões](luis-concept-patterns.md) e [listas de frase](luis-concept-feature.md). 

## <a name="do-and-dont"></a>O que fazer e o que não fazer
A lista a seguir inclui melhores práticas para aplicativos LUIS:

|Certo|Errado|
|--|--|
|[Definir intenções distintas](#do-define-distinct-intents) |[Adicionar muitos exemplos de declaração a intenções](#dont-add-many-example-utterances-to-intents) |
|[Localizar um ponto ideal entre muito genérico e muito específico para cada intenção](#do-find-sweet-spot-for-intents)|[Usar o LUIS como uma plataforma de treinamento](#dont-use-luis-as-a-training-platform)|
|[Criar seu aplicativo iterativamente](#do-build-the-app-iteratively)|[Adicionar muitas declarações de exemplo do mesmo formato, ignorando os outros formatos](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Adicionar listas de frase e padrões em iterações posteriores](#do-add-phrase-lists-and-patterns-in-later-iterations)|[Misturar a definição de intenções e entidades](#dont-mix-the-definition-of-intents-and-entities)|
|[Adicione declarações de exemplo à intenção None](#do-add-example-utterances-to-none-intent)|[Criar listas de frase com todos os valores possíveis](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Aproveitar o recurso de sugestão para aprendizado ativo](#do-leverage-the-suggest-feature-for-active-learning)|[Adicionar muitos padrões](#dont-add-many-patterns)|
|[Monitorar o desempenho do seu aplicativo](#do-monitor-the-performance-of-your-app)|[Treinar e publicar com cada declaração de exemplo única adicionada](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Defina intenções distintas
Verifique se o vocabulário para cada intenção é apenas para essa intenção e não está se sobrepondo a uma intenção diferente. Por exemplo, se desejar ter um aplicativo que manipula organizações de viagem como voos por companhia aérea e hotéis, será possível optar por tê-las como intenções separadas ou a mesma intenção com entidades para dados específicos dentro da declaração.

Se o vocabulário entre duas intenções for o mesmo, combine a intenção e use entidades. 

Considere os seguintes exemplos de declaração:

```
Book a flight
Book a hotel
```

"Reservar um voo" e "Reservar hotel" usam o mesmo vocabulário de "reservar um ". Isso está se sobrepondo, então ela deve ser a mesma intenção com as diferentes palavras de entidades extraídas de voo e hotel. 

## <a name="do-find-sweet-spot-for-intents"></a>Localizar ponto ideal para intenções
Use dados de previsão do LUIS para determinar se suas intenções estiverem se sobrepondo. Intenções sobrepostas confundem o LUIS. O resultado é que a principal intenção de pontuação está muito perto de outra intenção. Como o LUIS não usa o mesmo caminho exato por meio dos dados para treinamento a cada vez, uma intenção sobreposta tem uma chance de ser a primeira ou a segunda no treinamento. Convém que a pontuação da declaração para cada intenção esteja mais distante para que isso não aconteça. A boa distinção para intenções deve resultar na principal intenção esperada toda vez. 
 
## <a name="do-build-the-app-iteratively"></a>Criar o aplicativo iterativamente
Mantenha um conjunto de teste *cego* que não seja usado como [declarações de exemplo](luis-concept-utterance.md) ou declarações de ponto de extremidade. Continue melhorando o aplicativo para seu conjunto de testes. Adapte o conjunto de teste para refletir declarações de usuário real. Use este conjunto de teste cego para avaliar cada iteração. 

Os desenvolvedores devem ter três conjuntos de dados. O primeiro é o exemplo de declarações para criar o modelo. O segundo é para testar o modelo no ponto de extremidade. O terceiro são os dados do teste cego usados no [teste de lote](luis-how-to-batch-test.md). Esse último conjunto não é usado no treinamento do aplicativo nem enviado no ponto de extremidade.  

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>Adicione listas de frase e padrões em iterações posteriores
As [listas de frase](luis-concept-feature.md) permitem que você defina dicionários de palavras relacionadas ao seu domínio de aplicativo. Propague sua lista de frase com poucas palavras e use o recurso de sugestão para que o LUIS conheça mais palavras no vocabulário. Não adicione toda palavra ao vocabulário, pois a lista de frases não é uma correspondência exata. 

Declarações de usuário real do ponto de extremidade, muito semelhantes umas às outras, podem revelar padrões de escolha e de posicionamento de palavras. O recurso [padrão](luis-concept-patterns.md) usa esta escolha e posicionamento de palavras junto com expressões regulares para melhorar a precisão da previsão. Uma expressão regular no padrão possibilita palavras e pontuação que você pretende ignorar enquanto ainda estiver fazendo correspondência com o padrão. 

Use a sintaxe opcional do padrão para pontuação para que a pontuação possa ser ignorada.

Não aplique essas práticas antes de o seu aplicativo receber solicitações de ponto de extremidade, porque isso distorce a confiança.  

## <a name="do-add-example-utterances-to-none-intent"></a>Adicionar declarações de exemplo à intenção None
Essa é a intenção de fallback, indicava tudo fora do seu aplicativo. Adicione um exemplo de declaração à intenção None para todos os 10 exemplos de declarações no restante do seu aplicativo LUIS.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Aproveite o recurso de sugestão para aprendizado ativo
Use **Examinar declarações de ponto de extremidade** do [aprendizado ativo](luis-how-to-review-endoint-utt.md) regularmente, em vez de adicionar mais exemplos de declarações a intenções. Como o aplicativo está constantemente recebendo declarações de ponto de extremidade, essa lista está crescendo e mudando.

## <a name="do-monitor-the-performance-of-your-app"></a>Monitore o desempenho do seu aplicativo
Monitore a precisão da previsão usando um conjunto de teste. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Não adicione muitos exemplos de declaração a intenções
Depois que o aplicativo for publicado, adicione apenas declarações do aprendizado ativo no processo iterativo. Se as declarações forem semelhantes demais, adicione um padrão. 

## <a name="dont-use-luis-as-a-training-platform"></a>Não use o LUIS como uma plataforma de treinamento
O LUIS é específico para o domínio de um modelo de linguagem. Ele não é destinado para funcionar como uma plataforma de treinamento geral. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Não adicionar muitas declarações de exemplo do mesmo formato, ignorando os outros formatos
O LUIS espera variações nas declarações de uma intenção. As declarações podem variar e ter o mesmo significado geral. As variações podem incluir o comprimento da declaração, a escolha de palavras e o posicionamento de palavras. 

|Não use o mesmo formato|Use o formato variável|
|--|--|
|Comprar uma passagem para Seattle<br>Comprar uma passagem para Paris<br>Comprar uma passagem para Orlando|Comprar 1 passagem para Seattle<br>Reservar duas poltronas no voo para Paris na próxima segunda-feira<br>Eu gostaria de reservar três passagens para Orlando para a semana do saco cheio|

A segunda coluna usa diferentes verbos (comprar e reservar), diferentes quantidades (1, duas, 3) e diferentes organizações de palavras, mas todas têm a mesma intenção de comprar passagens de avião para viagem. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Não misture a definição de intenções e entidades
Criar uma intenção para qualquer ação que seu bot usará. Use entidades como parâmetros que possibilitam a ação. 

Para obter um chatbot que comprará voos de companhias aéreas, crie uma intenção **BookFlight**. Não crie uma intenção para toda companhia aérea ou para todo destino. Use essas partes de dados como [entidades](luis-concept-entity-types.md) e marque-as nos exemplos de declarações. 

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Não crie listas de frases com todos os valores possíveis
Forneça alguns exemplos nas [listas de frases](luis-concept-feature.md), mas não em toda palavra. O LUIS generaliza e leva em conta o contexto. 

## <a name="dont-add-many-patterns"></a>Não adicione muitos padrões
Não adicione [padrões](luis-concept-patterns.md) demais. O LUIS destina-se a aprender rapidamente com menos exemplos. Não sobrecarregue o sistema desnecessariamente.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Não treine nem publique com toda declaração de exemplo única
Adicione 10 ou 15 declarações antes do treinamento e da publicação. Isso permite ver o impacto sobre a precisão da previsão. Adicionar uma única declaração pode não ter um impacto visível sobre a pontuação. 

## <a name="next-steps"></a>Próximas etapas

* Saiba como [planejar seu aplicativo](luis-how-plan-your-app.md) no aplicativo LUIS.