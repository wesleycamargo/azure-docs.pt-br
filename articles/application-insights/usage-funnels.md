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
ms.openlocfilehash: 0396c59d9d95ab71f0af04029d87afbb6e47dc35
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2017
---
# <a name="discover-how-customers-are-using-your-application-with-the-application-insights-funnels"></a>Descobrir como os clientes estão usando seu aplicativo com os Funis do Application Insights

Entender a experiência do cliente é de extrema importância para seus negócios. Se o aplicativo envolver vários estágios, você precisará saber se a maioria dos clientes está progredindo por todo o processo ou se eles estão encerrando o processo em algum momento. A progressão por uma série de etapas em um aplicativo Web é conhecida como “funil”. Use os Funis do Application Insights para obter informações sobre os usuários e monitorar as taxas de conversão passo a passo. 

## <a name="create-your-funnel"></a>Criar o funil
Antes de criar o funil, você precisa decidir qual pergunta você deseja responder. Por exemplo, você pode querer saber quantos usuários estão exibindo a página inicial, exibindo um perfil de cliente e criar um tíquete. Neste exemplo, os proprietários da empresa Fabrikam Fiber desejam saber o percentual de clientes que criam com êxito um tíquete de cliente.

Estas são as etapas que eles realizam para criar seu funil.

1. Clique no botão Novo na ferramenta Funis.
1. Selecione o intervalo de tempo “Últimos 90 dias” na lista suspensa **Intervalo de Tempo**. Selecione "Meu funis" ou "Funis compartilhados"
1. Selecione o evento **Índice** na lista suspensa **Etapa 1**. 
1. Selecione o evento **Cliente** na lista suspensa **Etapa 2**.
1. Selecione o evento **Criar** na lista suspensa **Etapa 3**.
1. Adicione um nome ao funil e clique em **Salvar**.

A ilustração a seguir demonstra os dados gerados pela ferramenta Funis. Dali, os proprietários da Fabrikam podem ver que, durante os últimos 90 dias, 54,3% de seus clientes que visitam a página inicial criou um tíquete de cliente. Eles também podem ver que 2,7k de seus clientes vieram para o índice da página inicial, o que pode indicar um problema de atualização.


![Ferramenta de funis com os dados](./media/app-insights-understand-usage-patterns/funnel1.png)

### <a name="funnel-features"></a>Recursos de funil
1. Se seu aplicativo for amostrado, você verá um banner de amostragem. Clicar no banner abrirá um painel de contexto instruindo como desativar a amostragem. 
2. Você pode exportar o funil para [Power BI](app-insights-export-power-bi.md).
3. Clique em uma etapa para obter mais informações à direita. 
4. A conversão de histórico mostra a conversão nos últimos 90 dias. 
5. Compreenda mais bem seus usuários indo para a ferramenta de usuários de Funis. Cada etapa fornecerá a você filtros de usuários selecionados. 

## <a name="next-steps"></a>Próximas etapas
  * [Visão geral do uso](app-insights-usage-overview.md)
  * [Usuários, Sessões e Eventos](app-insights-usage-segmentation.md)
  * [Retenção](app-insights-usage-retention.md)
  * [Pastas de trabalho](app-insights-usage-workbooks.md)
  * [Adicionar contexto de usuário](app-insights-usage-send-user-context.md)
  * [Exportar para o Power BI](app-insights-export-power-bi.md)

