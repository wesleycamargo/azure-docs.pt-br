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
   ms.date="12/17/2015"
   ms.author="carlrab"/>

# Importar do BACPAC para o Banco de Dados SQL usando SSMS

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure Portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

Este artigo mostra como importar de um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) para o Banco de Dados SQL usando o Assistente para Exportação de Aplicativo de Camada de Dados no SQL Server Management Studio.

> [AZURE.NOTE]As etapas abaixo supõem que você já tenha provisionado sua instância lógica do Azure SQL e tenha as informações sobre a conexão a seu alcance.

1. Verifique se você tem a versão mais recente do SQL Server Management Studio. As novas versões do Management Studio são atualizadas mensalmente para permanecerem em sincronia com as atualizações para o portal do Azure.

	 >[AZURE.IMPORTANT]Recomendamos que você sempre use a versão mais recente do Management Studio a fim de permanecer sincronizado com as atualizações no Microsoft Azure e no Banco de Dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Abra o Management Studio e conecte-se ao banco de dados de origem no Pesquisador de Objetos.

	![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. Quando o BACPAC tiver sido criado, conecte-se ao seu servidor do Banco de Dados SQL do Azure, clique com o botão direito do mouse na pasta **Bancos de Dados** e clique em **Importar Aplicativo da Camada de Dados...**

    ![Importar o item de menu do aplicativo da camada de dados](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

4.	No assistente de importação, selecione o arquivo BACPAC recém-exportado para criar o novo Banco de Dados SQL do Azure.

    ![Configurações de importação](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

5.	Forneça o **Nome do novo banco de dados** para o banco de dados no Banco de Dados SQL do Azure, defina a **Edição do Banco de Dados SQL do Microsoft Azure** (camada de serviço), **Tamanho máximo do banco de dados**, **Objetivo de Serviço** (nível de desempenho).

    ![Configurações de banco de dados](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

6.	Clique em **Avançar** e clique em **Concluir** para importar o arquivo BACPAC para um novo banco de dados no servidor do Banco de Dados SQL do Azure.

7. Usando o Pesquisador de Objetos, conecte-se ao banco de dados migrado em seu servidor de Banco de Dados SQL do Azure.

8.	Usando o Portal do Azure, exiba seu banco de dados e suas propriedades.

<!---HONumber=AcomDC_1223_2015-->