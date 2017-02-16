---
title: 'SqlPackage: Importar de um arquivo BACPAC para o Banco de Dados SQL do Azure | Microsoft Docs'
description: "Este artigo mostra como importar o banco de dados SQL de um arquivo BACPAC usando o utilitário de linha de comando SqlPackage."
keywords: "Banco de Dados SQL do Microsoft Azure, migração de banco de dados, importar banco de dados, importar arquivo BACPAC, sqlpackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 424afa27-5f13-4ec3-98f6-99a511a6a2df
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: 048a8f333eb555d860df0fd485ec8d53c27e3cd6


---
# <a name="import-to-sql-database-from-a-bacpac-file-using-sqlpackage"></a>Importação de um arquivo BACPAC para o Banco de Dados SQL usando o SqlPackage
> [!div class="op_single_selector"]
> * [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
> * [Portal do Azure](sql-database-import.md)
> * [PowerShell](sql-database-import-powershell.md)
> 
> 

Este artigo mostra como importar o banco de dados SQL de um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) usando o utilitário de linha de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Esse utilitário acompanha as últimas versões do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), ou você pode baixar a última versão do [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) diretamente no Centro de Download da Microsoft.

> [!NOTE]
> As etapas a seguir supõem que você já configurou um servidor de Banco de Dados SQL, tem as informações de conexão disponíveis e verificou se o banco de dados de origem é compatível.
> 
> 

## <a name="import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage"></a>Importar de um arquivo BACPAC para o Banco de Dados SQL do Azure usando o SqlPackage
Use as etapas a seguir para usar o utilitário de linha de comando [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) para importar um banco de dados do SQL Server compatível (ou um banco de dados SQL do Azure) de um arquivo BACPAC.

> [!NOTE]
> As etapas a seguir supõem que você já tenha provisionado um servidor do Banco de Dados SQL do Azure e tenha as informações sobre a conexão a seu alcance.
> 
> 

1. Abra um prompt de comando e altere um diretório com o utilitário de linha de comando sqlpackage.exe – esse utilitário é fornecido com o Visual Studio e o SQL Server.
2. Execute o seguinte comando sqlpackage.exe com os seguintes argumentos para o seu ambiente:
   
    `sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >`
   
   | Argumento | Descrição |
   | --- | --- |
   | < nome_do_servidor > |nome do servidor de destino |
   | < nome_do_banco_de_dados > |nome do banco de dados de destino |
   | < nome_de_usuário > |o nome de usuário no servidor de destino |
   | < senha > |a senha do usuário |
   | < arquivo_de_origem > |o nome e o local do arquivo BACPAC que está sendo importado |
   
    ![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

## <a name="next-steps"></a>Próximas etapas
* [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Recursos adicionais
* [Recursos do Banco de Dados SQL](sql-database-features.md)
* [Funções como suporte parcial ou inexistente no Transact-SQL](sql-database-transact-sql-information.md)
* [Migrar bancos de dados não SQL Server usando o Assistente de Migração do SQL Server](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


