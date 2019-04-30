---
title: Configurar chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure do PowerShell
description: Saiba como usar o PowerShell para configurar as chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure. Chaves gerenciadas pelo cliente permitem que você criar, girar, desabilitar e revogar os controles de acesso.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 01d5226a2d6f4c4e2e376803a3f0a25d2d5b0fd6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61593556"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-powershell"></a>Configurar chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure do PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Este artigo mostra como configurar um cofre de chaves com chaves gerenciadas pelo cliente usando o PowerShell.

## <a name="assign-an-identity-to-the-storage-account"></a>Atribuir uma identidade para a conta de armazenamento

Para habilitar as chaves gerenciadas pelo cliente para sua conta de armazenamento, primeiro atribua uma identidade gerenciada atribuído pelo sistema para a conta de armazenamento. Você usará essa identidade gerenciada para conceder as permissões de conta de armazenamento para acessar o Cofre de chaves.

Para atribuir uma identidade gerenciada usando o PowerShell, chame [AzStorageAccount conjunto](/powershell/module/az.storage/set-azstorageaccount). Lembre-se de substituir os valores de espaço reservado entre colchetes pelos seus próprios valores.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Para obter mais informações sobre a configuração atribuída pelo sistema de identidades gerenciadas com o PowerShell, consulte [configurar gerenciadas identidades para recursos do Azure em uma VM do Azure usando o PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Criar um novo cofre de chaves

Para criar um novo cofre de chaves usando o PowerShell, chame [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). O Cofre de chaves que você usa para armazenar chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure deve ter duas configurações de proteção de chave habilitadas, **exclusão reversível** e **não limpar**. 

Lembre-se de substituir os valores de espaço reservado entre colchetes pelos seus próprios valores. 

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Configurar a política de acesso do Cofre de chaves

Em seguida, configure a política de acesso para o Cofre de chaves para que a conta de armazenamento tem permissões para acessá-lo. Nesta etapa, você usará a identidade gerenciada atribuídos previamente a conta de armazenamento.

Para definir a política de acesso para o Cofre de chaves, chame [AzKeyVaultAccessPolicy conjunto](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Lembre-se de substituir os valores de espaço reservado entre colchetes pelos seus próprios valores e para usar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Criar uma nova chave

Em seguida, crie uma nova chave no cofre de chaves. Para criar uma nova chave, chame [AzKeyVaultKey adicionar](/powershell/module/az.keyvault/add-azkeyvaultkey). Lembre-se de substituir os valores de espaço reservado entre colchetes pelos seus próprios valores e para usar as variáveis definidas nos exemplos anteriores.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurar a criptografia com chaves gerenciadas pelo cliente

Por padrão, a criptografia de armazenamento do Azure usa chaves gerenciadas pela Microsoft. Nesta etapa, configure sua conta de armazenamento do Azure para usar chaves gerenciadas pelo cliente e especifique a chave para associar a conta de armazenamento.

Chame [AzStorageAccount conjunto](/powershell/module/az.keyvault/set-azstorageaccount) ao atualizar as configurações de criptografia da conta de armazenamento. Lembre-se de substituir os valores de espaço reservado entre colchetes pelos seus próprios valores e para usar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Atualizar a versão da chave

Quando você cria uma nova versão de uma chave, você precisará atualizar a conta de armazenamento para usar a nova versão. Primeiro, chame [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) para obter a versão mais recente da chave. Em seguida, chame [AzStorageAccount conjunto](/powershell/module/az.keyvault/set-azstorageaccount) para atualizar as configurações de criptografia da conta de armazenamento para usar a nova versão da chave, como mostrado na seção anterior.

## <a name="next-steps"></a>Próximas etapas

- [Criptografia de armazenamento do Azure para dados em repouso](storage-service-encryption.md) 
- [O que é o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
