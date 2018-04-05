---
title: Criptografia do Serviço de Armazenamento do Microsoft Azure usando chaves gerenciadas pelo cliente no Azure Key Vault | Microsoft Docs
description: Use o recurso de Criptografia do Serviço de Armazenamento do Microsoft Azure para criptografar o Armazenamento de Blobs do Azure no lado do serviço ao armazenar os dados e descriptografá-lo ao recuperar os dados usando chaves gerenciadas pelo cliente.
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/07/2018
ms.author: lakasa
ms.openlocfilehash: 04688f943ac9eba27ca193aa2054c69b6a94547d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Criptografia do Serviço de Armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault

O Microsoft Azure está empenhado em ajudá-lo a proteger seus dados para atender aos compromissos de conformidade e segurança de sua organização. Uma das maneiras usadas pelo Armazenamento do Microsoft Azure para proteger seus dados é com a Criptografia do Serviço de Armazenamento (SSE), que criptografa os dados ao gravá-los no armazenamento e os descriptografa ao recuperá-los. A criptografia e descriptografia são automáticas, transparentes e usam a [criptografia AES](https://wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, uma das codificações de bloco mais fortes disponíveis.

Use chaves de criptografia gerenciadas pela Microsoft com a SSE ou suas próprias chaves de criptografia. Este artigo descreve como usar suas próprias chaves de criptografia. Para obter mais informações sobre como usar chaves gerenciadas pela Microsoft ou sobre a SSE em geral, consulte [Criptografia do Serviço de Armazenamento para dados em repouso](storage-service-encryption.md).

A SSE para armazenamento de Blobs e arquivos vem integrada ao Azure Key Vault para que você possa usar um cofre de chaves e gerenciar suas chaves de criptografia. Você pode criar suas próprias chaves de criptografia e armazená-las em um cofre de chaves ou usar as APIs do Azure Key Vault para gerar chaves de criptografia. Com o Azure Key Vault, você gerencia e controla suas chaves além de realizar auditoria do seu uso.

Por que criar suas próprias chaves? Chaves personalizadas oferecem mais flexibilidade para que você possa criar, alternar, desabilitar e definir controles de acesso. As chaves personalizadas também permitem que você realize auditoria das chaves de criptografia usadas para proteger seus dados.

## <a name="get-started-with-customer-managed-keys"></a>Começar a usar chaves gerenciadas pelo cliente

Para usar chaves gerenciadas pelo cliente com SSE, crie um novo cofre de chaves e uma nova chave ou use um cofre de chaves e uma chave existentes. A conta de armazenamento e o cofre de chaves devem estar na mesma região, mas podem estar em assinaturas diferentes. 

### <a name="step-1-create-a-storage-account"></a>Etapa 1: Criar uma conta de armazenamento

Se você não tiver uma, crie uma conta de armazenamento. Para saber mais sobre como [Criar uma nova conta de armazenamento](storage-quickstart-create-account.md).

### <a name="step-2-enable-sse-for-blob-and-file-storage"></a>Etapa 2: Habilitar a SSE para armazenamento de Blobs e arquivos

Para habilitar a SSE usando chaves gerenciadas pelo cliente é obrigatório ativar também os recursos de Proteção de dupla autenticação, Exclusão reversível e Não limpar. Essas configurações garantem que as chaves não serão excluídas de propósito ou por acidente. O período máximo de retenção das chaves é definido em 90 dias, protegendo os usuários de agentes mal-intencionados ou de ataques de ransomware.

Se desejar habilitar programaticamente as chaves gerenciadas pelo cliente por SSE, use a [API REST do Provedor de Recursos de Armazenamento do Microsoft Azure](https://docs.microsoft.com/rest/api/storagerp), a [Biblioteca de Cliente do Provedor de Recursos de Armazenamento do .NET](https://docs.microsoft.com/dotnet/api), o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) ou a [CLI do Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli).

Para usar chaves gerenciadas pelo cliente com SSE, atribua uma identidade de conta de armazenamento à conta de armazenamento. É possível definir a identidade executando o seguinte comando do PowerShell:

```powershell
Set-AzureRmStorageAccount -ResourceGroupName \$resourceGroup -Name \$accountName -AssignIdentity
```

Habilite a Exclusão reversível e Não limpar executando os seguintes comandos do PowerShell:

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enableSoftDelete -Value 'True'

Set-AzureRmResource -resourceid $resource.ResourceId -Properties
$resource.Properties

($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enablePurgeProtection -Value 'True'

Set-AzureRmResource -resourceid $resource.ResourceId -Properties
$resource.Properties
```

### <a name="step-3-enable-encryption-with-customer-managed-keys"></a>Etapa 3: Habilitar a criptografia com chaves gerenciadas pelo cliente

Por padrão, a SSE usa as chaves gerenciadas pela Microsoft. Habilite a SSE com chaves gerenciadas pelo cliente para a conta de armazenamento usando o [Portal do Azure](https://portal.azure.com/). Na folha **Configurações** da conta de armazenamento, clique em **Criptografia**. Selecione a opção **Usar sua própria chave**, conforme a imagem a seguir.

![Captura de tela do Portal mostrando a opção Criptografia](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

### <a name="step-4-select-your-key"></a>Etapa 4: Selecionar a chave

Você pode especificar a chave como um URI ou selecionando-a de um cofre de chaves.

#### <a name="specify-a-key-as-a-uri"></a>Especificar uma chave como URI

Para especificar a chave de um URI, siga estas etapas:

1. Escolha a opção **Digitar a chave URI**.  
2. No campo **Chave URI**, especifique o URI.

    ![Captura de tela do portal mostrando a opção Inserir URI da chave da Criptografia](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)


#### <a name="specify-a-key-from-a-key-vault"></a>Especificar uma chave de um cofre de chaves 

Para especificar a chave de um cofre de chaves, siga estas etapas:

1. Escolha a opção **Selecionar do Cofre de chaves**.  
2. Escolha o Cofre de chaves que contém a chave que deseja usar.
3. Escolha a chave do Cofre de chaves.

    ![Captura de tela do portal mostrando a opção Criptografias usam sua própria chave](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

Se a conta de armazenamento não tiver acesso ao cofre de chaves, execute o comando do Azure PowerShell mostrado na imagem a seguir para conceder acesso.

![Captura de tela do portal mostrando o acesso negado ao cofre de chaves](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

Você também pode conceder acesso por meio do Portal do Azure indo até o Azure Key Vault no Portal do Azure e concedendo acesso à conta de armazenamento.


Você pode associar a chave acima a uma conta de armazenamento existente usando os seguintes comandos do PowerShell:
```powershell
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount"
$keyVault = Get-AzureRmKeyVault -VaultName "mykeyvault"
$key = Get-AzureKeyVaultKey -VaultName $keyVault.VaultName -Name "keytoencrypt"
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVault.VaultName -ObjectId $storageAccount.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
Set-AzureRmStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName -AccountName $storageAccount.StorageAccountName -EnableEncryptionService "Blob" -KeyvaultEncryption -KeyName $key.Name -KeyVersion $key.Version -KeyVaultUri $keyVault.VaultUri
```


### <a name="step-5-copy-data-to-storage-account"></a>Etapa 5: Copiar dados para a conta de armazenamento

Para transferir dados para sua nova conta de armazenamento de forma que seja criptografado. Para saber mais, consulte [Perguntas Frequentes sobre a Criptografia do Serviço de Armazenamento](storage-service-encryption.md#faq-for-storage-service-encryption).

### <a name="step-6-query-the-status-of-the-encrypted-data"></a>Etapa 6: Consultar o status dos dados criptografados

Consultar o status dos dados criptografados.

## <a name="faq-for-sse-with-customer-managed-keys"></a>Perguntas frequentes sobre a SSE com chaves gerenciadas pelo cliente

**P: Estou usando o armazenamento Premium. Posso usar chaves gerenciadas pelo cliente com a SSE?**

R: Sim, tanto o armazenamento Standard quanto o Premium são compatíveis com a SSE com chaves gerenciadas pela Microsoft e pelo cliente.

**P: Posso criar novas contas de armazenamento com a SSE com chaves gerenciadas pelo cliente habilitadas usando o Azure PowerShell e a CLI do Azure?**

R: Sim.

**P: Qual é o custo adicional do Armazenamento do Microsoft Azure se eu usar as chaves gerenciadas pelo cliente com a SSE?**

R: Há um custo associado para o uso do Azure Key Vault. Obtenha mais detalhes em [Preços do Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). Não há custos adicionais para a SSE, que é habilitada para todas as contas de armazenamento.

**P: Posso revogar o acesso às chaves de criptografia?**

R: Sim, você pode revogar o acesso a qualquer momento. Há várias maneiras para revogar o acesso às chaves. Consulte [PowerShell do Azure Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) e [CLI do Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault) para obter mais detalhes. Revogar o acesso bloqueará o acesso a todos os blobs na conta de armazenamento, pois a chave de criptografia da conta não é acessível pelo Armazenamento do Microsoft Azure.

**P: Posso criar uma conta de armazenamento e uma chave em uma região diferente?**

R: Não, a conta de armazenamento assim como a chave e o Azure Key Vault precisam estar na mesma região.

**P: Posso habilitar as chaves gerenciadas pelo cliente com a SSE durante a criação da conta de armazenamento?**

R: não. Quando você cria a conta de armazenamento, somente as chaves gerenciadas pela Microsoft estão disponíveis para SSE. Para usar chaves gerenciadas pelo cliente, precisará atualizar as propriedades da conta de armazenamento. Use a REST ou uma das bibliotecas de clientes do armazenamento para atualizar de forma programática a conta de armazenamento ou atualizar as propriedades da conta de armazenamento usando o Portal do Azure após a criação da conta.

**P: Posso desabilitar a criptografia durante o uso da chaves gerenciadas pelo cliente com SSE?**

R: Não, não é possível desabilitar a criptografia. Por padrão, a criptografia é habilitada para todos os serviços, armazenamento de Blobs, de arquivos, tabelas e filas. Como alternativa, você pode alternar o uso das chaves gerenciadas pela Microsoft pelo das chaves gerenciadas pelo cliente e vice-versa.

**P: O SSE é habilitado por padrão quando eu crio uma nova conta de armazenamento?**

R: A SSE é habilitada por padrão para todas as contas de armazenamento e para todos os serviços de armazenamento de Blobs, de arquivos, tabelas e filas.

**P: Não consigo habilitar a SSE usando chaves gerenciadas pelo cliente na minha conta de armazenamento.**

R: É uma conta de armazenamento do Azure Resource Manager? As contas de armazenamento clássicas não são compatíveis com as chaves gerenciadas pelo cliente. Só é possível habilitar SSE com chaves gerenciadas pelo cliente em contas de armazenamento do Gerenciador de Recursos.

**P: O que é Exclusão reversível e Não limpar? É necessário habilitar essa configuração para usar a SSE com chaves gerenciadas pelo cliente?**

R: É necessário habilitar a Exclusão reversível e Não limpar para usar a SSE com chaves gerenciadas pelo cliente. Essas configurações garantem que a chave não será excluída de propósito ou por acidente. O período máximo de retenção das chaves é definido em 90 dias, protegendo os usuários de agentes mal-intencionados e de ataques de ransomware. Essa configuração não pode ser desabilitada.

**P: A SSE com chaves gerenciadas pelo cliente só é permitida em regiões específicas?**

R: A SSE com chaves gerenciadas pelo cliente está disponível em todas as regiões para armazenamento de Blobs e de arquivos.

**P: Como devo contatar alguém se eu tiver problemas ou quiser enviar comentários?**

R: Entre em contato com [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) para falar sobre problemas relacionados à Criptografia do Serviço de Armazenamento.

## <a name="next-steps"></a>Próximas etapas

-   Para saber mais sobre o conjunto abrangente de funcionalidades de segurança que ajuda os desenvolvedores a criar aplicativos seguros, consulte o [Guia de segurança de armazenamento](storage-security-guide.md).

-   Para obter informações gerais sobre o Azure Key Vault, consulte [O que é o Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)

-   Para começar a usar o Azure Key Vault, consulte [Introdução ao Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).
