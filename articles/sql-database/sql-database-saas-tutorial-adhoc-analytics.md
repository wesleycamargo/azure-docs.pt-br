---
title: "Executar consultas de análise ad hoc entre vários bancos de dados Azure SQL | Microsoft Docs"
description: "Execute consultas de análise ad hoc em vários bancos de dados SQL no aplicativo SaaS Wingtip multilocatário."
keywords: tutorial do banco de dados SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/22/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 31be50ca3f64cc183e516f1b0f06f5a4265f6103
ms.contentlocale: pt-br
ms.lasthandoff: 05/25/2017


---
# <a name="run-ad-hoc-analytics-queries-across-all-wingtip-saas-tenants"></a>Executar consultas de análise ad hoc em todos os locatários SaaS Wingtip

Neste tutorial, você criará um banco de dados de análise ad hoc e executará diversas consultas em todos os locatários. Essas consultas podem extrair informações escondidas nos dados operacionais diários do aplicativo WTP.

Para executar consultas de análise ad hoc (entre vários locatários), o aplicativo SaaS Wingtip utiliza [Consulta Elástica](sql-database-elastic-query-overview.md) juntamente com um banco de dados de análise.


Neste tutorial, você aprenderá a:

> [!div class="checklist"]

> * Implantar um banco de dados de análise ad hoc
> * Executar consultas distribuídas entre todos os bancos de dados de locatário



Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O aplicativo SaaS Wingtip é implantado. Para implantar em menos de cinco minutos, confira [Implantar e explorar o aplicativo de SaaS do Wingtip](sql-database-saas-tutorial.md)
* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)


## <a name="ad-hoc-analytics-pattern"></a>Padrão de análise ad hoc

Uma das ótimas oportunidades com aplicativos SaaS é usar a grande quantidade de dados do locatário armazenados centralmente na nuvem. Use esses dados para obter informações sobre a operação e o uso de aplicativos, locatários, usuários, suas preferências, comportamentos, etc. Essas informações podem guiar o desenvolvimento do recurso, aprimoramentos de usabilidade e outros investimentos no aplicativo e nos serviços.

É fácil acessar esses dados em um único banco de dados multilocatário, mas não tão fácil quando são distribuídos em escala por, potencialmente, milhares de bancos de dados. Uma abordagem é usar a Consulta Elástica, que permite consultas ad hoc em um conjunto distribuído de bancos de dados com esquema comum. A Consulta Elástica usa um único banco de dados *principal* no qual as tabelas externas são definidas que espelham tabelas ou modos de exibição nos bancos de dados distribuídos (de locatário). As consultas enviadas ao banco de dados principal são compiladas para produzir um plano de consulta distribuído, com partes da consulta propagadas para os bancos de dados de locatário, conforme necessário. As Consulta Elásticas usam o mapa de fragmento no banco de dados de catálogo para fornecer o local dos bancos de dados de locatário. A instalação e a consulta são diretas usando o [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) padrão e dá suporte para consultas ad hoc de ferramentas, como o Power BI e o Excel.

## <a name="get-the-wingtip-application-scripts"></a>Obter os scripts do aplicativo Wingtip

Os scripts de SaaS do Wingtip e o código-fonte do aplicativo estão disponíveis no repositório GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). [Etapas para baixar os scripts do SaaS Wingtip](sql-database-wtp-overview.md#download-the-wingtip-saas-scripts).


## <a name="explore-the-global-views-in-the-tenant-databases"></a>Explorar os modos de exibição global no bancos de dados locatários

O aplicativo SaaS Wingtip é criado usando um modelo de locatário por banco de dados e, por isso, o esquema de banco de dados do locatário é definido de uma perspectiva de locatário único. As informações específicas do locatário existem em uma tabela, *Local*, que sempre tem uma única linha e é projetada como um heap, sem uma chave primária.  As outras tabelas no esquema não precisam estar relacionadas com a tabela *Local* porque, em condições normais, nunca existe dúvida sobre a qual locatário pertencem os dados.  No entanto, ao consultar em todos os bancos de dados, é relevante correlacionar os dados de tabelas no banco de dados com um locatário específico. Para simplificar, um conjunto de modos de exibição é adicionado ao banco de dados locatário que fornece um modo de exibição 'global' de cada locatário. Esses modos de exibição global projetam uma ID de locatário em cada tabela que será consultada globalmente. Isso facilita a identificação dos dados de cada locatário. Como uma conveniência, esses modos de exibição têm sido criados previamente em todos os bancos de dados de locatário (bem como o banco de dados dourado, para que essas exibições fiquem disponíveis conforme novos locatários são provisionados).

1. Abra o SSMS e [conecte-se ao servidor tenants1 -&lt;USER&gt;](sql-database-wtp-overview.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Expanda **Bancos de Dados**, clique com botão direito do mouse em **contosoconcerthall**e selecione **Nova Consulta**.
1. Execute as seguintes consultas para explorar os modos de exibição global:

   ```T-SQL
   -- This is the base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   -- In the sample database we calculated an integer id from a hash of the Venue name,
   -- but any approach could be used to introduce a unique value.
   -- This is similar to how we create the tenant key in the catalog,
   -- but there is no requirement that the catalog key and this id be the same.
   SELECT * FROM Venues

   -- The base Events table which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

Para examinar um modo de exibição e ver como ele é criado:

1. Em **Gerenciador de Objeto**, expanda **contosoconcethall** > **Exibições**:

   ![Modos de exibição](media/sql-database-saas-tutorial-adhoc-analytics/views.png)

1. Clique com botão direito do mouse em **dbo.Venues**.
1. Selecione **Modo de Exibição de Script como** > **CRIAR para** > **Janela do Editor Nova Consulta**

Faça isso para qualquer *Modo de Exibição* a fim de examinar como ele é criado.

## <a name="deploy-the-database-used-for-ad-hoc-analytics-queries"></a>Implantar o banco de dados usado para consultas de análise ad hoc

Neste exercício implanta o banco de dados *adhocanalytics*. Esse banco de dados contém o esquema usado para consultar em todos os bancos de dados de locatário. O banco de dados é implantado no servidor de catálogo existente, que é o servidor que contém todos os bancos de dados relacionados ao gerenciamento.

1. Abra... \\Módulos de Aprendizado\\Análise Operacional\\Análise Adhoc\\*Demo-AdhocAnalytics.ps1* no *PowerShell ISE* e defina os seguintes valores:
   * **$DemoScenario** = 2, **Implantar banco de dados de análise Ad hoc**.

1. Role para baixo no script até o script SQL que contém o esquema do banco de dados.  Examine o script e observe o seguinte:

   1. A Consulta Elástica usa uma credencial com escopo de banco de dados para acessar cada um dos bancos de dados de locatário. Essa credencial precisa estar disponível em todos os bancos de dados e normalmente recebem os direitos mínimos para permitir essas consultas ad hoc.
   1. A fonte de dados externa, que é definida para usar o mapa do fragmento locatário no banco de dados de catálogo.  Usando isso como fonte de dados externa, as consultas serão distribuídas para todos os bancos de dados registrados no catálogo no ponto em que a consulta é emitida.
   1. As tabelas externas que fazem referência a modos de exibição global descritos na seção anterior.
   1. A tabela local *VenueTypes*, que é criada e preenchida.  Como esta tabela de dados de referência é comum em todos os bancos de dados de locatário, ela pode ser representada como uma tabela local, que pode reduzir a quantidade de dados movidos entre os bancos de dados do locatário para algumas consultas e o banco de dados *adhocanalytics*.

1. Pressione **F5** para executar o script e criar o banco de dados *adhocanalytics*.

   Tudo bem se você ignorar avisos aqui do tipo *O servidor RPC está indisponível*.


Agora você tem um banco de dados *adhocanalytics*, que pode ser usado para executar consultas distribuídas e reunir informações em todos os locatários!

## <a name="run-ad-hoc-analytics-queries"></a>Executar consultas de análise Ad hoc

Este exercício executa consultas de análise do ad hoc para descobrir informações de locatário do aplicativo WTP.

1. Abra... \\Módulos de Aprendizado\\Análise Operacional\\Análise Ad Hoc\\*Demo-AdhocAnalyticsQueries.sql* no SSMS.
1. Verifique se está conectado ao banco de dados do **adhocanalytics**
1. Selecione a consulta individual que deseja executar e pressione **F5**:

    ![query](media/sql-database-saas-tutorial-adhoc-analytics/query.png)




## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]

> * Implantar um banco de dados de análise ad hoc
> * Executar consultas distribuídas entre todos os bancos de dados de locatário

Agora, experimente o [Tutorial de análise de locatário](sql-database-saas-tutorial-tenant-analytics.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que aproveitam a implantação de aplicativo SaaS Wingtip inicial](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Consulta elástica](sql-database-elastic-query-overview.md)

