---
title: Funis do Azure Application Insights
description: "Saiba como você pode usar Funis para descobrir como os clientes estão interagindo com seu aplicativo."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: mbullwin
ms.openlocfilehash: bbb25af888f34737f6a61cf43890ff248c4cc4de
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2017
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Descobrir como os clientes estão usando seu aplicativo com os Funis do Application Insights

Entender a experiência do cliente é de extrema importância para seus negócios. Se o aplicativo envolver vários estágios, você precisará saber se a maioria dos clientes está progredindo por todo o processo ou se eles estão encerrando o processo em algum momento. A progressão por uma série de etapas em um aplicativo Web é conhecida como *funil*. Use os Funis do Azure Application Insights para obter informações sobre os usuários e monitorar as taxas de conversão passo a passo. 

## <a name="create-your-funnel"></a>Criar o funil
Antes de criar o funil, decida qual pergunta você deseja responder. Por exemplo, você pode querer saber quantos usuários estão exibindo a página inicial, exibindo um perfil de cliente e criar um tíquete. Neste exemplo, os proprietários da empresa Fabrikam Fiber desejam saber o percentual de clientes que criam com êxito um tíquete de cliente.

Estas são as etapas que eles realizam para criar seu funil.

1. Na ferramenta Funis do Application Insights, selecione **Novo**.
1. Do menu suspenso **Intervalo de Tempo**, selecione **Últimos 90 dias**. Selecione **Meu funis** ou **Funis compartilhados**.
1. Na lista suspensa **Etapa 1**, selecione **Índice**. 
1. Na lista **Etapa 2**, selecione **Cliente**.
1. Na lista **Etapa 3**, selecione **Criar**.
1. Adicione um nome ao funil e selecione **Salvar**.

A captura de tela a seguir mostra um exemplo do tipo de dados gerado pela ferramenta Funis. Os proprietários da Fabrikam podem ver que, durante os últimos 90 dias, 54,3% de seus clientes que visitam a página inicial criou um tíquete de cliente. Eles também podem ver que 2.700 de seus clientes vieram ao índice desde a home page. Isso pode indicar um problema de atualização.


![Captura de tela da ferramenta Funis com dados](./media/app-insights-understand-usage-patterns/funnel1.png)

### <a name="funnels-features"></a>Recursos de funis
A captura de tela anterior inclui cinco áreas realçadas. Esses são recursos de Funis. A lista a seguir explica mais sobre cada área correspondente na captura de tela:
1. Se seu aplicativo for amostrado, você verá um banner de amostragem. A seleção da faixa abre um painel de contexto, explicando coo desativar a amostragem. 
2. Você pode exportar o funil para [Power BI](app-insights-export-power-bi.md).
3. Selecione uma etapa para ver mais detalhes à direita. 
4. O gráfico de conversão de histórico mostra as taxas de conversão nos últimos 90 dias. 
5. Compreenda melhor seus usuários ao acessar a ferramenta de usuários. Você pode usar filtros em cada etapa. 

## <a name="next-steps"></a>Próximas etapas
  * [Visão geral do uso](app-insights-usage-overview.md)
  * [Usuários, Sessões e Eventos](app-insights-usage-segmentation.md)
  * [Retenção](app-insights-usage-retention.md)
  * [Pastas de trabalho](app-insights-usage-workbooks.md)
  * [Adicionar contexto de usuário](app-insights-usage-send-user-context.md)
  * [Exportar para o Power BI](app-insights-export-power-bi.md)

