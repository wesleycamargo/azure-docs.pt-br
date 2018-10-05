---
title: Transparent Data Encryption para Data Warehouse e Banco de Dados SQL do Azure | Microsoft Docs
description: Uma visão geral do Transparent Data Encryption para Data Warehouse e Banco de Dados SQL do Azure. O documento abrange os benefícios e as opções de configuração que incluem Transparent Data Encryption gerenciado pelo serviço e Bring Your Own Key.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: becczhang
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 07/09/2018
ms.openlocfilehash: 50b433c65dec1f667f32aaf60148a6e393c67320
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165919"
---
# <a name="transparent-data-encryption-for-sql-database-and-data-warehouse"></a>Transparent Data Encryption para Data Warehouse e Banco de Dados SQL do Azure

A TDE (Transparent Data Encryption) ajuda a proteger o Banco de Dados SQL do Azure e Data Warehouse do Azure contra a ameaça de atividade mal-intencionada. Ela realiza a criptografia e a descriptografia em tempo real do banco de dados, de backups associados e de arquivos de log de transações em repouso, sem a necessidade de alterações no aplicativo. Por padrão, a TDE está habilitada para todos os bancos de dados SQL do Azure recém-implantados. A TDE não pode ser utilizada para criptografar o banco de dados **mestre** lógico no Banco de Dados SQL.  O banco de dados **mestre** contém os objetos necessários para executar as operações de TDE nos bancos de dados do usuário.

Será necessário habilitar a TDE manualmente para bancos de dados mais antigos ou para SQL Data Warehouse do Microsoft Azure.  

A Transparent Data Encryption criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada de chave de criptografia de banco de dados. Esta chave de criptografia de banco de dados é protegida pelo protetor de Transparent Data Encryption. O protetor é um certificado de serviço gerenciado (Transparent Data Encryption de serviço gerenciado) ou uma chave assimétrica armazenada no Azure Key Vault (Bring Your Own Key). Você define o protetor de Transparent Data Encryption no nível do servidor. 

Na inicialização do banco de dados, a chave de criptografia de banco de dados criptografada é descriptografada e, em seguida, usada para descriptografia e nova criptografia dos arquivos de banco de dados no processo do Mecanismo de Banco de Dados do SQL Server. A Transparent Data Encryption executa criptografia e descriptografia de E/S em tempo real dos dados no nível de página. Cada página é descriptografada quando é lida na memória e, em seguida, criptografada antes de ser gravada no disco. Para obter uma descrição geral de Transparent Data Encryption, consulte [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).

O SQL Server em execução em uma máquina virtual do Azure também pode usar uma chave assimétrica do Key Vault. As etapas de configuração são diferentes de usar uma chave assimétrica no Banco de Dados SQL. Para obter mais informações, consulte [Gerenciamento de chave extensível usando Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).

## <a name="service-managed-transparent-data-encryption"></a>Transparent Data Encryption de serviço gerenciado

No Azure, a configuração padrão para Transparent Data Encryption é a chave de criptografia do banco de dados protegida por um certificado de servidor interno. O certificado do servidor interno é exclusivo para cada servidor do Azure. Se um banco de dados estiver em um relacionamento de replicação geográfica, tanto o banco de dados primário como o banco de dados geográfico secundário serão protegidos pela chave do servidor pai do banco de dados primário. Se dois bancos de dados estiverem conectados ao mesmo servidor, eles compartilharão o mesmo certificado interno. A Microsoft alterna automaticamente esses certificados pelo menos a cada 90 dias.

A Microsoft também move e gerencia as chaves conforme necessário para replicação geográfica e restaurações. 

> [!IMPORTANT]
> Todos os bancos de dados SQL criados recentemente são criptografados por padrão usando Transparent Data Encryption de serviço gerenciado. Os bancos de dados existentes antes de maio de 2017 e os bancos de dados criados por meio de restauração, replicação geográfica e cópia do banco de dados não são criptografados por padrão.
>

## <a name="bring-your-own-key"></a>Bring Your Own Key

Com o suporte Bring Your Own Key, você pode controlar as chaves de Transparent Data Encryption e controlar quem pode acessá-las e quando. O Key Vault, que é o sistema de gerenciamento de chave externa baseado em nuvem do Azure, é o primeiro serviço de gerenciamento importante que a Transparent Data Encryption integrou ao suporte Bring Your Own Key. Com o suporte Bring Your Own Key, a chave de criptografia do banco de dados é protegida por uma chave assimétrica armazenada no Key Vault. A chave assimétrica nunca sai do Key Vault. Depois que o servidor tiver permissões para um cofre de chaves, o servidor enviará solicitações básicas de operação de chave para o cofre através do Key Vault. Você define a chave assimétrica no nível do servidor e todos os bancos de dados nesse servidor herdam a chave.

Com o suporte Bring Your Own Key, é possível controlar as principais tarefas de gerenciamento como rotações de chave e permissões de cofre de chaves. Você também pode excluir chaves e habilitar auditoria/relatório em todas as chaves de criptografia. O Key Vault fornece gerenciamento central de chaves e usa módulos de segurança de hardware altamente monitorados. O Key Vault promove separação do gerenciamento de chaves e dados para ajudar no cumprimento da conformidade regulatória. Para saber mais sobre Key Vault, consulte a [página de documentação do Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

Para saber mais sobre Transparent Data Encryption com suporte Bring Your Own Key para Banco de Dados SQL e Data Warehouse, consulte [Transparent Data Encryption com suporte Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).

Para começar a usar a Transparent Data Encryption com o suporte Bring Your Own Key, consulte o guia de instruções [Ativar Transparent Data Encryption com sua própria chave no Key Vault usando PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Mover um banco de dados protegido por Transparent Data Encryption

Não é necessário descriptografar bancos de dados para operações no Azure. As configurações de Transparent Data Encryption no banco de dados de origem ou banco de dados primário são herdadas de forma transparente no destino. As operações incluídas envolvem:
- Replicação geográfica.
- Recuperação pontual de autoatendimento.
- Restauração de um banco de dados excluído.
- Replicação geográfica ativa.
- Criação de uma cópia do banco de dados.

Ao exportar um banco de dados protegido por Transparent Data Encryption, o conteúdo exportado do banco de dados não é criptografado. Esse conteúdo exportado é armazenado em arquivos BACPAC não criptografados. Certifique-se de proteger os arquivos BACPAC adequadamente e habilite a Transparent Data Encryption após a conclusão da importação do novo banco de dados.

Por exemplo, se o arquivo BACPAC for exportado de uma instância do SQL Server local, o conteúdo importado do novo banco de dados não será automaticamente criptografado. Da mesma forma, se o arquivo BACPAC for exportado para uma instância do SQL Server local, o novo banco de dados também não será automaticamente criptografado.

A única exceção é quando você exporta para e de um banco de dados SQL. A Transparent Data Encryption está habilitada no novo banco de dados, mas o próprio arquivo BACPAC ainda não está criptografado.

## <a name="manage-transparent-data-encryption-in-the-azure-portal"></a>Gerenciar Transparent Data Encryption no portal do Azure

Para configurar a Transparent Data Encryption por meio do portal do Azure, será necessário estar conectado como Proprietário do Azure, Colaborador ou Gerenciador de Segurança de SQL. 

Você define a Transparent Data Encryption no nível do banco de dados. Para habilitar a Transparent Data Encryption em um banco de dados, vá para o [portal do Azure](https://portal.azure.com) e entre com a conta de Administrador ou Colaborador do Azure. Localize as configurações de Transparent Data Encryption no banco de dados do usuário. Por padrão, é utilizada a Transparent Data Encryption de serviço gerenciado. Um certificado de Transparent Data Encryption é gerado automaticamente para o servidor que contém o banco de dados. 

![Transparent Data Encryption de serviço gerenciado](./media/transparent-data-encryption-azure-sql/service-managed-tde.png)  

Você define a chave mestra de Transparent Data Encryption, também conhecida como protetor de Transparent Data Encryption, no nível do servidor. Para usar a Transparent Data Encryption com suporte Bring Your Own Key e proteger os bancos de dados com uma chave do Key Vault, consulte as configurações de Transparent Data Encryption no servidor. 

![Transparent Data Encryption com suporte Bring Your Own Key](./media/transparent-data-encryption-azure-sql/tde-byok-support.png) 

## <a name="manage-transparent-data-encryption-by-using-powershell"></a>Gerenciar Transparent Data Encryption usando PowerShell

Para configurar a Transparent Data Encryption por meio do PowerShell, é necessário estar conectado como Proprietário do Azure, Colaborador ou Gerenciador de Segurança de SQL. 

| Cmdlet | DESCRIÇÃO |
| --- | --- |
| [Set-AzureRmSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) |Habilita ou desabilita a Transparent Data Encryption para um banco de dados|
| [Get-Azure-Rm-Sql-Database-Transparent-Data-Encryption](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption) |Obtém o estado de Transparent Data Encryption para um banco de dados |
| [Get-Azure-Rm-Sql-Database-Transparent-Data-Encryption-Activity](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryptionactivity) |Verifica o progresso de Transparent Data Encryption de um banco de dados |
| [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) |Adiciona uma chave do Key Vault a uma instância do SQL Server |
| [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) |Obtém as chaves do Key Vault para uma instância do SQL Server  |
| [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) |Define o protetor de Transparent Data Encryption para uma instância do SQL Server |
| [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) |Obtém o protetor de Transparent Data Encryption |
| [Remove-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/remove-azurermsqlserverkeyvaultkey) |Remove uma chave do Key Vault de uma instância do SQL Server |
|  | |

## <a name="manage-transparent-data-encryption-by-using-transact-sql"></a>Gerenciar Transparent Data Encryption usando Transact-SQL

Conecte o banco de dados usando um logon que seja um administrador ou membro da função **dbmanager** no banco de dados mestre.

| Comando | DESCRIÇÃO |
| --- | --- |
| [ALTER DATABASE (Banco de Dados SQL do Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF criptografa ou descriptografa um banco de dados |
| [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Retorna informações sobre o estado de criptografia de um banco de dados e as chaves de criptografia de banco de dados associadas |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Retorna informações sobre o estado de criptografia de cada nó de data warehouse e as chaves de criptografia de banco de dados associadas | 
|  | |

Não é possível alternar o protetor de Transparent Data Encryption para uma chave do Key Vault usando o Transact-SQL. Use PowerShell o portal do Azure.

## <a name="manage-transparent-data-encryption-by-using-the-rest-api"></a>Gerenciar Transparent Data Encryption usando a API REST
 
Para configurar a Transparent Data Encryption por meio da API REST, é necessário estar conectado como o Proprietário do Azure, Colaborador ou Gerenciador de Segurança do SQL. 

| Comando | DESCRIÇÃO |
| --- | --- |
|[Criar ou atualizar o servidor](/rest/api/sql/servers/createorupdate)|Adiciona uma identidade do Azure Active Directory a uma instância do SQL Server (usada para conceder acesso ao Key Vault)|
|[Criar ou atualizar a chave do servidor](/rest/api/sql/serverkeys/createorupdate)|Adiciona uma chave do Key Vault a uma instância do SQL Server|
|[Excluir chave do servidor](/rest/api/sql/serverkeys/delete)|Remove uma chave do Key Vault de uma instância do SQL Server|
|[Obter chaves do servidor](/rest/api/sql/serverkeys/get)|Obtém uma chave específica do Key Vault de uma instância do SQL Server|
|[Listar chaves do servidor por servidor](/rest/api/sql/serverkeys/listbyserver)|Obtém as chaves do Key Vault para uma instância do SQL Server |
|[Criar ou atualizar o protetor de criptografia](/rest/api/sql/encryptionprotectors/createorupdate)|Define o protetor de Transparent Data Encryption para uma instância do SQL Server|
|[Obter protetor de criptografia](/rest/api/sql/encryptionprotectors/get)|Obtém o protetor de Transparent Data Encryption para uma instância do SQL Server|
|[Listar protetores de criptografia por servidor](/rest/api/sql/encryptionprotectors/listbyserver)|Obtém os protetores de Transparent Data Encryption para uma instância do SQL Server |
|[Criar ou atualizar a configuração de Transparent Data Encryption](/rest/api/sql/transparentdataencryptions/createorupdate)|Habilita ou desabilita a Transparent Data Encryption para um banco de dados|
|[Obter configuração de Transparent Data Encryption](/rest/api/sql/transparentdataencryptions/get)|Obtém a configuração de Transparent Data Encryption para um banco de dados|
|[Lista de resultados de configuração de Transparent Data Encryption](/rest/api/sql/transparentdataencryptionactivities/ListByConfiguration)|Obtém o resultado da criptografia para um banco de dados|

## <a name="next-steps"></a>Próximas etapas

- Para obter uma descrição geral de Transparent Data Encryption, consulte [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Para saber mais sobre Transparent Data Encryption com suporte Bring Your Own Key para Banco de Dados SQL e Data Warehouse, consulte [Transparent Data Encryption com suporte Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).
- Para começar a usar a Transparent Data Encryption com o suporte Bring Your Own Key, consulte o guia de instruções [Ativar Transparent Data Encryption com sua própria chave no Key Vault usando PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).
- Para obter mais informações sobre Key Vault, consulte a [página de documentação do Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
