---
title: Criptografia do Serviço de Armazenamento do Azure usando chaves gerenciadas pelo cliente no Azure Key Vault | Microsoft Docs
description: Use o recurso de Criptografia do Serviço de Armazenamento do Microsoft Azure para criptografar o Armazenamento de Blobs do Azure e os Arquivos do Azure no lado do serviço, quando armazenar os dados, e descriptografe-os quando recuperar os dados usando chaves gerenciadas pelo cliente.
services: storage
author: lakasa
ms.service: storage
ms.topic: article
ms.date: 10/11/2018
ms.author: lakasa
ms.component: common
ms.openlocfilehash: e2497233ec97ffc88bf13797f62d601d4da373a1
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53628486"
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Criptografia do Serviço de Armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault

O Microsoft Azure está empenhado em ajudá-lo a proteger seus dados para atender aos compromissos de conformidade e segurança de sua organização. Uma maneira pela qual a plataforma de armazenamento do Azure protege seus dados é por meio do SSE (Storage Service Encryption, criptografia de serviço de armazenamento), que criptografa seus dados ao gravá-los em armazenamento e os descriptografa ao recuperá-los. A criptografia e descriptografia são automáticas, transparentes e usam a [criptografia AES](https://wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, uma das codificações de bloco mais fortes disponíveis.

Use chaves de criptografia gerenciadas pela Microsoft com a SSE ou suas próprias chaves de criptografia. Este artigo descreve como usar suas próprias chaves de criptografia. Para obter mais informações sobre o uso de chaves gerenciadas pela Microsoft ou sobre o SSE em geral, consulte [Storage Service Encryption para dados em repouso](storage-service-encryption.md).

O SSE para armazenamento de Blobs do Azure e o Azure Files está integrado ao Azure Key Vault, para que você possa usar um cofre de chaves para gerenciar suas chaves de criptografia. Você pode criar suas próprias chaves de criptografia e armazená-las em um cofre de chaves ou pode usar as APIs do Azure Key Vault para gerar chaves de criptografia. Com o Azure Key Vault, você gerencia e controla suas chaves além de realizar auditoria do seu uso.

> [!Note]  
> Criptografia do Serviço de Armazenamento usando chaves gerenciadas pelo cliente não está disponível para [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md). O [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md), usa o [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) padrão do setor no Windows e [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) no Linux para fornecer solução de criptografia integrada com o KeyVault.

Por que criar suas próprias chaves? Chaves personalizadas oferecem mais flexibilidade para que você possa criar, alternar, desabilitar e definir controles de acesso. As chaves personalizadas também permitem que você realize auditoria das chaves de criptografia usadas para proteger seus dados.

## <a name="get-started-with-customer-managed-keys"></a>Começar a usar chaves gerenciadas pelo cliente

Para usar chaves gerenciadas pelo cliente com SSE, crie um novo cofre de chaves e uma nova chave ou use um cofre de chaves e uma chave existentes. A conta de armazenamento e o cofre de chaves devem estar na mesma região, mas podem estar em assinaturas diferentes.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="step-1-create-a-storage-account"></a>Etapa 1: Criar uma conta de armazenamento

Se você não tiver uma, crie uma conta de armazenamento. Para obter mais informações, consulte [Criar uma conta de armazenamento](storage-quickstart-create-account.md).

### <a name="step-2-enable-sse-for-blob-and-file-storage"></a>Etapa 2: habilitar a SSE para armazenamento de blobs e arquivos

Para habilitar o SSE usando chaves gerenciadas pelo cliente, dois recursos principais de proteção, Exclusão Suave e Não Eliminar, também devem estar ativados no Cofre de Chaves do Azure. Essas configurações garantem que as chaves não serão excluídas de propósito ou por acidente. O período máximo de retenção das chaves é definido em 90 dias, protegendo os usuários de agentes mal-intencionados ou de ataques de ransomware.

Se desejar habilitar programaticamente as chaves gerenciadas pelo cliente por SSE, use a [API REST do Provedor de Recursos de Armazenamento do Azure](https://docs.microsoft.com/rest/api/storagerp), a [Biblioteca de Cliente do Provedor de Recursos de Armazenamento do .NET](https://docs.microsoft.com/dotnet/api), o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) ou a [CLI do Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli).

Para usar chaves gerenciadas pelo cliente com SSE, atribua uma identidade de conta de armazenamento à conta de armazenamento. É possível definir a identidade executando o seguinte comando do PowerShell ou da CLI do Azure:

```powershell
Set-AzStorageAccount -ResourceGroupName \$resourceGroup -Name \$accountName -AssignIdentity
```

```azurecli-interactive
az storage account \
    --account-name <account_name> \
    --resource-group <resource_group> \
    --assign-identity
```

Habilite a Exclusão reversível e Não limpar executando os seguintes comandos do PowerShell ou da CLI do Azure:

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enableSoftDelete -Value 'True'

Set-AzResource -resourceid $resource.ResourceId -Properties
$resource.Properties

($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enablePurgeProtection -Value 'True'

Set-AzResource -resourceid $resource.ResourceId -Properties
$resource.Properties
```

```azurecli-interactive
az resource update \
    --id $(az keyvault show --name <vault_name> -o tsv | awk '{print $1}') \
    --set properties.enableSoftDelete=true

az resource update \
    --id $(az keyvault show --name <vault_name> -o tsv | awk '{print $1}') \
    --set properties.enablePurgeProtection=true
```

### <a name="step-3-enable-encryption-with-customer-managed-keys"></a>Etapa 3: habilitar a criptografia com chaves gerenciadas pelo cliente

Por padrão, a SSE usa as chaves gerenciadas pela Microsoft. Habilite a SSE com chaves gerenciadas pelo cliente para a conta de armazenamento usando o [Portal do Azure](https://portal.azure.com/). Na folha **Configurações** da conta de armazenamento, clique em **Criptografia**. Selecione a opção **Usar sua própria chave**, conforme a imagem a seguir.

![Captura de tela do Portal mostrando a opção Criptografia](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

### <a name="step-4-select-your-key"></a>Etapa 4: selecionar sua chave

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
$storageAccount = Get-AzStorageAccount -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount"
$keyVault = Get-AzKeyVault -VaultName "mykeyvault"
$key = Get-AzureKeyVaultKey -VaultName $keyVault.VaultName -Name "keytoencrypt"
Set-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName -ObjectId $storageAccount.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName -AccountName $storageAccount.StorageAccountName -KeyvaultEncryption -KeyName $key.Name -KeyVersion $key.Version -KeyVaultUri $keyVault.VaultUri
```

### <a name="step-5-copy-data-to-storage-account"></a>Etapa 5: copiar dados para a conta de armazenamento

Para transferir dados para sua nova conta de armazenamento para que ela seja criptografada. Para saber mais, consulte [Perguntas Frequentes sobre a Criptografia do Serviço de Armazenamento](storage-service-encryption.md#faq-for-storage-service-encryption).

### <a name="step-6-query-the-status-of-the-encrypted-data"></a>Etapa 6: consultar o status dos dados criptografados

Consultar o status dos dados criptografados.

## <a name="faq-for-sse-with-customer-managed-keys"></a>Perguntas frequentes sobre a SSE com chaves gerenciadas pelo cliente

**estou usando o armazenamento premium; posso usar chaves gerenciadas pelo cliente com o SSE?**  
Sim, o SSE com chaves gerenciadas pelo cliente e gerenciadas pela Microsoft é suportado no armazenamento Standard e no armazenamento Premium.

**Posso criar novas contas de armazenamento com SSE com chaves gerenciadas pelo cliente ativadas usando o Azure PowerShell e o Azure CLI?**  
Sim.

**Quanto custa o Armazenamento do Azure se eu usar chaves gerenciadas pelo cliente com o SSE?**  
Há um custo associado ao uso do Cofre de Chaves do Azure. Obtenha mais detalhes em [Preços do Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). Não há custos adicionais para a SSE, que é habilitada para todas as contas de armazenamento.

**A criptografia de serviço de armazenamento está disponível em discos gerenciados do Azure?**  
A Criptografia do Serviço de Armazenamento está disponível para o Azure Managed Disks com chaves gerenciadas pela Microsoft, mas não com cliente chaves gerenciados. Em vez de Discos Gerenciados com suporte para o SSE com chaves gerenciadas pelo cliente, é recomendável [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md), que usa o padrão do setor [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) no Windows e [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt)no Linux para fornecer uma criptografia integrada com o KeyVault.

**Qual é a diferença entre a Criptografia de Serviço de Armazenamento e a Criptografia de Disco do Azure?**  
A Criptografia de Disco do Azure fornece integração entre soluções baseadas em SO, como BitLocker e DM-Crypt, e o Azure KeyVault. A Criptografia de Serviço de Armazenamento fornece criptografia nativamente na camada da plataforma de armazenamento do Azure, abaixo da máquina virtual.

**Pode revogar o acesso às chaves de criptografia?**
Sim, você pode revogar o acesso a qualquer momento. Há várias maneiras para revogar o acesso às chaves. Consulte [PowerShell do Azure Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/) e [CLI do Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault) para obter mais detalhes. Revogar o acesso bloqueará o acesso a todos os blobs na conta de armazenamento, pois a chave de criptografia da conta não é acessível pelo Armazenamento do Azure.

**Posso criar uma conta de armazenamento e digitar uma região diferente?**  
Não, a conta de armazenamento e o Cofre e a Chave de Chave do Azure precisam estar na mesma região.

**Posso ativar as chaves gerenciadas pelo cliente para o SSE ao criar a conta de armazenamento?**  
 Não. Quando você cria a conta de armazenamento, somente as chaves gerenciadas pela Microsoft estão disponíveis para SSE. Para usar chaves gerenciadas pelo cliente, precisará atualizar as propriedades da conta de armazenamento. Use a REST ou uma das bibliotecas de clientes do armazenamento para atualizar de forma programática a conta de armazenamento ou atualizar as propriedades da conta de armazenamento usando o Portal do Azure após a criação da conta.

**Posso desativar a criptografia ao usar chaves gerenciadas pelo cliente com o SSE?**  
Não, você não pode desabilitar a criptografia. A criptografia é habilitada por padrão para armazenamento de Blobs do Azure, Azure Files, Azure Queue e Azure Table. Como alternativa, você pode alternar o uso das chaves gerenciadas pela Microsoft pelo das chaves gerenciadas pelo cliente e vice-versa.

**A SSE é habilitada quando crio uma nova conta de armazenamento?**  
O SSE está habilitado para todas as contas de armazenamento e para o armazenamento de Blobs do Azure, Azure Files, Azure Queue storage e Azure Table storage.

**Não consigo ativar o SSE usando chaves gerenciadas pelo cliente na minha conta de armazenamento.**  
É uma conta de armazenamento do Azure Resource Manager? As contas de armazenamento clássicas não são compatíveis com as chaves gerenciadas pelo cliente. Só é possível habilitar SSE com chaves gerenciadas pelo cliente em contas de armazenamento do Gerenciador de Recursos.

**O que é exclusão reversível e não limpar? É necessário habilitar essa configuração para usar a SSE com chaves gerenciadas pelo cliente?**  
Soft Delete e Do Not Purge devem estar habilitados para usar o SSE com chaves gerenciadas pelo cliente. Essas configurações garantem que a chave não será excluída de propósito ou por acidente. O período máximo de retenção das chaves é definido em 90 dias, protegendo os usuários de agentes mal-intencionados e de ataques de ransomware. Essa configuração não pode ser desabilitada.

**O SSE com chaves gerenciadas pelo cliente é permitido somente em regiões específicas?**  
O SSE com chaves gerenciadas pelo cliente está disponível em todas as regiões para armazenamento de Blobs do Azure e arquivos do Azure.

**Como posso entrar em contato com alguém se tiver algum problema ou quiser fornecer feedback?**  
Entre em contato com [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) para os problemas relacionados à criptografia do serviço de armazenamento.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o conjunto abrangente de funcionalidades de segurança que ajuda os desenvolvedores a criar aplicativos seguros, consulte o [Guia de segurança de armazenamento](storage-security-guide.md).
- Para obter informações gerais sobre o Azure Key Vault, consulte [O que é o Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)
- Para começar a usar o Azure Key Vault, consulte [Introdução ao Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).
