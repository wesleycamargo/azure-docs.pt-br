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
ms.date: 02/08/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e6f0d661465c813ec310b8c69ab1ee06e4f95401
ms.openlocfilehash: 23c5da7b9e171312274aec02262b139ee701deb1


---
# <a name="import-a-database-from-a-bacpac-file-to-azure-sql-database-using-sqlpackage"></a>Importar um banco de dados de um arquivo BACPAC para o Banco de Dados SQL do Azure usando SqlPackage

Este artigo mostra como importar o banco de dados SQL de um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) usando o utilitário de linha de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Esse utilitário acompanha as últimas versões do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), ou você pode baixar a última versão do [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) diretamente no Centro de Download da Microsoft.

> [!NOTE]
> As etapas a seguir supõem que você já configurou um servidor de Banco de Dados SQL, tem as informações de conexão disponíveis e verificou se o banco de dados de origem é compatível.
> 
> 

## <a name="import-the-database"></a>Importar o banco de dados
Use o utilitário de linha de comando [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) para importar um banco de dados do SQL Server compatível (ou um banco de dados SQL do Azure) de um arquivo BACPAC.

> [!NOTE]
> As etapas a seguir supõem que você já tenha provisionado um servidor do Banco de Dados SQL do Azure e tenha as informações sobre a conexão a seu alcance.
>  

Em um prompt de comando no diretório que contém a versão mais recente do utilitário de linha de comando sqlpackage.exe, execute um comando semelhante ao exemplo de comando a seguir, que importa um arquivo BACPAC para um Banco de Dados do SQL Azure como um Premium P11.

```
SqlPackage.exe /a:import /tcs:"Data Source=SERVER;Initial Catalog=DBNAME;User Id=USER;Password=PASSWORD" /sf:C:\db.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P11
```   

## <a name="next-steps"></a>Próximas etapas

* Para ver uma discussão de todo o processo de migração do banco de dados do SQL Server, incluindo as recomendações de desempenho, confira [Migrar um banco de dados do SQL Server para o Banco de Dados SQL do Azure](sql-database-cloud-migrate.md).
* Para obter conteúdo de referência no SQLPackage, confira [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx).
* Para baixar a versão mais recente do SQLPackage, confira [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876).
* Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrando do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC).




<!--HONumber=Feb17_HO2-->


