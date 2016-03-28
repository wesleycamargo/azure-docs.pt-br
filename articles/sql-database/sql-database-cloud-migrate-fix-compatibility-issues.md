<properties
   pageTitle="Corrigir problemas de compatibilidade do banco de dados do SQL Server antes da migração para o Banco de Dados SQL"
   description="Banco de Dados SQL do Microsoft Azure, migração de banco de dados, compatibilidade, Assistente de Migração do SQL Azure"
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
   ms.date="03/14/2016"
   ms.author="carlrab"/>

# Corrigir problemas de compatibilidade do banco de dados do SQL Server antes da migração para o Banco de Dados SQL

Se você determinar que o banco de dados do SQL Server de origem não é compatível, terá várias opções para corrigir os problemas de compatibilidade do banco de dados identificados.

> [AZURE.SELECTOR]
- Usar [Assistente de Migração do SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Usar [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Usar [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

## Assistente de Migração do SQL Azure

Use a ferramenta CodePlex do [Assistente de Migração do SQL Azure](http://sqlazuremw.codeplex.com/) para gerar um script T-SQL de um banco de dados de origem incompatível que é transformado pelo assistente a fim de torná-lo compatível com o Banco de Dados SQL e conectá-lo ao Banco de Dados SQL do Azure para execução do script. Essa ferramenta também analisará arquivos de rastreamento para determinar problemas de compatibilidade. O script pode ser gerado apenas com esquemas ou pode incluir dados no formato BCP. Há uma documentação adicional, incluindo instruções passo a passo, disponível no CodePlex em [Assistente de Migração do SQL Azure](http://sqlazuremw.codeplex.com/).

 ![Diagrama de migração do SAMW](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

  > [AZURE.NOTE] Observe que nem todos os esquemas incompatíveis que podem ser detectados pelo assistente podem ser corrigidos por suas transformações internas. O script incompatível que não puder ser incluído será apontado como erro, com comentários injetados no script gerado. Se vários erros forem detectados, use o Visual Studio ou o SQL Server Management Studio para depurar e corrigir cada erro que não puder ser corrigido pelo Assistente de Migração do SQL Server.

## Próxima etapa: selecionar o método de migração e executar a migração

[Selecionar método de migração](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database).

<!---HONumber=AcomDC_0316_2016-->