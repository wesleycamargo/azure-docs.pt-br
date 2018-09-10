---
title: Monitorar execuções de aplicativo lógico com o Log Analytics – Aplicativo Lógico do Azure | Microsoft Docs
description: Obter insights e dados de depuração sobre suas execuções de aplicativo lógico com o Log Analytics para solução de problemas e diagnósticos
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/19/2018
ms.openlocfilehash: 1aa55728b222c2838026cf5b06175736c5c84194
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123283"
---
# <a name="monitor-and-get-insights-about-logic-app-runs-with-log-analytics"></a>Monitore e obtenha informações sobre o aplicativo lógico executado com Log Analytics

Para obter informações monitoramento e depuração mais detalhadas, ative o Log Analytics durante a criação de um aplicativo lógico. O Log Analytics fornece monitoramento e log de diagnósticos para as execuções do aplicativo lógico por meio do Portal do Azure. Ao adicionar a solução de Gerenciamento de Aplicativos Lógicos, você obtém o status agregado das execuções do aplicativo lógico e os detalhes específicos como status, tempo de execução, status de reenvio e IDs de correlação.

Esse artigo mostra como ativar o Log Analytics para que seja possível exibir eventos e dados de tempo de execução para a execução do aplicativo lógico.

 > [!TIP]
 > Para monitorar os aplicativos lógicos existentes, siga essa etapas para [ativar o log de diagnósticos e enviar dados de tempo de execução de aplicativo lógico para o Log Analytics](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

## <a name="requirements"></a>Requisitos

Antes de iniciar, é necessário ter um espaço de trabalho do Log Analytics. Saiba [como criar um espaço de trabalho do Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). 

## <a name="turn-on-diagnostics-logging-when-creating-logic-apps"></a>Ativar o registro em log de diagnóstico durante a criação de aplicativos lógicos

1. No [Portal do Azure](https://portal.azure.com), crie um aplicativo lógico. Escolha **Criar um recurso** > **Integração corporativa** > **Aplicativo Lógico**.

   ![Criar um aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/find-logic-apps-azure.png)

2. Na página **Criar aplicativo lógico**, execute essas tarefas, conforme mostrado:

   1. Dê um nome para seu aplicativo lógico e selecione sua assinatura do Azure. 
   2. Crie ou selecione um grupo de recursos do Azure.
   3. Defina **Log Analytics** como **Ativado**. 
   Selecione o espaço de trabalho do Log Analytics para o qual você deseja enviar dados para suas execuções de aplicativo lógico. 
   4. Quando você estiver pronto, escolha **Fixar no painel** > **Criar**.

      ![Criar aplicativo lógico](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app.png)

      Após a conclusão dessa etapa, o Azure criará o aplicativo lógico, que agora está associado ao espaço de trabalho do Log Analytics. 
      Além disso, essa etapa também instala automaticamente a solução de Gerenciamento de Aplicativos Lógicos no espaço de trabalho.

3. Para exibir as execuções do aplicativo lógico, [continue com essas etapas](#view-logic-app-runs-oms).

## <a name="install-the-logic-apps-management-solution"></a>Instalar a solução de Gerenciamento de Aplicativos Lógicos

Se você já ativou o Log Analytics durante a criação de seu aplicativo lógico, ignore esta etapa. Você já tem a solução de Gerenciamento de Aplicativos Lógicos instalada.

1. No [portal do Azure](https://portal.azure.com), escolha **Mais serviços**. Pesquise por "log analytics" enquanto filtra e, em seguida, escolha **Log Analytics**, conforme exibido:

   ![Escolher “Log Analytics”](media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

2. Em **Log Analytics**, localize e selecione o espaço de trabalho do Log Analytics. 

   ![Selecionar o espaço de trabalho do Log Analytics](media/logic-apps-monitor-your-logic-apps-oms/select-logic-app.png)

3. Em **Gerenciamento**, escolha **Visão geral**.

   ![Escolher “Portal do OMS”](media/logic-apps-monitor-your-logic-apps-oms/ibiza-portal-page.png)

4. Na página de Visão Geral, escolha **Adicionar** para abrir o bloco de Soluções de Gerenciamento. 

   ![Escolha "Gerenciamento de Aplicativos Lógicos"](media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

5. Percorra a lista de **Soluções de Gerenciamento**, escolha a solução **Gerenciamento de Aplicativos Lógicos** e escolha **Criar** para instalá-lo na página de Visão Geral.

   ![Escolha "Adicionar" para "Gerenciamento de Aplicativos Lógicos"](media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-your-logic-app-runs-in-your-log-analytics-workspace"></a>Exibir as execuções do aplicativo lógico no espaço de trabalho do Log Analytics

1. Para exibir a contagem e o status das execuções do aplicativo lógico, acesse a página de visão geral do espaço de trabalho do Log Analytics. Examine os detalhes no bloco **Gerenciamento de Aplicativos Lógicos**.

   ![Bloco de visão geral mostrando a contagem e o status da execução do aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

2. Para exibir um resumo com mais detalhes sobre as execuções de seu aplicativo lógico, escolha o bloco **Gerenciamento de Aplicativos Lógicos**.

   Aqui, as execuções de seu aplicativo lógica são agrupadas por nome ou por status de execução. Você também pode ver detalhes sobre as falhas em ações ou gatilhos para as execuções do aplicativo lógico.

   ![Resumo do status das execuções de seu aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-runs-summary.png)
   
3. Para exibir todas as execuções de um aplicativo lógico específico ou status, selecione a linha de um aplicativo lógico ou um status.

   Veja um exemplo que mostra todas as execuções de um aplicativo lógico específico:

   ![Exibir execuções de um aplicativo lógico ou um status](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   Há duas opções avançadas nesta página:
   * **Propriedades rastreadas:** essa coluna mostra as propriedades rastreadas, agrupadas por ações, do aplicativo lógico. Para exibir as propriedades rastreadas, escolha **Exibir**. Você pode pesquisar as propriedades rastreadas usando o filtro de coluna.
   
     ![Exibir propriedades rastreadas para um aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

     Quaisquer propriedades rastreadas recém-adicionada poderão demorar de 10 a 15 minutos antes de aparecerem pela primeira vez. Saiba [como adicionar propriedades rastreadas ao seu aplicativo lógico](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details).

   * **Reenviar:** você pode enviar novamente uma ou mais execuções de aplicativo lógico que falharam, que obtiveram êxito ou que ainda estão em andamento. Marque as caixas de seleção das execuções que você deseja reenviar e escolha **Reenviar**. 

     ![Reenviar execuções de aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

4. Para filtrar esses resultados, execute a filtragem tanto no lado do cliente quanto no lado do servidor.

   * Filtro no lado do cliente: para cada coluna, escolha os filtros desejados. 
   Estes são alguns exemplos:

     ![Exemplo de filtros de coluna](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * Filtro no lado do servidor: para escolher um período específico, ou para limitar o número de execuções exibidas, use o controle de escopo na parte superior da página. 
   Por padrão, apenas 1.000 registros aparecem por vez. 
   
     ![Alterar o período](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)
 
5. Para exibir todas as ações e os detalhes de uma execução específica, selecione uma linha para uma execução de aplicativo lógico.

   Veja um exemplo que mostra todas as ações de uma execução de aplicativo lógico específica:

   ![Exibir ações para uma execução de aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)
   
6. Em qualquer página de resultados, para exibir a consulta por trás dos resultados ou para ver todos os resultados, escolha **Ver Tudo**, que abre a página Pesquisa de Logs.
   
   ![Ver Tudo nas páginas Resultados](media/logic-apps-monitor-your-logic-apps-oms/logic-app-seeall.png)
   
   Na página Pesquisa de Logs,
   * Para exibir os resultados da consulta em uma tabela, escolha **Tabela**.
   * Para alterar a consulta, edite a cadeia de consulta na barra de pesquisa. 
   Para ter uma experiência melhor, escolha **Análise Avançada**.

     ![Exibir ações e detalhes da execução de um aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)
     
     Aqui na página do Azure Log Analytics, você pode atualizar consultas e exibir os resultados da tabela. 
     Essa consulta usa a [linguagem de consulta Kusto](https://docs.loganalytics.io/docs/Language-Reference), que pode ser editada se você quiser exibir resultados diferentes. 

     ![Azure Log Analytics – modo de exibição de consulta](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Próximas etapas

* [Monitorar mensagens do B2B](../logic-apps/logic-apps-monitor-b2b-message.md)

