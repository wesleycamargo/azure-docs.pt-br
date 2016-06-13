
<properties
   pageTitle="Exportar um Banco de Dados do SQL Server para um arquivo BACPAC usando SSMS"
   description="Banco de Dados SQL do Microsoft Azure, migração do banco de dados, exportar banco de dados, exportar arquivo BACPAC, Assistente para Exportação de Aplicativo de Camada de Dados"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="05/31/2016"
   ms.author="carlrab"/>

# Exportar um Banco de Dados do SQL Server para um arquivo BACPAC usando SSMS

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

 
Este artigo mostra como exportar um banco de dados do SQL Server para um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) usando o Assistente para Exportação de Aplicativo de Camada de Dados no SQL Server Management Studio.

1. Verifique se você tem a versão mais recente do SQL Server Management Studio. As novas versões do Management Studio são atualizadas mensalmente para permanecerem em sincronia com as atualizações para o portal do Azure.

	 > [AZURE.IMPORTANT] Recomendamos que você sempre use a versão mais recente do Management Studio a fim de permanecer sincronizado com as atualizações no Microsoft Azure e no Banco de Dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Abra o Management Studio e conecte-se ao banco de dados de origem no Pesquisador de Objetos.

	![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. Clique com o botão direito do mouse no banco de dados de origem no Pesquisador de Objetos, aponte para **Tarefas** e clique em **Exportar Aplicativo de Camada de Dados...**

	![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. No assistente de exportação, configure a exportação para salvar o arquivo BACPAC em um disco local ou em um blob do Azure. O BACPAC exportado sempre inclui o esquema de banco de dados completo e, por padrão, os dados de todas as tabelas. Se você quiser excluir dados de algumas ou de todas as tabelas, use a guia Avançado. Por exemplo, você pode optar por exportar somente os dados das tabelas de referência em vez de todas as tabelas.

***Importante*** ao exportar um BACPAC para o armazenamento de blobs do Azure, use o armazenamento standard. Não há suporte para a importação de um BACPAC do armazenamento premium.

	![Export settings](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)


## Próximas etapas

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Importar um BACPAC para o Banco de Dados SQL do Azure usando o SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Importar um BACPAC para o SqlPackage do Banco de Dados SQL do Azure](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Importar um BACPAC para o Portal do Azure, Banco de Dados SQL do Azure](sql-database-import.md)
- [Importar um BACPAC para o PowerShell do Banco de Dados SQL do Azure](sql-database-import-powershell.md)

## Saiba mais

- [Banco de Dados SQL V12](sql-database-v12-whats-new.md)
- [Funções como suporte parcial ou inexistente no Transact-SQL](sql-database-transact-sql-information.md)
- [Migrar bancos de dados não SQL Server usando o Assistente de Migração do SQL Server](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0601_2016-->