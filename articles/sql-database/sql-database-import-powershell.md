---
title: Importar um arquivo BACPAC para criar um Banco de Dados SQL do Azure usando o PowerShell | Microsoft Docs
description: Importar um arquivo BACPAC para criar um Banco de Dados SQL do Azure usando o PowerShell
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 8d78da13-43fe-4447-92e0-0a41d0321fd4
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 02/07/2017
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: e6f0d661465c813ec310b8c69ab1ee06e4f95401
ms.openlocfilehash: 45ec817e62e7967549602adfd2c9d2d3f2484987


---
# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-by-using-powershell"></a>Importar um arquivo BACPAC para criar um Banco de Dados SQL do Azure usando o PowerShell

Este artigo fornece instruções para criar um banco de dados SQL do Azure importando um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) com o PowerShell.

## <a name="prequisites"></a>Pré-requisitos

Para importar um banco de dados SQL, você precisará de:

* Uma assinatura do Azure. Caso você precise de uma assinatura do Azure, basta clicar em **Avaliação gratuita** na parte superior desta página e, em seguida, voltar para concluir este artigo.
* Um arquivo BACPAC do banco de dados que você deseja importar. O BACPAC deve estar em um contêiner de blob da [conta de Armazenamento do Azure](../storage/storage-create-storage-account.md) .

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="set-up-the-variables-for-your-environment"></a>Configurar as variáveis do ambiente
Existem algumas variáveis em que você precisará substituir os valores de exemplo pelos valores específicos do seu banco de dados e conta de armazenamento.

O nome do servidor deve ser um servidor que existe atualmente na assinatura selecionada na etapa anterior. Ele deve ser o servidor no qual você deseja que o banco de dados seja criado. Não há suporte para a importação de um banco de dados diretamente em um pool elástico. No entanto, você pode primeiro importar como um banco de dados individual e, em seguida, mover o banco de dados para um pool.

O nome do banco de dados é o nome desejado para o novo banco de dados.

    $ResourceGroupName = "resource group name"
    $ServerName = "server name"
    $DatabaseName = "database name"


As variáveis a seguir são da conta de armazenamento onde seu BACPAC está localizado. No [Portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento para obter esses valores. É possível encontrar a chave de acesso primária clicando em **Todas as configurações** e em **Chaves** na folha da sua conta de armazenamento.

O nome do blob é o nome de um arquivo BACPAC existente do qual você deseja criar o banco de dados. Você precisa incluir a extensão .bacpac.

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


A execução do cmdlet [Get-Credential](https://msdn.microsoft.com/library/azure/hh849815\(v=azure.300\).aspx) abrirá uma janela que solicitará seu nome de usuário e senha. Insira o logon e a senha de administrador do servidor do Banco de Dados SQL ($ServerName acima) e não o nome de usuário e a senha da sua conta do Azure.

    $credential = Get-Credential


## <a name="import-the-database"></a>Importar o banco de dados
Esse comando envia ao serviço uma solicitação para importar o banco de dados. Dependendo do tamanho do banco de dados, a operação de importação pode demorar para ser concluída.

    $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -StorageKeytype $StorageKeyType -StorageKey $StorageKey -StorageUri $StorageUri -AdministratorLogin $credential.UserName -AdministratorLoginPassword $credential.Password -Edition Standard -ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000


## <a name="monitor-the-progress-of-the-operation"></a>Monitorar o andamento da operação
Depois de executar [New-AzureRmSqlDatabaseImport](https://msdn.microsoft.com/library/azure/mt707793\(v=azure.300\).aspx), você poderá verificar o status da solicitação executando [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/azure/mt707794\(v=azure.300\).aspx).

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="sql-database-powershell-import-script"></a>Script de importação do PowerShell do banco de dados SQL
    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -StorageKeytype $StorageKeyType -StorageKey $StorageKey -StorageUri $StorageUri -AdministratorLogin $credential.UserName -AdministratorLoginPassword $credential.Password -Edition Standard -ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="next-steps"></a>Próximas etapas
* Para aprender a se conectar e a consultar um Banco de Dados SQL importado, confira [Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio e executar um exemplo de consulta T-SQL](sql-database-connect-query-ssms.md).
* Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrando do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
* Para ver uma discussão de todo o processo de migração do banco de dados do SQL Server, incluindo as recomendações de desempenho, confira [Migrar um banco de dados do SQL Server para o Banco de Dados SQL do Azure](sql-database-cloud-migrate.md).





<!--HONumber=Feb17_HO2-->


