---
title: "Aplicativo multilocatário de exemplo de Banco de Dados do Azure SQL | Microsoft Docs"
description: "Introdução ao aplicativo WTP (Wingtip Tickets) de exemplo de Banco de Dados SQL"
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
ms.openlocfilehash: 265eab8104d8af7c510a88dffb9d70a2b3b37631
ms.contentlocale: pt-br
ms.lasthandoff: 05/12/2017


---
# <a name="introduction-to-the-wingtip-tickets-platform-wtp-sample-saas-application"></a>Introdução ao aplicativo SaaS WTP (Wingtip Tickets Platform) de exemplo

O aplicativo SaaS WTP (Wingtip Tickets Platform) é um aplicativo multilocatário de exemplo que demonstra as vantagens exclusivas do Banco de Dados SQL. O aplicativo usa um padrão de aplicativo SaaS de um banco de dados por locatário para atender a vários locatários. O aplicativo WTP foi projetado para demonstrar os recursos do Banco de Dados SQL do Azure que habilitam cenários de SaaS, incluindo padrões de design e gerenciamento de SaaS. Para entrar rapidamente em funcionamento, [o aplicativo WTP é implantado em menos de cinco minutos](sql-database-saas-tutorial.md)!

Depois de implantar o aplicativo WTP, explore a [coleção de tutoriais](#sql-database-saas-tutorials) que aproveitam a implantação inicial. Cada tutorial concentra-se em tarefas típicas que são implementadas em aplicativos SaaS. As tarefas são implementadas seguindo os padrões de SaaS que aproveitam os recursos internos do Banco de Dados SQL. Os padrões descritos incluem, provisionamento de novos locatários, restauração de bancos de dados de locatário, execução de consultas distribuídas entre todos os locatários e disponibilização de alterações de esquema em todos os bancos de dados de locatário. Cada tutorial inclui scripts reutilizáveis, com explicações detalhadas que simplificam bastante a compreensão e a implementação dos mesmos padrões de gerenciamento de SaaS em seus aplicativos.

Ainda que o aplicativo WTP seja um tanto completo e interessante como um aplicativo de exemplo, é importante concentrar-se nos padrões principais de SaaS, pois eles se relacionam com a camada de dados. Em outras palavras, concentre-se na camada de dados e não na análise excessiva do aplicativo em si. A compreensão da implementação desses padrões principais de SaaS é a chave para implementar esses padrões nos seus aplicativos, considerando todas as modificações necessárias para as suas necessidades corporativas específicas.



## <a name="application-architecture"></a>Arquitetura do aplicativo

O aplicativo WTP usa o modelo de banco de dados por locatário e usa pools elásticos de SQL para maximizar a eficiência.
Uso de um catálogo de locatário para provisionamento, gerenciamento e conectividade.
Aplicativo, pool e banco de dados de monitoramento integrados, além de alertas (OMS).
Gerenciamento de dados de referência e de esquema entre locatários (trabalhos de banco de dados elástico).
Consulta entre locatários e análise operacional (consulta elástica).
Uso de dados distribuídos geograficamente para alcance expandido.
Recuperação de único locatário para continuidade de negócios (PITR), RD em escala (restauração geográfica, replicação geográfica, RD automática), gerenciamento de autoatendimento de locatário (por meio de APIs de gerenciamento), PITR para recuperação de Opa! autoinfligido.

O aplicativo Wingtip principal, usa um pool com três locatários de exemplo, além de um banco de dados do catálogo.

![Arquitetura do WTP](media/sql-database-wtp-overview/wtp-architecture.png)


## <a name="sql-database-wtp-saas-tutorials"></a>Tutoriais do SaaS WTP do Banco de Dados SQL

Os seguintes tutoriais aproveitam a implantação inicial do [aplicativo SaaS Wingtip Tickets Platform de exemplo](sql-database-saas-tutorial.md):

| Área | Descrição | Local do script |
|:--|:--|:--|
|[Tutorial para provisionar e catalogar locatários](sql-database-saas-tutorial-provision-and-catalog.md)| Provisionar novos locatários e registrá-los no catálogo | [Scripts no GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Provision%20and%20Catalog) |
|[Tutorial para monitorar e gerenciar o desempenho](sql-database-saas-tutorial-performance-monitoring.md)| Monitorar e gerenciar o desempenho do banco de dados e do pool | [Scripts no GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Performance%20Monitoring%20and%20Management) |
|[Tutorial para restaurar um locatário único](sql-database-saas-tutorial-restore-single-tenant.md)| Restaurar bancos de dados de locatário | [Scripts no GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Business%20Continuity%20and%20Disaster%20Recovery/RestoreTenant) |
|[Tutorial para gerenciar esquema de locatário](sql-database-saas-tutorial-schema-management.md)| Executar consultas em todos os locatários  | [Scripts no GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Schema%20Management) |
|[Tutorial para executar análise ad hoc](sql-database-saas-tutorial-adhoc-analytics.md) | Criar um banco de dados de análise ad hoc e executar consultas em todos os locatários  | [Scripts no GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Operational%20Analytics/Adhoc%20Analytics) |
|[Tutorial para gerenciar com o Log Analytics (OMS)](sql-database-saas-tutorial-log-analytics.md) | Configurar e explorar o Log Analytics | [Scripts no GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Performance%20Monitoring%20and%20Management/LogAnalytics) |
|[Tutorial para executar análise de locatário](sql-database-saas-tutorial-tenant-analytics.md) | Configurar e executar consultas de análise de locatário | [Scripts no GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Operational%20Analytics/Tenant%20Analytics) |

## <a name="get-the-wingtip-application-scripts"></a>Obter os scripts do aplicativo Wingtip

Os scripts do Wingtip Tickets e o código-fonte do aplicativo estão disponíveis no repositório GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Os arquivos de script estão localizados na [pasta de Módulos de Aprendizado](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Baixe a pasta **Módulos de Aprendizado** em seu computador local, mantendo a estrutura de pastas.

## <a name="working-with-the-wtp-powershell-scripts"></a>Trabalhando com os scripts do PowerShell do WTP

Os benefícios do trabalho com o aplicativo WTP vêm do aprofundamento nos scripts fornecidos e do exame de como os diferentes padrões de SaaS são implementados.

Para exibir os módulos e os scripts fornecidos e para facilitar percorrê-los para uma melhor compreensão, use o [ISE do Windows PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). Como a maioria dos scripts prefixados com *Demo-* contém variáveis que podem ser modificadas antes da execução, o uso do ISE do PowerShell simplifica o trabalho com esses scripts.

Para cada implantação de aplicativo WTP, há um arquivo **UserConfig.psm1** contendo dois parâmetros de configuração dos valores de grupo de recursos e nome de usuário que você definiu durante a implantação. Após a conclusão da implantação, edite o módulo do **UserConfig.psm1** configurando os parâmetros _ResourceGroupName_ e _Nome_. Esses valores são usados por outros scripts para serem executados com êxito, portanto, é recomendável defini-los quando a implantação for concluída!



### <a name="execute-scripts-by-pressing-f5"></a>Executar Scripts pressionando F5

Vários scripts usam *$PSScriptRoot* para permitir a navegação em pastas e essa variável é avaliada somente quando o script é executado, ao pressionar **F5**.  Realçar e executar uma seleção (**F8**) poderá resultar em erros, então pressione **F5** ao executar scripts do WTP.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Percorrer os scripts para examinar a implementação

O valor real da exploração dos scripts é obtido ao percorrê-los a fim de ver o que eles fazem. Confira os scripts _Demo-_ de primeiro nível que fornecem um fluxo de trabalho de alto nível fácil de ler, mostrando as etapas necessárias para realizar cada tarefa. Investigue mais as chamadas individuais para ver os detalhes de implementação dos diferentes padrões de SaaS.

Dicas para o trabalho com scripts e a [depuração de scripts do PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise):

* Abra e configure os scripts demo- no ISE do PowerShell.
* Execute ou continue com o **F5**. O uso de **F8** não é recomendável porque o *$PSScriptRoot* não é avaliado durante a execução de seleções de um script.
* Coloque pontos de interrupção, clicando ou selecionando uma linha e pressionando **F9**.
* Passe por uma função ou chamada de script usando **F10**.
* Intervenha em uma função ou chamada de script usando **F11**.
* Saia da função atual ou da chamada de script usando **Shift + F11**.




## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Explorar o esquema de banco de dados e executar consultas SQL usando o SSMS

Use o [SSMS (SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para se conectar e procurar os servidores e bancos de dados do WTP.

O aplicativo de exemplo WTP inicialmente tem dois servidores de Banco de Dados SQL aos quais se conectar – o servidor *tenants1* e o servidor *catalog*:


1. Abra o *SSMS* e se conecte ao servidor *tenants1-&lt;User&gt;.database.windows.net*.
2. Clique em **Conectar** > **Mecanismo de Banco de Dados...**:

   ![servidor catalog](media/sql-database-wtp-overview/connect.png)

1. As credenciais de demonstração são: logon = *developer*, senha = *P@ssword1*

   ![connection](media\sql-database-wtp-overview\tenants1-connect.png)

1. Repita as etapas 2 a 3 e conecte-se ao servidor *catalog-&lt;User&gt;.database.windows.net*.

Após se conectar com êxito, você deverá ver ambos os servidores. Você poderá ver mais ou menos bancos de dados dependendo de quantos locatários provisionar:

![pesquisador de objetos](media/sql-database-wtp-overview/object-explorer.png)



## <a name="next-steps"></a>Próximas etapas

[Implantar o aplicativo SaaS Wingtip Tickets de exemplo](sql-database-saas-tutorial.md)
