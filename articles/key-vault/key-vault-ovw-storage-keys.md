---
ms.assetid: ''
title: O Azure Key Vault gerenciados a conta de armazenamento – CLI
description: Chaves da conta de armazenamento fornecem uma integração contínua entre o Azure Key Vault e o acesso baseado em chave para a Conta de Armazenamento do Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: prashanthyv
ms.author: pryerram
manager: mbaldwin
ms.date: 10/03/2018
ms.openlocfilehash: 152e1e5892e3a72286205c2f5bf4e18b2a2bcbf7
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814836"
---
# <a name="azure-key-vault-managed-storage-account---cli"></a>O Azure Key Vault gerenciados a conta de armazenamento – CLI

> [!NOTE]
> [Integração de armazenamento do Azure com o Microsoft Azure Active Directory (Azure AD) agora está em versão prévia](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). É recomendável usar o Microsoft Azure Active Directory para autenticação e autorização, que fornece acesso baseado em token OAuth2 no armazenamento do Azure, assim como o Azure Key Vault. Isso permite que você:
> - Autentique seu aplicativo cliente usando um aplicativo ou identidade do usuário, em vez de credenciais da conta de armazenamento. 
> - Use uma [identidade gerenciada do Microsoft Azure Active Directory](/azure/active-directory/managed-identities-azure-resources/) ao executar no Azure. Gerenciar identidades remove a necessidade de autenticação de cliente tudo junto e armazenar credenciais no ou com seu aplicativo.
> - Use o Controle de Acesso com Base da Função (RBAC) para gerenciar a autorização, que também é compatível com o Azure Key Vault.

- O Azure Key Vault gerencia chaves de uma ASA (Conta de Armazenamento do Azure).
    - Internamente, o Azure Key Vault pode listar (sincronizar) chaves com uma Conta de Armazenamento do Azure.    
    - O Azure Key Vault gera novamente (gira) as chaves periodicamente.
    - Os valores de chave nunca são retornados em resposta ao chamador.
    - O Azure Key Vault gerencia chaves tanto de Contas de Armazenamento quanto de Contas de Armazenamento Clássicas.
    
> [!IMPORTANT]
> O Azure Active Directory fornece cada aplicativo registrado com uma **[entidade de serviço](/azure/active-directory/develop/developer-glossary#service-principal-object)**, que serve como a identidade do aplicativo. A ID do aplicativo da entidade de serviço é usada ao dar autorização para acessar outros recursos do Azure, por meio do controle de acesso baseado na função (RBAC). Como o Key Vault é um aplicativo da Microsoft, ele é registrado previamente em todos os locatários do Azure AD com a mesma ID de aplicativo dentro de cada nuvem do Azure:
> - Locatários do Microsoft Azure Active Directory na nuvem de governo do Azure usam a ID do Aplicativo `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - Os locatários do Microsoft Azure Active Directory na nuvem pública do Azure e todos os outros usam a ID do Aplicativo`cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.

<a name="prerequisites"></a>Pré-requisitos
--------------
1. [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) instalar CLI do Azure   
2. [Crie uma conta de armazenamento](https://azure.microsoft.com/services/storage/)
    - Siga as etapas neste [documento](https://docs.microsoft.com/azure/storage/) para criar uma conta de armazenamento  
    - **Diretrizes de nomenclatura:** Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas.        
      
<a name="step-by-step-instructions-on-how-to-use-key-vault-to-manage-storage-account-keys"></a>Instruções passo a passo sobre como usar o Key Vault para gerenciar chaves de conta de armazenamento
--------------------------------------------------------------------------------
Nas instruções abaixo, estamos atribuindo Key Vault como um serviço para ter permissões de operador em sua conta de armazenamento

> [!NOTE]
> Observe que, depois de configurar as chaves da conta de armazenamento gerenciado do Cofre do Teclado do Azure, elas devem **NÃO** ser alteradas por mais tempo, exceto via Cofre da Chave. As chaves da conta de armazenamento gerenciado significam que o Key Vault gerenciaria a rotação da chave da conta de armazenamento

1. Depois de criar uma conta de armazenamento, execute o seguinte comando para obter a ID de recurso da conta de armazenamento que você deseja gerenciar

    ```
    az storage account show -n storageaccountname 
    ```
    Copie o campo da ID do resultado do comando acima
    
2. Execute o comando a seguir para obter a ID do objeto de serviço da entidade de serviço do Azure Key Vault

    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
    
    Após a conclusão bem-sucedida desse comando, localize a ID de objeto no resultado
    ```console
        {
            ...
            "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
            ...
        }
    ```
    
3. Atribuir função de operador da chave de armazenamento a identidade do Cofre de chave Azure

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ObjectIdOfKeyVault> --scope <IdOfStorageAccount>
    ```
    
4. Criar uma Conta de Armazenamento Gerenciado do cofre de chaves.     <br /><br />
   Abaixo, estamos definindo um período de regeneração de 90 dias. Após 90 dias, o Cofre de chaves será regenerará 'key1' e trocará a chave ativa de 'key2' para 'key1'. Agora ele marcará a Key1 como a chave ativa. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```
    No caso do usuário não tiver criado a conta de armazenamento e não tem permissões para a conta de armazenamento, as etapas a seguir defina as permissões para sua conta garantir que você pode gerenciar todas as permissões de armazenamento no cofre de chaves.
    
 > [!NOTE] 
 > No caso em que o usuário não tem permissões à conta de armazenamento, obtemos primeiro a Object-Id do usuário


    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    
    ```
    
## <a name="how-to-access-your-storage-account-with-sas-tokens"></a>Como acessar sua conta de armazenamento com tokens SAS

Nesta seção, discutiremos como você pode fazer operações em sua conta de armazenamento buscando [tokens SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) do Key Vault

Na seção abaixo, demonstramos como buscar sua chave de conta de armazenamento armazenada no Key Vault e usar isso para criar uma definição de SAS (Assinatura de Acesso Compartilhado) para sua conta de armazenamento.

> [!NOTE] 
  Há três maneiras de se autenticar no Key Vault, como você pode ler nos [conceitos básicos](key-vault-whatis.md#basic-concepts)
> - Usando a Identidade de Serviço Gerenciada (altamente recomendado)
> - Usando a Entidade de serviço e o certificado 
> - Usando a Entidade de serviço e a senha (NÃO recomendado)

```cs
// Once you have a security token from one of the above methods, then create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault. SecretUri is of the format https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Se seu token de SAS está prestes a expirar, então busque o token SAS novamente no Key Vault e atualize o código

```cs
// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


### <a name="relavant-azure-cli-cmdlets"></a>Cmdlets da CLI do Azure Relavant
[Cmdlets de armazenamento da CLI do Azure](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

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
