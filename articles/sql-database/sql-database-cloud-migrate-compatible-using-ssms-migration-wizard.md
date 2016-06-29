<properties
   pageTitle="Migrar o banco de dados do SQL Server para o Banco de Dados SQL usando o Assistente para implantação de Banco de Dados no Banco de Dados do Microsoft Azure | Microsoft Azure"
   description="Banco de Dados SQL do Microsoft Azure, migração de banco de dados, Assistente de Banco de Dados do Microsoft Azure"
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
   ms.workload="sqldb-migrate"
   ms.date="06/07/2016"
   ms.author="carlrab"/>

# Migrar o banco de dados do SQL Server para o Banco de Dados SQL usando o Assistente para implantação de Banco de Dados no Banco de Dados do Microsoft Azure


> [AZURE.SELECTOR]
- [Assistente de Migração do SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Exportar para um arquivo BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importar de um arquivo BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Replicação Transacional](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

O assistente para Implantar Banco de Dados no Banco de Dados do Microsoft Azure no SQL Server Management Studio realiza a migração de um [banco de dados SQL Server compatível](sql-database-cloud-migrate.md) diretamente em sua instância de servidor do Banco de Dados SQL do Azure.

## Usar o assistente para Implantar Banco de Dados no Banco de Dados do Microsoft Azure

> [AZURE.NOTE] As etapas a seguir pressupõem que você tenha um [servidor de Banco de Dados SQL provisionado](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/).

1. Verifique se você tem a versão mais recente do SQL Server Management Studio. As novas versões do Management Studio são atualizadas mensalmente para permanecerem em sincronia com as atualizações para o portal do Azure.

    > [AZURE.IMPORTANT] Recomendamos que você sempre use a versão mais recente do Management Studio a fim de permanecer sincronizado com as atualizações no Microsoft Azure e no Banco de Dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Abra o Management Studio e conecte-se ao banco de dados do SQL Server que será migrado no Pesquisador de Objetos.
3. Clique com o botão direito do mouse no banco de dados no Pesquisador de Objetos, aponte para **Tarefas** e clique em **Implantar Banco de Dados no Banco de Dados SQL do Microsoft Azure...**

	![Implantar para o Azure por meio do menu de Tarefas](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)

4.	No assistente de implantação, clique em **Avançar** e clique em **Conectar** para configurar a conexão com seu servidor do Banco de Dados SQL.

	![Implantar para o Azure por meio do menu de Tarefas](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard002.png)

5. Na caixa de diálogo Conectar-se ao Servidor, insira suas informações de conexão para conectar-se ao seu servidor do Banco de Dados SQL.

	![Implantar para o Azure por meio do menu de Tarefas](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard00.png)

5.	Forneça o **Nome do novo banco de dados** para o banco de dados, defina a **Edição do Banco de Dados SQL do Microsoft Azure** [(camada de serviço)](sql-database-service-tiers.md), **Tamanho máximo do banco de dados**, **Objetivo de Serviço** (nível de desempenho) e **Nome do arquivo temporário** para o arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) criado por esse assistente durante o processo de migração.

	![Exportar configurações](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)

6.	Siga o assistente para migrar o banco de dados. Dependendo do tamanho e da complexidade do banco de dados, a implantação talvez demore de alguns minutos até algumas horas. Se este assistente detectar problemas de compatibilidade, os erros serão exibidos na tela e a migração não continuará. Para obter diretrizes sobre como corrigir problemas de compatibilidade do banco de dados, acesse [corrigir problemas de compatibilidade do banco de dados](sql-database-cloud-migrate-fix-compatibility-issues.md).

7.	Usando o Pesquisador de Objetos, conecte-se ao banco de dados migrado em seu servidor de Banco de Dados SQL do Azure.
8.	Usando o Portal do Azure, exiba seu banco de dados e suas propriedades.

## Próximas etapas

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## Recursos adicionais

- [Banco de Dados SQL V12](sql-database-v12-whats-new.md)
- [Funções como suporte parcial ou inexistente no Transact-SQL](sql-database-transact-sql-information.md)
- [Migrar bancos de dados não SQL Server usando o Assistente de Migração do SQL Server](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0615_2016-->