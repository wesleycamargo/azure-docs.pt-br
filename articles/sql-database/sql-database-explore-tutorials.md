---        
title: Explorar Tutoriais do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre recursos e funcionalidades do Banco de Dados SQL
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 04c0fd7f-d260-4e43-a4f0-41cdcd5e3786
ms.service: sql-database
ms.custom: overview
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 02/08/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 9b09383350284c8f1cd3e384c802c25c962b1226
ms.openlocfilehash: 1fe15e7ad3667d42995cd487c793fae496216fca
ms.lasthandoff: 02/16/2017

        
---
 
# <a name="explore-azure-sql-database-tutorials"></a>Explorar Tutoriais do Banco de Dados SQL do Azure
Os links nas tabelas a seguir levam você a uma visão geral de cada área de recurso listado e a um tutorial com passo a passo simples para cada área. 

## <a name="create-servers-databases-and-server-level-firewall-rules"></a>Criar servidores, bancos de dados e regras de firewall de nível de servidor
Nos tutoriais a seguir, você criará servidores, bancos de dados e regras de firewall de nível de servidor e aprenderá como se conectar e consultar servidores e bancos de dados.

| Tutorial | Descrição |
| --- | --- | 
| [Seu primeiro banco de dados SQL do Azure](sql-database-get-started.md) | Ao concluir este tutorial de início rápido, você terá um banco de dados de exemplo e um banco de dados em branco em execução em um grupo de recursos do Azure e conectado a um servidor lógico. Você também criará duas regras de firewall de nível de servidor configuradas para habilitar a entidade de segurança no nível do servidor a fazer logon no servidor por meio de dois endereços IP especificados. Por fim, você aprenderá a consultar um banco de dados no portal do Azure, conectar e consultar usando o SQL Server Management Studio. |
| [Provisionar e acessar um Banco de Dados SQL do Azure usando o PowerShell](sql-database-get-started-powershell.md) | Ao concluir este tutorial, você terá um banco de dados de exemplo e um banco de dados em branco em execução em um grupo de recursos do Azure e conectado a um servidor lógico. Você também terá uma regra de firewall de nível de servidor configurada para habilitar a entidade de segurança no nível do servidor a fazer logon no servidor de um endereço IP especificado (ou intervalo de endereços IP). |
| [Use o C# para criar um banco de dados SQL com a Biblioteca do Banco de Dados SQL para .NET](sql-database-get-started-csharp.md)| Neste tutorial, você usa o C# para criar um servidor de Banco de Dados SQL, a regra de firewall e o banco de dados SQL. Você também cria um aplicativo do Active Directory (AD) e a entidade de serviço necessária para autenticar o aplicativo C#. |
|  | |

## <a name="backups-long-term-retention-and-database-recovery"></a>Backups, retenção de longo prazo e recuperação de banco de dados
Nos tutoriais a seguir, você aprenderá a usar [backups de banco de dados](sql-database-automated-backups.md), [retenção de backup de longo prazo](sql-database-long-term-retention.md) e [recuperação de banco de dados usando backups](sql-database-recovery-using-backups.md).

| Tutorial | Descrição |
| --- | --- | 
| [Recuperação e backup usando o portal do Azure](sql-database-get-started-backup-recovery-portal.md) | Neste tutorial, você aprenderá a usar o portal do Azure para exibir e recuperar backups a partir de um momento específico, configurar retenção de backup de longo prazo e recuperar a partir de um backup no cofre dos serviços de recuperação do Azure
| [Fazer backup e restaurar usando o PowerShell](sql-database-get-started-backup-recovery-powershell.md) | Neste tutorial, você aprenderá a usar o PowerShell para exibir os backups, recuperar backups a partir de um momento específico, configurar retenção de backup de longo prazo e recuperar de um backup no cofre dos serviços de recuperação do Azure
|  | |

## <a name="sharded-databases"></a>Bancos de dados fragmentados
Nos tutoriais a seguir, você aprenderá como [expandir bancos de dados com o gerenciador de mapa de fragmento](sql-database-elastic-scale-shard-map-management.md).

| Tutorial | Descrição |
| --- | --- | 
| [Como criar um aplicativo fragmentado](sql-database-elastic-scale-get-started.md) |Nesse tutorial, você aprenderá a usar as funcionalidades das ferramentas do banco de dados elástico usando um aplicativo fragmentado simples. |
| [Implantar um serviço de divisão e mesclagem](sql-database-elastic-scale-configure-deploy-split-and-merge.md) |Nesse tutorial, você aprenderá a mover dados entre bancos de dados fragmentados. |
|  | |

## <a name="elastic-database-jobs"></a>Trabalhos de banco de dados elástico
Nos tutoriais a seguir, você aprenderá a usar [trabalhos de banco de dados elásticos](sql-database-elastic-jobs-overview.md).

| Tutorial | Descrição |
| --- | --- | 
| [Introdução aos trabalhos elásticos do Banco de Dados SQL do azure](sql-database-elastic-jobs-getting-started.md) |Nesse tutorial, você aprenderá a criar e a gerenciar trabalhos que gerenciam um grupo de bancos de dados relacionados. |
|  | |

## <a name="elastic-queries"></a>Consultas elásticas
Nos tutoriais a seguir, você aprenderá sobre a execução de [consultas elásticas](sql-database-elastic-query-overview.md).

| Tutorial | Descrição |
| --- | --- | 
| [Criação de consultas elásticas](sql-database-elastic-query-getting-started-vertical.md) | Neste tutorial, você aprenderá a executar consultas do T-SQL que abrangem vários bancos de dados usando um ponto de conexão único |
| [Relatar entre bancos de dados na nuvem escalados horizontalmente](sql-database-elastic-query-getting-started.md) |Neste tutorial, você aprenderá a criar relatórios de todos os bancos de dados em um banco de dados particionado horizontalmente (fragmentado) |
| [Como consultar entre bancos de dados em nuvem com esquemas diferentes](sql-database-elastic-query-vertical-partitioning.md) | Neste tutorial, você aprenderá a executar consultas do T-SQL que abrangem vários bancos de dados com diferentes esquemas |
| [Relatórios entre bancos de dados em nuvem expandidos](sql-database-elastic-query-horizontal-partitioning.md) |Neste tutorial, você aprenderá a criar relatórios que abrangem todos os bancos de dados em um banco de dados fragmentado. |
|  | |

## <a name="database-authentication-and-authorization"></a>Autenticação e Autorização do banco de dados
Nos tutoriais a seguir, você aprenderá a [criar e gerenciar logons e usuários](sql-database-manage-logins.md).

| Tutorial | Descrição |
| --- | --- | --- |
| [Autenticação e autorização de SQL](sql-database-control-access-sql-authentication-get-started.md) | Neste tutorial, você aprenderá a criar logons e usuários usando a autenticação do SQL Server e a adicionar funções, bem como conceder permissões a eles |
| [Autenticação e autorização do Azure AD](sql-database-control-access-aad-authentication-get-started.md) | Neste tutorial, você aprenderá a criar logons e usuários usando a autenticação do Azure Active Directory |
|  | |

## <a name="secure-and-protect-data"></a>Resguardar e proteger dados
Nos tutoriais a seguir, você aprenderá sobre a [proteção de dados do banco de dados SQL do Azure](sql-database-security-overview.md).

| Tutorial | Descrição |
| --- | --- | 
| [Como proteger dados confidenciais usando o Always Encrypted ](sql-database-always-encrypted-azure-key-vault.md) |Nesse tutorial, você usará o assistente Always Encrypted para proteger dados confidenciais em um banco de dados SQL do Azure. |
|  | |

## <a name="develop"></a>Desenvolver
Nos tutoriais a seguir, você aprenderá sobre o desenvolvimento de aplicativo e banco de dados.

| Tutorial | Descrição |
| --- | --- | 
| [Como criar um relatório usando o Excel](sql-database-connect-excel.md) |Aprenda a conectar o Excel a um banco de dados SQL em nuvem para poder importar dados e criar tabelas e gráficos com base nos valores no banco de dados. |
| [Como criar um aplicativo usando o SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) |Neste tutorial, você aprenderá a criar um aplicativo usando o SQL Server |
| [Tabelas temporais](sql-database-temporal-tables.md) | Neste tutorial, você aprenderá sobre tabelas temporais.
| [Usar o entity framework com ferramentas de banco de dados elástico](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) |Neste tutorial, você aprenderá sobre as alterações em um aplicativo do Entity Framework necessárias para integrar os recursos das ferramentas de banco de dados elástico. |
| [Adotar OLTP na memória](sql-database-in-memory-oltp-migration.md) | Neste tutorial, você aprenderá a usar o [in-memory OLTP](sql-database-in-memory.md) para melhorar o desempenho de processamento de transações. |
| [Code First para um novo banco de dados](https://msdn.microsoft.com/data/jj193542.aspx) | Neste tutorial, você aprenderá sobre o desenvolvimento Code First.
| [Aplicativo de exemplo Tailspin Surveys](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md) | Neste tutorial, você trabalha com um aplicativo de exemplo Tailspin Surveys. |
| [Aplicativo de demonstração Contoso Clinic](https://github.com/Microsoft/azure-sql-security-sample) | Neste tutorial, você trabalha com um aplicativo de demonstração Contoso Clinic. |
|  | |

## <a name="data-sync"></a>Sincronização de dados
Neste tutorial, você aprenderá sobre a [sincronização de dados](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

| Tutorial | Descrição |
| --- | --- | 
| [Introdução à Sincronização de Dados do SQL do Azure (visualização)](sql-database-get-started-sql-data-sync.md) |Neste tutorial, você aprenderá os conceitos básicos da Sincronização de Dados do SQL do Azure usando o portal clássico do Azure. |
|  | |

## <a name="monitor-and-tune"></a>Monitorar e ajustar
Nos tutoriais a seguir, você aprenderá sobre monitoramento e ajuste.
| Tutorial | Descrição |
| --- | --- | 
| [Telemetria de pool elástico usando o PowerShell](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools)| Neste tutorial, você aprenderá sobre a coleta de telemetria de pool elástico usando o PowerShell. |
| [Painel personalizado do pool elástico para SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) | Neste tutorial, você aprenderá sobre a criação de um painel personalizado para monitoramento de pools elásticos. |
| [Capturar o evento estendido para o arquivo de destino de evento](sql-database-xevent-code-event-file.md)| Neste tutorial, você aprenderá a capturar eventos estendidos para um arquivo de destino de evento.|
| [Capturar o evento estendido para o buffer de anel de destino](sql-database-xevent-code-ring-buffer.md)| Neste tutorial, você aprenderá a capturar eventos estendidos para um buffer de anel de código.|
|  | |



