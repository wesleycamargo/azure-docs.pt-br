---
ms.assetid: ''
title: Chaves de conta de Armazenamento do Azure Key Vault
description: Chaves da conta de armazenamento fornecem uma integração contínua entre o Azure Key Vault e o acesso baseado em chave para a Conta de Armazenamento do Azure.
ms.topic: article
services: key-vault
ms.service: key-vault
author: lleonard-msft
ms.author: alleonar
manager: mbaldwin
ms.date: 10/12/2017
ms.openlocfilehash: 4f42a47a6d934bf0538efccbcf7f057fd28e2c03
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32179581"
---
# <a name="azure-key-vault-storage-account-keys"></a>Chaves de conta de Armazenamento do Azure Key Vault

Antes das Chaves de Conta de Armazenamento do Azure Key Vault, os desenvolvedores precisavam gerenciar suas próprias chaves de ASA (Conta de Armazenamento do Azure) e girá-las manualmente ou por meio de um automação externa. Agora, as Chave de Conta de Armazenamento do Key Vault são implementadas como [Segredos do Key Vault](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets) para autenticação com uma conta de Armazenamento do Azure.

O recurso de chave ASA (Conta de Armazenamento do Azure) gerencia rotação do segredo para você. Elimina a necessidade de contato direto com uma chave ASA ao oferecer SAS (Assinaturas de Acesso Compartilhado) como um método.

Para obter mais informações gerais sobre Contas de Armazenamento do Azure, consulte [Sobre contas de armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

## <a name="supporting-interfaces"></a>Interfaces de suporte

Você encontrará uma lista completa e links para nossas interfaces de programação e script no [Guia do Desenvolvedor do Key Vault](key-vault-developers-guide.md#coding-with-key-vault).


## <a name="what-key-vault-manages"></a>O que o Key Vault gerencia

O Key Vault executa várias funções de gerenciamento interno em seu nome quando você usa Chaves Gerenciadas de Conta de Armazenamento.

- O Azure Key Vault gerencia chaves de uma ASA (Conta de Armazenamento do Azure).
    - Internamente, o Azure Key Vault pode listar (sincronizar) chaves com uma Conta de Armazenamento do Azure.
    - O Azure Key Vault gera novamente (gira) as chaves periodicamente.
    - Os valores de chave nunca são retornados em resposta ao chamador.
    - O Azure Key Vault gerencia chaves tanto de Contas de Armazenamento quanto de Contas de Armazenamento Clássicas.
- O Azure Key Vault permite que você, o proprietário do cofre/objeto, crie definições de SAS (SAS de conta ou serviço).
    - O valor SAS, criado usando a definição de SAS, é retornado como um segredo por meio do caminho do URI REST. Para obter mais informações, consulte [Operações de conta de armazenamento do Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations).

## <a name="naming-guidance"></a>Diretrizes de nomenclatura

- Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas.
- Um nome de definição de SAS deve ter de 1 a 102 caracteres, contendo somente 0–9, a–z, A–Z.

## <a name="developer-experience"></a>Experiência do desenvolvedor

### <a name="before-azure-key-vault-storage-keys"></a>Antes das chaves de armazenamento do Azure Key Vault

Os desenvolvedores costumavam precisar fazer o seguinte com uma chave de conta de armazenamento para obterem acesso ao armazenamento do Azure.
1. Armazenar a cadeia de conexão ou token SAS nas configurações de aplicação do Azure AppService ou outro armazenamento.
1. Na inicialização do aplicativo, busque a cadeia de conexão ou o token SAS.
1. Criar [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) para interagir com o armazenamento.

```cs
// The Connection string is being fetched from App Service application settings
var connectionStringOrSasToken = CloudConfigurationManager.GetSetting("StorageConnectionString");
var storageAccount = CloudStorageAccount.Parse(connectionStringOrSasToken);
var blobClient = storageAccount.CreateCloudBlobClient();
 ```

### <a name="after-azure-key-vault-storage-keys"></a>Depois das chaves de armazenamento do Azure Key Vault

Os desenvolvedores criam um [KeyVaultClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault.keyvaultclient) e aproveitam para obter o token SAS para seu armazenamento. Depois disso, eles criam [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) com esse token.

```cs
// Create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();

// Use the blobClientWithSas
...

// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

 ### <a name="developer-guidance"></a>Orientação para desenvolvedores

- Permita somente que o Key Vault gerencie suas chaves ASA. Não tentar gerenciá-las você mesmo, você interferirá nos processos do Key Vault.
- Não permita que chaves ASA sejam gerenciadas por mais de um objeto do Key Vault.
- Se precisar regenerar manualmente as chaves ASA, recomendamos que você as regenere por meio do Key Vault.

## <a name="getting-started"></a>Introdução

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>Configuração para permissões de RBAC (controle de acesso baseadas em função)

A identidade do aplicativo Azure Key Vault precisa de permissões para *listar* e *regenerar* chaves para uma conta de armazenamento. Configure essas permissões usando as seguintes etapas:

```powershell
# Get the resource ID of the Azure Storage Account you want to manage.
# Below, we are fetching a storage account using Azure Resource Manager
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure Key Vault Identity
$servicePrincipal = Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093

# Assign Storage Key Operator role to Azure Key Vault Identity
New-AzureRmRoleAssignment -ObjectId $servicePrincipal.Id -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storage.Id
```

    >[!NOTE]
    > For a classic account type, set the role parameter to *"Classic Storage Account Key Operator Service Role."*

## <a name="working-example"></a>Exemplo de trabalho

O exemplo a seguir demonstra como criar uma Conta de Armazenamento do Azure gerenciada por Key Vault e as definições de SAS (Assinatura de Acesso Compartilhado).

### <a name="prerequisite"></a>Pré-requisito

Certifique-se de que você concluiu [Configuração para permissões de RBAC (controle de acesso baseadas em função)](#setup-for-role-based-access-control-rbac-permissions).

### <a name="setup"></a>Configuração

```powershell
# This is the name of our Key Vault
$keyVaultName = "mykeyVault"

# Fetching all the storage account object, of the ASA we want to manage with KeyVault
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure KeyVault Identity service principal
$servicePrincipalId = $(Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093).Id
```

Em seguida, defina as permissões para **sua conta** para garantir que você pode gerenciar todas as permissões de armazenamento no Key Vault. No exemplo a seguir, a nossa conta do Azure é _developer@contoso.com_.

```powershell
# Searching our Azure Active Directory for our account's ObjectId
$userPrincipalId = $(Get-AzureRmADUser -SearchString "developer@contoso.com").Id

# We use the ObjectId we found to setting permissions on the vault
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $userPrincipalId -PermissionsToStorage all
```

### <a name="create-a-key-vault-managed-storage-account"></a>Criar uma Conta de Armazenamento Gerenciado do Key Vault

Agora, crie uma Conta de Armazenamento Gerenciado no Azure Key Vault e use uma chave de acesso da sua conta de armazenamento para criar os tokens SAS.
- `-ActiveKeyName` usa 'key2' para gerar os tokens SAS.
- `-AccountName` é usado para identificar a conta de armazenamento gerenciado. Abaixo, estamos usando o nome da conta de armazenamento para simplificar, mas pode ser qualquer nome.
- `-DisableAutoRegenerateKey` especifica não regenerar as chaves da conta de armazenamento.

```powershell
# Adds your storage account to be managed by Key Vault and will use the access key, key2
Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storage.StorageAccountName -AccountResourceId $storage.Id -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="key-regeneration"></a>Regeneração de chave

Se você quiser que o Key Vault regenere suas chaves de acesso do armazenamento periodicamente, você pode definir um período de regeneração. Abaixo, um período de regeneração de 3 dias está sendo configurado. Depois de 3 dias, o Key Vault regenerará 'key1' e trocará a chave ativa de 'key2' para 'key1'.

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
$accountName = $storage.StorageAccountName

Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $accountName -AccountResourceId $storage.Id -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>Configurar as definições de SAS

A conta SAS fornece acesso ao serviço de blob com permissões diferentes.
Configure as definições de SAS no Key Vault para sua conta de armazenamento gerenciado.
- `-AccountName` é o nome da conta de armazenamento gerenciado no Key Vault.
- `-Name` é o identificador para o token SAS no seu armazenamento.
- `-ValidityPeriod` define a data de expiração do token SAS gerado.

```powershell
$validityPeriod = [System.Timespan]::FromDays(1)
$readSasName = "readBlobSas"
$writeSasName = "writeBlobSas"

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName $keyVaultName -AccountName $accountName -Name $readSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName $keyVaultName -AccountName $accountName -Name $writeSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List,Write
```

### <a name="get-sas-tokens"></a>Obter tokens SAS

Obtenha os tokens SAS correspondentes e faça chamadas para o armazenamento. `-SecretName` é criada usando a entrada dos parâmetros `AccountName` e `Name` quando você executou [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition).

```powershell
$readSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$readSasName").SecretValueText
$writeSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$writeSasName").SecretValueText
```

### <a name="create-storage"></a>Criar armazenamento

Observe que quando tentamos acessar com *$readSasToken* recebemos um erro, mas conseguimos acessar com *$writeSasToken*.

```powershell
$context1 = New-AzureStorageContext -SasToken $readSasToken -StorageAccountName $storage.StorageAccountName
$context2 = New-AzureStorageContext -SasToken $writeSasToken -StorageAccountName $storage.StorageAccountName

Set-AzureStorageBlobContent -Container containertest1 -File "abc.txt" -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "file.txt" -Context $context2
```

Você pode acessar o conteúdo do blob de armazenamento com o token SAS que tem acesso de gravação.

### <a name="relevant-powershell-cmdlets"></a>Cmdlets relevantes do Powershell

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="storage-account-onboarding"></a>Integração da conta de armazenamento

Exemplo: como um proprietário de objeto do Key Vault, você adiciona um objeto de conta de armazenamento ao seu Azure Key Vault para integrar uma conta de armazenamento.

Durante a integração, o Key Vault precisa verificar se a identidade da conta de integração tem permissões para *listar* e *regenerar* chaves de armazenamento. Para verificar essas permissões, o Key Vault obtém um token OBO (em nome de) do serviço de autenticação com público definido para o Azure Resource Manager e faz uma chamada de chave de *lista* para o serviço de armazenamento do Azure. Se a chamada de *lista* falhar, a criação do objeto Key Vault falhará com o código de status HTTP *Forbidden*. As chaves listadas dessa maneira são armazenadas em cache com o seu armazenamento de entidade do cofre de chaves.

O Key Vault deve verificar se a identidade tem permissões para *regenerar* antes de poder assumir a propriedade da regeneração das suas chaves. Para verificar se a identidade, via token OBO, bem como a identidade de primeira parte do Key Vault têm essas permissões:

- O Key Vault lista permissões de RBAC no recurso de conta de armazenamento.
- O Key Vault valida a resposta por meio correspondência de expressão regular de ações e não ações.

Encontre alguns exemplos de suporte em [Key Vault – Amostras de Chaves de Conta de Armazenamento Gerenciado](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/KeyVault/dataPlane/Microsoft.Azure.KeyVault.Samples/samples/HelloKeyVault/Program.cs#L167).

Se a identidade não tiver a permissão *regenerar* ou se a identidade da primeira parte do Key Vault não tiver a permissão *lista* ou *regenerar*, a solicitação de integração falhará, retornando um código de erro apropriado e uma mensagem.

O token OBO só funcionará quando você usar os aplicativos cliente nativos de primeira parte do PowerShell ou da CLI.

## <a name="other-applications"></a>Outros aplicativos

- Tokens SAS, construídos usando chaves de conta de armazenamento do Key Vault, fornecem acesso ainda mais controlado a uma conta de armazenamento do Azure. Para obter mais informações, confira [Como usar assinaturas de acesso compartilhado](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1).

## <a name="see-also"></a>Consulte também

- [Sobre chaves, segredos e certificados](https://docs.microsoft.com/rest/api/keyvault/)
- [Blog da equipe do Key Vault](https://blogs.technet.microsoft.com/kv/)
