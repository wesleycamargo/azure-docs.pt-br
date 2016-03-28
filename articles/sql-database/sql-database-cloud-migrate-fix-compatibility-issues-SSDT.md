<properties
   pageTitle="Corrigir problemas de compatibilidade do banco de dados do SQL Server antes da migração para o Banco de Dados SQL"
   description="Banco de Dados SQL do Microsoft Azure, migração de banco de dados, compatibilidade, Assistente de Migração do SQL Azure, SSDT"
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
- Usar [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-SSMS.md)

## Como usar o SQL Server Data Tools para Visual Studio

Usar o SQL Server Data Tools para Visual Studio (“SSDT”) para importar o esquema de banco de dados em um projeto de banco de dados do Visual Studio para análise. Para analisar, especifique a plataforma de destino para o projeto como Banco de Dados SQL V12 e crie o projeto. Se a criação for bem-sucedida, o banco de dados é compatível. Se a compilação falhar, você poderá resolver os erros no SSDT (ou em uma das outras ferramentas discutidas neste tópico). Depois que o projeto for criado com êxito, você poderá publicá-lo como uma cópia do banco de dados de origem e usar o recurso de comparação de dados no SSDT para copiar os dados do banco de dados de origem no banco de dados compatível com o SQL V12 do Azure. Em seguida, você pode migrar esse banco de dados atualizado. Para usar essa opção, baixe a [versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx).

  ![Diagrama de migração do VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

  > [AZURE.NOTE] Se a migração somente de esquema for necessária, o esquema poderá ser publicado diretamente do Visual Studio no Banco de Dados SQL do Azure. Use esse método quando o esquema de banco de dados requer mais alterações do que o Assistente de migração pode administrar sozinho.

## Próxima etapa: selecionar o método de migração e executar a migração

[Selecionar método de migração](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database).

<!---HONumber=AcomDC_0316_2016-->