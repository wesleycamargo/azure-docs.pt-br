---
title: Painel do aplicativo
titleSuffix: Language Understanding - Azure Cognitive Services
description: Saiba mais sobre o painel do aplicativo, uma ferramenta de relatório visualizado que permite monitorar seus aplicativos em uma visão rápida.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: c173152d0a59e391fe77ee855311a867a1b2b6c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60198353"
---
# <a name="model-and-usage-statistics-in-the-dashboard"></a>Estatísticas de uso e o modelo no painel
O painel do aplicativo permite que você monitore seu aplicativo em uma visão rápida. O **Painel** é exibido quando você abre um aplicativo clicando no nome do aplicativo na página **Meus Aplicativos** e selecione **Painel** no painel superior. 

> [!CAUTION]
> Se você quiser as métricas mais recentes para o LUIS, precisará:
> * Usar uma [chave do ponto de extremidade](luis-how-to-azure-subscription.md) do LUIS criado no Azure
> * Usar a chave do ponto de extremidade do LUIS para todas as solicitações de ponto de extremidade incluindo [API](https://aka.ms/luis-endpoint-apis) e bot do LUIS
> * Use uma chave do ponto de extremidade diferente para cada aplicativo do LUIS. Não use uma chave de ponto de extremidade para todos os aplicativos. A chave do ponto de extremidade é rastreada no nível da chave, não no nível do aplicativo.  

A página **Painel** oferece uma visão geral do aplicativo LUIS, incluindo o estado do modelo atual, além do uso do [ponto de extremidade](luis-glossary.md#endpoint) ao longo do tempo. 
  
## <a name="app-status"></a>Status do aplicativo
O painel exibe o treinamento do aplicativo e o status de publicação, incluindo a data e a hora quando o aplicativo foi treinado e publicado pela última vez.  

![Painel – Status do aplicativo](./media/luis-how-to-use-dashboard/app-state.png)

## <a name="model-data-statistics"></a>Estatísticas de dados do modelo
O painel exibe o número total de intenções, entidades e declarações rotuladas existentes no aplicativo. 

![Estatísticas de dados do aplicativo](./media/luis-how-to-use-dashboard/app-model-count.png)

## <a name="endpoint-hits"></a>Ocorrências de ponto de extremidade
O painel exibe o total de ocorrências de ponto de extremidade que o aplicativo do LUIS recebe e permite que você exiba as ocorrências dentro de um período especificado. O número total de ocorrências exibido é a soma das ocorrências do ponto de extremidade que usam uma [Chave do ponto de extremidade](./luis-concept-keys.md#endpoint-key) e ocorrências do ponto de extremidade que usam uma [Chave de criação](./luis-concept-keys.md#authoring-key).

![Ocorrências de ponto de extremidade](./media/luis-how-to-use-dashboard/dashboard-endpointhits.png)

> [!NOTE] 
> A contagem de ocorrências de ponto de extremidade mais atual está no portal do Azure na visão geral do serviço do LUIS. 
 
### <a name="total-endpoint-hits"></a>Total de ocorrências de ponto de extremidade
O número total de ocorrências de ponto de extremidade recebidas para o seu aplicativo desde a criação do aplicativo até a data atual.

### <a name="endpoint-hits-per-period"></a>Ocorrências de ponto de extremidade por período
O número de ocorrências recebidas em um período anterior, exibidas por dia. Os pontos entre as datas de início e término representam os dias que caem nesse período. Passe o ponteiro do mouse sobre cada ponto para ver a contagem de ocorrências em cada dia dentro do período. 

Para selecionar um período para ser exibido no gráfico:
 
1. Clique em **Configurações Adicionais** ![botão Configurações Adicionais](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) para acessar a lista de períodos. Você pode selecionar os períodos que variam de uma semana até um ano. 

    ![Ocorrências de ponto de extremidade por período](./media/luis-how-to-use-dashboard/timerange.png)

2. Selecione um período da lista e clique na seta Voltar ![Seta Voltar](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) para exibir o gráfico.

### <a name="key-usage"></a>Uso de chave
O número de acessos consumidos a partir da chave de ponto de extremidade do aplicativo. Para obter mais informações sobre chaves de ponto de extremidade, consulte [Chaves no LUIS](luis-concept-keys.md). 
  
## <a name="intent-breakdown"></a>Divisão de intenções
A **Divisão de intenções** exibe uma divisão de intenções com base nas declarações rotuladas ou ocorrências do ponto de extremidade. Este gráfico de resumo mostra a importância relativa de cada intenção no aplicativo. Quando você passa o ponteiro do mouse sobre uma fatia, verá o nome da intenção e a porcentagem que ela representa da contagem total das ocorrências rotuladas de declarações/pontos de extremidade. 

![Divisão de intenções](./media/luis-how-to-use-dashboard/intent-breakdown.png)

## <a name="entity-breakdown"></a>Divisão de entidades
O painel exibe uma divisão de entidades com base nas declarações rotuladas ou ocorrências do ponto de extremidade. Este gráfico de resumo mostra a importância relativa de cada entidade no aplicativo. Quando você passa o ponteiro do mouse sobre uma fatia, verá o nome da entidade e a porcentagem nas ocorrências rotuladas de declarações/pontos de extremidade. 

![Divisão de entidades](./media/luis-how-to-use-dashboard/entity-breakdown.png)

