---
title: Monitorar aplicativos lógicos com os logs do Azure Monitor - aplicativos lógicos do Azure | Microsoft Docs
description: Obter insights e dados de depuração para solucionar problemas e diagnosticar suas execuções de aplicativo lógico com o Log Analytics do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 3f890e6cabd757fdd38374befaaccd1a10c9bd96
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192455"
---
# <a name="monitor-logic-apps-with-azure-monitor-logs"></a>Monitorar aplicativos lógicos com os logs do Azure Monitor

Para monitorar e obter os detalhes de depuração mais avançados sobre seus aplicativos lógicos, ative [registra em log do Azure Monitor](../log-analytics/log-analytics-overview.md) quando você cria seu aplicativo lógico. Os logs do Azure Monitor fornece monitoramento para seus aplicativos lógicos, quando você instala a solução de gerenciamento de aplicativos lógicos no portal do Azure e log de diagnósticos. Essa solução também fornece informações agregadas sobre as execuções de seu aplicativo lógico com detalhes específicos, como status, tempo de execução, status de reenvio e IDs de correlação. Este artigo mostra como ativar logs do Azure Monitor, portanto, você pode exibir eventos de tempo de execução e dados para seu aplicativo lógico é executado.

Para ativar os logs do Azure Monitor para aplicativos lógicos existentes, siga estas etapas para [ativar o log de diagnóstico e enviar dados de tempo de execução do aplicativo lógico para os logs do Azure Monitor](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

> [!NOTE]
> Anteriormente, esta página descrevia as etapas para executar essas tarefas com o OMS (Microsoft Operations Management Suite), que será [desativado em janeiro de 2019](../azure-monitor/platform/oms-portal-transition.md), substituindo essas etapas pelo Azure Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, é necessário ter um workspace do Log Analytics. Saiba [como criar um workspace do Log Analytics](../azure-monitor/learn/quick-create-workspace.md). 

## <a name="turn-on-diagnostics-logging-when-creating-logic-apps"></a>Ativar o registro em log de diagnóstico durante a criação de aplicativos lógicos

1. No [Portal do Azure](https://portal.azure.com), crie um aplicativo lógico. Escolher **Criar um recurso** > **integração** > **Aplicativo lógico**.

   ![Criar um aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/find-logic-apps-azure.png)

1. Em **Criar aplicativo lógico**, execute essas tarefas, conforme mostrado:

   1. Dê um nome para seu aplicativo lógico e selecione sua assinatura do Azure. 

   1. Crie ou selecione um grupo de recursos do Azure.

   1. Defina **Log Analytics** como **Ativado**. 

   1. Na lista de espaço de trabalho do Log Analytics, selecione o espaço de trabalho no qual você deseja enviar dados para as execuções do seu aplicativo lógico. 

      ![Criar aplicativo lógico](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app.png)

      Após a conclusão dessa etapa, o Azure criará o aplicativo lógico, que agora está associado ao workspace do Log Analytics. 
      Além disso, essa etapa também instala automaticamente a solução de Gerenciamento de Aplicativos Lógicos no workspace.

   1. Quando terminar, escolha **Criar**.

1. Para exibir as execuções do aplicativo lógico, [continue com essas etapas](#view-logic-app-runs-oms).

## <a name="install-logic-apps-management-solution"></a>Instalar a solução de Gerenciamento dos Aplicativos Lógicos

Se você já ativou o Azure Monitor registra quando criou seu aplicativo lógico, ignore esta etapa. Você já tem a solução de Gerenciamento de Aplicativos Lógicos instalada.

1. No [portal do Azure](https://portal.azure.com), selecione **Todos os serviços**. Na caixa de pesquisa, encontre "log analytics" e selecione **Log Analytics**.

   ![Selecione “Log Analytics”](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. Em **Log Analytics**, localize e selecione o workspace do Log Analytics. 

   ![Selecionar o workspace do Log Analytics](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. Em **Introdução ao Log Analytics** > **Configurar soluções de monitoramento**, escolha **Exibir soluções**.

   ![Escolha "Exibir soluções"](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. Na página de visão geral, escolha **Adicionar**, que abre a lista **Soluções de gerenciamento**. Nessa lista, selecione **Gerenciamento de Aplicativos Lógicos**. 

   ![Selecione "Gerenciamento de Aplicativos Lógicos"](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

   Se você não encontrar a solução, na parte inferior da lista, escolha **Carregar mais** até que a solução seja exibida.

1. Escolha **Criar**, confirme o workspace do Log Analytics onde deseja instalar a solução e, em seguida, escolha **Criar** novamente.   

   ![Escolha "Criar" para “Gerenciamento de Aplicativos Lógicos"](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

   Se não quiser usar um workspace existente, você poderá criar um novo workspace neste momento.

   Quando você terminar, a solução de Gerenciamento de Aplicativos Lógicos será exibida na página de visão geral. 

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-run-information"></a>Exibir informações sobre a execução do aplicativo lógico

Após o aplicativo lógico ser executado, você pode exibir o status e a contagem dessas execuções no bloco **Gerenciamento de Aplicativos Lógicos**. 

1. Vá para seu workspace do Log Analytics e abra a página de visão geral. Escolha **Gerenciamento de Aplicativos Lógicos**. 

   ![Status e contagem de execuções do aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

   Aqui, as execuções de seu aplicativo lógica são agrupadas por nome ou por status de execução. 
   Esta página também mostra detalhes sobre falhas em ações ou gatilhos para executar o aplicativo lógico.

   ![Resumo do status das execuções de seu aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-runs-summary.png)
   
1. Para exibir todas as execuções de um aplicativo lógico específico ou status, selecione a linha de um aplicativo lógico ou um status.

   Veja um exemplo que mostra todas as execuções de um aplicativo lógico específico:

   ![Exibir execuções de um aplicativo lógico ou um status](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   Esta página possui essas opções avançadas:

   * **Propriedades rastreadas:**

     Essa coluna mostra as propriedades rastreadas, agrupadas por ações, do aplicativo lógico. Para exibir as propriedades rastreadas, escolha **Exibir**. 
     Para pesquisar as propriedades rastreadas, use o filtro de coluna.
   
     ![Exibir propriedades rastreadas para um aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

     Quaisquer propriedades rastreadas recém-adicionada poderão demorar de 10 a 15 minutos antes de aparecerem pela primeira vez. Saiba [como adicionar propriedades rastreadas ao seu aplicativo lógico](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details).

   * **Reenviar:** É possível reenviar uma ou mais execuções de aplicativos lógicos que falharam, obtiveram êxito ou que ainda estejam em execução. Marque as caixas de seleção das execuções que você deseja reenviar e escolha **Reenviar**. 

     ![Reenviar execuções de aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. Para filtrar esses resultados, execute a filtragem tanto no lado do cliente quanto no lado do servidor.

   * **Filtro do lado do cliente**: Para cada coluna, escolha os filtros desejados, por exemplo:

     ![Exemplo de filtros de coluna](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **Filtro do lado do servidor**: Para escolher uma janela de tempo específica ou limitar o número de execuções exibidas, use o controle de escopo na parte superior da página. Por padrão, apenas 1.000 registros aparecem por vez.
   
     ![Alterar o período](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)
 
1. Para exibir todas as ações e os detalhes de uma execução específica, selecione uma linha para uma execução de aplicativo lógico.

   Veja um exemplo que mostra todas as ações de uma execução de aplicativo lógico específica:

   ![Exibir ações para uma execução de aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)
   
1. Em qualquer página de resultados, para exibir a consulta por trás dos resultados ou para ver todos os resultados, escolha **Ver Tudo**, que abre a página Pesquisa de Logs.
   
   ![Ver Tudo nas páginas Resultados](media/logic-apps-monitor-your-logic-apps-oms/logic-app-seeall.png)
   
   Na página Pesquisa de Logs,

   * Para exibir os resultados da consulta em uma tabela, escolha **Tabela**.

   * Para alterar a consulta, edite a cadeia de consulta na barra de pesquisa. 
   Para ter uma experiência melhor, escolha **Análise Avançada**.

     ![Exibir ações e detalhes da execução de um aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)
     
     Na página do log analytics, você pode atualizar consultas e exibir os resultados da tabela. Essa consulta usa a [linguagem de consulta Kusto](https://aka.ms/LogAnalyticsLanguageReference), que pode ser editada se você quiser exibir resultados diferentes. 

     ![log analytics – modo de exibição de consulta](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Próximas etapas

* [Monitorar mensagens do B2B](../logic-apps/logic-apps-monitor-b2b-message.md)