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
ms.date: 10/11/2018
ms.openlocfilehash: 177c361734a88acab5fc10d6b460645be82bf437
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457129"
---
# <a name="monitor-and-get-insights-about-logic-app-runs-with-log-analytics"></a>Monitore e obtenha informações sobre o aplicativo lógico executado com Log Analytics

Para obter informações monitoramento e depuração mais detalhadas, ative o Log Analytics durante a criação de um aplicativo lógico. O Log Analytics fornece monitoramento e log de diagnósticos para as execuções do aplicativo lógico por meio do Portal do Azure. Ao adicionar a solução de Gerenciamento de Aplicativos Lógicos, você obtém o status agregado das execuções do aplicativo lógico e os detalhes específicos como status, tempo de execução, status de reenvio e IDs de correlação.

Esse artigo mostra como ativar o Log Analytics para que seja possível exibir eventos e dados de tempo de execução para a execução do aplicativo lógico.

 > [!TIP]
 > Para monitorar os aplicativos lógicos existentes, siga essa etapas para [ativar o log de diagnósticos e enviar dados de tempo de execução de aplicativo lógico para o Log Analytics](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

## <a name="requirements"></a>Requisitos

Antes de iniciar, é necessário ter um workspace do Log Analytics. Saiba [como criar um workspace do Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). 

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

## <a name="install-the-logic-apps-management-solution"></a>Instalar a solução de Gerenciamento de Aplicativos Lógicos

Se você já ativou o Log Analytics durante a criação de seu aplicativo lógico, ignore esta etapa. Você já tem a solução de Gerenciamento de Aplicativos Lógicos instalada.

1. No [portal do Azure](https://portal.azure.com), selecione **Todos os serviços**. Na caixa de pesquisa, digite "log analytics" como filtro e selecione **Log Analytics**.

   ![Selecione “Log Analytics”](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. Em **Log Analytics**, localize e selecione o workspace do Log Analytics. 

   ![Selecionar o workspace do Log Analytics](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. Em **configurar monitoramento de soluções**, escolha **Exibir soluções**.

   ![Escolha "Exibir soluções"](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. Na página de visão geral, escolha **Adicionar**, que abre a lista **Soluções de gerenciamento**. Nessa lista, selecione **Gerenciamento de Aplicativos Lógicos**. 

   ![Selecione "Gerenciamento de Aplicativos Lógicos"](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

   Se você não encontrar a solução, na parte inferior da lista, escolha **Carregar mais** até que a solução seja exibida.

1. Escolha **Criar**, que instala a solução.

   ![Escolha "Adicionar" para "Gerenciamento de Aplicativos Lógicos"](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-runs-in-log-analytics-workspace"></a>Exibir as execuções do aplicativo lógico no espaço de trabalho do Log Analytics

1. Para exibir a contagem e o status de execução do seu aplicativo lógico, vá para seu espaço de trabalho do Log Analytics e abra a página de Visão geral. 

   Os detalhes sobre seu aplicativo lógico executados aparecem na peça **Gerenciamento de Aplicativos Lógicos**. Para exibir um resumo com mais detalhes sobre as execuções de seu aplicativo lógico, escolha o bloco **Gerenciamento de Aplicativos Lógicos**. 

   ![Bloco de visão geral mostrando a contagem e o status da execução do aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

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

   * **Reenviar:** você pode enviar novamente uma ou mais execuções de aplicativo lógico que falharam, que obtiveram êxito ou que ainda estão em andamento. Marque as caixas de seleção das execuções que você deseja reenviar e escolha **Reenviar**. 

     ![Reenviar execuções de aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. Para filtrar esses resultados, execute a filtragem tanto no lado do cliente quanto no lado do servidor.

   * **Filtro do lado do cliente**: para cada coluna, escolha os filtros que desejar, por exemplo:

     ![Exemplo de filtros de coluna](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **Filtro no lado do servidor**:para escolher um período específico, ou para limitar o número de execuções exibidas, use o controle de escopo na parte superior da página. Por padrão, apenas 1.000 registros aparecem por vez.
   
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
     
     Na página do Azure Log Analytics, você pode atualizar consultas e exibir os resultados da tabela. Essa consulta usa a [linguagem de consulta Kusto](https://aka.ms/LogAnalyticsLanguageReference), que pode ser editada se você quiser exibir resultados diferentes. 

     ![Azure Log Analytics – modo de exibição de consulta](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Próximas etapas

* [Monitorar mensagens do B2B](../logic-apps/logic-apps-monitor-b2b-message.md)