---
title: Bring Your Own Key do Apache Kafka no Azure HDInsight (versão prévia)
description: Este artigo descreve como usar sua própria chave do Azure Key Vault para criptografar dados armazenados no Apache Kafka no Azure HDInsight.
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: ce9df58e9640cab2e6ba50fce772f1e30739dc5a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64714845"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight"></a>Traga sua própria chave do Apache Kafka no HDInsight do Azure

O Azure HDInsight inclui suporte ao BYOK (Bring Your Own Key) para Apache Kafka. Essa funcionalidade permite que você tenha e gerencie as chaves usadas para criptografar dados em repouso. 

Todos os discos gerenciados no HDInsight são protegidos com o SSE (Criptografia do Serviço de Armazenamento) do Azure. Por padrão, os dados nesses discos são criptografados usando chaves gerenciadas pela Microsoft. Se você habilitar o BYOK, poderá fornecer a chave de criptografia para o HDInsight usar e gerenciá-la usando o Azure Key Vault. 

A criptografia de BYOK é um processo de uma etapa realizado durante a criação do cluster sem nenhum custo adicional. Tudo o que você precisa fazer é registrar o HDInsight como uma identidade gerenciada com o Azure Key Vault e adicionar a chave de criptografia ao criar o cluster.

Todas as mensagens para o cluster do Kafka (incluindo réplicas mantidas pelo Kafka) são criptografadas com uma DEK (Chave de Criptografia de Dados). A DEK é protegida usando a KEK (Chave de Criptografia de Chave) do seu cofre de chaves. Os processos de criptografia e descriptografia são realizados inteiramente pelo Azure HDInsight. 

Você pode usar o portal do Azure ou a CLI do Azure para gorar as chaves com segurança no cofre de chaves. Quando uma chave é girada, o cluster do Kafka do HDInsight começa a usar a nova chave em questão de minutos. Habilite os recursos de proteção de chave de "Exclusão reversível" para se proteger contra exclusão acidental e cenários de ransomware. Cofres de chaves sem esse recurso de proteção não têm suporte.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-byok"></a>Introdução ao BYOK
Para criar um BYOK habilitado cluster Kafka, vamos percorrer as etapas a seguir:
1. Criar identidades de gerenciado para recursos do Azure
2. Configurar o Azure Key Vault e chaves
3. Criar o cluster HDInsight Kafka com BYOK habilitado
4. Girando a chave de criptografia

## <a name="create-managed-identities-for-azure-resources"></a>Criar identidades de gerenciado para recursos do Azure

   Para autenticar para o Cofre de chaves, criar uma identidade atribuída pelo usuário gerenciada usando o [portal do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [do Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [do Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md), ou [ CLI do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Para obter mais informações sobre o trabalho de identidades como gerenciado no Azure HDInsight, consulte [gerenciados identidades no Azure HDInsight](../hdinsight-managed-identities.md). Embora o Azure Active Directory seja necessário para as identidades gerenciadas e o BYOK para Kafka, o ESP (Enterprise Security Package) não é um requisito. Certifique-se de salvar a ID do recurso de identidade gerenciada para quando você a adicionar à política de acesso do Key Vault.

   ![Criar uma identidade gerenciada atribuída pelo usuário no portal do Azure](./media/apache-kafka-byok/user-managed-identity-portal.png)

## <a name="setup-the-key-vault-and-keys"></a>Configurar o Key Vault e chaves

   O HDInsight é compatível apenas com o Azure Key Vault. Se você tiver seu próprio cofre de chaves, poderá importar suas chaves para o Azure Key Vault. Lembre-se de que as chaves devem ter "Exclusão reversível". O recurso de "Exclusão reversível" está disponível por meio de REST, .NET /C#, interfaces do PowerShell e CLI do Azure.

   1. Para criar um novo cofre de chaves, siga o início rápido do [Azure Key Vault](../../key-vault/key-vault-overview.md). Para obter mais informações sobre como importar as chaves existentes, visite [Sobre chaves, segredos e certificados](../../key-vault/about-keys-secrets-and-certificates.md).

   2. Habilite "exclusão reversível" no cofre de chaves usando o [atualização de keyvault az](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) comando da cli.
        ' ' Atualização de keyvault az CLI do azure – nome <Key Vault Name> – enable-exclusão reversível
        ```

   3. Create keys

        a. To create a new key, select **Generate/Import** from the **Keys** menu under **Settings**.

        ![Generate a new key in Azure Key Vault](./media/apache-kafka-byok/kafka-create-new-key.png)

        b. Set **Options** to **Generate** and give the key a name.

        ![Generate a new key in Azure Key Vault](./media/apache-kafka-byok/kafka-create-a-key.png)

        c. Select the key you created from the list of keys.

        ![Azure Key Vault key list](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

        d. When you use your own key for Kafka cluster encryption, you need to provide the key URI. Copy the **Key identifier** and save it somewhere until you're ready to create your cluster.

        ![Copy key identifier](./media/apache-kafka-byok/kafka-get-key-identifier.png)
   
    4. Add managed identity to the key vault access policy.

        a. Create a new Azure Key Vault access policy.

        ![Create new Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy.png)

        b. Under **Select Principal**, choose the user-assigned managed identity you created.

        ![Set Select Principal for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

        c. Set **Key Permissions** to **Get**, **Unwrap Key**, and **Wrap Key**.

        ![Set Key Permissions for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png)

        d. Set **Secret Permissions** to **Get**, **Set**, and **Delete**.

        ![Set Key Permissions for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png)

        e. Click on **Save**. 

        ![Save Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## Create HDInsight cluster

   You're now ready to create a new HDInsight cluster. BYOK can only be applied to new clusters during cluster creation. Encryption can't be removed from BYOK clusters, and BYOK can't be added to existing clusters.

   ![Kafka disk encryption in Azure portal](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   During cluster creation, provide the full key URL, including the key version. For example, `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. You also need to assign the managed identity to the cluster and provide the key URI.

## Rotating the Encryption key
   There might be scenarios where you might want to change the encryption keys used by the Kafka cluster after it has been created. This can be easily via the portal. For this operation, the cluster must have access to both the current key and the intended new key, otherwise the rotate key operation will fail.

   To rotate the key, you must have the full url of the new key (See Step 3 of [Setup the Key Vault and Keys](#setup-the-key-vault-and-keys)). Once you have that, go to the Kafka cluster properties section in the portal and click on **Change Key** under **Disk Encryption Key URL**. Enter in the new key url and submit to rotate the key.

   ![Kafka rotate disk encryption key](./media/apache-kafka-byok/kafka-change-key.png)

## FAQ for BYOK to Apache Kafka

**How does the Kafka cluster access my key vault?**

   Associate a managed identity with the HDInsight Kafka cluster during cluster creation. This managed identity can be created before or during cluster creation. You also need to grant the managed identity access to the key vault where the key is stored.

**Is this feature available for all Kafka clusters on HDInsight?**

   BYOK encryption is only possible for Kafka 1.1 and above clusters.

**Can I have different keys for different topics/partitions?**

   No, all managed disks in the cluster are encrypted by the same key.

**What happens if the cluster loses access to the key vault or the key?**
   If the cluster loses access to the key, warnings will be shown in the Ambari portal. In this state, the **Change Key** operation will fail. Once key access is restored, ambari warnings will go away and operations such as key rotation can be successfully performed.

   ![Kafka key access ambari alert](./media/apache-kafka-byok/kafka-byok-ambari-alert.png)

**How can I recover the cluster if the keys are deleted?**

   Since only “Soft Delete” enabled keys are supported, if the keys are recovered in the key vault, the cluster should regain access to the keys. To recover an Azure Key Vault key, see [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) or [az-keyvault-key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Can I have producer/consumer applications working with a BYOK cluster and a non-BYOK cluster simultaneously?**

   Yes. The use of BYOK is transparent to producer/consumer applications. Encryption happens at the OS layer. No changes need to be made to existing producer/consumer Kafka applications.

**Are OS disks/Resource disks also encrypted?**

   No. OS disks and Resource disks are not encrypted.

**If a cluster is scaled up, will the new brokers support BYOK seamlessly?**

   Yes. The cluster needs access to the key in the key vault during scale up. The same key is used to encrypt all managed disks in the cluster.

**Is BYOK available in my location?**

   Kafka BYOK is available in all public clouds.

## Next steps

* For more information about Azure Key Vault, see [What is Azure Key Vault](../../key-vault/key-vault-whatis.md)?
* To get started with Azure Key Vault, see [Getting Started with Azure Key Vault](../../key-vault/key-vault-overview.md).
