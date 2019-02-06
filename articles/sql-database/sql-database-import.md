---
title: Importar um arquivo BACPAC para criar um Banco de Dados SQL do Azure | Microsoft Docs
description: Crie um novo banco de dados SQL do Azure importando um arquivo BACPAC existente.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: douglaslMS
ms.author: douglasl
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: c1b6c55475c1600c89c1ac1cae9dee0068b92070
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478212"
---
# <a name="quickstart-import-a-bacpac-file-to-a-new-azure-sql-database"></a>Início rápido: Importar um arquivo BACPAC para um novo Banco de Dados SQL do Azure

Você pode migrar um banco de dados SQL Server para um banco de dados SQL do Azure usando um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) (um arquivo zip com uma extensão `.bacpac` que tem metadados e dados de banco de dados). Você pode importar um arquivo BACPAC do armazenamento de blobs do Azure (apenas no armazenamento padrão) ou de uma determinada localização do armazenamento local. Para maximizar a velocidade de importação, você poderá especificar uma camada de serviço mais alta e o tamanho da computação (por exemplo, P6). Em seguida, você poderá reduzir verticalmente após a importação ser realizada. O nível de compatibilidade do banco de dados se baseia no nível de compatibilidade do banco de dados de origem.

> [!IMPORTANT]
> Após a importação dos dados, é possível operar o banco de dados em seu nível de compatibilidade atual (nível 100 para o banco de dados AdventureWorks2008R2) ou em um nível superior. Para obter mais informações sobre as implicações e as opções para a operação de um banco de dados em um nível de compatibilidade específico, consulte [nível de compatibilidade de ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). Consulte também [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) para obter informações sobre configurações de nível de banco de dados adicionais relacionadas aos níveis de compatibilidade.

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Importar de um arquivo BACPAC no portal do Azure

Esta seção mostra como criar um banco de dados SQL do Azure a partir de um arquivo BACPAC salvo no armazenamento de blobs do Azure usando o [Portal do Azure](https://portal.azure.com). O portal *só* oferece suporte à importação de um arquivo BACPAC do armazenamento de blobs do Azure.

> [!NOTE]
> A [Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance.md) oferece suporte para importação de um arquivo BACPAC usando os outros métodos deste artigo mas, atualmente, não oferece suporte para migração no portal do Azure.

Para importar um banco de dados no portal do Azure, abra a página para o servidor de Banco de Dados SQL que hospedará a importação e, na barra de ferramentas, selecione **Importar banco de dados**.  

   ![Importação de banco de dados](./media/sql-database-import/import.png)

Selecione a conta de armazenamento, o contêiner e o arquivo BACPAC que você deseja importar. Especifique o tamanho do novo banco de dados (normalmente, o mesmo que o original) e forneça as credenciais do SQL Server de destino. 

### <a name="monitor-imports-progress"></a>Monitorar o progresso da importação

Para monitorar o progresso de uma importação, abra a página do servidor do banco de dados importado e, em **Configurações**, selecione **Histórico de importação/exportação**. Quando obtiver êxito, a importação terá o status **Concluído**.

Para verificar se o banco de dados está ativo no servidor, selecione **Bancos de dados SQL** e verifique se o novo banco de dados está **Online**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Importar de um arquivo BACPAC usando o SqlPackage

Para importar um Banco de Dados SQL usando o utilitário de linha de comando [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage), consulte [Importar parâmetros e propriedades](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). O SqlPackage tem as versões mais recentes do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e do [SQL Server Data Tools para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Você também pode fazer o download da versão mais recente do [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) do Centro de Download da Microsoft.

Para escala e desempenho, recomendamos usar o SqlPackage na maioria dos ambientes de produção. Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrando do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC).

O seguinte comando SqlPackage importa o banco de dados **AdventureWorks2008R2** do armazenamento local para um servidor de Banco de Dados SQL do Azure chamado **mynewserver20170403**. Ele cria um novo banco de dados chamado **myMigratedDatabase** com uma camada de serviço **Premium** e um Objetivo de serviço **P6**. Altere esses valores conforme apropriado para o seu ambiente.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Um servidor de Banco de Dados SQL escuta na porta 1433. Para se conectar a um servidor do Banco de Dados SQL do Azure por trás de um firewall corporativo, essa porta do firewall precisa estar aberta.
>

Este exemplo mostra como importar um banco de dados usando o SqlPackage com a Autenticação Universal do Active Directory.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>Importação de um arquivo BACPAC usando o PowerShell

Use o cmdlet [New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) para enviar uma solicitação de importação de banco de dados para o serviço de Banco de Dados SQL do Azure. Dependendo do tamanho do banco de dados, a importação pode levar algum tempo para ser concluída.

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport 
    -ResourceGroupName "<your_resource_group>" `
    -ServerName "<your_server>" `
    -DatabaseName "<your_database>" `
    -DatabaseMaxSizeBytes "<database_size_in_bytes>" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "<your_resource_group>" -StorageAccountName "<your_storage_account").Value[0] `
    -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "<your_server_admin_account_user_id>" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "<your_server_admin_account_password>" -AsPlainText -Force)

 ```

 Você pode usar o cmdlet [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) para verificar o progresso da importação. A execução do cmdlet imediatamente após a solicitação geralmente retorna **Status: InProgress**. A importação estará concluída quando você vir **Status: Succeeded**.

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
Para outro exemplo de script, confira [Importar um banco de dados de um arquivo BACPAC](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Limitações

Não há suporte para a importação de um banco de dados no pool elástico. Você pode importar dados para um banco de dados individual e, em seguida, mover o banco de dados para um pool elástico.

## <a name="import-using-wizards"></a>Importar usando assistentes

Você também pode usar esses assistentes.

- [Importar o Assistente de Aplicativo da Camada de Dados no SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Assistente de Importação e Exportação do SQL Server](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Próximas etapas

- Para saber como se conectar e consultar um Banco de Dados SQL importado, consulte [Início rápido: Banco de Dados SQL do Azure: use o SQL Server Management Studio para conectar e consultar dados](sql-database-connect-query-ssms.md).
- Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrando do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC).
- Para ver uma discussão sobre todo o processo de migração do banco de dados do SQL Server, incluindo as recomendações de desempenho, consulte [Migração de um banco de dados do SQL Server para o Banco de Dados SQL do Azure](sql-database-cloud-migrate.md).
- Para aprender como gerenciar e compartilhar chaves de armazenamento e assinaturas de acesso compartilhado com segurança, consulte [Guia de Segurança do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
