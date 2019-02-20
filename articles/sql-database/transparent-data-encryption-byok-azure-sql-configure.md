---
title: 'PowerShell e CLI: Habilitar TDE do SQL – com o Azure Key Vault – Bring Your Own Key – Banco de Dados SQL do Azure| Microsoft Docs'
description: Saiba como configurar um Data Warehouse e Banco de Dados SQL do Azure para começar a usar a TDE (Transparent Data Encryption) para criptografia em repouso usando PowerShell ou CLI.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: f1cb99799e3aa5c0b37643112f8644d1aabfd666
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56108079"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell e CLI: Habilitar Transparent Data Encryption com chave gerenciada pelo cliente do Azure Key Vault

Este artigo descreve como usar uma chave do Azure Key Vault para TDE (Transparent Data Encryption) em um Banco de Dados SQL ou Data Warehouse. Para saber mais sobre a integração da TDE com o Azure Key Vault - Suporte a Bring Your Own Key (BYOK), visite [TDE com chaves gerenciadas pelo cliente no Azure Key Vault](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>Pré-requisitos para PowerShell

- É necessário ter uma assinatura do Azure e ser um administrador nessa assinatura.
- [Recomendado, mas opcional] Tenha um HSM (Módulo de Segurança de Hardware) ou armazenamento de chave local para criar uma cópia local do material da chave do Protetor de TDE.
- É necessário ter o Azure PowerShell versão 4.2.0 ou mais recente instalado e em execução. 
- Crie um Azure Key Vault e Chave para usar na TDE.
   - [Instruções do PowerShell do Key Vault](../key-vault/key-vault-overview.md)
   - [Instruções para usar um HSM (Módulo de Segurança de Hardware) e Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
 - O cofre de chaves deve ter a seguinte propriedade a ser usada para TDE:
   - [exclusão reversível](../key-vault/key-vault-ovw-soft-delete.md)
   - [Como usar a exclusão reversível do Key Vault com o PowerShell](../key-vault/key-vault-soft-delete-powershell.md) 
- A chave deve ter os seguintes atributos a serem usados para TDE:
   - Sem data de validade
   - Não desabilitado
   - Capaz de realizar operações *get*, *codificar chave*, *decodificar chave*

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>Etapa 1. Atribuir uma identidade do Azure AD ao servidor 

Se houver um servidor existente, use o seguinte para adicionar uma identidade do Azure AD ao servidor:

   ```powershell
   $server = Set-AzureRmSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

Se estiver criando um servidor, use o cmdlet [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) com a marca -Identity para adicionar uma identidade do Azure AD durante a criação do servidor:

   ```powershell
   $server = New-AzureRmSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>Etapa 2. Conceder permissões do Key Vault ao servidor

Use o cmdlet [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) para conceder ao servidor acesso ao cofre de chaves antes de usar uma chave dele para TDE.

   ```powershell
   Set-AzureRmKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Etapa 3. Adicionar a chave do Key Vault ao servidor e definir o Protetor de TDE

- Use o cmdlet [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) para adicionar a chave do Key Vault ao servidor.
- Use o cmdlet [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) para definir a chave como o protetor de TDE a todos os recursos do servidor.
- Use o cmdlet [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) para confirmar que o protetor de TDE foi configurado conforme o esperado.

> [!Note]
> O comprimento combinado para o nome do cofre de chaves e o nome da chave não pode exceder 94 caracteres.
> 

>[!Tip]
>Um exemplo de KeyId do Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzureRmSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>Etapa 4. Ativar TDE 

Use o cmdlet [Set-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) para ativar a TDE.

   ```powershell
   Set-AzureRMSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

Agora, o banco de dados ou data warehouse possui a TDE habilitada com uma chave de criptografia no Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Etapa 5. Verificar o estado de criptografia e a atividade de criptografia

Use o [Get-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption) para obter o estado de criptografia e [Get-AzureRMSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryptionactivity) para verificar o progresso da criptografia de um banco de dados ou data warehouse.

   ```powershell
   # Get the encryption state
   Get-AzureRMSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzureRMSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>Outros cmdlets do PowerShell úteis

- Use o cmdlet [Set-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) para desativar a TDE.

   ```powershell
   Set-AzureRMSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled”
   ```
 
- Use o cmdlet [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) para retornar a lista de chaves do Key Vault adicionada ao servidor.

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzureRmSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- Use o [Remove-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/remove-azurermsqlserverkeyvaultkey) para remover uma chave do Key Vault do servidor.

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzureRmSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>solução de problemas

Se um problema ocorrer, verifique o seguinte:
- Se o cofre de chaves não puder ser localizado, verifique se você está na assinatura correta usando o cmdlet [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

   ```powershell
   Get-AzureRmSubscription `
   -SubscriptionId <SubscriptionId>
   ```

- Se a nova chave não puder ser adicionada ao servidor, ou a nova chave não puder ser atualizada como o protetor de TDE, verifique o seguinte:
   - A chave não deve ter uma data de validade
   - A chave deve ter as operações *get*, *codificar chave* e *decodificar chave* habilitadas.

## <a name="next-steps"></a>Próximas etapas

- Saiba como girar o Protetor de TDE de um servidor para atender aos requisitos de segurança: [Girar o protetor de Transparent Data Encryption usando o PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Em caso de risco à segurança, saiba como remover um Protetor de TDE potencialmente comprometido: [Remover uma chave potencialmente comprometida](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md). 

## <a name="prerequisites-for-cli"></a>Pré-requisitos para CLI

- É necessário ter uma assinatura do Azure e ser um administrador nessa assinatura.
- [Recomendado, mas opcional] Tenha um HSM (Módulo de Segurança de Hardware) ou armazenamento de chave local para criar uma cópia local do material da chave do Protetor de TDE.
- Interface de Linha de Comando versão 2.0 ou posterior. Para instalar a última versão e conectá-la à sua assinatura do Azure, consulte [Instalar e configurar a Interface de Linha de Comando 2.0 de plataforma cruzada do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 
- Crie um Azure Key Vault e Chave para usar na TDE.
   - [Gerenciar o Key Vault usando CLI 2.0](../key-vault/key-vault-manage-with-cli2.md)
   - [Instruções para usar um HSM (Módulo de Segurança de Hardware) e Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
 - O cofre de chaves deve ter a seguinte propriedade a ser usada para TDE:
   - [exclusão reversível](../key-vault/key-vault-ovw-soft-delete.md)
   - [Como usar a exclusão reversível do Key Vault com a CLI](../key-vault/key-vault-soft-delete-cli.md) 
- A chave deve ter os seguintes atributos a serem usados para TDE:
   - Sem data de validade
   - Não desabilitado
   - Capaz de realizar operações *get*, *codificar chave*, *decodificar chave*
   
## <a name="step-1-create-a-server-and-assign-an-azure-ad-identity-to-your-server"></a>Etapa 1. Criar um servidor e atribuir uma identidade do Azure AD ao servidor
      cli
      # create server (with identity) and database
      az sql server create -n "ServerName" -g "ResourceGroupName" -l "westus" -u "cloudsa" -p "YourFavoritePassWord99@34" -i 
      az sql db create -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 
      

 
## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>Etapa 2. Conceder permissões do Key Vault ao servidor
      cli
      # create key vault, key and grant permission
      az keyvault create -n "VaultName" -g "ResourceGroupName" 
      az keyvault key create -n myKey -p software --vault-name "VaultName" 
      az keyvault set-policy -n "VaultName" --object-id "ServerIdentityObjectId" -g "ResourceGroupName" --key-permissions wrapKey unwrapKey get list 
      

 
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Etapa 3. Adicionar a chave do Key Vault ao servidor e definir o Protetor de TDE
  
     cli
     # add server key and update encryption protector
      az sql server key create -g "ResourceGroupName" -s "ServerName" -t "AzureKeyVault" -u "FullVersionedKeyUri 
      az sql server tde-key update -g "ResourceGroupName" -s "ServerName" -t AzureKeyVault -u "FullVersionedKeyUri" 
      
  
  > [!Note]
> O comprimento combinado para o nome do cofre de chaves e o nome da chave não pode exceder 94 caracteres.
> 

>[!Tip]
>Um exemplo de KeyId do Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
  
## <a name="step-4-turn-on-tde"></a>Etapa 4. Ativar TDE 
      cli
      # enable encryption
      az sql db tde create -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" --status Enabled 
      

Agora, o banco de dados ou data warehouse possui a TDE habilitada com uma chave de criptografia no Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Etapa 5. Verificar o estado de criptografia e a atividade de criptografia

     cli
      # get encryption scan progress
      az sql db tde show-activity -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 

      # get whether encryption is on or off
      az sql db tde show-configuration -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 

## <a name="sql-cli-references"></a>Referências da CLI do SQL

https://docs.microsoft.com/cli/azure/sql?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/server/key?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/server/tde-key?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/db/tde?view=azure-cli-latest 

