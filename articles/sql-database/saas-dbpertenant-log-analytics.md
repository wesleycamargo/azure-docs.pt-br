---
title: "Usar o Log Analytics com um aplicativo multilocatário de Banco de Dados SQL | Microsoft Docs"
description: "Configurar e usar o Log Analytics (OMS) com um aplicativo SaaS multilocatário do Banco de Dados SQL do Azure"
keywords: tutorial do banco de dados SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: billgib; sstein
ms.openlocfilehash: 90510520e5f6bbfa8aea4026d7437a4a4881984f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2018
---
# <a name="set-up-and-use-log-analytics-oms-with-a-multi-tenant-azure-sql-database-saas-app"></a>Configurar e usar o Log Analytics (OMS) com um aplicativo SaaS multilocatário do Banco de Dados SQL do Azure

Neste tutorial, você configura e usa o *Log Analytics ([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))* para monitorar pools elásticos e bancos de dados. Este tutorial se baseia no [tutorial de Gerenciamento e Monitoramento do Desempenho](saas-dbpertenant-performance-monitoring.md). Ele mostra como usar o *Log Analytics* para ampliar o monitoramento e os alertas fornecidos no portal do Azure. O Log Analytics dá suporte a milhares de monitoramentos de pools elásticos e a centenas de milhares de bancos de dados. O Log Analytics também fornece uma única solução de monitoramento, que pode integrar o monitoramento de diferentes aplicativos e serviços do Azure, entre várias assinaturas do Azure.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Instalar e configurar o Log Analytics (OMS)
> * Usar o Log Analytics para monitorar pools e bancos de dados

Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O aplicativo Wingtip Tickets SaaS Database Per Tenant é implantado. Para implantá-lo em menos de cinco minutos, veja [Implantar e explorar o aplicativo Wingtip Tickets SaaS Database Per Tenant](saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

Consulte o [Tutorial de monitoramento e gerenciamento de desempenho](saas-dbpertenant-performance-monitoring.md) para uma discussão sobre padrões e cenários de SaaS, e como eles afetam os requisitos de uma solução de monitoramento.

## <a name="monitoring-and-managing-database-and-elastic-pool-performance-with-log-analytics-or-operations-management-suite-oms"></a>Monitorando e gerenciando desempenho do pool elástico e do banco de dados com o Log Analytics ou o OMS (Operations Management Suite)

Para o Banco de Dados SQL, o monitoramento e o alerta estão disponíveis em bancos de dados e pools no portal do Azure. Esse monitoramento e alerta internos são convenientes mas, sendo específicos de cada recurso, são menos adequados para monitoramento de grandes instalações ou para fornecer uma exibição unificada entre diferentes assinaturas e recursos.

Para cenários de alto volume, o Log Analytics pode ser usado para monitoramento e alertas. O Log Analytics é um serviço do Azure separado que habilita a análise de logs de diagnóstico e telemetria coletados em um espaço de trabalho de possivelmente vários serviços. O Log Analytics fornece uma linguagem de consulta e ferramentas de visualização de dados internas que permitem a análise de dados operacionais. A solução Análise do SQL fornece várias exibições e consultas de monitoramento e alertas de pools elásticos e bancos de dados. O OMS também fornece um designer de exibição personalizado.

Os espaços de trabalho e as soluções de análise do Log Analytics podem ser abertos no Portal do Azure e no OMS. O Portal do Azure é o ponto de acesso mais recente, mas pode estar por trás do portal do OMS em algumas áreas.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Criar dados de diagnóstico de desempenho simulando uma carga de trabalho em seus locatários 

1. No **ISE do PowerShell**, abra *... \\WingtipTicketsSaaS-MultiTenantDb-master\\Módulos de Aprendizado\\Gerenciamento e Monitoramento do desempenho\\**Demo-PerformanceMonitoringAndManagement.ps1***. Mantenha esse script aberto pois você talvez queira executar vários dos cenários de geração de carga durante este tutorial.
1. Se você já não fez isso, provisione um lote de locatários para proporcionar um contexto de monitoramento mais interessante. Isso leva alguns minutos:
   1. Defina **$DemoScenario = 1,** _Provisionar um lote de locatários_
   1. Para executar o script e implantar mais 17 locatários, pressione **F5**.  

1. Agora, inicie o gerador de carga para executar uma carga simulada em todos os locatários.  
    1. Defina **$DemoScenario** = 2, _Gerar carga de intensidade normal (aproximadamente 30 DTU)_.
    1. Para executar o script, pressione **F5**.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts do aplicativo Wingtip Tickets SaaS Database Per Tenant

Os scripts e o código-fonte do aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets estão disponíveis no repositório [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) do GitHub. Confira as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts de PowerShell do Wingtip Tickets.

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>Instalando e configurando o Log Analytics e a solução Análise de SQL do Azure

O Log Analytics é um serviço separado que precisa ser configurado. O Log Analytics coleta dados de log, telemetria e métrica em um espaço de trabalho do Log Analytics. Um espaço de trabalho do Log Analytics é um recurso, assim como outros recursos no Azure e deve ser criado. Embora o espaço de trabalho não precise ser criado no mesmo grupo de recursos que os aplicativos que ele está monitorando, isso geralmente faz mais sentido. Para o aplicativo Wingtip Tickets, usar um único grupo de recursos faz com que o espaço de trabalho seja excluído com o aplicativo.

1. No **ISE do PowerShell**, abra *... \\WingtipTicketsSaaS-MultiTenantDb-master\\Módulos de Aprendizado\\Gerenciamento e Monitoramento do Desempenho\\Log Analytics\\**Demo-LogAnalytics.ps1***.
1. Para executar o script, pressione **F5**.

Nesse ponto, você deve ser capaz de abrir o Log Analytics no Portal do Azure (ou no portal do OMS). Leva alguns minutos para que a telemetria seja coletada no espaço de trabalho do Log Analytics e se torne visível. Quanto mais tempo você deixa o sistema coletando dados, mais interessante é a experiência. Agora é um bom momento para pegar algo pra beber. Só dê uma olhadinha para ter certeza de que o gerador de carga ainda está em execução!

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Usar o Log Analytics e a solução de Análise de SQL para monitorar pools e bancos de dados


Neste exercício, abra o Log Analytics e o portal do OMS para examinar a telemetria que está sendo coletada para os bancos de dados e pools.

1. Navegue até o [Portal do Azure](https://portal.azure.com) e abra o Log Analytics clicando em **Todos os serviços** e pesquise por Log Analytics:

   ![abrir log analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Selecione o espaço de trabalho chamado _wtploganalytics-&lt;user&gt;_.

1. Selecione **Visão geral** para abrir a solução Log Analytics no Portal do Azure.

   ![overview-link](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Pode demorar alguns minutos até que a solução esteja ativa. Tenha paciência!

1. Clique no bloco Análise de SQL do Azure para abri-lo.

    ![visão geral](media/saas-dbpertenant-log-analytics/overview.png)

    ![análise](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Os modos de exibição na solução rolam lateralmente, com sua própria barra de rolagem interna na parte inferior (atualize a página, se necessário).

1. Explore a página de resumo clicando nos blocos ou em um banco de dados individual para abrir o gerenciador de detalhes.

1. Altere a configuração de filtro para modificar o intervalo de tempo; para este tutorial, selecione _Última hora_

    ![filtro de tempo](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Selecione um único banco de dados para explorar o uso de consulta e a métrica para esse banco de dados.

    ![análise de banco de dados](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Para ver a métrica de uso, role a página de análise para a direita.
 
     ![métrica de banco de dados](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Role a página de análise para a esquerda e clique no bloco do servidor na lista Informações do Recurso. Isso abre uma página mostrando os pools e os bancos de dados no servidor. 

     ![informações do recurso](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

 
     ![servidor com pools e bancos de dados](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Na página de servidor exibida que mostra os pools e os bancos de dados no servidor, clique no pool.  Na página do pool que é aberta, role para a direita a fim de ver as métricas do pool.  

     ![métricas do pool](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)



1. Voltando para o espaço de trabalho do Log Analytics, selecione **Portal do OMS** para abrir o espaço de trabalho nele.

    ![oms](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

No portal do OMS, você pode explorar mais os dados de log e métrica no espaço de trabalho.  

O monitoramento e os alertas no Log Analytics e no OMS baseiam-se em consultas sobre os dados no espaço de trabalho, ao contrário dos alertas definidos em cada folha de recursos no portal do Azure. Baseando alertas em consultas, você pode definir um único alerta sobre todos os bancos de dados em vez de uma definição para cada banco de dados. As consultas são limitadas apenas pelos dados disponíveis no espaço de trabalho.

Para saber mais sobre como usar o OMS para consultar e definir alertas, confira [Trabalhando com regras de alerta no Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-alerts-creating).

O Log Analytics para o Banco de Dados SQL é cobrado com base no volume de dados no espaço de trabalho. Nesse tutorial, você criou um espaço de trabalho Gratuito, que é limitado a 500 MB por dia. Quando esse limite é atingido, os dados não são mais adicionados ao espaço de trabalho.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Instalar e configurar o Log Analytics (OMS)
> * Usar o Log Analytics para monitorar pools e bancos de dados

[Tutorial de análise de locatário](saas-dbpertenant-log-analytics.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que aproveitam a implantação inicial do aplicativo Wingtip Tickets Saas Banco de dados Per Tenant](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)
