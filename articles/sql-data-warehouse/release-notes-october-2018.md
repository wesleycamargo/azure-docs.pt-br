---
title: Notas de versão do Azure SQL Data Warehouse Outubro de 2018 | Microsoft Docs
description: Notas de versão do SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 11/14/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 1edac9f7eac1f47974f4c94f3cae5bb3451f92fd
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51705371"
---
# <a name="whats-new-in-azure-sql-data-warehouse-october-2018"></a>O que há de novo no SQL Data Warehouse do Azure? Outubro de 2018
O SQL Data Warehouse do Azure recebe melhorias continuamente. Este artigo descreve os novos recursos e alterações que foram introduzidos em outubro de 2018.

## <a name="devops-for-data-warehousing"></a>DevOps para Data Warehousing
O recurso altamente solicitado para o SQL Data Warehouse (SQL DW) está agora em visualização com o suporte para o SSDT (SQL Server Data Tool) no Visual Studio! Equipes de desenvolvedores agora podem colaborar em uma única base de código controlada por versão e implantar rapidamente as alterações em qualquer instância do mundo. Interessado em participar? Esse recurso está disponível para visualização hoje mesmo! Você pode se registrar visitando o [SSDT (Ferramentas de dados do SQL Server do SQL Data Warehouse Visual Studio) - formulário de inscrição prévia](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u). Dada a alta demanda, estamos gerenciando a aceitação na visualização para garantir a melhor experiência para nossos clientes. Depois de se inscrever, nossa meta é confirmar seu status em até sete dias úteis.

## <a name="row-level-security-generally-available"></a>Segurança em nível de linha geralmente disponível
O SQL Data Warehouse (SQL DW) do Azure agora oferece suporte à segurança no nível de linha (RLS), adicionando um recurso poderoso para proteger seus dados confidenciais. Com a introdução do RLS, você pode implementar políticas de segurança para controlar o acesso a linhas em suas tabelas, como em quem pode acessar quais linhas. O RLS permite esse controle de acesso de baixa granularidade sem ter que reprojetar seu data warehouse. O RLS simplifica o modelo geral de segurança, pois a lógica de restrição de acesso está localizada na própria camada do banco de dados, e não longe dos dados em outro aplicativo. O RLS também elimina a necessidade de introduzir exibições para filtrar linhas para o gerenciamento de controle de acesso. Não há custo adicional para esse recurso de segurança de nível corporativo para todos os nossos clientes.

## <a name="advanced-advisors"></a>Consultores avançadas
O ajuste avançado para o SQL Data Warehouse do Azure (SQL DW) ficou mais simples com recomendações e métricas adicionais de data warehouse. Existem recomendações de desempenho avançadas adicionais por meio do Assistente do Azure à sua disposição, incluindo:
1.  Cache adaptável - Esteja avisado quando dimensionar para otimizar a utilização do cache.
2.  Distribuição de tabelas - Determine quando replicar tabelas para reduzir a movimentação de dados e aumentar o desempenho da carga de trabalho. 
3.  Tempdb - reconhecimento quando dimensionar e configurar classes de recursos para reduzir a contenção de tempdb.

Há uma integração mais profunda das métricas do data warehouse com o [Azure Monitor](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/), incluindo um gráfico de monitoramento personalizável aprimorado para métricas quase em tempo real na folha de visão geral. Você não precisa mais deixar a folha de visão geral do data warehouse para acessar as métricas do Azure Monitor ao monitorar o uso ou validar e aplicar as recomendações do data warehouse. Além disso, há novas métricas disponíveis, como tempdb e utilização de cache adaptável para complementar suas recomendações de desempenho.

## <a name="advanced-tuning-with-integrated-advisors"></a>Ajuste avançado com assessores integrados
O ajuste avançado para o SQL Data Warehouse (SQL DW) do Azure ficou mais simples com recomendações e métricas adicionais de data warehouse e um novo design da folha de visão geral do portal que fornece uma experiência integrada com o Assistente do Azure e o Azure Monitor.

## <a name="accelerated-database-recovery-adr"></a>Recuperação Acelerada de Banco de Dados (ADR)
A Recuperação de Banco de Dados Acelerada (ADR) do Azure SQL Data Warehouse está agora na Visualização Pública. O ADR é um novo Mecanismo do SQL Server que melhora bastante a disponibilidade do banco de dados, especialmente na presença de transações de longa duração, reformulando completamente o processo de recuperação atual do zero. Os principais benefícios do ADR são a recuperação rápida e consistente de bancos de dados e a reversão instantânea de transações.

## <a name="azure-monitor-diagnostics-logs"></a>Logs de diagnóstico do Azure Monitor
O SQL Data Warehouse (SQL DW) agora permite insights aprimorados em cargas de trabalho analíticas, integrando-se diretamente aos logs de diagnóstico do Azure Monitor. Esse novo recurso permite que os desenvolvedores analisem o comportamento da carga de trabalho durante um período prolongado e tomem decisões informadas sobre otimização de consulta ou gerenciamento de capacidade. Introduzimos agora um processo de registro externo por meio de [Logs de diagnóstico do Azure Monitor](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json#logs) que fornecem informações adicionais sobre a carga de trabalho do data warehouse. Com um único clique de um botão, você agora pode configurar os logs de diagnóstico para os recursos de solução de problemas de desempenho de consulta histórica usando [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries). Os logs de diagnóstico do Azure Monitor suportam períodos de retenção personalizáveis, salvando os logs em uma conta de armazenamento para fins de auditoria, a capacidade de transmitir logs para hubs de eventos próximos a insights de telemetria em tempo real e a capacidade de analisar logs usando Log Analytics com [queries de log](). Os logs de diagnóstico consistem em visualizações de telemetria de seu data warehouse equivalentes às DMVs de solução de problemas de desempenho mais comumente usadas para o SQL Data Warehouse. Para esta versão inicial, ativamos as visualizações das seguintes visualizações de gerenciamento dinâmico do sistema:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql)

## <a name="columnstore-memory-management"></a>Gerenciamento de memória Columnstore
À medida que aumenta o número de grupos de linhas de repositório de coluna compactados, aumenta a memória necessária para gerenciar os metadados de segmento de coluna interna para os rowgroups.  Como resultado, podem degradar o desempenho de consulta e consultas executadas em relação a algumas exibições de Gerenciamento Dinâmico Columnstore (DMVs).  Foram feitos aperfeiçoamentos nesta versão para otimizar o tamanho dos metadados internos para esses casos, levando a experiência aprimorada e desempenho para essas consultas. 

## <a name="azure-data-lake-storage-gen2-integration-ga"></a>Integração Azure Data Lake Storage Gen2 (GA)
O SQL Data Warehouse do Azure (SQL DW) agora tem integração nativa com o Azure Data Lake Storage Gen2. Os clientes agora podem carregar dados usando tabelas externas do ABFS no SQL DW. Essa funcionalidade permite aos clientes integrar Lagos seus dados no Azure Data Lake Storage Gen2. 

## <a name="bug-fixes"></a>Correções de bug

| Title | DESCRIÇÃO |
|:---|:---|
| **CETAS para falhas de Parquet em classes de recursos pequenos em data warehouses de DW2000 e mais** | Essa correção identifica corretamente uma referência nula no caminho de código Criar tabela externa como para o Parquete. |
|**Valor da coluna de identidade pode perder em alguma operação do CTAS** | O valor de uma coluna de identificação pode não ser preservado quando CTASed para outra tabela. Relatado em um blog: [https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/). |
| **Falha interna em alguns casos, quando uma sessão é encerrada enquanto ainda está em execução a uma consulta** | Gatilhos de correção de um InvalidOperationException se uma sessão é encerrada quando a consulta ainda está em execução. |


## <a name="next-steps"></a>Próximas etapas
Agora que você conhece um pouco do SQL Data Warehouse, aprenda como [criar um SQL Data Warehouse][create a SQL Data Warehouse] rapidamente. Se você ainda não conhece o Azure, poderá achar o [Glossário do Azure][Azure glossary] útil à medida que encontrar nova terminologia. Ou, dê uma olhada em alguns desses outros Recursos do SQL Data Warehouse.  

* [Histórias de sucesso de clientes]
* [Blogs]
* [Solicitações de recursos]
* [Vídeos]
* [Blogs da Equipe Consultoria para Clientes]
* [Fórum Stack Overflow]
* [Twitter]


[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs da Equipe Consultoria para Clientes]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Histórias de sucesso de clientes]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Solicitações de recursos]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
