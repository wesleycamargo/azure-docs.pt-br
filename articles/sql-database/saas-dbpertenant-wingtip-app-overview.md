---
title: "Exemplo de aplicativo de multilocatário do Banco de Dados SQL do Azure – Wingtip SaaS | Microsoft Docs"
description: "Aprenda usando um aplicativo de multilocatário de exemplo que usa o Banco de Dados SQL do Azure, o exemplo do Wingtip SaaS"
keywords: tutorial do banco de dados SQL
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: sstein
ms.openlocfilehash: 2871d2b1208013808958e8a5b0c62fce31af86ec
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2018
---
# <a name="introduction-to-a-multi-tenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Introdução a um aplicativo SaaS multilocatário que usa o padrão de banco de dados por locatário com o Banco de Dados SQL

O aplicativo *SaaS Wingtip* é um exemplo de aplicativo multilocatário. O aplicativo usa o padrão de aplicativo SaaS de um banco de dados por locatário para atender a vários locatários. O aplicativo demonstra recursos do Banco de Dados SQL do Azure que habilitam cenários de SaaS usando vários padrões de design e gerenciamento de SaaS. Para obter rapidamente e executar, o aplicativo Wingtip SaaS é implantado em menos de cinco minutos!

O código-fonte do aplicativo e os scripts de gerenciamento estão disponíveis no repositório [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) do GitHub. Antes de começar, confira as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas a fim de baixar e desbloquear os scripts SaaS do Wingtip Tickets.

## <a name="application-architecture"></a>Arquitetura do aplicativo

O aplicativo SaaS Wingtip usa o modelo de banco de dados por locatário e usa pools elásticos de SQL para maximizar a eficiência. Para provisionar e mapear locatários para os seus dados, é usado um banco de dados do catálogo. O aplicativo SaaS Wingtip principal, usa um pool com três locatários de exemplo, além do banco de dados do catálogo. Completando muitos dos tutoriais do SaaS Wingtip estão os complementos para a implantação inicial, introduzindo bancos de dados analíticos, bancos de dados gerenciamento de esquema, etc.


![Arquitetura de SaaS Wingtip](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Ao percorrer os tutoriais e trabalhar com o aplicativo, é importante ter em mente os padrões de SaaS como eles se relacionam com a camada de dados. Em outras palavras, concentre-se na camada de dados e não na análise excessiva do aplicativo em si. A compreensão da implementação desses padrões de SaaS é a chave para implementar esses padrões nos seus aplicativos, considerando todas as modificações necessárias para as suas necessidades corporativas específicas.

## <a name="sql-database-wingtip-saas-tutorials"></a>Tutoriais do SaaS Wingtip do Banco de Dados SQL

Depois de implantar o aplicativo, explore a seguinte coleção de tutoriais que aproveitam a implantação inicial. Esses tutoriais exploram padrões comuns de SaaS que aproveitam os recursos internos do banco de dados SQL, SQL Data Warehouse e outros serviços do Azure. Os tutoriais incluem scripts PowerShell, com explicações detalhadas que simplificam bastante a compreensão e a implementação dos mesmos padrões de gerenciamento de SaaS em seus aplicativos.


| Tutorial | DESCRIÇÃO |
|:--|:--|
| [Diretrizes e dicas para o exemplo de aplicativo SaaS de multilocatário do Banco de Dados SQL do Azure](saas-tenancy-wingtip-app-guidance-tips.md) | **COMECE AQUI!** Baixe e execute scripts do PowerShell para preparar as partes do aplicativo. |
|[Implantar e explorar o aplicativo SaaS Wingtip](saas-dbpertenant-get-started-deploy.md)|  Implantar e explorar o aplicativo SaaS Wingtip à sua assinatura do Azure. |
|[Provisionar e catalogar locatários](saas-dbpertenant-provision-and-catalog.md)| Saiba como o aplicativo se conecta aos locatários usando um banco de dados do catálogo, e como o catálogo mapeia locatários para seus dados. |
|[Monitorar e gerenciar o desempenho](saas-dbpertenant-performance-monitoring.md)| Saiba como usar os recursos de monitoramento do Banco de Dados SQL e como definir alertas quando os limites de desempenho são excedidos. |
|[Monitorar com o Log Analytics (OMS)](saas-dbpertenant-log-analytics.md) | Saiba mais sobre como usar [Log Analytics](../log-analytics/log-analytics-overview.md) para monitorar grandes quantidades de recursos, em vários pools. |
|[Restaurar um único locatário](saas-dbpertenant-restore-single-tenant.md)| Saiba como restaurar um banco de dados de locatário em um ponto anterior no tempo. As etapas para restaurar um banco de dados paralelo, deixando o banco de dados existente do locatário online, também estão incluídas. |
|[Gerenciar o esquema de banco de dados do locatário](saas-tenancy-schema-management.md)| Saiba como atualizar o esquema e atualizar dados de referência, em todos os bancos de dados de locatários. |
|[Executar consultas distribuídas entre locatários](saas-tenancy-cross-tenant-reporting.md) | Criar um banco de dados de análise ad hoc e executar consultas distribuídas em tempo real em todos os locatários.  |
|[Executar análise em dados extraídos de locatário](saas-tenancy-tenant-analytics.md) | Extraia dados de locatário em um análise banco de dados ou data warehouse para consultas analíticas offline. |


## <a name="next-steps"></a>Próximas etapas

- [Diretrizes gerais e dicas ao implementar e usar o exemplo de aplicativo SaaS Wingtip Tickets](saas-tenancy-wingtip-app-guidance-tips.md)

- [Implantar o aplicativo SaaS Wingtip](saas-dbpertenant-get-started-deploy.md)
