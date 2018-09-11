---
title: Planejar seus aplicativos de Compreensão de Idioma (LUIS) - Serviço Cognitivo do Azure | Microsoft Docs
description: Descreva intenções e entidades de aplicativo relevantes e crie seus planos de aplicativo no LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/06/2017
ms.author: diberry
ms.openlocfilehash: 7c8974767621ad574b243fba5c3e49d97ec142e6
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2018
ms.locfileid: "43842265"
---
# <a name="plan-your-luis-app"></a>Planejar seu aplicativo LUIS

É importante planejar seu aplicativo. Identificar seu domínio, inclusive possíveis intenções e entidades que são relevantes para seu aplicativo.  

## <a name="identify-your-domain"></a>Identificar seu domínio
Um aplicativo LUIS gira em torno de um tópico específico do domínio.  Por exemplo, você pode ter um aplicativo de viagem que executa a reserva de tíquetes, voos, hotéis e carros de aluguel. Outro aplicativo pode fornecer conteúdo relacionado a exercícios, acompanhamento de esforços fitness e definição de metas. Identifica o domínio ajuda a localizar palavras ou frases que são importantes para seu domínio.

> [!TIP]
> O LUIS oferece [domínios predefinidos](luis-how-to-use-prebuilt-domains.md) para muitos cenários comuns.
> Verifique para ver se é possível usar um domínio predefinido como ponto de partida para seu aplicativo.

## <a name="identify-your-intents"></a>Identificar suas intenções
Pense nas [intenções](luis-concept-intent.md) importantes para a tarefa do seu aplicativo. Vamos usar o exemplo de um aplicativo de viagem, com funções para reservar um voo e verificar o clima no destino do usuário. É possível definir as intenções "BookFlight" e "GetWeather" para essas ações. Em um aplicativo mais complexo com mais funções, você tem mais intenções e deve defini-las com cuidado para não serem muito específicas. Por exemplo, "BookFlight" e "BookHotel" podem precisar ser intenções separadas, mas "BookInternationalFlight" e "BookDomesticFlight" podem ser muito semelhantes.

> [!NOTE]
> É uma melhor prática usar apenas as intenções de que você precisar para executar as funções do seu aplicativo. Se você definir intenções demais, ficará mais difícil para o LUIS classificar declarações corretamente. Se você definir poucas demais, elas podem ser tão genéricas que se sobrepõem.

## <a name="create-example-utterances-for-each-intent"></a>Crie exemplos de declarações para cada intenção
Depois de determinar as intenções, crie 10 ou 15 declarações de exemplo para cada intenção. Para começar, não tenha menos que este número ou crie muitos enunciados para cada intenção. Cada enunciado deve ser diferente do enunciado anterior. Uma boa variedade nos enunciados inclui contagem global de palavras, escolha de palavras, tempo verbal e pontuação. 

## <a name="identify-your-entities"></a>Identificar suas entidades
Nos exemplos de declarações, identifique as entidades que você deseja extrair. Para reservar um voo, são necessárias algumas informações, como o destino, data, companhia aérea, categoria do tíquete e classe da viagem. Você cria entidades para esses tipos de dados e, em seguida, marca as [entidades](luis-concept-entity-types.md) nas declarações de exemplo porque elas são importantes para realizar uma intenção. 

Quando você determina quais entidades usar em seu aplicativo, tenha em mente que há diferentes tipos de entidades para capturar relacionamentos entre tipos de objetos. [Entidades no LUIS](luis-concept-entity-types.md) oferece mais detalhes sobre os tipos diferentes.

### <a name="simple-entity"></a>Entidade simples
Uma entidade simples descreve um único conceito.

![entidade simples](./media/luis-plan-your-app/simple-entity.png)

Veja [Extração de Dados](luis-concept-data-extraction.md#simple-entity-data) para saber mais sobre como extrair a entidade simples da resposta da consulta JSON do ponto de extremidade. Experimente isto [quickstart](luis-quickstart-primary-and-secondary-data.md) para saber mais sobre como usar uma entidade simples.

### <a name="hierarchical-entity"></a>Entidade hierárquica
Uma entidade hierárquica é um tipo especial de uma entidade **simples**; definir uma categoria e seus membros na forma de uma relação pai-filho. O relacionamento é determinado pelo contexto de dentro do enunciado. Filhos de uma entidade hierárquica também são entidades simples.

![entidade hierárquica](./media/luis-plan-your-app/hierarchical-entity.png)

Veja [Extração de Dados](luis-concept-data-extraction.md#hierarchical-entity-data) para saber mais sobre como extrair a entidade hierárquica da resposta da consulta JSON do ponto de extremidade. Experimente este [início rápido](luis-quickstart-intent-and-hier-entity.md) para saber mais sobre como usar uma entidade hierárquica.

### <a name="composite-entity"></a>Entidade composta
Uma entidade composta é composta por outras entidades que formam partes de um todo. Uma entidade composta contém uma variedade de tipos de entidades.

![entidade composta](./media/luis-plan-your-app/composite-entity.png)

Veja [Extração de Dados](luis-concept-data-extraction.md#composite-entity-data) para saber mais sobre como extrair a entidade composta da resposta da consulta JSON do ponto de extremidade. Experimente este [tutorial](luis-tutorial-composite-entity.md) para aprender mais sobre como usar uma entidade composta.

### <a name="prebuilt-entity"></a>Entidade predefinida
O LUIS fornece [entidades pré-construídas](luis-prebuilt-entities.md) para tipos de dados comuns, como número, dados, endereço de e-mail e URL. Você pode usar a entidade numérica de predefinidos para o número de tíquetes em uma ordem de tíquete.

![Numerar entidade predefinida](./media/luis-plan-your-app/number-entity.png)

Consulte [Data Extraction](luis-concept-data-extraction.md#prebuilt-entity-data) para saber mais sobre como extrair a entidade Prebuilt da resposta da consulta JSON do nó de extremidade. 

### <a name="list-entity"></a>Entidade de lista 
Uma entidade de lista é uma lista de valores especificada explicitamente. Cada valor é composto por um ou mais sinônimos. Em um aplicativo de viagem, você pode optar por criar uma entidade de lista para representar nomes de aeroporto.

![entidade de lista](./media/luis-plan-your-app/list-entity.png)

Veja [Extração de Dados](luis-concept-data-extraction.md#list-entity-data) para saber mais sobre como extrair entidades de lista da resposta da consulta JSON do ponto de extremidade. Experimente este [início rápido](luis-quickstart-intent-and-list-entity.md) para saber mais sobre como usar uma entidade de lista.

### <a name="regular-expression-entity"></a>Entidade de expressão regular
Uma entidade de expressão regular permite que o LUIS extraia dados bem formatados de um enunciado com base em uma expressão regular.

![Entidade de expressão regular](./media/luis-plan-your-app/regex-entity.png)

Confira [Extração de Dados](luis-concept-data-extraction.md#regular-expression-entity-data) para saber mais sobre como extrair entidades de expressão regular da resposta da consulta JSON do ponto de extremidade. Experimente o [início rápido](luis-quickstart-intents-regex-entity.md) para saber mais sobre como usar uma entidade de expressão regular.

## <a name="next-steps"></a>Próximas etapas
Depois que seu aplicativo for treinado, publicado e receber declarações de ponto final, planeje implementar melhorias de previsão com [aprendizado ativo](luis-how-to-review-endoint-utt.md), [listas de frases](luis-concept-feature.md) e [padrões](luis-concept-patterns.md). 


* Consulte [Criar o primeiro aplicativo de LUIS (Serviço Inteligente de Reconhecimento Vocal)](luis-get-started-create-app.md) para obter um breve passo a passo sobre como criar um aplicativo de LUIS.
