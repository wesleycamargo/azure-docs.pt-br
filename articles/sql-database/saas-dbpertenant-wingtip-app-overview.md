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
ms.openlocfilehash: ddd51c23c7e7d01e38b02c79c27d1951eea61e70
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2017
---
# <a name="introduction-to-a-sql-database-multi-tenant-saas-app-example"></a>Introdução a um exemplo de aplicativo SaaS multilocatário do Banco de Dados SQL

O aplicativo *SaaS Wingtip* é um aplicativo multilocatário de exemplo que demonstra as vantagens exclusivas do Banco de Dados SQL. O aplicativo usa um padrão de aplicativo SaaS de um banco de dados por locatário para atender a vários locatários. O aplicativo foi projetado para demonstrar os recursos do Banco de Dados SQL do Azure que habilitam cenários de SaaS, incluindo vários padrões de design e gerenciamento de SaaS. Para obter rapidamente e executar, o aplicativo Wingtip SaaS é implantado em menos de cinco minutos!

Os scripts de gerenciamento e o código-fonte do aplicativo estão disponíveis no repositório GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Para executar os scripts, [baixe a pasta Módulos de Aprendizado](#download-and-unblock-the-wingtip-saas-scripts) em seu computador local.

## <a name="application-architecture"></a>Arquitetura do aplicativo

O aplicativo SaaS Wingtip usa o modelo de banco de dados por locatário e usa pools elásticos de SQL para maximizar a eficiência. Para provisionar e mapear locatários para os seus dados, é usado um banco de dados do catálogo. O aplicativo SaaS Wingtip principal, usa um pool com três locatários de exemplo, além do banco de dados do catálogo. Completando muitos dos tutoriais do SaaS Wingtip estão os complementos para a implantação inicial, introduzindo bancos de dados analíticos, bancos de dados gerenciamento de esquema, etc.


![Arquitetura de SaaS Wingtip](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Ao percorrer os tutoriais e trabalhar com o aplicativo, é importante ter em mente os padrões de SaaS como eles se relacionam com a camada de dados. Em outras palavras, concentre-se na camada de dados e não na análise excessiva do aplicativo em si. A compreensão da implementação desses padrões de SaaS é a chave para implementar esses padrões nos seus aplicativos, considerando todas as modificações necessárias para as suas necessidades corporativas específicas.

## <a name="sql-database-wingtip-saas-tutorials"></a>Tutoriais do SaaS Wingtip do Banco de Dados SQL

Depois de implantar o aplicativo, explore a seguinte coleção de tutoriais que aproveitam a implantação inicial. Esses tutoriais exploram padrões comuns de SaaS que aproveitam os recursos internos do banco de dados SQL, SQL Data Warehouse e outros serviços do Azure. Os tutoriais incluem scripts PowerShell, com explicações detalhadas que simplificam bastante a compreensão e a implementação dos mesmos padrões de gerenciamento de SaaS em seus aplicativos.


| Tutorial | Descrição |
|:--|:--|
| [Diretrizes e dicas para o exemplo de aplicativo SaaS de multilocatário do Banco de Dados SQL do Azure](saas-dbpertenant-wingtip-app-guidance-tips.md) | **COMECE AQUI!** Baixe e execute scripts do PowerShell para preparar as partes do aplicativo. |
|[Implantar e explorar o aplicativo SaaS Wingtip](saas-dbpertenant-get-started-deploy.md)|  Implantar e explorar o aplicativo SaaS Wingtip à sua assinatura do Azure. |
|[Provisionar e catalogar locatários](saas-dbpertenant-provision-and-catalog.md)| Saiba como o aplicativo se conecta aos locatários usando um banco de dados do catálogo, e como o catálogo mapeia locatários para seus dados. |
|[Monitorar e gerenciar o desempenho](saas-dbpertenant-performance-monitoring.md)| Saiba como usar os recursos de monitoramento do Banco de Dados SQL e como definir alertas quando os limites de desempenho são excedidos. |
|[Monitorar com o Log Analytics (OMS)](saas-dbpertenant-log-analytics.md) | Saiba mais sobre como usar [Log Analytics](../log-analytics/log-analytics-overview.md) para monitorar grandes quantidades de recursos, em vários pools. |
|[Restaurar um único locatário](saas-dbpertenant-restore-single-tenant.md)| Saiba como restaurar um banco de dados de locatário em um ponto anterior no tempo. As etapas para restaurar um banco de dados paralelo, deixando o banco de dados existente do locatário online, também estão incluídas. |
|[Gerenciar esquemas de locatário](saas-tenancy-schema-management.md)| Saiba como atualizar o esquema e atualizar dados de referência, em todos os locatários Wingtip SaaS. |
|[Executar análise local](saas-tenancy-adhoc-analytics.md) | Criar um banco de dados de análise ad hoc e executar consultas distribuídas em tempo real em todos os locatários.  |
|[Executar análise de locatário](saas-tenancy-tenant-analytics.md) | Extrai dados de locatário em um análise banco de dados ou data warehouse para executar consultas analíticas offline. |


## <a name="next-steps"></a>Próximas etapas

- [Diretrizes e dicas para o exemplo de aplicativo SaaS de multilocatário do Banco de Dados SQL do Azure](saas-dbpertenant-wingtip-app-guidance-tips.md)

- [Implantar o aplicativo SaaS Wingtip](saas-dbpertenant-get-started-deploy.md)
