---
title: Usar o Log Analytics com um aplicativo multilocatário de Banco de Dados SQL | Microsoft Docs
description: Configurar e usar o Log Analytics com um aplicativo SaaS multilocatário do Banco de Dados SQL do Azure
keywords: tutorial do banco de dados SQL
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: 02c380c78fa773b56a3c8b666e890836a3d8e54b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942045"
---
# <a name="set-up-and-use-log-analytics-with-a-multitenant-sql-database-saas-app"></a>Configurar e usar o Log Analytics com um aplicativo SaaS multilocatário do Banco de Dados SQL

Neste tutorial, você configura e usa o [Log Analytics do Azure](/azure/log-analytics/log-analytics-overview) para monitorar pools elásticos e bancos de dados. Este tutorial se baseia no [Tutorial de gerenciamento e monitoramento do desempenho](saas-dbpertenant-performance-monitoring.md). Ele mostra como usar o Log Analytics para ampliar o monitoramento e os alertas fornecidos no portal do Azure. O Log Analytics dá suporte a milhares de monitoramentos de pools elásticos e a centenas de milhares de bancos de dados. O Log Analytics também fornece uma única solução de monitoramento, que pode integrar o monitoramento de diferentes aplicativos e serviços do Azure, entre várias assinaturas do Azure.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Instalar e configurar o Log Analytics.
> * Usar o Log Analytics para monitorar pools e bancos de dados.

Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O aplicativo de banco de dados por locatário SaaS Wingtip Tickets é implantado. Para implantá-lo em menos de cinco minutos, veja [Implantar e explorar o aplicativo de banco de dados por locatário SaaS Wingtip Tickets](saas-dbpertenant-get-started-deploy.md).
* O Azure PowerShell está instalado. Para obter mais informações, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Consulte o [Tutorial de monitoramento e gerenciamento de desempenho](saas-dbpertenant-performance-monitoring.md) para uma discussão sobre padrões e cenários de SaaS, e como eles afetam os requisitos de uma solução de monitoramento.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-log-analytics"></a>Monitorar e gerenciar desempenho do pool elástico e do banco de dados com o Log Analytics

Para o Banco de Dados SQL do Azure, o monitoramento e o alerta estão disponíveis em bancos de dados e pools no portal do Azure. Esse monitoramento e alertas internos são convenientes, mas também específicos do recurso. Isso significa que são adequados para monitorar grandes instalações ou fornecer uma exibição unificada de recursos e assinaturas.

Para cenários de alto volume, você pode usar o Log Analytics para monitoramento e alertas. O Log Analytics é um serviço do Azure separado que habilita a análise de logs de diagnóstico e telemetria coletados em um espaço de trabalho de possivelmente vários serviços. O Log Analytics fornece uma linguagem de consulta e ferramentas de visualização de dados internas que permitem a análise de dados operacionais. A solução Análise do SQL fornece várias exibições e consultas de monitoramento e alertas de pools elásticos e bancos de dados. O Log Analytics também fornece um designer de exibição personalizado.

Os espaços de trabalho e as soluções de análise do Log Analytics abrem no Portal do Azure e no Operations Management Suite. O Portal do Azure é o ponto de acesso mais recente, mas pode estar por trás do portal do Operations Management Suite em algumas áreas.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Criar dados de diagnóstico de desempenho simulando uma carga de trabalho em seus locatários 

1. No ISE do PowerShell, abra *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Módulos de Aprendizado\\Gerenciamento e Monitoramento do desempenho\\Demo-PerformanceMonitoringAndManagement.ps1*. Mantenha esse script aberto, pois você talvez queira executar vários dos cenários de geração de carga durante este tutorial.
2. Se você já não fez isso, provisione um lote de locatários para tornar o contexto de monitoramento mais interessante. Esse processo leva alguns minutos.

   a. Defina **$DemoScenario = 1,** _Provisionar um lote de locatários_.

   b. Para executar o script e implantar mais 17 locatários, pressione F5.

3. Agora, inicie o gerador de carga para executar uma carga simulada em todos os locatários.

    a. Defina **$DemoScenario** = 2, _Gerar carga de intensidade normal (aproximadamente 30 DTU)_.

    b. Para executar o script, pressione F5.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts do aplicativo de banco de dados por locatário SaaS Wingtip Tickets

Os scripts e o código-fonte do aplicativo SaaS de banco de dados multilocatário Wingtip Tickets estão disponíveis no repositório [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) do GitHub. Para obter as etapas para fazer o download e desbloquear os scripts de PowerShell do Wingtip Tickets, consulte as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-and-the-azure-sql-analytics-solution"></a>Instalar e configurar o Log Analytics e a solução Análise de SQL do Azure

O Log Analytics é um serviço separado que precisa ser configurado. O Log Analytics coleta dados de log, telemetria e métrica em um espaço de trabalho do Log Analytics. Assim como outros recursos no Azure, o espaço de trabalho do Log Analytics deve ser criado. O espaço de trabalho não precisa ser criado no mesmo grupo de recursos que os aplicativos que ele está monitorando. Mas isso geralmente faz mais sentido. Para o aplicativo Wingtip Tickets, use um único grupo de recursos para garantir que o espaço de trabalho seja excluído com o aplicativo.

1. No ISE do PowerShell, abra *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Módulos de Aprendizado\\Gerenciamento e Monitoramento do Desempenho\\Log Analytics\\Demo-LogAnalytics.ps1*.
2. Para executar o script, pressione F5.

Agora você pode abrir o Log Analytics no portal do Azure ou no portal do Operations Management Suite. Leva alguns minutos para coletar a telemetria no espaço de trabalho do Log Analytics e para torná-la visível. Quanto mais tempo você deixa o sistema coletando dados, mais interessante é a experiência. 

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Usar o Log Analytics e a solução de Análise de SQL para monitorar pools e bancos de dados


Neste exercício, abra o Log Analytics e o portal do Operations Management Suite para examinar a telemetria coletada para os bancos de dados e pools.

1. Navegue até o [Portal do Azure](https://portal.azure.com). Selecione **Todos os serviços** para abrir o Log Analytics. Pesquise por Log Analytics.

   ![Abrir Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

2. Selecione o espaço de trabalho chamado _wtploganalytics-&lt;user&gt;_.

3. Selecione **Visão geral** para abrir a solução Log Analytics no Portal do Azure.

   ![Visão geral](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Pode demorar alguns minutos até que a solução esteja ativa. 

4. Selecione o bloco **Análise de SQL do Azure** para abri-lo.

    ![Bloco Visão Geral](media/saas-dbpertenant-log-analytics/overview.png)

5. Os modos de exibição na solução rolam lateralmente, com sua própria barra de rolagem interna na parte inferior. Se necessário, atualize a página.

6. Para explorar a página de resumo, selecione os blocos ou bancos de dados individuais para abrir um gerenciador de detalhes.

    ![Painel do Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

7. Altere a configuração de filtro para modificar o intervalo de tempo. Para este tutorial, selecione **Última 1 hora**.

    ![Filtro de tempo](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

8. Selecione um único banco de dados para explorar o uso de consulta e a métrica para esse banco de dados.

    ![Análise de banco de dados](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

9. Para ver a métrica de uso, role a página de análise para a direita.
 
     ![Métrica de banco de dados](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

10. Role a página de análise para a esquerda e selecione o bloco do servidor na lista **Informações do Recurso**.  

    ![Lista de Informações do Recursos](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Uma página é aberta mostrando os pools e os bancos de dados no servidor.

    ![Servidor com pools e bancos de dados](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

11. Selecione um pool. Na página do pool que é aberta, role para a direita a fim de ver as métricas do pool. 

    ![Métricas do pool](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


12. Voltando para o espaço de trabalho do Log Analytics, selecione **Portal do OMS** para abrir o espaço de trabalho nele.

    ![Bloco Portal do Operations Management Suite](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

No portal do Operations Management Suite, você pode explorar mais os dados de log e métrica no espaço de trabalho. 

O monitoramento e os alertas no Log Analytics baseiam-se em consultas sobre os dados no espaço de trabalho, ao contrário dos alertas definidos em cada folha de recursos no Portal do Azure. Baseando alertas em consultas, você pode definir um único alerta sobre todos os bancos de dados em vez de uma definição para cada banco de dados. As consultas são limitadas apenas pelos dados disponíveis no espaço de trabalho.

Para saber mais sobre como usar o Log Analytics para consultar e definir alertas, confira [Trabalhando com regras de alerta no Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

O Log Analytics para o Banco de Dados SQL realiza cobrança com base no volume de dados no espaço de trabalho. Nesse tutorial, você criou um espaço de trabalho gratuito, que é limitado a 500 MB por dia. Após esse limite ser atingido, os dados não são mais adicionados ao espaço de trabalho.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Instalar e configurar o Log Analytics.
> * Usar o Log Analytics para monitorar pools e bancos de dados.

Experimente o [Tutorial de análise de locatário](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que aproveitam a implantação inicial do aplicativo de banco de dados por locatário Saas Wingtip Tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
