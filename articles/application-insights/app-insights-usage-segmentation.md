---
title: "Análise de usuários, sessões e eventos no Application Insights do Azure | Microsoft Docs"
description: "Análise demográfica dos usuários de seu aplicativo Web."
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 912e3e25680034828aee93d23c6a725c2cec0d45
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---

# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Análise de usuários, sessões e eventos no Application Insights

Descubra quando as pessoas usam seu aplicativo Web, em quais páginas elas estão mais interessadas, onde os usuários estão localizados e quais navegadores e sistemas operacionais eles usam. Analisar a telemetria de negócios e de uso com o [Application Insights do Azure](app-insights-overview.md).

## <a name="get-started"></a>Introdução

Caso ainda não veja dados nas folhas de usuários, sessões ou eventos no portal do Application Insights, [veja como começar a usar as ferramentas de uso](app-insights-usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>A ferramenta de segmentação de Usuários, Sessões e Eventos

Três das folhas de uso usam a mesma ferramenta para dividir a telemetria do seu aplicativo Web em três perspectivas diferentes. Filtrando e dividindo os dados, você pode descobrir informações sobre o uso relativo de diferentes páginas e recursos.

* **Ferramenta de Usuários**: quantas pessoas usaram seu aplicativo e seus recursos.  Os usuários são contados usando IDs anônimas armazenadas em cookies do navegador. Uma única pessoa que usar diferentes navegadores ou computadores será contada como mais de um usuário.
* **Ferramenta de Sessões**: quantas sessões de atividade do usuário incluíram determinadas páginas e recursos de seu aplicativo. Uma sessão é contada após meia hora de inatividade do usuário ou após 24 horas contínuas de uso.
* **Ferramenta de Eventos**: com que frequência determinadas páginas e recursos de seu aplicativo são usados. Uma exibição de página é contada quando um navegador carrega uma página do seu aplicativo, desde que você a tenha [instrumentado](app-insights-javascript.md). 

    Um evento personalizado representa uma ocorrência de algo que esteja acontecendo em seu aplicativo, geralmente uma interação do usuário, como um clique de botão ou a conclusão de uma tarefa. Insira o código em seu aplicativo para [gerar eventos personalizados](app-insights-api-custom-events-metrics.md#trackevent).

![Ferramenta de uso](./media/app-insights-usage-segmentation/users.png)

## <a name="querying-for-certain-users"></a>Consultar determinados usuários 

Explore diferentes grupos de usuários, ajustando as opções de consulta na parte superior da ferramenta de Usuários: 

* Quem usou: escolha exibições de página e eventos personalizados. 
* Durante: escolha um intervalo de tempo. 
* Por: escolha como compartimentar os dados, seja segundo um período ou segundo outra propriedade, como navegador ou cidade. 
* Dividido por: escolha uma propriedade segundo a qual o segmento ou os dados deverão ser divididos. 
* Adicionar filtros: limite a consulta a determinados usuários, sessões ou eventos com base em suas propriedades, como navegador ou cidade. 
 
## <a name="saving-and-sharing-reports"></a>Salvar e compartilhar relatórios 
Você pode salvar relatórios de Usuários, de forma privada na seção Meus Relatórios ou de forma compartilhada com quem tiver acesso a esse recurso do Application Insights na seção Relatórios Compartilhados.  
 
Ao salvar um relatório ou editar suas propriedades, escolher "Intervalo de tempo relativo atual" para salvar um relatório atualizará continuamente os dados, voltando um período determinado.  
 
Escolha "Intervalo de tempo absoluto atual" para salvar um relatório com um conjunto fixo de dados. Tenha em mente que os dados no Application Insights são armazenados somente por 90 dias, portanto, se mais de 90 dias tiverem se passado desde um relatório com um intervalo de tempo absoluto foi salvo, o relatório aparecerá vazio. 
 
## <a name="example-instances"></a>Instâncias de exemplo

A seção de Instâncias do exemplo mostra informações sobre alguns eventos, sessões ou usuários individuais que correspondem à consulta atual. Considerar e explorar os comportamentos de indivíduos, além de agregados, pode fornecer informações sobre como as pessoas realmente usam seu aplicativo. 
 
## <a name="insights"></a>Insights 

A barra lateral Insights mostra grandes clusters de usuários que compartilham propriedades comuns. Esses clusters podem revelar tendências surpreendentes de como as pessoas usam seu aplicativo. Por exemplo, se 40% de todo o uso do seu aplicativo vem de pessoas que usam um único recurso.  


## <a name="next-steps"></a>Próximas etapas

* [Visão geral do uso](app-insights-usage-overview.md)
* [Retenção](app-insights-usage-retention.md)
* [Codificação de eventos personalizados](app-insights-api-custom-events-metrics.md)


