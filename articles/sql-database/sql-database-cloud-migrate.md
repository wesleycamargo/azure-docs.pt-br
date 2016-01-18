<properties
   pageTitle="Migrando um banco de dados do SQL Server para o Banco de Dados SQL do Azure"
   description="Banco de Dados SQL do Microsoft Azure, implantação de banco de dados, migração de banco de dados, banco de dados de importação, exportação de banco de dados, assistente de migração"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="01/05/2016"
   ms.author="carlrab"/>

# Migrando um banco de dados do SQL Server para o Banco de Dados SQL do Azure

Neste artigo, você aprenderá a migrar um banco de dados local do SQL Server 2005 ou posterior para o Banco de Dados SQL do Azure. Nesse processo de migração, você migra o esquema e os dados do banco de dados SQL Server em seu ambiente atual para o Banco de Dados SQL, desde que o banco de dados existente passe nos testes de compatibilidade. Com o [Banco de Dados SQL V12](sql-database-v12-whats-new.md), existem bem poucos problemas de compatibilidade restantes, a não ser de operações entre banco de dados e no nível de servidor. Os bancos de dados e os aplicativos que dependem de [funções com suporte parcial ou sem suporte](sql-database-transact-sql-information.md) precisarão de alguma reengenharia para [corrigir essas incompatibilidades](sql-database-cloud-migrate-fix-compatibility-issues.md) para que o banco de dados SQL Server possa ser migrado.

> [AZURE.NOTE]Para migrar um banco de dados não SQL Server, incluindo Microsoft Access, Sybase, MySQL Oracle e DB2 para o Banco de Dados SQL do Azure, confira [SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/).

## Determinar se um banco de dados SQL Server é compatível para migrar para o Banco de Dados SQL

> [AZURE.SELECTOR]
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)

Para testar os problemas de compatibilidade do Banco de Dados SQL antes de iniciar o processo de migração, use um dos seguintes métodos:

- [Usar SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md): SqlPackage é um utilitário de prompt de comando que será testado e, se encontrado, vai gerar um relatório contendo problemas de compatibilidade detectados.
- [Usar SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md): o assistente Exportar Aplicativo da Camada de Dados no SQL Server Management Studio exibirá os erros detectados na tela.

## Corrigir problemas de compatibilidade

Se forem detectados problemas de compatibilidade, você deverá corrigi-los antes de continuar com a migração.

- Usar [Assistente de Migração do SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Usar [SQL Server Data Tools para Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Usar [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

## Migrar um banco de dados SQL Server compatível para o Banco de Dados SQL

Para migrar um banco de dados do SQL Server compatível, a Microsoft fornece vários métodos de migração para vários cenários. O método escolhido depende da sua tolerância para tempo de inatividade, do tamanho e da complexidade do seu banco de dados SQL Server, bem como da conectividade com a nuvem do Microsoft Azure.

> [AZURE.SELECTOR]
- [SSMS Migration Wizard](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Export to BACPAC File](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Import from BACPAC File](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Transactional Replication](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Para escolher o método de migração, a primeira pergunta a ser feita é se você pode arcar com a retirada do banco de dados da produção durante a migração. Migrar um banco de dados enquanto transações ativas estão ocorrendo pode resultar em inconsistências e na possível corrupção do banco de dados. Há vários métodos para fechar um banco de dados para novas sessões, desde desabilitar a conectividade do cliente até criar um [instantâneo do banco de dados](https://msdn.microsoft.com/library/ms175876.aspx).

Para migrar com tempo de inatividade mínimo, use a [replicação de transação do SQL Server](sql-database-cloud-migrate-compatible-using-transactional-replication.md) se seu banco de dados atender aos requisitos de replicação transacional. Se for possível suportar algum tempo de inatividade ou se você estiver executando um teste de migração de um banco de dados de produção para migração posterior, leve em consideração um dos três métodos a seguir:

- [Assistente de Migração do SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md): no caso de bancos de dados pequenos a médios, a migração de um banco de dados SQL Server 2005 ou posterior compatível é tão simples quanto executar o [Assistente para Implantar Banco de Dados no Banco de Dados do Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) no SQL Server Management Studio. 
- [Exportar para arquivo BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) e [Importar do arquivo BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md): se você tiver desafios de conectividade (nenhuma conectividade, largura de banda baixa ou problemas de tempo limite) e bancos de dados de médios a grandes, use um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). Com esse método, você exporta o esquema e os dados do SQL Server para um arquivo BACPAC e, em seguida, importa o arquivo BACPAC no Banco de Dados SQL usando o Assistente para Exportar Aplicativo da Camada de Dados no SQL Server Management Studio ou o utilitário de prompt de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx).
- Usar o BACPAC e o BCP juntos: use um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) e o [BCP](https://msdn.microsoft.com/library/ms162802.aspx) para bancos de dados muito grandes, a fim de alcançar maior paralelização para aumento de desempenho, ainda que com maior complexidade. Com esse método, migre o esquema e os dados separadamente. 
 - [Exportar o esquema somente para um arquivo BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
 - [Importar o esquema somente do arquivo BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) no Banco de Dados SQL. 
 - Usar o [BCP](https://msdn.microsoft.com/library/ms162802.aspx) para extrair os dados em arquivos simples e, em seguida, [carregar paralelamente](https://technet.microsoft.com/library/dd425070.aspx) esses arquivos no Banco de Dados SQL.

	 ![Diagrama de migração do SSMS](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

<!---HONumber=AcomDC_0107_2016-->