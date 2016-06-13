<properties
   pageTitle="Migrando um banco de dados do SQL Server para o Banco de Dados SQL do Azure"
   description="Banco de Dados SQL do Microsoft Azure, implantação de banco de dados, migração de banco de dados, banco de dados de importação, exportação de banco de dados, assistente de migração"
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

# Importar do BACPAC para o Banco de Dados SQL usando SSMS

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Portal do Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

Este artigo mostra como importar de um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) para o Banco de Dados SQL usando o Assistente para Exportação de Aplicativo de Camada de Dados no SQL Server Management Studio.

> [AZURE.NOTE] As etapas abaixo supõem que você já tenha provisionado sua instância lógica do Azure SQL e tenha as informações sobre a conexão a seu alcance.

1. Verifique se você tem a versão mais recente do SQL Server Management Studio. As novas versões do Management Studio são atualizadas mensalmente para permanecerem em sincronia com as atualizações para o portal do Azure.

	 > [AZURE.IMPORTANT] Recomendamos que você sempre use a versão mais recente do Management Studio a fim de permanecer sincronizado com as atualizações no Microsoft Azure e no Banco de Dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Conecte-se ao seu servidor do Banco de Dados SQL do Azure, clique com o botão direito do mouse na pasta **Bancos de Dados** e clique em **Importar Aplicativo da Camada de Dados...**

    ![Importar o item de menu do aplicativo da camada de dados](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

3.	No assistente de importação, importe seu arquivo BACPAC de seu disco local ou selecione a conta de Armazenamento do Azure e o contêiner para o qual você carregou o arquivo BACPAC para criar o novo banco de dados no Banco de Dados SQL do Azure.

    ![Configurações de importação](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

    ***Importante:*** ao importar um BACPAC do armazenamento de blobs do Azure, use o armazenamento standard. Não há suporte para a importação de um BACPAC do armazenamento premium.

4.	Forneça o **Nome do novo banco de dados** para o banco de dados no Banco de Dados SQL do Azure, defina a **Edição do Banco de Dados SQL do Microsoft Azure** (camada de serviço), **Tamanho máximo do banco de dados**, **Objetivo de Serviço** (nível de desempenho).

    ![Configurações de banco de dados](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

5.	Clique em **Avançar** e clique em **Concluir** para importar o arquivo BACPAC para um novo banco de dados no servidor do Banco de Dados SQL do Azure.

6. Usando o Pesquisador de Objetos, conecte-se ao banco de dados migrado em seu servidor de Banco de Dados SQL do Azure.

6.	Usando o Portal do Azure, exiba seu banco de dados e suas propriedades.

## Próximas etapas

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## Saiba mais

- [Banco de Dados SQL V12](sql-database-v12-whats-new.md)
- [Funções como suporte parcial ou inexistente no Transact-SQL](sql-database-transact-sql-information.md)
- [Migrar bancos de dados não SQL Server usando o Assistente de Migração do SQL Server](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0601_2016-->