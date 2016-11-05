---
title: Migrar para o Banco de Dados SQL usando a replicação transacional | Microsoft Docs
description: Banco de Dados SQL do Microsoft Azure, migração de banco de dados, importar banco de dados, replicação transacional
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 08/23/2016
ms.author: carlrab

---
# Migrar o banco de dados do SQL Server para o Banco de Dados SQL do Azure usando a replicação transacional
> [!div class="op_single_selector"]
> * [Assistente de Migração do SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
> * [Exportar para um arquivo BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [Importar de um arquivo BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [Replicação Transacional](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
> 
> 

Neste artigo, você aprenderá a migrar um banco de dados do SQL Server compatível para o Banco de Dados SQL do Azure com tempo de inatividade mínimo usando a replicação transacional do SQL Server.

## Noções básicas sobre a arquitetura de Replicação Transacional
Quando não houver a possibilidade de remover seu banco de dados do SQL Server da produção durante a migração, você poderá usar a replicação transacional do SQL Server como sua solução de migração. Para usar esta solução, você pode configurar o Banco de Dados SQL do Azure como um assinante da instância do SQL Server local que você deseja migrar. O distribuidor de replicação transacional local sincroniza os dados do banco de dados local a ser sincronizado (o editor), enquanto as novas transações continuam a ocorrer.

Você também pode usar a replicação transacional para migrar um subconjunto de seu banco de dados local. A publicação que você replica no Banco de Dados SQL do Azure pode ser limitada a um subconjunto de tabelas no banco de dados que está sendo replicado. Para cada tabela sendo replicada, você poderá limitar os dados a um subconjunto de linhas e/ou um subconjunto de colunas.

Com a replicação transacional, todas as alterações de dados ou esquema aparecem no seu Banco de Dados SQL do Azure. Quando a sincronização for concluída e você estiver pronto para migrar, altere a cadeia de conexão de seus aplicativos para apontá-los para seu Banco de Dados SQL do Azure. Assim que a replicação transacional realizar todas as alterações restantes em seu banco de dados local e todos os seus aplicativos apontarem para o Banco de Dados do Azure, você poderá desinstalar a replicação transacional. O Banco de Dados SQL do Azure agora é o sistema de produção.

 ![Diagrama do SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## Requisitos de replicação transacional
A replicação transacional é uma tecnologia interna e integrada ao SQL Server desde o SQL Server 6.5. É uma tecnologia madura e comprovada, conhecida pela maioria dos DBAs e na qual eles têm experiência. Com o [SQL Server 2016](https://www.microsoft.com/pt-BR/cloud-platform/sql-server), é possível configurar o Banco de Dados SQL do Azure como um [assinante de replicação transacional](https://msdn.microsoft.com/library/mt589530.aspx) para a sua publicação local. A experiência de configurá-lo no Management Studio é a mesma de configurar um assinante de replicação transacional em um servidor local. Haverá suporte para esse cenário quando o editor e o distribuidor estiverem em pelo menos uma das seguintes versões do SQL Server:

* SQL Server 2016 e posterior
* SQL Server 2014 SP1 CU3 e posterior
* SQL Server 2014 RTM CU10 e posterior
* SQL Server 2012 SP2 CU8 e posterior
* SQL Server 2012 SP3 e posterior

> [!IMPORTANT]
> Você deve usar a versão mais recente do SQL Server Management Studio a fim de permanecer sincronizado com as atualizações no Microsoft Azure e no Banco de Dados SQL. Versões anteriores do SQL Server Management Studio não podem configurar o Banco de Dados SQL como um assinante. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## Próximas etapas
* [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [SQL Server 2016](https://www.microsoft.com/pt-BR/cloud-platform/sql-server)

## Recursos adicionais
* [Replicação Transacional](https://msdn.microsoft.com/library/mt589530.aspx)
* [Banco de Dados SQL V12](sql-database-v12-whats-new.md)
* [Funções como suporte parcial ou inexistente no Transact-SQL](sql-database-transact-sql-information.md)
* [Migrar bancos de dados não SQL Server usando o Assistente de Migração do SQL Server](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0824_2016-->