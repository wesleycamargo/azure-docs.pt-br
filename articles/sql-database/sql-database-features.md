---
title: "Visão geral dos recursos do Banco de Dados SQL do Azure | Microsoft Docs"
description: "Esta página fornece uma visão geral dos servidores lógicos e bancos de dados do Banco de Dados SQL do Azure e inclui uma matriz de suporte a recursos com links de cada recurso listado."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/01/2017
ms.author: carlrab; jognanay
translationtype: Human Translation
ms.sourcegitcommit: 21be71a1b4c79ecec8af02d08f65c41128c5ef73
ms.openlocfilehash: 50a465f314909c10bc3c3f95be2d9dc377d433a7


---
# <a name="azure-sql-database-features"></a>Recursos do Banco de Dados SQL do Azure
Este tópico fornece uma visão geral dos servidores lógicos e bancos de dados do Banco de Dados SQL do Azure e inclui uma matriz de suporte a recursos com links de cada recurso listado. 

## <a name="what-is-an-azure-sql-database-logical-server"></a>O que é um servidor lógico do Banco de Dados SQL do Azure?
Um servidor lógico do Banco de Dados SQL do Azure atua como um ponto administrativo central para vários bancos de dados. No Banco de Dados SQL, um servidor é um constructo lógico diferente de uma instância do SQL Server com a qual você talvez esteja familiarizado no mundo local. Especificamente, o serviço do Banco de Dados SQL não faz garantias da localização dos bancos de dados em relação a seus servidores lógicos e não expõe nenhum acesso ou recursos no nível da instância. Para obter mais informações sobre os servidores lógicos do SQL Azure, consulte [Servidores lógicos](sql-database-server-overview.md). 

## <a name="what-is-an-azure-sql-database"></a>O que é um banco de dados do Azure SQL?
Cada banco de dados no Banco de Dados SQL do Azure é associado a um servidor lógico. O banco de dados pode ser:

- Um banco de dados individual com seu [próprio conjunto de recursos](sql-database-what-is-a-dtu.md#what-are-database-transaction-units-dtus) (DTUs)
- Parte de um [pool de bancos de dados](sql-database-elastic-pool.md) que [compartilha um conjunto de recursos](sql-database-what-is-a-dtu.md#what-are-elastic-database-transaction-units-edtus) (eDTUs)
- Parte de um [conjunto expandido de bancos de dados fragmentados](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling), que pode ser individual ou bancos de dados em pool
- Parte de um conjunto de bancos de dados que participa de um [padrão de design de SaaS multilocatário](sql-database-design-patterns-multi-tenancy-saas-applications.md), e cujos bancos de dados podem ser individuais ou bancos de dados em pool (ou ambos) 

Para obter mais informações sobre bancos de dados SQL do Azure, consulte [Bancos de dados SQL](sql-database-overview.md).

## <a name="what-features-are-supported"></a>Há suporte para quais recursos?

As tabelas a seguir listam os principais recursos do Banco de Dados SQL do Azure e do SQL Server, especifica sua capacidade de suporte e fornece um link para mais informações sobre o recurso em cada plataforma. Para recursos do Transact-SQL, siga o link na tabela até a categoria do recurso. Consulte também [Diferenças do Transact-SQL do Banco de Dados SQL do Azure](sql-database-transact-sql-information.md) para obter mais informações sobre os motivos da falta de suporte para determinados tipos de recursos.

Continuamos a adicionar recursos à V12. Portanto, recomendamos que você visite a nossa página da Web de atualizações de serviço do Azure e use os seus filtros:

* Filtrado para o [Serviço de Banco de Dados SQL](https://azure.microsoft.com/updates/?service=sql-database).
* Filtrado para disponibilidade geral [Anúncios (GA)](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) para recursos do Banco de Dados SQL.

> [!TIP]
> Para testar um banco de dados existente para compatibilidade com o Banco de Dados SQL do Azure, veja [Migrar um banco de dados do SQL Server para o Azure](sql-database-cloud-migrate.md).
>

| **Recurso** | **SQL Server** | **Banco de Dados SQL do Azure** | 
| --- | :---: | :---: | 
| Replicação geográfica ativa | Sem suporte – consulte [Grupos de Disponibilidade AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx) | [Com suporte](sql-database-geo-replication-overview.md)
| Always Encrypted | [Com suporte](https://msdn.microsoft.com/library/mt163865.aspx) | [Com suporte](sql-database-always-encrypted.md) |
| Grupos de disponibilidade AlwaysOn | [Com suporte](https://msdn.microsoft.com/library/hh510230.aspx) | Sem suporte – consulte [Replicação geográfica ativa](sql-database-geo-replication-overview.md) |
| Anexar um banco de dados | [Com suporte](https://msdn.microsoft.com/library/ms190209.aspx) | Sem suporte |
| Funções de aplicativo | [Com suporte](https://msdn.microsoft.com/library/ms190998.aspx) | [Com suporte](https://msdn.microsoft.com/library/ms190998.aspx) |
| Escala automática | Sem suporte | [Com suporte](sql-database-scale-up.md) |
| Azure Active Directory | Sem suporte | [Com suporte](sql-database-aad-authentication.md) |
| Fábrica de dados do Azure | Sem suporte – consulte [SSIS (SQL Server Integration Services)](https://msdn.microsoft.com/library/ms141026.aspx) | [Com suporte](https://azure.microsoft.com/services/data-factory/) |
| Auditoria | [Com suporte](https://msdn.microsoft.com/library/cc280386.aspx) | [Com suporte](sql-database-auditing-get-started.md) |
| Arquivo BACPAC (exportação) | [Com suporte](https://msdn.microsoft.com/library/hh213241.aspx) | [Com suporte](sql-database-export.md) |
| Arquivo BACPAC (importação) | [Com suporte](https://msdn.microsoft.com/library/hh710052.aspx) | [Com suporte](sql-database-import.md) |
| Instruções BACKUP e RESTORE | [Com suporte](https://msdn.microsoft.com/library/ff848768.aspx) | Sem suporte |
| Funções internas | [Com suporte](https://msdn.microsoft.com/library/ms174318.aspx) | [A maioria](https://msdn.microsoft.com/library/ms174318.aspx) |
| Captura de dados de alteração | [Com suporte](https://msdn.microsoft.com/library/cc645937.aspx) | Sem suporte |
| Controle de alterações | [Com suporte](https://msdn.microsoft.com/library/bb933875.aspx) | [Com suporte](https://msdn.microsoft.com/library/bb933875.aspx) |
| Instruções de agrupamento | [Com suporte](https://msdn.microsoft.com/library/ff848763.aspx) | [Com suporte](https://msdn.microsoft.com/library/ff848763.aspx) |
| Índices ColumnStore | [Com suporte](https://msdn.microsoft.com/library/gg492088.aspx) | [Somente no Premium Edition](https://msdn.microsoft.com/library/gg492088.aspx) |
| CLR (Common Language Runtime) | [Com suporte](https://msdn.microsoft.com/library/ms131102.aspx) | Sem suporte |
| Bancos de dados independentes | [Com suporte](https://msdn.microsoft.com/library/ff929071.aspx) | Interno |
| Usuários independentes | [Com suporte](https://msdn.microsoft.com/library/ff929188.aspx) | [Com suporte](sql-database-manage-logins.md#non-administrator-users) |
| Controle de palavras-chave da linguagem de controle de fluxo | [Com suporte](https://msdn.microsoft.com/library/ms174290.aspx) | [Com suporte](https://msdn.microsoft.com/library/ms174290.aspx) |
| Consultas entre bancos de dados | [Com suporte](https://msdn.microsoft.com/library/dn584627.aspx) | [Consultas elásticas](sql-database-elastic-query-overview.md) |
| Cursores | [Com suporte](https://msdn.microsoft.com/library/ms181441.aspx) | [Com suporte](https://msdn.microsoft.com/library/ms181441.aspx) | 
| Compactação de dados | [Com suporte](https://msdn.microsoft.com/library/cc280449.aspx) | [Com suporte](https://msdn.microsoft.com/library/cc280449.aspx) |
| Backups de banco de dados | [Exposto para usuários](https://msdn.microsoft.com/library/ms187048.aspx) | [Interno](sql-database-automated-backups.md) |
| Database Mail | [Com suporte](https://msdn.microsoft.com/library/ms189635.aspx) | Sem suporte |
| Espelhamento de banco de dados | [Com suporte](https://msdn.microsoft.com/library/ms189852.aspx) | Sem suporte |
| Opções de configuração do banco de dados | [Com suporte](https://msdn.microsoft.com/library/mt629158.aspx) | [Com suporte](https://msdn.microsoft.com/library/mt629158.aspx) |
| DQS (Data Quality Services) | [Com suporte](https://msdn.microsoft.com/library/ff877925.aspx) | Sem suporte |
| Instantâneos de banco de dados | [Com suporte](https://msdn.microsoft.com/library/ms175158.aspx) | Sem suporte |
| Tipos de dados | [Com suporte](https://msdn.microsoft.com/library/ms187752.aspx) | [Com suporte](https://msdn.microsoft.com/library/ms187752.aspx) |  
| Instruções DBCC | [Todas](https://msdn.microsoft.com/library/ms188796.aspx) | [Alguns](https://msdn.microsoft.com/library/ms188796.aspx) |
| Instruções DDL | [Com suporte](https://msdn.microsoft.com/library/ff848799.aspx) | [A maioria](https://msdn.microsoft.com/library/ff848799.aspx)
| Gatilhos DDL | [Com suporte](https://msdn.microsoft.com/library/ms175941.aspx) | [Somente banco de dados](https://msdn.microsoft.com/library/ms175941.aspx) |
| Transações distribuídas | [MS DTC](https://msdn.microsoft.com/library/ms131665.aspx) | Somente em cenários limitados internos do Banco de Dados SQL |
| Instruções DML | [Com suporte](https://msdn.microsoft.com/library/ff848766.aspx) | [A maioria](https://msdn.microsoft.com/library/ff848766.aspx) |
| Gatilhos DML | [Com suporte](https://msdn.microsoft.com/library/ms178110.aspx) | [Com suporte](https://msdn.microsoft.com/library/ms178110.aspx) |
| DMVs | [Todas](https://msdn.microsoft.com/library/ms188754.aspx) | [Alguns](https://msdn.microsoft.com/library/ms188754.aspx) |
| pools elásticos | Sem suporte | [Com suporte](sql-database-elastic-pool.md) |
| Trabalhos elásticos | Sem suporte – consulte [SQL Server Agent](https://msdn.microsoft.com/library/ms189237.aspx) | [Com suporte](sql-database-elastic-jobs-getting-started.md) | 
| Consultas elásticas | Sem suporte – consulte [Consultas de bancos de dados](https://msdn.microsoft.com/library/dn584627.aspx) | [Com suporte](sql-database-elastic-query-overview.md) |
| Notificações de eventos | [Com suporte](https://msdn.microsoft.com/library/ms186376.aspx) | [Com suporte](sql-database-insights-alerts-portal.md) |
| Expressões | [Com suporte](https://msdn.microsoft.com/library/ms190286.aspx) | [Com suporte](https://msdn.microsoft.com/library/ms190286.aspx) |
| Eventos estendidos | [Com suporte](https://msdn.microsoft.com/library/bb630282.aspx) | [Alguns](sql-database-xevent-db-diff-from-svr.md) |
| Procedimentos armazenados estendidos | [Com suporte](https://msdn.microsoft.com/library/ms164627.aspx) | Sem suporte |
| Grupos de arquivos | [Com suporte](https://msdn.microsoft.com/library/ms189563.aspx#Anchor_2) | [Somente primário](https://msdn.microsoft.com/library/ms189563.aspx#Anchor_2) |
| Fluxo de arquivos | [Com suporte](https://msdn.microsoft.com/library/gg471497.aspx) | Sem suporte |
| Pesquisa de texto completo | [Com suporte](https://msdn.microsoft.com/library/ms142571.aspx) | [Separadores de palavras de terceiros sem suporte](https://msdn.microsoft.com/library/ms142571.aspx) |
| Funções | [Com suporte](https://msdn.microsoft.com/library/ms174318.aspx) | [A maioria](https://msdn.microsoft.com/library/ms174318.aspx) |
| Otimização na memória | [Com suporte](https://msdn.microsoft.com/library/dn133186.aspx) | [Somente no Premium Edition](https://msdn.microsoft.com/library/dn133186.aspx) |
| Trabalhos | [SQL Server Agent](https://msdn.microsoft.com/library/ms189237.aspx) | [Com suporte](sql-database-elastic-jobs-getting-started.md) |
| Suporte a dados JSON | [Com suporte](https://msdn.microsoft.com/library/dn921897.aspx) | [Com suporte](sql-database-json-features.md) |
| Elementos de linguagem | [Com suporte](https://msdn.microsoft.com/library/ff848807.aspx) | [A maioria](https://msdn.microsoft.com/library/ff848807.aspx) |  
| Servidores vinculados | [Com suporte](https://msdn.microsoft.com/library/ms188279.aspx) | Sem suporte – consulte [Consulta elástica](sql-database-elastic-query-horizontal-partitioning.md) |
| Envio de logs | [Com suporte](https://msdn.microsoft.com/library/ms187103.aspx) | Sem suporte – consulte [Replicação geográfica ativa](sql-database-geo-replication-overview.md) |
| Comandos de gerenciamento | [Com suporte](https://msdn.microsoft.com/library/ms190286.aspx)| [Sem suporte](https://msdn.microsoft.com/library/ms190286.aspx) |
| MDS (Master Data Services) | [Com suporte](https://msdn.microsoft.com/library/ff487003.aspx) | Sem suporte |
| Log mínimo na importação em massa | [Com suporte](https://msdn.microsoft.com/library/ms190422.aspx) | Sem suporte |
| Modificação dos dados do sistema | [Com suporte](https://msdn.microsoft.com/library/ms178028.aspx) | Sem suporte |
| Operações de índice online | [Com suporte](https://msdn.microsoft.com/library/ms177442.aspx) | [Tamanho de transação limitado pela camada de serviço](https://msdn.microsoft.com/library/ms177442.aspx) |
| Operadores | [Com suporte](https://msdn.microsoft.com/library/ms174986.aspx) | [A maioria](https://msdn.microsoft.com/library/ms174986.aspx) |
| Recuperação pontual de banco de dados | [Com suporte](https://msdn.microsoft.com/library/ms179451.aspx) | [Com suporte](sql-database-recovery-using-backups.md#point-in-time-restore) |
| Polybase | [Com suporte](https://msdn.microsoft.com/library/mt143171.aspx) | [Sem suporte]
| Gerenciamento baseado em políticas | [Com suporte](https://msdn.microsoft.com/library/bb510667.aspx) | Sem suporte |
| Predicados | [Com suporte](https://msdn.microsoft.com/library/ms189523.aspx) | [A maioria](https://msdn.microsoft.com/library/ms189523.aspx)
| Administrador de recursos | [Com suporte](https://msdn.microsoft.com/library/bb933866.aspx) | [Interno](sql-database-service-tiers.md) |
| Restauração do banco de dados por meio de backup | [Com suporte](https://msdn.microsoft.com/library/ms187048.aspx#anchor_6) | [Somente de backups internos](sql-database-recovery-using-backups.md) |
| Segurança em nível de linha | [Com suporte](https://msdn.microsoft.com/library/dn765131.aspx) | [Com suporte](https://msdn.microsoft.com/library/dn765131.aspx) |
| Instruções de segurança | [Com suporte](https://msdn.microsoft.com/library/ff848791.aspx) | [Alguns](https://msdn.microsoft.com/library/ff848791.aspx) |
| Pesquisa semântica | [Com suporte](https://msdn.microsoft.com/library/gg492075.aspx) | Sem suporte |
| Números de sequência | [Com suporte](https://msdn.microsoft.com/library/ff878058.aspx) | [Com suporte](https://msdn.microsoft.com/library/ff878058.aspx) |
| Agente de Serviço | [Com suporte](https://msdn.microsoft.com/library/bb522893.aspx) | Sem suporte |
| Opções de configuração do servidor | [Com suporte](https://msdn.microsoft.com/library/ms189631.aspx) | Sem suporte – consulte [Opções de configuração do banco de dados](https://msdn.microsoft.com/library/mt629158.aspx) |
| Instruções SET | [Com suporte](https://msdn.microsoft.com/library/ms190356.aspx) | [A maioria](https://msdn.microsoft.com/library/ms190356.aspx) 
| Espacial | [Com suporte](https://msdn.microsoft.com/library/bb933790.aspx) | [Com suporte](https://msdn.microsoft.com/library/bb933790.aspx) |
| SQL Server Agent | [Com suporte](https://msdn.microsoft.com/library/ms189237.aspx) | Sem suporte – consulte [Trabalhos elásticos](sql-database-elastic-jobs-getting-started.md) |
| SSAS (SQL Server Analysis Services) | [Com suporte](https://msdn.microsoft.com/library/bb522607.aspx) | Sem suporte – consulte [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| SQL Server Integration Services (SSIS) | [Com suporte](https://msdn.microsoft.com/library/ms141026.aspx) | Sem suporte – consulte [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) |
| SQL Server PowerShell | [Com suporte](https://msdn.microsoft.com/library/hh245198.aspx) | [Com suporte](https://msdn.microsoft.com/library/hh245198.aspx) |
| SQL Server Profiler | [Com suporte](https://msdn.microsoft.com/library/ms181091.aspx) | Sem suporte – consulte [Eventos estendidos](https://msdn.microsoft.com/library/ms181091.aspx) |
| Replicação do SQL Server | [Com suporte](https://msdn.microsoft.com/library/ms151198.aspx) | [Somente assinante de replicação de instantâneo e transacional](sql-database-cloud-migrate-compatible-using-transactional-replication.md) |
| SSRS (SQL Server Reporting Services) | [Com suporte](https://msdn.microsoft.com/library/ms159106.aspx) | Sem suporte |
| Procedimentos armazenados | [Com suporte](https://msdn.microsoft.com/library/ms190782.aspx) | [Com suporte](https://msdn.microsoft.com/library/ms190782.aspx) |
| Funções armazenadas do sistema | [Com suporte](https://msdn.microsoft.com/library/ff848780.aspx) | [Alguns](https://msdn.microsoft.com/library/ff848780.aspx) |
| Procedimentos armazenados do sistema | [Com suporte](https://msdn.microsoft.com/library/ms187961.aspx) | [Alguns](https://msdn.microsoft.com/library/ms187961.aspx) |
| Tabelas do sistema | [Com suporte](https://msdn.microsoft.com/library/ms179932.aspx) | [Alguns](https://msdn.microsoft.com/library/ms179932.aspx) |
| Exibições do sistema | [Com suporte](https://msdn.microsoft.com/library/ms177862.aspx) | [Alguns](https://msdn.microsoft.com/library/ms177862.aspx)
| Particionamento de tabela | [Com suporte](https://msdn.microsoft.com/library/ms190787.aspx) | [Somente no grupo de arquivos primário](https://msdn.microsoft.com/library/ms190787.aspx) |
| Tabelas temporárias | [Local e global](https://msdn.microsoft.com/library/ms174979.aspx#Anchor_4) | [Somente local](https://msdn.microsoft.com/library/ms174979.aspx#Anchor_4) |
| Tabelas temporais | [Com suporte](https://msdn.microsoft.com/library/dn935015.aspx) | [Com suporte](sql-database-temporal-tables.md) |
| Instruções de transação | [Com suporte](https://msdn.microsoft.com/library/ms174377.aspx) | [Com suporte](https://msdn.microsoft.com/library/ms174377.aspx) |
| variáveis | [Com suporte](https://msdn.microsoft.com/library/ff848809.aspx) | | [Com suporte](https://msdn.microsoft.com/library/ff848809.aspx) | 
| TDE (Transparent Data Encryption)  | [Com suporte](https://msdn.microsoft.com/library/bb934049.aspx) | [Com suporte](https://msdn.microsoft.com/dn948096.aspx) |
| Windows Server Failover clustering | [Com suporte](https://msdn.microsoft.com/library/hh270278.aspx) | Sem suporte – consulte [Replicação geográfica ativa](sql-database-geo-replication-overview.md) |
| Índices XML | [Com suporte](http://msdn.microsoft.com/library/bb934097.aspx) | [Com suporte](http://msdn.microsoft.com/library/bb934097.aspx) |
| Instruções XML | [Com suporte](https://msdn.microsoft.com/library/ff848798.aspx) | [Com suporte](https://msdn.microsoft.com/library/ff848798.aspx) |

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre o serviço do Banco de Dados SQL do Azure, consulte [O que é o Banco de Dados SQL?](sql-database-technical-overview.md)
- Para obter uma visão geral dos servidores lógicos do SQL Azure, consulte [Visão geral do servidor lógico do Banco de Dados SQL](sql-database-server-overview.md)
- Para obter uma visão geral de bancos de dados SQL do Azure, consulte [Visão geral do Banco de Dados SQL](sql-database-overview.md)
- Para obter informações sobre o suporte ao Transact-SQL e as diferenças, consulte [Diferenças do Transact-SQL do Banco de Dados SQL do Azure](sql-database-transact-sql-information.md).
- Para obter informações sobre cotas e limitações de recursos específicos com base na **camada de serviço**. Para obter uma visão geral das camadas de serviço, consulte [Camadas de serviço do banco de dados SQL](sql-database-service-tiers.md).
- Para obter uma visão geral de segurança, veja [Visão geral de segurança do Banco de Dados SQL do Azure](sql-database-security-overview.md).
- Para obter informações sobre a disponibilidade de drivers e o suporte ao Banco de Dados SQL, veja [Bibliotecas de conexões para Banco de Dados SQL e SQL Server](sql-database-libraries.md).



<!--HONumber=Feb17_HO2-->


