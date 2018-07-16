---
title: Planejar seus aplicativos LUIS | Microsoft Docs
description: Descreva intenções e entidades de aplicativo relevantes e crie seus planos de aplicativo no LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: DeniseMak
manager: hsalama
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: c67a2c16077c9033b52a909360b21cb7f88a5a9d
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344981"
---
# <a name="plan-your-luis-app"></a>Planejar seu aplicativo LUIS

É importante planejar seu aplicativo antes de começar a criá-lo no LUIS. Prepare uma descrição ou esquema das intenções e entidades possíveis relevantes para o tópico específico de domínio do seu aplicativo.  

## <a name="identify-your-domain"></a>Identificar seu domínio
Um aplicativo LUIS gira em torno de um tópico específico do domínio.  Por exemplo, você pode ter um aplicativo de viagem que executa a reserva de tíquetes, voos, hotéis e carros de aluguel. Outro aplicativo pode fornecer conteúdo relacionado a exercícios, acompanhamento de esforços fitness e definição de metas. 

> [!TIP]
> O LUIS oferece [domínios predefinidos](luis-how-to-use-prebuilt-domains.md) para muitos cenários comuns.
> Verifique para ver se é possível usar um domínio predefinido como ponto de partida para seu aplicativo.

## <a name="identify-your-intents"></a>Identificar suas intenções
Pense nas [intenções](luis-concept-intent.md) importantes para a tarefa do seu aplicativo. Vamos usar o exemplo de um aplicativo de viagem, com funções para reservar um voo e verificar o clima no destino do usuário. É possível definir as intenções "BookFlight" e "GetWeather" para essas ações. Em um aplicativo mais complexo com mais funções, você tem mais intenções e deve defini-las com cuidado para não serem muito específicas. Por exemplo, "BookFlight" e "BookHotel" podem precisar ser intenções separadas, mas "BookInternationalFlight" e "BookDomesticFlight" podem ser muito semelhantes.

> [!NOTE]
> É uma melhor prática usar apenas as intenções de que você precisar para executar as funções do seu aplicativo. Se você definir intenções demais, ficará mais difícil para o LUIS classificar declarações corretamente. Se você definir poucas demais, elas podem ser tão genéricas que se sobrepõem.


## <a name="identify-your-entities"></a>Identificar suas entidades
Para reservar um voo, são necessárias algumas informações, como o destino, data, companhia aérea, categoria do tíquete e classe da viagem. É possível adicioná-los como [entidades](luis-concept-entity-types.md), porque são importantes para realizar uma intenção. 

Quando você determina quais entidades usar em seu aplicativo, tenha em mente que há diferentes tipos de entidades para capturar relacionamentos entre tipos de objetos. [Entidades no LUIS](luis-concept-entity-types.md) oferece mais detalhes sobre os tipos diferentes.

### <a name="simple-entity"></a>Entidade simples
Uma entidade simples descreve um único conceito.

![entidade simples](./media/luis-plan-your-app/simple-entity.png)

Confira [Extração de Dados](luis-concept-data-extraction.md#simple-entity-data) para saber mais sobre como extrair a entidade simples da resposta da consulta JSON do ponto de extremidade. Experimente o [início rápido](luis-quickstart-primary-and-secondary-data.md) da entidade simples para saber mais sobre como usar uma entidade simples.

### <a name="hierarchical-entity"></a>Entidade hierárquica
Uma entidade hierárquica é um tipo especial de uma entidade **simples**; definir uma categoria e seus membros na forma de uma relação pai-filho.

![entidade hierárquica](./media/luis-plan-your-app/hierarchical-entity.png)

Confira [Extração de Dados](luis-concept-data-extraction.md#hierarchical-entity-data) para saber mais sobre como extrair a entidade hierárquica da resposta da consulta JSON do ponto de extremidade. Experimente o [início rápido](luis-quickstart-intent-and-hier-entity.md) da entidade hierárquica para saber mais sobre como usar uma entidade hierárquica.

### <a name="composite-entity"></a>Entidade composta
Uma entidade composta é composta por outras entidades que formam partes de um todo. 

![entidade composta](./media/luis-plan-your-app/composite-entity.png)

Confira [Extração de Dados](luis-concept-data-extraction.md#composite-entity-data) para saber mais sobre como extrair a entidade composta da resposta da consulta JSON do ponto de extremidade. Experimente o [tutorial](luis-tutorial-composite-entity.md) da entidade composta para saber mais sobre como usar uma entidade composta.

### <a name="prebuilt-entity"></a>Entidade predefinida
O LUIS oferece [entidades predefinidas](luis-prebuilt-entities.md) para tipos comuns como `Number`, que você pode usar para o número de tíquetes em uma ordem de tíquetes.

![Numerar entidade predefinida](./media/luis-plan-your-app/number-entity.png)

Confira [Extração de Dados](luis-concept-data-extraction.md#prebuilt-entity-data) para saber mais sobre como extrair entidades de expressão regular da resposta da consulta JSON do ponto de extremidade. 

### <a name="list-entity"></a>Entidade de lista 
Uma entidade de lista é uma lista de valores especificada explicitamente. Cada valor é composto por um ou mais sinônimos. Em um aplicativo de viagem, você pode optar por criar uma entidade de lista para representar nomes de aeroporto.

![entidade de lista](./media/luis-plan-your-app/list-entity.png)

Confira [Extração de Dados](luis-concept-data-extraction.md#list-entity-data) para saber mais sobre como extrair entidades de lista da resposta da consulta JSON do ponto de extremidade. Experimente o [início rápido](luis-quickstart-intent-and-list-entity.md) para saber mais sobre como usar uma entidade de lista.

### <a name="regular-expression-entity"></a>Entidade de expressão regular
Uma entidade de expressão regular permite que o LUIS extraia dados de uma declaração com base em uma expressão regex.

![Entidade de expressão regular](./media/luis-plan-your-app/regex-entity.png)

Confira [Extração de Dados](luis-concept-data-extraction.md#regular-expression-entity-data) para saber mais sobre como extrair entidades de expressão regular da resposta da consulta JSON do ponto de extremidade. Experimente o [início rápido](luis-quickstart-intents-regex-entity.md) para saber mais sobre como usar uma entidade de expressão regular.

## <a name="after-getting-endpoint-utterances"></a>Após obter declarações de ponto de extremidade
Depois que o aplicativo obtiver declarações de ponto de extremidade, planeje implementar melhorias de previsão com [aprendizado ativo](label-suggested-utterances.md), [listas de frase](luis-concept-feature.md) e [padrões](luis-concept-patterns.md). 

### <a name="patternany-entity"></a>Entidade pattern.any
Patterns.any é um espaço reservado de comprimento variável usado somente na declaração de modelo de um [padrão](luis-concept-patterns.md) para marcar onde a entidade começa e termina. As declarações de modelo estão em conformidade com a [sintaxe adequada](luis-concept-patterns.md#pattern-syntax) para identificar entidades e texto que pode ser ignorado.


## <a name="next-steps"></a>Próximas etapas
* Confira [Create your first Language Understanding Intelligent Services (LUIS) app][luis-get-started-create-app] (Crie seu primeiro aplicativo LUIS [Serviço Inteligente de Reconhecimento Vocal]) para obter um passo a passo rápido sobre como criar um aplicativo LUIS.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app