---
title: Exportar um banco de dados SQL do Azure para um arquivo BACPAC | Microsoft Docs
description: Exportar um Banco de Dados SQL do Azure para um arquivo BACPAC usando o Portal do Azure
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 41d63a97-37db-4e40-b652-77c2fd1c09b7
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 3f0af43c103e34535fd114e33c40da010ea69d69
ms.openlocfilehash: b8a2a33909f77dd1bbe0a7b454e15df7432164a8


---
# <a name="export-an-azure-sql-database-or-a-sql-server-database-to-a-bacpac-file"></a>Exportar um banco de dados SQL do Azure ou um banco de dados do SQL Server para um arquivo BACPAC

Este artigo aborda como exportar seu banco de dados SQL do Azure ou um banco de dados do SQL Server para um arquivo BACPAC. 

> [!IMPORTANT]
> A Exportação Automatizada do Banco de Dados SQL do Azure está agora em visualização e será desativada em 1º de março de 2017. A partir de 1 de dezembro de 2016, não será mais possível configurar a exportação automatizada em qualquer Banco de Dados SQL. Todos os trabalhos de exportação automatizada existentes continuarão funcionando até 1º de março de 2017. Após 1º de dezembro de 2016, você poderá usar a [retenção de backup de longo prazo](sql-database-long-term-retention.md) ou a [Automação do Azure](../automation/automation-intro.md) para arquivar bancos de dados SQL periodicamente, usando o PowerShell periodicamente de acordo com um agendamento de sua escolha. Para obter um script de exemplo, baixe o [script de exemplo no GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export). 
>

## <a name="overview"></a>Visão geral

Quando for preciso exportar um banco de dados para arquivamento ou para mover para outra plataforma, você pode exportar os dados e o esquema do banco de dados para um arquivo BACPAC. Um arquivo BACPAC é simplesmente um arquivo ZIP com uma extensão de BACPAC. Posteriormente, um arquivo BACPAC poderá ser colocado no armazenamento de blobs do Azure ou no armazenamento local em um local e depois importado novamente para o Banco de Dados SQL do Azure ou uma instalação local do SQL Server. 

* É possível exportar seu banco de dados SQL do Azure usando o [Portal do Azure](sql-database-export-portal.md), [PowerShell](sql-database-export-powershell.md), [SQLPackage](sql-database-export-sqlpackage.md) ou [SQL Server Management Studio](sql-database-export-ssms.md).
* É possível exportar um banco de dados do SQL Server usando o [PowerShell](sql-database-export-powershell.md), [SQLPackage](sql-database-export-sqlpackage.md) ou [SQL Server Management Studio](sql-database-export-ssms.md).

> [!IMPORTANT]
> Se você estiver exportando do SQL Server como um prelúdio para a migração para o Banco de Dados SQL do Azure, confira [Migrar um banco de dados do SQL Server para o Banco de Dados SQL do Azure](sql-database-cloud-migrate.md).
> 

## <a name="considerations"></a>Considerações

* Para que uma exportação seja transacionalmente consistente, você deve assegurar que nenhuma atividade de gravação esteja ocorrendo durante a exportação ou que você esteja exportando de uma [cópia transacionalmente consistente](sql-database-copy.md) de seu banco de dados SQL do Azure.
* Se você estiver exportando para o armazenamento de blobs, o tamanho máximo de um arquivo BACPAC é de 200 GB. Para arquivar um arquivo BACPAC maior, exporte para o armazenamento local.
* Não há suporte para a exportação de um arquivo BACPAC no armazenamento Premium do Azure usando os métodos discutidos neste artigo.
* Se a operação de exportação do Banco de Dados SQL do Azure exceder 20 horas, ela poderá ser cancelada. Para aumentar o desempenho durante a exportação, você pode:
  * Aumentar temporariamente seu nível de serviço.
  * Interromper toda a atividade de leitura e gravação durante a exportação.
  * Use um [índice clusterizado](https://msdn.microsoft.com/library/ms190457.aspx) com valores não nulos em todas as tabelas grandes. Sem índices clusterizados, a exportação poderá falhar se demorar mais de 6 a 12 horas. Isso ocorre porque o serviço de exportação precisa concluir a verificação da tabela para tentar exportar a tabela inteira. Uma boa maneira de determinar se as tabelas são otimizadas para exportação é executar **DBCC SHOW_STATISTICS** e verificar se *RANGE_HI_KEY* não é nulo e seu valor tem boa distribuição. Para obter detalhes, consulte [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPACs não devem ser usados para operações de backup e restauração. O Banco de Dados SQL do Azure cria automaticamente backups de todos os bancos de dados de usuário. Para obter detalhes, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md) e [Backups do Banco de Dados SQL](sql-database-automated-backups.md).  
> 


## <a name="next-steps"></a>Próximas etapas

* Para ver uma discussão de todo o processo de migração do banco de dados do SQL Server, confira [Migrar um banco de dados do SQL Server para o Banco de Dados SQL do Azure](sql-database-cloud-migrate.md).
* Para obter uma visão geral de como copiar um banco de dados no Azure, confira [Copiando um banco de dados SQL do Azure](sql-database-copy.md).
* Você pode criar um banco de dados SQL do Azure no Azure usando o [Portal do Azure](sql-database-copy-portal.md), [PowerShell](sql-database-copy-powershell.md) ou [Transact-SQL](sql-database-copy-transact-sql.md). 



<!--HONumber=Feb17_HO2-->


