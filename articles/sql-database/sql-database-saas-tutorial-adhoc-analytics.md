---
title: "Executar consultas de análise ad hoc entre vários bancos de dados Azure SQL | Microsoft Docs"
description: "Executar consultas de análise ad hoc entre vários bancos de dados em um aplicativo multilocatário"
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
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: dd41e7f1f131f6c18e03d2434982c3d681342b8b
ms.contentlocale: pt-br
ms.lasthandoff: 05/12/2017


---
# <a name="run-ad-hoc-analytics-queries-across-all-wtp-tenants"></a>Executar consultas de análise ad hoc em todos os locatários WTP

Neste tutorial, você criará um banco de dados de análise ad hoc e executará diversas consultas em todos os locatários. Essas consultas podem extrair informações escondidas nos dados operacionais diários do aplicativo WTP.

Para executar consultas de análise ad hoc (entre vários locatários), o aplicativo WTP utiliza [Consulta Elástica](sql-database-elastic-query-overview.md) juntamente com um banco de dados de análise.


Neste tutorial, você aprenderá a:

> [!div class="checklist"]

> * Implantar um banco de dados de análise ad hoc
> * Executar consultas distribuídas entre todos os bancos de dados de locatário



Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O aplicativo WTP foi implantado. Para implantar em menos de cinco minutos, consulte [Implantar e explorar o aplicativo WTP SaaS](sql-database-saas-tutorial.md)
* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)


## <a name="ad-hoc-analytics-pattern"></a>Padrão de análise ad hoc

Uma das ótimas oportunidades com aplicativos SaaS é usar a grande quantidade de dados do locatário armazenados centralmente na nuvem. Use esses dados para obter informações sobre a operação e o uso de aplicativos, locatários, usuários e sobre suas preferências e comportamentos. Essas informações que você encontrar podem guiar o desenvolvimento do recurso, aprimoramentos de usabilidade e outros investimentos no aplicativo e nos serviços.

É fácil acessar esses dados em um único banco de dados multilocatário, mas não tão fácil quando são distribuídos em escala por, potencialmente, milhares de bancos de dados. Uma abordagem é usar a Consulta Elástica, que permite consultas ad hoc em um conjunto distribuído de bancos de dados com esquema comum. A Consulta Elástica usa um único banco de dados *principal* no qual as tabelas externas são definidas que espelham tabelas nos bancos de dados distribuídos (de locatário). As consultas enviadas ao banco de dados principal são compiladas para produzir um plano de consulta distribuído, com partes da consulta propagadas para os bancos de dados de locatário, conforme necessário. As Consulta Elásticas usam o mapa de fragmento no banco de dados de catálogo para fornecer o local dos bancos de dados de locatário. A instalação e a consulta são diretas usando o T-SQL normal e dá suporte para consultas ad hoc de ferramentas, como o Power BI e o Excel.

## <a name="get-the-wingtip-application-scripts"></a>Obter os scripts do aplicativo Wingtip

Os scripts do Wingtip Tickets e o código-fonte do aplicativo estão disponíveis no repositório GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Os arquivos de script estão localizados na [pasta de Módulos de Aprendizado](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Baixe a pasta **Módulos de Aprendizado** em seu computador local, mantendo a estrutura de pastas.

## <a name="deploy-the-database-used-for-ad-hoc-analytics-queries"></a>Implantar o banco de dados usado para consultas de análise ad hoc

Este exercício implanta o banco de dados de Análise Ad hoc, que contém o esquema usado para emitir consultas ad hoc que abrangem todos os bancos de dados de locatário.

1. Abra... \\Módulos de Aprendizado\\Análise Operacional\\Análise Adhoc\\*Demo-AdhocAnalytics.ps1* no *PowerShell ISE* e defina os seguintes valores:
   * **$DemoScenario** = 2, **Implantar banco de dados de análise Ad hoc**.

1. Pressione **F5** para executar o script e criar um novo banco de dados SQL para análise ad hoc e adicioná-lo ao catálogo de WTP. Tabelas de locais, tíquetes e compras de tíquete são adicionadas como tabelas externas que podem ser consultadas.
   Tudo bem se você encontrar avisos aqui do tipo *O servidor RPC está indisponível*.


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

[Tutorial do Log Analytics (OMS)](sql-database-saas-tutorial-log-analytics.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que aproveitam a implantação inicial do aplicativo WTP (Wingtip Tickets Platform)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Consulta elástica](sql-database-elastic-query-overview.md)

