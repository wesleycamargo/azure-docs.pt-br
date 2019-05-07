---
title: Painel - reconhecimento vocal
titleSuffix: Azure Cognitive Services
description: Corrigi as intenções com o painel de resumo de análise, uma ferramenta de relatório visualizado.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: a518a697369ff74689a0c4ac05af96453b6a5ca4
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072574"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Como usar o painel para melhorar seu aplicativo

Localize e corrija problemas com as intenções do seu aplicativo treinado quando você estiver usando declarações de exemplo. O painel de resumo exibe informações gerais do aplicativo, com realces de tentativas que devem ser corrigidas. 

Examine o painel análise é um processo iterativo, repetido como alterar e melhorar seu modelo.

Esta página não terá análise relevante para aplicativos que não têm quaisquer declarações de exemplo em intenções, conhecidas como _somente padrão_ aplicativos. 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Quais problemas podem ser corrigidos no painel?

Três problemas abordados no painel de controle são:

|Problema|Cor do gráfico|Explicação|
|--|--|--|
|Desequilíbrio de dados|-|Isso ocorre quando a quantidade de declarações de exemplo varia significativamente. Todas as intenções precisam ter _aproximadamente_ o mesmo número de declarações de exemplo - exceto a intenção None. Ele deve ter apenas 10 a 15% da quantidade total de declarações no aplicativo.<br><br> Se os dados são desequilibrados, mas a intenção precisão estiver acima de determinado limite, esse desequilíbrio não será relatado como um problema.<br><br>**Iniciar com esse problema - ele pode ser a causa raiz dos outros problemas.**|
|Previsões claras|Laranja|Isso ocorre quando a intenção superior e pontuações da intenção próxima são próximos o suficiente para que eles podem ativar o próximo treinamento, devido a [amostragem negativa](luis-how-to-train.md#train-with-all-data) ou mais declarações de exemplo adicionadas à intenção. |
|Previsões incorretas|Vermelho|Isso ocorre quando uma expressão de exemplo não é previsível para a intenção de rotulado (a intenção é em).|

Previsões corretas são representadas com a cor azul.

O painel de resumo mostra esses problemas e informa quais tentativas são afetadas e sugere que você deve fazer para melhorar o aplicativo. 

## <a name="before-app-is-trained"></a>Antes que o aplicativo é treinado 

Antes de treinar o aplicativo, o painel de resumo não contém alguma sugestão de correções. Treine seu aplicativo para ver essas sugestões.  

## <a name="check-your-publishing-status"></a>Verificar o status da publicação

O **status da publicação** cartão contém informações sobre o Active Directory versão de publicação pela última vez. 

Verifique se a versão ativa é a versão que você deseja corrigir. 

![Os serviços externos do aplicativo do painel de resumo mostra, publicados regiões e agregados ocorrências de ponto de extremidade.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

Isso também mostra quaisquer serviços externos, regiões publicados e agregados de ocorrências de ponto de extremidade. 

## <a name="review-training-evaluation"></a>Avaliação de treinamento de revisão

O **avaliação de treinamento** cartão contém o resumo agregado da precisão geral do seu aplicativo por área. A pontuação indica a qualidade de intenção. 

![O cartão de avaliação de treinamento contém a primeira área de informações sobre a precisão geral do seu aplicativo.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

O gráfico indica as tentativas corretamente previstas e as áreas de problema com diferentes cores. Como melhorar o aplicativo com as sugestões, esse aumento de pontuação. 

As correções sugeridas são separadas pelo tipo de problema e são mais significativos para seu aplicativo. Se você preferir analisar e corrigir problemas por intenção, use o **[intenções com erros](#intents-with-errors)** cartão na parte inferior da página. 

Cada área de problema tem intenções que precisam ser corrigidos. Quando você seleciona o nome da intenção, o **intenção** página é aberta com um filtro aplicado as declarações. Esse filtro permite que você se concentre nas declarações que estão causando o problema.

### <a name="compare-changes-across-versions"></a>Compare as alterações entre versões

Crie uma nova versão antes de fazer alterações no aplicativo. Na nova versão, faça as alterações sugeridas para declarações de exemplo da intenção e, em seguida, treinar novamente. Na página do painel **avaliação de treinamento** cartão, use o **Mostrar alterações da versão treinado** para comparar as alterações. 

![Compare as alterações entre versões](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Corrigir versão pela adição ou edição de declarações de exemplo e a readaptação

O principal método de correção de seu aplicativo será adicionar ou editar as declarações de exemplo e treinar novamente. As declarações de novas ou alteradas precisam seguir as diretrizes para [variadas declarações](luis-concept-utterance.md).

Adicionar declarações de exemplo deve ser feita por alguém que:

* tem um alto grau de compreensão de quais declarações são nas intenções diferentes
* Saiba como declarações em uma intenção podem ser confundidas com a intenção de outro
* é possível decidir se dois intenções, que frequentemente são confundidas uns com os outros, devem ser recolhidas em uma única tentativa, e os diferentes dados extraídos com entidades

### <a name="patterns-and-phrase-lists"></a>Padrões e listas de frase

A página de análise não indica quando usar [padrões](luis-concept-patterns.md) ou [frase listas](luis-concept-feature.md). Se você adicioná-los, ele pode ajudar com previsões incorretas ou confusa, mas não ajudará com o desequilíbrio de dados. 

### <a name="review-data-imbalance"></a>Desequilíbrio de dados de revisão

Iniciar com esse problema - ele pode ser a causa raiz dos outros problemas.

O **desequilíbrio dados** intenção lista mostra as intenções que precisam de mais declarações para corrigir o desequilíbrio de dados. 

**Para corrigir esse problema**:

* Adicione mais declarações à intenção e treinar novamente. 

Não adicione declarações como a intenção None, a menos que o que é sugerido no painel de resumo.

> [!Tip]
> Use a terceira seção na página de **declarações por intenção** com o **declarações (número)** definir, como um guia visual rápido do que as intenções precisam de mais declarações.  
    ![Use 'Declarações (número)' para localizar as intenções com desequilíbrio de dados.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Previsões incorretas de revisão

O **previsão incorreta** intenção lista mostra as intenções com declarações, que são usadas como exemplos para um propósito específico, mas estão previstas para propósitos diferentes. 

**Para corrigir esse problema**:

* Edite declarações para ser mais específico para a intenção e treinar novamente.
* Combine as intenções se declarações são alinhadas muito perto e treinar novamente.

### <a name="review-unclear-predictions"></a>Analisar as previsões claras

O **previsão claro** intenção lista mostra as intenções com declarações com pontuações de previsão que não é suficiente maneira do seu rival mais próximo, o que a intenção superior para a expressão pode ser alterada no próximo treinamento, devido a [ amostragem negativa](luis-how-to-train.md#train-with-all-data).

**Para corrigir esse problema**;

* Edite declarações para ser mais específico para a intenção e treinar novamente.
* Combine as intenções se declarações são alinhadas muito perto e treinar novamente.

## <a name="utterances-per-intent"></a>Declarações por intenção

Este cartão mostra a integridade geral do aplicativo entre as tentativas. Como corrigir as intenções e treinar novamente, continue olhei esse cartão para problemas.

O gráfico a seguir mostra um aplicativo bem equilibrado com quase nenhuma problemas a serem corrigidos.

![O gráfico a seguir mostra um aplicativo bem equilibrado com quase nenhuma problemas a serem corrigidos.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

O gráfico a seguir mostra um aplicativo mal equilibrado com muitos problemas a serem corrigidos.

![O gráfico a seguir mostra um aplicativo bem equilibrado com quase nenhuma problemas a serem corrigidos.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Passe o mouse sobre a barra do cada tentativa para obter informações sobre a intenção. 

![O gráfico a seguir mostra um aplicativo bem equilibrado com quase nenhuma problemas a serem corrigidos.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

Use o **classificar por** recursos para organizar as tentativas por tipo de problema para que você possa se concentrar nas tentativas de mais de um problema com esse problema. 

## <a name="intents-with-errors"></a>Tentativas com erros

Esse cartão permite que você analise os problemas para um propósito específico. A exibição padrão desse cartão é as intenções mais problemáticas, portanto, você saberá onde concentrar seus esforços.

![As tentativas com cartão de erros permite que você analise os problemas para um propósito específico. O cartão é filtrado para as tentativas mais problemáticas, por padrão, para que você saiba onde concentrar seus esforços.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

O gráfico de rosca superior mostra os problemas com a intenção entre os tipos de problema de três. Se houver problemas nos tipos de problema de três, cada tipo tem seu próprio gráfico abaixo, junto com quaisquer tentativas rival. 

### <a name="filter-intents-by-issue-and-percentage"></a>Tentativas de filtro pelo problema e porcentagem

Esta seção do cartão permite que você localize as declarações de exemplo estão ficando fora do seu limite de erro. O ideal é que você deseja previsões corretas para ser significativo. Essa porcentagem é voltada para os clientes e negócios. 

Determine as porcentagens de limite que você estiver familiarizado com para a sua empresa. 

O filtro permite que você encontre intenções com problema específico:

|Filter|Porcentagem sugerida|Finalidade|
|--|--|--|
|Intenções mais problemáticas|-|**Comece aqui** -corrigindo as declarações nessa intenção melhorará o aplicativo mais do que outras correções.|
|Previsões corretas abaixo|60%|Esta é a porcentagem de declarações na intenção selecionada que estão corretos, mas têm uma pontuação de confiança abaixo do limite. |
|Previsões claras acima|15%|Esta é a porcentagem de declarações na intenção selecionada que são confundidos com a intenção de rival mais próxima.|
|Acima de previsões incorretas|15%|Esta é a porcentagem de declarações na intenção selecionada que foram previstos incorretamente. |

### <a name="correct-prediction-threshold"></a>Limite de previsão correta

O que é uma pontuação de confiança de previsão confiante para você? No início do desenvolvimento de aplicativos, 60% pode ser o destino. Use o **corrigir previsões abaixo** com a porcentagem de 60% para localizar quaisquer declarações na intenção selecionada que precisam ser corrigidos.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Limite de previsão claro ou incorreto

Esses dois filtros permitem que você encontre declarações na intenção ultrapassando o limite selecionada. Você pode pensar essas dois porcentagens como porcentagens de erro. Se você estiver familiarizado com uma taxa de erro de 10 a 15% para previsões, defina o limite de filtro para 15% para localizar todas as declarações acima desse valor. 

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar recursos do Azure](luis-how-to-azure-subscription.md)
