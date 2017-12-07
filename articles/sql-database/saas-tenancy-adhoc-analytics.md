---
title: "Executar consultas de relatório ad hoc em vários bancos de dados SQL do Azure | Microsoft Docs"
description: "Execute consultas de relatório ad hoc em vários bancos de dados SQL em um exemplo de aplicativo multilocatário."
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
ms.topic: articles
ms.date: 11/13/2017
ms.author: billgib; sstein; AyoOlubeko
ms.openlocfilehash: ddad47ccac57ddbb9387709ababbc5be6bad3462
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2017
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Executar consultas de análise ad hoc em vários bancos de dados SQL do Azure

Neste tutorial, você executa consultas distribuídas em todo o conjunto de bancos de dados de locatários para habilitar os relatórios ad hoc interativos. Essas consultas podem extrair informações escondidas nos dados operacionais diários do aplicativo SaaS Wingtip Tickets. Para fazer isso, você implanta um banco de dados de análise adicional no servidor de catálogo e usa a Consulta Elástica para habilitar consultas distribuídas.


Neste tutorial, você aprende:

> [!div class="checklist"]

> * Sobre as exibições globais em cada banco de dados, que permitem a consulta eficiente entre locatários
> * Como implantar um banco de dados de relatórios ad hoc
> * Como executar consultas distribuídas entre todos os bancos de dados de locatário



Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:


* O aplicativo Wingtip Tickets SaaS Database Per Tenant é implantado. Para implantá-lo em menos de cinco minutos, veja [Implantar e explorar o aplicativo Wingtip Tickets SaaS Database Per Tenant](saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* O SSMS (SQL Server Management Studio) está instalado. Para baixar e instalar a versão mais recente do SSMS, confira [Baixar o SSMS (SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Padrão dos relatórios ad hoc

![padrão de relatórios ad hoc](media/saas-tenancy-adhoc-analytics/adhocreportingpattern_dbpertenant.png)

Uma das ótimas oportunidades com aplicativos SaaS é a capacidade de usar a enorme quantidade de dados de locatário armazenados centralmente na nuvem para obter informações sobre a operação e o uso do seu aplicativo. Essas informações podem guiar o desenvolvimento do recurso, aprimoramentos de usabilidade e outros investimentos no aplicativo e nos serviços.

É fácil acessar esses dados em um único banco de dados multilocatário, mas não tão fácil quando são distribuídos em escala por, potencialmente, milhares de bancos de dados. Uma abordagem é usar a [Consulta Elástica](sql-database-elastic-query-overview.md), que permite consultas ad hoc em um conjunto distribuído de bancos de dados com esquema comum. Esses bancos de dados podem ser distribuídos entre diferentes grupos de recursos e assinaturas, mas precisam compartilhar um logon comum. A Consulta Elástica usa um único banco de dados *principal* no qual as tabelas externas são definidas que espelham tabelas ou modos de exibição nos bancos de dados distribuídos (de locatário). As consultas enviadas ao banco de dados principal são compiladas para produzir um plano de consulta distribuído, com partes da consulta propagadas para os bancos de dados de locatário, conforme necessário. A Consulta Elástica usa o mapa de fragmentos no banco de dados de catálogo para determinar o local de todos os bancos de dados de locatários. A instalação e a consulta são simples com o [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) padrão e dão suporte a consultas ad hoc em ferramentas como o Power BI e o Excel.

Ao distribuir consultas entre os bancos de dados de locatário, a Consulta Elástica fornece ideias imediatas sobre dados de produção em tempo real. No entanto, como a Consulta Elástica extrai dados possivelmente de vários bancos de dados, a latência da consulta, às vezes, pode ser maior que para consultas equivalentes enviadas para um único banco de dados de vários locatários. Certifique-se de criar consultas para minimizar os dados retornados. A Consulta Elástica é geralmente mais adequada para consultar pequenas quantidades de dados em tempo real, em vez de criar consultas ou relatórios de análise complexos ou usados com frequência. Se as consultas não tiverem um bom desempenho, examine o [plano de execução](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) para ver qual parte da consulta foi propagada para o banco de dados remoto e a quantidade de dados sendo retornada. Consultas que exigem processamento analítico complexo podem ser melhor atendidas em alguns casos extraindo dados de locatário em um banco de dados dedicado ou data warehouse otimizado para consultas de análise. Esse padrão é explicado no [tutorial de análise de locatário](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts do aplicativo Wingtip Tickets SaaS Database Per Tenant

Os scripts e o código-fonte do aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets estão disponíveis no repositório [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) do GitHub. Confira as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts SaaS do Wingtip Tickets.

## <a name="create-ticket-sales-data"></a>Criar dados de vendas de ingresso

Para executar consultas em um conjunto de dados mais interessante, crie dados de vendas de ingresso executando o gerador de ingressos.

1. No *ISE do PowerShell*, abra o script ...\\Módulos de Aprendizado\\Operational Analytics\\Adhoc Reporting\\ *Demo-AdhocReporting.ps1* e defina os seguintes valores:
   * **$DemoScenario** = 1, **Comprar ingressos de eventos em todos os locais**.
2. Pressione **F5** para executar o script e gerar vendas de ingresso. Enquanto o script é executado, continue as etapas neste tutorial. Os dados de ingresso são consultados na seção *Executar consultas distribuídas ad hoc*, portanto, aguarde o gerador de ingressos ser concluído.

## <a name="explore-the-global-views"></a>Explore as exibições globais

No aplicativo Wingtip Tickets SaaS Database Per Tenant, cada locatário recebe um banco de dados. Portanto, os dados contidos nas tabelas de banco de dados estão no escopo da perspectiva de um único locatário. No entanto, ao consultar em todos os bancos de dados, é importante que a Consulta Elástica possa tratar os dados como se fizessem parte de um único banco de dados lógico fragmentado por locatário. 

Para simular este padrão, um conjunto de exibições 'globais' é adicionado ao banco de dados do locatário que projeta uma ID de locatário em cada uma das tabelas consultadas globalmente. Por exemplo, a exibição *VenueEvents* adiciona uma *VenueId* computada às colunas projetadas da tabela *Eventos*. Da mesma forma, as exibições *VenueTicketPurchases* e *VenueTickets* adicionam uma coluna *VenueId* computada projetada desde as respectivas tabelas. Essas exibiç~eos são usadas pela Consulta Elástica para paralelizar consultas e enviá-las por push para o banco de dados de locatário remoto adequado quando uma coluna *VenueId* estiver presente. Isso reduz significativamente a quantidade de dados retornada, o que resulta em um aumento significativo no desempenho para várias consultas. Essas exibições globais foram pré-criadas em todos os bancos de dados de locatário.

1. Abra o SSMS e [conecte-se ao servidor tenants1 -&lt;USER&gt;](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
2. Expanda **Bancos de Dados**, clique com botão direito do mouse em **contosoconcerthall**e selecione **Nova Consulta**.
3. Execute as seguintes consultas para explorar a diferença entre as tabelas de único locatário e as exibições globais:

   ```T-SQL
   -- The base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table, which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

Nessas exibições, a *VenueId* é calculada como um hash do nome local, mas qualquer abordagem pode ser usada para introduzir um valor exclusivo. Essa abordagem é semelhante à forma como a chave de locatário é calculada para uso no catálogo.

Para examinar a definição da exibição *Locais*:

1. Em **Pesquisador de Objetos**, expanda **contosoconcerthall** > **Exibições**:

   ![Modos de exibição](media/saas-tenancy-adhoc-analytics/views.png)

2. Clique com botão direito do mouse em **dbo.Venues**.
3. Selecione **Modo de Exibição de Script como** > **CRIAR para** > **Janela do Editor Nova Consulta**

Gere o script de qualquer uma das outras exibições *Local* para ver como elas adicionam a *VenueId*.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Implantar o banco de dados usado para consultas distribuídas ad hoc

Este exercício implanta o banco de dados *adhocreporting*. Esse é o banco de dados principal que contém o esquema usado para consultar em todos os bancos de dados de locatário. O banco de dados é implantado no servidor de catálogo existente, que é o servidor usado para todos os bancos de dados relacionados ao gerenciamento no aplicativo de exemplo.

1. Abra ...\\Módulos de Aprendizado\\Análise Operacional\\Relatórios Ad hoc\\*Demo-AdhocReporting.ps1* no *ISE do PowerShell* e defina os seguintes valores:
   * **$DemoScenario** = 2, **Implantar banco de dados de análise Ad hoc**.

2. Pressione **F5** para executar o script e criar o banco de dados *adhocreporting*.

Na próxima seção, você adiciona o esquema ao banco de dados para que ele possa ser usado para executar consultas distribuídas.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Configurar o banco de dados ‘principal’ para executar consultas distribuídas

Este exercício adiciona o esquema (a fonte de dados externa e as definições de tabela externa) ao banco de dados de análise ad hoc que habilita a consulta em todos os bancos de dados de locatário.

1. Abra o SQL Server Management Studio e conecte-se ao banco de dados Relatórios Ad hoc criado na etapa anterior. O nome do banco de dados é *adhocreporting*.
2. Abra ...\Módulos de Aprendizado\Operational Analytics\Adhoc Reporting\ *Initialize-AdhocReportingDB.sql* no SSMS.
3. Examine o script SQL e observe:

   A Consulta Elástica usa uma credencial com escopo de banco de dados para acessar cada um dos bancos de dados de locatário. Essa credencial precisa estar disponível em todos os bancos de dados e normalmente deve receber os direitos mínimos necessários para permitir essas consultas ad hoc.

    ![criar credencial](media/saas-tenancy-adhoc-analytics/create-credential.png)

   Usando o banco de dados do catálogo como a fonte de dados externa, as consultas são distribuídas para todos os bancos de dados registrados no catálogo quando a consulta é executada. Como os nomes de servidor são diferentes para cada implantação, esse script de inicialização obtém a localização do banco de dados de catálogo recuperando o servidor atual (@@servername) em que o script é executado.

    ![Criar fonte de dados externa](media/saas-tenancy-adhoc-analytics/create-external-data-source.png)

   As tabelas externas que fazem referência a exibições globais descritas na seção anterior e definidas com **DISTRIBUTION = SHARDED(VenueId)**. Como cada *VenueId* mapeia para um banco de dados individual, isso melhora o desempenho em muitos cenários, como mostrado na próxima seção.

    ![criar tabelas externas](media/saas-tenancy-adhoc-analytics/external-tables.png)

   A tabela local *VenueTypes*, que é criada e preenchida. Essa tabela de dados de referência é comum em todos os bancos de dados de locatário, portanto ela pode ser representada como uma tabela local e populada com os dados comuns. Para algumas consultas, isso pode reduzir a quantidade de dados movidos entre os bancos de dados de locatários e o banco de dados *adhocreporting*.

    ![criar tabela](media/saas-tenancy-adhoc-analytics/create-table.png)

   Se você incluir tabelas de referência dessa maneira, certifique-se de atualizar o esquema de tabela e os dados sempre que atualizar os bancos de dados de locatário.

4. Pressione **F5** para executar o script e inicializar o banco de dados *adhocreporting*. 

Agora você pode executar consultas distribuídas e reunir informações entre todos os locatários!

## <a name="run-ad-hoc-distributed-queries"></a>Executar consultas ad hoc distribuídas

Agora que o banco de dados *adhocreporting* está configurado, siga em frente e execute algumas consultas distribuídas. Inclua o plano de execução para uma melhor compreensão sobre onde o processamento de consultas está acontecendo. 

Ao inspecionar o plano de execução, passe o mouse sobre os ícones de plano para obter detalhes. 

É importante observar que configurar **DISTRIBUTION = SHARDED(VenueId)**, quando definida a fonte de dados externa, melhora o desempenho em muitos cenários. Como cada *VenueId* mapeia para um banco de dados individual, a filtragem é facilmente feita de forma remota, retornando apenas os dados necessários.

1. Abra ...\\Módulos de Aprendizado\\Análise Operacional\\Relatórios Ad hoc\\*Demo-AdhocReportingQueries.sql* no SSMS.
2. Verifique se você está conectado ao banco de dados **adhocreporting**.
3. Selecione o menu **Consulta** e clique em **Incluir Plano de Execução Atual**
4. Realce a consulta *Which venues are currently registered? (Quais locais estão registrados no momento?)* e pressione **F5**.

   A consulta retorna a lista de locais inteira, ilustrando o quão rápido e fácil é consultar todos os locatários e retornar dados de cada um.

   Inspecione o plano e veja que todo o custo é a consulta remota, porque cada banco de dados do inquilino manipula sua própria consulta e retorna suas informações de local.

   ![SELECT * FROM dbo.Venues](media/saas-tenancy-adhoc-analytics/query1-plan.png)

5. Selecione a próxima consulta e pressione **F5**.

   Essa consulta une dados dos bancos de dados de locatários e da tabela *VenueTypes* local (local, pois é uma tabela do banco de dados *adhocreporting*).

   Inspecionar o plano e veja que a maior parte do custo é a consulta remota. Cada banco de dados de locatário retorna suas informações de local e executa uma junção local com a tabela *VenueTypes* para exibir o nome amigável.

   ![Unir dados remotos e locais](media/saas-tenancy-adhoc-analytics/query2-plan.png)

6. Agora selecione a consulta *On which day were the most tickets sold? (Em que dia a maioria dos ingressos foi vendida?)* e pressione **F5**.

   Essa consulta faz uma união e uma agregação um pouco mais complexas. O que é importante observar é que a maioria do processamento é feita remotamente e novamente, retorna apenas as linhas necessárias, uma única linha para a contagem de venda de ingressos agregada por dia de cada local.

   ![query](media/saas-tenancy-adhoc-analytics/query3-plan.png)


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]

> * Executar consultas distribuídas entre todos os bancos de dados de locatário
> * Implante um banco de dados de relatórios ad hoc e adicione o esquema a ele para executar consultas distribuídas.


Agora, experimente o [Tutorial de análise de locatário](saas-tenancy-tenant-analytics.md) para explorar a extração de dados para um banco de dados de análise separado para o processamento de análise mais complexo.

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais criados com base no aplicativo implantação inicial do aplicativo Wingtip Tickets SaaS Database Per Tenant](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Consulta elástica](sql-database-elastic-query-overview.md)
