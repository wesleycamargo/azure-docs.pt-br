---
ms.assetid: ''
title: O Azure Key Vault gerenciados a conta de armazenamento – CLI
description: Chaves da conta de armazenamento fornecem uma integração contínua entre o Azure Key Vault e o acesso baseado em chave para a Conta de Armazenamento do Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 10/03/2018
ms.openlocfilehash: e110a24af6a0d49ea949db6bc30ab344c81d0f48
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52619275"
---
# <a name="azure-key-vault-managed-storage-account---cli"></a>O Azure Key Vault gerenciados a conta de armazenamento – CLI

> [!NOTE]
> [O armazenamento do Azure agora dá suporte a autorização do AAD](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). Recomendamos o uso do Azure Active Directory para autenticação e autorização para Armazenamento, pois os usuários não precisariam se preocupar com a rotação de suas chaves da Conta de Armazenamento.

- O Azure Key Vault gerencia chaves de uma ASA (Conta de Armazenamento do Azure).
    - Internamente, o Azure Key Vault pode listar (sincronizar) chaves com uma Conta de Armazenamento do Azure.    
    - O Azure Key Vault gera novamente (gira) as chaves periodicamente.
    - Os valores de chave nunca são retornados em resposta ao chamador.
    - O Azure Key Vault gerencia chaves tanto de Contas de Armazenamento quanto de Contas de Armazenamento Clássicas.

<a name="prerequisites"></a>Pré-requisitos
--------------
1. [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) instalar CLI do Azure   
2. [Crie uma conta de armazenamento](https://azure.microsoft.com/services/storage/)
    - Siga as etapas neste [documento](https://docs.microsoft.com/azure/storage/) para criar uma conta de armazenamento  
    - **Diretrizes de nomenclatura:** nomes de conta de armazenamento deve ter entre 3 e 24 caracteres de comprimento e pode conter números e apenas letras minúsculas.        
      
<a name="step-by-step-instructions-on-how-to-use-key-vault-to-manage-storage-account-keys"></a>Instruções passo a passo sobre como usar o Key Vault para gerenciar chaves de conta de armazenamento
--------------------------------------------------------------------------------
Nas instruções abaixo, estamos atribuindo Key Vault como um serviço para ter permissões de operador em sua conta de armazenamento

> [!NOTE]
> Observe que, depois de configurar as chaves da conta de armazenamento gerenciado do Cofre do Teclado do Azure, elas devem **NÃO** ser alteradas por mais tempo, exceto via Cofre da Chave. As chaves da conta de armazenamento gerenciado significam que o Key Vault gerenciaria a rotação da chave da conta de armazenamento

1. Depois de criar uma conta de armazenamento, execute o seguinte comando para obter a ID de recurso da conta de armazenamento que você deseja gerenciar

    ```
    az storage account show -n storageaccountname (Copy ID field out of the result of this command)
    ```
    
2. Obter serviço da Azure Key Vault do ID aplicativo entidade de segurança 

    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
    
3. Atribuir função de operador da chave de armazenamento a identidade do Cofre de chave Azure

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ApplicationIdOfKeyVault> --scope <IdOfStorageAccount>
    ```
    
4. Criar uma Conta de Armazenamento Gerenciado do cofre de chaves.     <br /><br />
   Abaixo, estamos definindo um período de regeneração de 90 dias. Após 90 dias, o Cofre de chaves será regenerará 'key1' e trocará a chave ativa de 'key2' para 'key1'.
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key2 --auto-regenerate-key --regeneration-period P90D --resource-id <Resource-id-of-storage-account>
    ```
    No caso do usuário não tiver criado a conta de armazenamento e não tem permissões para a conta de armazenamento, as etapas a seguir defina as permissões para sua conta garantir que você pode gerenciar todas as permissões de armazenamento no cofre de chaves.
 > [!NOTE] 
    No caso em que o usuário não tem permissões à conta de armazenamento, obtemos primeiro a Object-Id do usuário

    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```
### <a name="relavant-azure-cli-cmdlets"></a>Cmdlets da CLI do Azure Relavant
- [Cmdlets de armazenamento da CLI do Azure](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

### <a name="relevant-powershell-cmdlets"></a>Cmdlets relevantes do Powershell

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="see-also"></a>Consulte também

- [Sobre chaves, segredos e certificados](https://docs.microsoft.com/rest/api/keyvault/)
- [Blog da equipe do Key Vault](https://blogs.technet.microsoft.com/kv/)
