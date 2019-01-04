---
title: Como configurar o Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como configurar e gerenciar o Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 12/14/2018
ms.openlocfilehash: b4dd21324591075d7625a82fbbb661c4a8e84b1d
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53439533"
---
# <a name="how-to-use-azure-sql-database"></a>Como usar o Banco de Dados SQL do Azure

Nesta seção, é possível encontrar vários guias, scripts e explicações que podem ajudar você a gerenciar e configurar seu Banco de Dados SQL do Azure. Você também pode encontrar guias de instruções específicos para [Banco de dados individual](sql-database-howto-single-database.md) e [Instância Gerenciada](sql-database-howto-managed-instance.md).

## <a name="load-data"></a>Carregar dados

- [Copiar um banco de dados individual dentro do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-copy)
- [Importar um BD de um BACPAC](https://docs.microsoft.com/azure/sql-database/sql-database-import)
- [Exportar um BD para um BACPAC](https://docs.microsoft.com/azure/sql-database/sql-database-export)
- [Carregar dados com o BCP](https://docs.microsoft.com/azure/sql-database/sql-database-load-from-csv-with-bcp)
- [Carregar dados com o ADF](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>Sincronização de dados

- [Sincronização de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-sync-data)
- [Agente de Sincronização de Dados](https://docs.microsoft.com/azure/sql-database/sql-database-data-sync-agent)
- [Replicar alterações de esquema](https://docs.microsoft.com/azure/sql-database/sql-database-update-sync-schema)
- [Monitorar com o OMS](https://docs.microsoft.com/azure/sql-database/sql-database-sync-monitor-oms)
- [Melhores práticas para a sincronização de dados](https://docs.microsoft.com/azure/sql-database/sql-database-best-practices-data-sync)
- [Solucionar problemas da sincronização de dados](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-data-sync)

## <a name="monitoring-and-tuning"></a>Monitoramento e ajuste

-  [Ajuste manual](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
- [Utilizar DMVs para monitorar o desempenho](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
- [Usar o Repositório de Consultas para monitorar o desempenho](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)
- [Solucionar problemas de desempenho com o Intelligent Insights](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance)
- [Usar o log de diagnóstico do Intelligent Insights](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-use-diagnostics-log)
- [Monitorar o espaço OLTP in-memory](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring)

### <a name="extended-events"></a>Eventos estendidos

- [Eventos estendidos](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-db-diff-from-svr)
- [Armazenar eventos estendidos no arquivo de evento](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-code-event-file)
- [Armazenar eventos estendidos no buffer de anel](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-code-ring-buffer)

## <a name="configure-features"></a>Configurar recursos

- [Configurar a autenticação do Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)
- [Configurar acesso condicional](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- [Autenticação multifator do AAD](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication)
- [Configurar autenticação multifator](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication-configure)
- [Configurar a política de retenção temporal](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables-retention-policy)
- [Configurar TDE com BYOK](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-configure)
- [Girar chaves BYOK da TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-key-rotation)
- [Remover protetor da TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)
- [Configurar OLTP na memória](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-migration)
- [Configurar Automação do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-manage-automation)

## <a name="develop-applications"></a>Desenvolver aplicativos

- [Conectividade](https://docs.microsoft.com/azure/sql-database/sql-database-libraries)
- [Usar o Conector do Spark](https://docs.microsoft.com/azure/sql-database/sql-database-spark-connector)
- [Autenticar aplicativo](https://docs.microsoft.com/azure/sql-database/sql-database-client-id-keys)
- [Mensagens de erro](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages)
- [Usar o envio em lote para obter melhor desempenho](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance)
- [Diretrizes de conectividade](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)
- [Aliases de DNS](https://docs.microsoft.com/azure/sql-database/dns-alias-overview)
- [Configurar PowerShell de alias do DNS](https://docs.microsoft.com/azure/sql-database/dns-alias-powershell)
- [Portas - ADO.NET](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12)
- [C e C ++](https://docs.microsoft.com/azure/sql-database/sql-database-develop-cplusplus-simple)
- [Excel](https://docs.microsoft.com/azure/sql-database/sql-database-connect-excel)

## <a name="design-applications"></a>Design de aplicativos

- [Design para a recuperação de desastres](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery)
- [Design para pools elásticos](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool)
- [Design para atualizações do aplicativo](https://docs.microsoft.com/azure/sql-database/sql-database-manage-application-rolling-upgrade)

### <a name="design-multi-tenant-saas-applications"></a>Design de aplicativos SaaS multilocatários

- [Padrões de design do SaaS](https://docs.microsoft.com/azure/sql-database/saas-tenancy-app-design-patterns)
- [Video Indexer de SaaS](https://docs.microsoft.com/azure/sql-database/saas-tenancy-video-index-wingtip-brk3120-20171011)
- [Segurança de aplicativo SaaS](https://docs.microsoft.com/azure/sql-database/saas-tenancy-elastic-tools-multi-tenant-row-level-security)

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [Guias de instruções na Instância Gerenciada](sql-database-howto-managed-instance.md).
- Saiba mais sobre [Guias de instruções em um banco de dados individual](sql-database-howto-single-database.md).
