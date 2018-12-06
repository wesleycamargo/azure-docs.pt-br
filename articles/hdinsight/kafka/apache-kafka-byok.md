---
title: Bring Your Own Key do Apache Kafka no Azure HDInsight (versão prévia)
description: Este artigo descreve como usar sua própria chave do Azure Key Vault para criptografar dados armazenados no Apache Kafka no Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 8525b1450ae8b7badfe1c569c2040ecf1ab78070
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52314364"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight-preview"></a>Bring Your Own Key do Apache Kafka no Azure HDInsight (versão prévia)

O Azure HDInsight inclui suporte ao BYOK (Bring Your Own Key) para Apache Kafka. Essa funcionalidade permite que você tenha e gerencie as chaves usadas para criptografar dados em repouso. 

Todos os discos gerenciados no HDInsight são protegidos com o SSE (Criptografia do Serviço de Armazenamento) do Azure. Por padrão, os dados nesses discos são criptografados usando chaves gerenciadas pela Microsoft. Se você habilitar o BYOK, poderá fornecer a chave de criptografia para o HDInsight usar e gerenciá-la usando o Azure Key Vault. 

A criptografia de BYOK é um processo de uma etapa realizado durante a criação do cluster sem nenhum custo adicional. Tudo o que você precisa fazer é registrar o HDInsight como uma identidade gerenciada com o Azure Key Vault e adicionar a chave de criptografia ao criar o cluster.

Todas as mensagens para o cluster do Kafka (incluindo réplicas mantidas pelo Kafka) são criptografadas com uma DEK (Chave de Criptografia de Dados). A DEK é protegida usando a KEK (Chave de Criptografia de Chave) do seu cofre de chaves. Os processos de criptografia e descriptografia são realizados inteiramente pelo Azure HDInsight. 

Você pode usar o portal do Azure ou a CLI do Azure para gorar as chaves com segurança no cofre de chaves. Quando uma chave é girada, o cluster do Kafka do HDInsight começa a usar a nova chave em questão de minutos. Habilite os recursos de proteção de chave "Não Limpar" e "Exclusão Reversível" para se proteger contra cenários de ransomware e exclusão acidental. As chaves sem esses recursos de proteção não são compatíveis.

## <a name="get-started-with-byok"></a>Introdução ao BYOK

1. Crie identidades gerenciadas para os recursos do Azure.

   Para autenticar para o Key Vault, crie uma identidade gerenciada atribuída pelo usuário usando o [portal do Azure](../../active-directory/managed-service-identity/how-to-manage-ua-identity-portal.md), o [Azure PowerShell](../../active-directory/managed-service-identity/how-to-manage-ua-identity-powershell.md), o [Azure Resource Manager](../../active-directory/managed-service-identity/how-to-manage-ua-identity-arm.md) ou a [CLI do Azure](../../active-directory/managed-service-identity/how-to-manage-ua-identity-cli.md). Embora o Azure Active Directory seja necessário para as identidades gerenciadas e o BYOK para Kafka, o ESP (Enterprise Security Package) não é um requisito. Certifique-se de salvar a ID do recurso de identidade gerenciada para quando você a adicionar à política de acesso do Key Vault.

   ![Criar uma identidade gerenciada atribuída pelo usuário no portal do Azure](./media/apache-kafka-byok/user-managed-identity-portal.png)

2. Importe um cofre de chaves existente ou crie um novo.

   O HDInsight é compatível apenas com o Azure Key Vault. Se você tiver seu próprio cofre de chaves, poderá importar suas chaves para o Azure Key Vault. Lembre-se de que as chaves devem ter os recursos "Exclusão Reversível" e "Não Limpar" habilitados. Os recursos "Exclusão Reversível" e "Não Limpar" estão disponíveis por meio das interfaces REST, .NET/C#, PowerShell e CLI do Azure.

   Para criar um novo cofre de chaves, siga o início rápido do [Azure Key Vault](../../key-vault/key-vault-get-started.md). Para obter mais informações sobre como importar as chaves existentes, visite [Sobre chaves, segredos e certificados](../../key-vault/about-keys-secrets-and-certificates.md).

   Para criar uma nova chave, selecione **Gerar/Importar** do menu **Chaves** em **Configurações**.

   ![Gerar uma nova chave no Azure Key Vault](./media/apache-kafka-byok/kafka-create-new-key.png)

   Defina **Opções** para **Gerar** e forneça um nome à chave.

   ![Gerar uma nova chave no Azure Key Vault](./media/apache-kafka-byok/kafka-create-a-key.png)

   Selecione a chave que você criou na lista de chaves.

   ![Lista de chaves do Azure Key Vault](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

   Ao usar sua própria chave de criptografia de cluster do Kafka, será necessário fornecer o URI da chave. Copie o **Identificador de chave** e salve-o em algum local até que esteja pronto para criar o cluster.

   ![Copiar o identificador de chave](./media/apache-kafka-byok/kafka-get-key-identifier.png)
   
3. Adicione identidade gerenciada à política de acesso do cofre de chaves.

   Crie uma nova política de acesso do Azure Key Vault.

   ![Criar uma nova política de acesso do Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy.png)

   Em **Selecionar Entidade de Segurança**, escolha a identidade gerenciada atribuída pelo usuário que você criou.

   ![Definir Selecionar Entidade de Segurança para a política de acesso do Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

   Defina **Permissões de Chave** como **Obter**, **Decodificar Chave** e **Codificar Chave**.

   ![Definir permissões de chave para a política de acesso do Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png)

   Defina **Permissões do Segredo** como **Obter**, **Definir** e **Excluir**.

   ![Definir permissões de chave para a política de acesso do Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png)

4. Criar cluster HDInsight

   Agora você está pronto para criar um novo cluster do HDInsight. O BYOK pode ser aplicado apenas a novos clusters durante a criação do cluster. Não é possível remover a criptografia de clusters de BYOK e o BYOK não pode ser adicionado a clusters existentes.

   ![Criptografia de disco do Kafka no portal do Azure](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   Durante a criação do cluster, forneça a URL completa, incluindo a versão da chave. Por exemplo, `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Você também precisa atribuir a identidade gerenciada ao cluster e fornecer o URI da chave.

## <a name="faq-for-byok-to-apache-kafka"></a>Perguntas frequentes para BYOK para Apache Kafka

**Como o cluster do Kafka acessa meu cofre de chaves?**

   Associe uma identidade gerenciada ao cluster de Kafka do HDInsight durante a criação do cluster. Essa identidade gerenciada pode ser criada antes ou durante a criação do cluster. Você também precisa conceder o acesso de identidade gerenciada ao cofre de chaves em que a chave está armazenada.

**Esse recurso está disponível para todos os clusters do Kafka no HDInsight?**

   A criptografia de BYOK é possível apenas para o cluster do Kafka 1.1 e posteriores.

**É possível ter chaves diferentes para partições/tópicos diferentes?**

   Não, todos os discos gerenciados no cluster são criptografados pela mesma chave.

**Como poderei recuperar o cluster se as chaves forem excluídas?**

   Já que somente as chaves habilitadas com "Exclusão Reversível" são compatíveis, se as chaves forem restauradas no cofre de chaves, o cluster deverá obter o acesso às chaves novamente. Para restaurar uma chave do Azure Key Vault, consulte [Restore-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey).

**É possível ter aplicativos de produtor/consumidor trabalhando com um cluster de BYOK e não BYOK simultaneamente?**

   Sim. O uso de BYOK é transparente para os aplicativos de produtor/consumidor. A criptografia ocorre na camada do sistema operacional. Não é necessário fazer nenhuma alteração nos aplicativos Kafka de produtor/consumidor existentes.

**Os discos de recursos/discos de sistema operacional também são criptografados?**

    Não. Os discos de sistema operacional e os discos de recursos não são criptografados.

**Se um cluster for escalado verticalmente, os novos agentes darão suporte ao BYOK perfeitamente?**

   Sim. O cluster precisa acessar a chave no cofre de chaves durante ao escalar verticalmente. As mesmas chaves são usadas para criptografar todos os discos gerenciados no cluster.

**O BYOK está disponível na minha localização?**

   O BYOK do Kafka está disponível em todas as nuvens públicas.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre o Azure Key Vault, consulte [O que é o Azure Key Vault?](../../key-vault/key-vault-whatis.md)
* Para começar a usar o Azure Key Vault, consulte [Introdução ao Azure Key Vault](../../key-vault/key-vault-get-started.md).
