<properties
   pageTitle="Use o SQL Server Management Studio para Determinar a compatibilidade do Banco de Dados SQL antes da migração para o Banco de Dados SQL do Azure | Microsoft Azure"
   description="Banco de Dados SQL do Microsoft Azure, migração do banco de dados, compatibilidade com o Banco de Dados SQL, Assistente para Exportação de Aplicativo de Camada de Dados"
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

# Use o SQL Server Management Studio para Determinar a compatibilidade do Banco de Dados SQL antes da migração para o Banco de Dados SQL do Azure

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Supervisor de Atualização](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
 
Neste artigo que você aprenderá a determinar se um banco de dados do SQL Server é compatível para migrar para o Banco de Dados SQL usando o Assistente para Exportação de Aplicativo de Camada de Dados no SQL Server Management Studio.

## Como usar o SQL Server Management Studio.

1. Verifique se você tem a versão mais recente do SQL Server Management Studio. As novas versões do Management Studio são atualizadas mensalmente para permanecerem em sincronia com as atualizações para o portal do Azure.

 	 > [AZURE.IMPORTANT] Recomendamos que você sempre use a versão mais recente do Management Studio a fim de permanecer sincronizado com as atualizações no Microsoft Azure e no Banco de Dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Abra o Management Studio e conecte-se ao banco de dados de origem no Pesquisador de Objetos.
3. Clique com o botão direito do mouse no banco de dados de origem no Pesquisador de Objetos, aponte para **Tarefas** e clique em **Exportar Aplicativo de Camada de Dados...**

	![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. No assistente de exportação, clique em **Avançar** e, na guia **Configurações**, configure a exportação para salvar o arquivo BACPAC em um disco local ou em um blob do Azure. Um arquivo BACPAC só será salvo se você não tiver nenhum problema de compatibilidade de banco de dados. Se houver problemas de compatibilidade, eles serão exibidos no console.

	![Exportar configurações](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. Clique na **guia Avançado** e desmarque a caixa de seleção **Selecionar Tudo** para ignorar a exportação de dados. Neste ponto, nosso objetivo será apenas testar a compatibilidade.

	![Exportar configurações](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. Clique em **Avançar** e então clique em **Concluir**. Os problemas de compatibilidade do banco de dados, se houver, serão exibidos depois que o assistente validar o esquema.

	![Exportar configurações](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. Se nenhum erro for exibido, o banco de dados é compatível e você está pronto para migrar. Se houver erros, você precisará corrigi-los. Para ver os erros, clique em **Erro** em **Validando esquema**. ![Exportar configurações](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

8.	Se o arquivo *.BACPAC for gerado com êxito, seu banco de dados será compatível com o Banco de Dados SQL, e você estará pronto para migrar.

## Próximas etapas

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Corrigir problemas de compatibilidade de migração do banco de dados](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [Migrar um banco de dados SQL Server compatível para o Banco de Dados SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## Saiba mais

- [Banco de Dados SQL V12](sql-database-v12-whats-new.md)
- [Funções como suporte parcial ou inexistente no Transact-SQL](sql-database-transact-sql-information.md)
- [Migrar bancos de dados não SQL Server usando o Assistente de Migração do SQL Server](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0608_2016-->