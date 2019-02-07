---
title: PowerShell - Girar o protetor de TDE - Banco de Dados SQL do Microsoft Azure| Microsoft Docs
description: Saiba como girar o protetor de TDE (Transparent Data Encryption) para um servidor SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: jhubbard
ms.date: 12/06/2018
ms.openlocfilehash: 14a39d283d9ec4f8d5267e6a6628609ac79879ee
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2019
ms.locfileid: "55567495"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Girar o protetor de TDE (Transparent Data Encryption) usando PowerShell

Este artigo descreve a rotação de chave para um servidor SQL do Azure usando um protetor de TDE do Azure Key Vault. Girar um protetor de TDE do servidor SQL do Azure significa alternar para uma nova chave assimétrica que protege os bancos de dados no servidor. A rotação de chave é uma operação online e deve demorar apenas alguns segundos para ser concluída, porque isso somente descriptografa e criptografa novamente a chave de criptografia de dados do banco de dados, não o banco de dados inteiro.

Este guia aborda duas opções para girar o protetor de TDE no servidor.

> [!NOTE]
> Um SQL Data Warehouse pausado deve ser retomado antes das rotações de chave.
>

> [!IMPORTANT]
> **Não Exclua** as versões anteriores da chave após uma substituição.  Quando as teclas são substituídas, alguns dados ainda são criptografados com as chaves anteriores, como backups de banco de dados mais antigos. 
>

## <a name="prerequisites"></a>Pré-requisitos

- Este guia de instruções assume que você já esteja usando uma chave do Azure Key Vault como protetor de TDE para um Data Warehouse ou Banco de Dados SQL do Azure. Consulte [Transparent Data Encryption com suporte de BYOK](transparent-data-encryption-byok-azure-sql.md).
- É necessário ter o Azure PowerShell versão 3.7.0 ou mais recente instalado e em execução. 
- [Recomendado, mas opcional] Crie o material da chave para o protetor de TDE em um HSM (Módulo de Segurança de Hardware) ou o armazenamento de chaves local primeiro e importe o material da chave para o Azure Key Vault. Siga as [instruções para usar um HSM (Módulo de Segurança de Hardware) e Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) para saber mais.

## <a name="option-1-auto-rotation"></a>Opção 1: Rotação automática

Gere uma nova versão da chave de protetor de TDE existente no Key Vault, no mesmo nome de chave e cofre de chaves. O serviço SQL do Azure começará a usar essa nova versão em 24 horas. 

Para criar uma nova versão do protetor de TDE usando o cmdlet [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey):

   ```powershell
   Add-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>
   ```

## <a name="option-2-manual-rotation"></a>Opção 2: Rotação manual

A opção usa os cmdlets [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey), [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) e [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) para adicionar uma chave completamente nova, que pode estar sob um novo nome de chave ou até mesmo em outro cofre de chaves. 

>[!NOTE]
>O comprimento combinado para o nome do cofre de chaves e o nome da chave não pode exceder 94 caracteres.
>

   ```powershell
   # Add a new key to Key Vault
   Add-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzureRmSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>   
  
   <# Set the key as the TDE protector for all resources 
   under the server #>
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```
  
## <a name="other-useful-powershell-cmdlets"></a>Outros cmdlets do PowerShell úteis

- Para alternar o protetor de TDE do modo gerenciado pela Microsoft para o modo BYOK, use o cmdlet [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector).

   ```powershell
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Para alternar o protetor de TDE do modo BYOK para gerenciado pela Microsoft, use o cmdlet [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector).

   ```powershell
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>Próximas etapas

- Em caso de risco de segurança, saiba como remover um protetor de TDE potencialmente comprometido: [Remover uma chave potencialmente comprometida](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

- Introdução ao suporte do Bring Your Own Key para TDE: [Ativar o TDE usando sua própria chave do Key Vault usando o PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
