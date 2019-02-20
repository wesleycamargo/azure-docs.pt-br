---
ms.assetid: ''
title: O Azure Key Vault gerenciados a conta de armazenamento – CLI
description: As chaves da conta de armazenamento fornecem uma integração contínua entre o Azure Key Vault e o acesso baseado em chave para a Conta de Armazenamento do Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: prashanthyv
ms.author: pryerram
manager: barbkess
ms.date: 10/03/2018
ms.openlocfilehash: 9b1a4e23ed0da0637b44ac52dd4d1baeb22cd6ce
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118047"
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
Conceitualmente, a lista de etapas seguidas são
- Primeiro, obtenha uma conta de armazenamento (preexistente)
- Em seguida, busque um cofre de chaves (preexistente)
- Depois, adicione uma conta de armazenamento gerenciada pelo KeyVault ao cofre, definindo Key1 como chave ativa e com um período de regeneração de 180 dias
- Por fim, defina um contexto de armazenamento para a conta de armazenamento especificada, com Key1

Nas instruções abaixo, estamos atribuindo Key Vault como um serviço para ter permissões de operador em sua conta de armazenamento

> [!NOTE]
> Observe que, depois de configurar as chaves da conta de armazenamento gerenciado do Cofre do Teclado do Azure, elas devem **NÃO** ser alteradas por mais tempo, exceto via Cofre da Chave. As chaves da conta de armazenamento gerenciado significam que o Key Vault gerenciaria a rotação da chave da conta de armazenamento

1. Depois de criar uma conta de armazenamento, execute o seguinte comando para obter a ID de recurso da conta de armazenamento que você deseja gerenciar

    ```
    az storage account show -n storageaccountname 
    ```
    Copie o campo da ID do resultado do comando acima
    
2. Obtenha a ID do objeto da entidade de serviço do Azure Key Vault executando o comando a seguir

    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
    
    Após a conclusão bem-sucedida desse comando, localize a ID de objeto no resultado:
    ```console
        {
            ...
            "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
            ...
        }
    ```
    
3. Atribua a função de operador da chave de armazenamento à identidade do Azure Key Vault.

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ObjectIdOfKeyVault> --scope <IdOfStorageAccount>
    ```
    
4. Criar uma Conta de Armazenamento Gerenciado do cofre de chaves.     <br /><br />
   Abaixo, estamos definindo um período de regeneração de 90 dias. Após 90 dias, o Cofre de chaves será regenerará 'key1' e trocará a chave ativa de 'key2' para 'key1'. Agora ele marcará a Key1 como a chave ativa. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```
    No caso do usuário não tiver criado a conta de armazenamento e não tem permissões para a conta de armazenamento, as etapas a seguir defina as permissões para sua conta garantir que você pode gerenciar todas as permissões de armazenamento no cofre de chaves.
    

<a name="step-by-step-instructions-on-how-to-use-key-vault-to-create-and-generate-sas-tokens"></a>Instruções passo a passo sobre como usar o Key Vault para criar e gerenciar tokens SAS
--------------------------------------------------------------------------------
Também é possível solicitar que o Key Vault gere tokens SAS (Assinatura de Acesso Compartilhado). Uma assinatura de acesso compartilhado fornece acesso delegado aos recursos da sua conta de armazenamento. Com uma SAS, você pode conceder aos clientes acesso aos recursos em sua conta de armazenamento, sem compartilhar as chaves de conta. Este é o ponto principal do uso de assinaturas de acesso compartilhado em seus aplicativos: uma SAS é uma maneira segura de compartilhar seus recursos de armazenamento sem comprometer as chaves da conta.

Após concluir as etapas listadas acima, execute os seguintes comandos para solicitar ao Key Vault a geração de tokens SAS. 

A lista de itens que seriam obtidos nas etapas a seguir são
- Configura uma definição de SAS de conta chamada '<YourSASDefinitionName>' em uma conta de armazenamento gerenciada pelo KeyVault '<YourStorageAccountName>' no seu cofre '<VaultName>'. 
- Cria um token SAS de conta para serviços de Blob, Arquivo, Tabela e Fila, para tipos de recursos de Serviço, Contêiner e Objeto, com todas as permissões, via https e com as datas de início e término especificadas
- Configura uma define de SAS de armazenamento gerenciado pelo KeyVault no cofre, com o URI do modelo como o token SAS criado acima, de tipo SAS "conta" e válido por N dias
- Recupera o token de acesso real do segredo do KeyVault correspondente à definição de SAS

1. Nesta etapa, criaremos uma definição de SAS. Após a criação dessa definição de SAS, será possível solicitar ao Key Vault a geração de mais tokens SAS. Esta operação requer a permissão de armazenamento/setsas.

```
$sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
```
Você pode ver mais ajuda sobre a operação anterior [aqui](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas)

Quando essa operação for executada com êxito, você verá uma saída semelhante à mostrada abaixo. Copie

```console
   "se=2020-01-01&sp=***"
```

2. Nesta etapa, usaremos a saída ($sasToken) gerada acima para criar uma definição de SAS. Para obter mais documentação, leia [aqui](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters)   

```
az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
```
                        

 > [!NOTE] 
 > No caso em que o usuário não tem permissões à conta de armazenamento, obtemos primeiro a Object-Id do usuário

    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    
    ```
    
## <a name="fetch-sas-tokens-in-code"></a>Buscar tokens SAS no código

Nesta seção, discutiremos como você pode fazer operações em sua conta de armazenamento buscando [tokens SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) do Key Vault

Na seção abaixo, demonstramos como buscar tokens SAS depois que uma definição de SAS é criada, conforme mostrado acima.

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

### <a name="relevant-azure-cli-commands"></a>Comandos importantes da CLI do Azure

[Comandos de Armazenamento para CLI do Azure](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

## <a name="see-also"></a>Consulte também

- [Sobre chaves, segredos e certificados](https://docs.microsoft.com/rest/api/keyvault/)
- [Blog da equipe do Key Vault](https://blogs.technet.microsoft.com/kv/)
