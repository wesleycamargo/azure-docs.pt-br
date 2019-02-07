---
title: Conta de armazenamento gerenciado do Azure Key Vault - versão PowerShell
description: O recurso da conta de armazenamento gerenciada fornece uma integração perfeita, entre o Azure Key Vault e uma conta de armazenamento do Azure.
ms.topic: conceptual
ms.service: key-vault
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 11/28/2018
ms.openlocfilehash: 1c0502458a5c20991ada6f5a33d067a38596752b
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55817556"
---
# <a name="azure-key-vault-managed-storage-account---powershell"></a>Conta de armazenamento gerenciado do Azure Key Vault - PowerShell

> [!NOTE]
> [Integração de armazenamento do Azure com o Microsoft Azure Active Directory (Azure AD) agora está em versão prévia](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). É recomendável usar o Microsoft Azure Active Directory para autenticação e autorização, que fornece acesso baseado em token OAuth2 no armazenamento do Azure, assim como o Azure Key Vault. Isso permite que você:
> - Autentique seu aplicativo cliente usando um aplicativo ou identidade do usuário, em vez de credenciais da conta de armazenamento. 
> - Use uma [identidade gerenciada do Microsoft Azure Active Directory](/azure/active-directory/managed-identities-azure-resources/) ao executar no Azure. Gerenciar identidades remove a necessidade de autenticação de cliente tudo junto e armazenar credenciais no ou com seu aplicativo.
> - Use o Controle de Acesso com Base da Função (RBAC) para gerenciar a autorização, que também é compatível com o Azure Key Vault.

Uma [conta de armazenamento do Azure](/azure/storage/storage-create-storage-account) usa uma credencial que consiste em um nome de conta e uma chave. A chave é gerada automaticamente e atua mais como uma "senha" em vez de uma chave de criptografia. O Key Vault pode gerenciar essas chaves de conta de armazenamento, armazenando-as como [segredos do Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

## <a name="overview"></a>Visão geral

O recurso de conta de armazenamento gerenciado do Key Vault realiza várias funções de gerenciamento em seu nome:

- Listas (sincronização) chaves com uma conta de armazenamento do Azure.
- Regenera (gira) as chaves periodicamente.
- Gerencia chaves tanto de contas de armazenamento quanto de contas de armazenamento clássicas.
- Os valores de chave nunca são retornados em resposta ao chamador.

Quando você usa o recurso de chave de conta de armazenamento gerenciado:

- **Permita somente que o Key Vault gerencie suas chaves de conta.** Não tente gerenciá-las você mesmo, pois você interferirá nos processos do Key Vault.
- **Não permita que chaves de armazenamento sejam gerenciadas por mais de um objeto do Key Vault**.
- **Não regenere manualmente as chaves da conta de armazenamento**. É recomendável que você as regenere por meio do Key Vault.

O exemplo a seguir mostra como permitir que o Key Vault gerencie as chaves da conta de armazenamento.

## <a name="authorize-key-vault-to-access-to-your-storage-account"></a>Conceder acesso ao Key Vault para a conta de armazenamento

> [!IMPORTANT]
> O Azure Active Directory fornece cada aplicativo registrado com uma **[entidade de serviço](/azure/active-directory/develop/developer-glossary#service-principal-object)**, que serve como a identidade do aplicativo. A ID do aplicativo da entidade de serviço é usada ao dar autorização para acessar outros recursos do Azure, por meio do controle de acesso baseado na função (RBAC). Como o Key Vault é um aplicativo da Microsoft, ele é registrado previamente em todos os locatários do Azure AD com a mesma ID de aplicativo dentro de cada nuvem do Azure:
> - Locatários do Microsoft Azure Active Directory na nuvem de governo do Azure usam a ID do Aplicativo `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - Os locatários do Microsoft Azure Active Directory na nuvem pública do Azure e todos os outros usam a ID do Aplicativo`cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.

Antes que o Key Vault possa acessar e gerenciar suas chaves de conta de armazenamento, você deve autorizar o acesso a sua conta de armazenamento. O aplicativo do Key Vault requer permissões para *lista* e *regenerar* chaves para sua conta de armazenamento. Essas permissões são habilitadas por meio da função RBAC interna [Função do Serviço do Operador de Chaves da Conta de Armazenamento](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role). 

Atribua essa função para a entidade de serviço do Key Vault, limitando o escopo para sua conta de armazenamento usando as etapas a seguir. Certifique-se de atualizar o `$resourceGroupName`, `$storageAccountName`, `$storageAccountKey`, e as variáveis `$keyVaultName` antes de executar o script:

```azurepowershell-interactive
# TODO: Update with the resource group where your storage account resides, your storage account name, the name of your active storage account key, and your Key Vault instance name
$resourceGroupName = "rgContoso"
$storageAccountName = "sacontoso"
$storageAccountKey = "key1"
$keyVaultName = "kvContoso"
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093" # See "IMPORTANT" block above for information on Key Vault Application IDs

# Authenticate your PowerShell session with Azure AD, for use with Azure Resource Manager cmdlets
$azureProfile = Connect-AzureRmAccount

# Get a reference to your Azure storage account
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzureRmRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Após a atribuição de função bem-sucedida, você verá uma saída semelhante ao exemplo a seguir:

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : storage account Key Operator Service Role
RoleDefinitionId   : 81a9662b-bebf-436f-a333-f67b29880f12
ObjectId           : 93c27d83-f79b-4cb2-8dd4-4aa716542e74
ObjectType         : ServicePrincipal
CanDelegate        : False
```

Se o Key Vault já foi adicionado à função na sua conta de armazenamento, você receberá a mensagem *"A atribuição de função já existe".* erro. Você também pode verificar a atribuição de função usando a página de "Controle de acesso (IAM)" da conta de armazenamento no portal do Azure.  

## <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Dê a sua permissão de conta de usuário para as contas de armazenamento gerenciadas

>[!TIP] 
> Assim como o Microsoft Azure Active Directory fornece uma **entidade de serviço** para a identidade do aplicativo, um **UPN** é fornecido para a identidade do usuário. A entidade de usuário, em seguida, pode receber autorização para acessar o Key Vault, por meio de permissões de política de acesso do Key Vault.

Usar a mesma sessão do PowerShell, atualize a política de acesso do Key Vault para contas de armazenamento gerenciado. Essa etapa se aplica a permissões de conta de armazenamento à sua conta de usuário, garantindo que você pode acessar os recursos da conta de armazenamento gerenciados: 

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $azureProfile.Context.Account.Id -PermissionsToStorage get, list, listsas, delete, set, update, regeneratekey, recover, backup, restore, purge
```

Observe que as permissões para contas de armazenamento não estão disponíveis na página de "Políticas de acesso" da conta de armazenamento no portal do Azure.

## <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Adicione uma conta de armazenamento gerenciada para a sua instância do Key Vault

Usando a mesma sessão do PowerShell, crie uma conta de armazenamento gerenciada na sua instância do Key Vault. O switch `-DisableAutoRegenerateKey` especifica NÃO regenerar as chaves da conta de armazenamento.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts
Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Após a adição bem sucedida da conta de armazenamento sem regeneração de chave, você verá saída semelhante para o exemplo a seguir:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : False
Regeneration Period : 90.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

### <a name="enable-key-regeneration"></a>Habilitar regeneração de chave

Se você quiser que o Key Vault regenere suas chaves de acesso do armazenamento periodicamente, você pode definir um período de regeneração. No exemplo a seguir, vamos definir um período de regeneração de três dias. Após três dias, o Key Vault regenerará 'key1' e trocará a chave ativa de 'key2' para 'key1'.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

Após a adição bem-sucedida da conta de armazenamento com a regeneração de chave, você verá uma saída semelhante ao exemplo a seguir:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : True
Regeneration Period : 3.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

## <a name="next-steps"></a>Próximas etapas

- [Amostras da conta de armazenamento gerenciada](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
- [Referência do PowerShell do Key Vault](/powershell/module/azurerm.keyvault/)
