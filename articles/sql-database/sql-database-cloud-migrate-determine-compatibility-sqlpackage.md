---
title: Determinar a compatibilidade do Banco de Dados SQL usando o SqlPackage.exe | Microsoft Docs
description: "Banco de Dados SQL do Microsoft Azure, migração de banco de dados, compatibilidade com o Banco de Dados SQL, SqlPackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: ebe2cf3e-9561-4ede-8fb9-f3e6ce3fb7a6
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 06563b181a8d4ff568eb7cce15189defab0225ca
ms.openlocfilehash: 16cc9beb3c239e583f47f33fe2f8c7caf44e8eb6


---
# <a name="determine-sql-database-compatibility-using-sqlpackageexe"></a>Determinar a compatibilidade com o Banco de Dados SQL usando SqlPackage.exe
> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 

Neste artigo, você aprenderá a determinar se um banco de dados do SQL Server é compatível para migrar para o Banco de Dados SQL usando o utilitário de prompt de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) .

## <a name="using-sqlpackageexe"></a>Como usar o SqlPackage.exe
1. Abra um prompt de comando e altere um diretório com a versão mais recente do sqlpackage.exe. Esse utilitário acompanha as últimas versões do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), ou você pode baixar a última versão do [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) diretamente no Centro de Download da Microsoft.

2. Execute o seguinte comando SqlPackage com os seguintes argumentos para o seu ambiente:

   ```   
    sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1
   ```   

   | Argumento | Descrição |
   | --- | --- |
   | < nome_do_servidor > |nome do servidor de origem |
   | < nome_do_banco_de_dados > |nome do banco de dados de origem |
   | < arquivo_de_destino > |nome do arquivo e um local para o arquivo BACPAC |
   | < nome_do_esquema.nome_da_tabela > |as tabelas cujos dados serão enviados para o arquivo de destino |
   | < arquivo_de_saída > |o nome do arquivo e o local do arquivo de saída com erros, se houver algum |
   
    O motivo da presença do argumento /p:TableName é que só queremos testar a compatibilidade de banco de dados para exportar para o Banco de Dados SQL do Azure V12, em vez de exportar os dados de todas as tabelas. Infelizmente, o argumento de exportação para sqlpackage.exe não permite a extração de zero tabelas. Você precisa especificar pelo menos uma tabela, como uma única tabela pequena. O < arquivo_de_saída > contém o relatório de erros. A cadeia de caracteres ">2>&1" redireciona a saída padrão e o erro padrão resultantes da execução do comando para o arquivo de saída especificado.
   
    ![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Abra o arquivo de saída e examine os erros de compatibilidade, se houver. 
   
    ![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## <a name="next-steps"></a>Próximas etapas
* [Última versão do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
  [Última versão do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Corrigir problemas de compatibilidade de migração do banco de dados](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
* [Migrar um banco de dados SQL Server compatível para o Banco de Dados SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Recursos adicionais
* [Recursos do Banco de Dados SQL](sql-database-features.md)
* [Funções como suporte parcial ou inexistente no Transact-SQL](sql-database-transact-sql-information.md)
* [Migrar bancos de dados não SQL Server usando o Assistente de Migração do SQL Server](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Dec16_HO2-->


