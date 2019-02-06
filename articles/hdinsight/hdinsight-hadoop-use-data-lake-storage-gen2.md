---
title: Usar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight
description: Saiba como criar e usar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: howto
ms.date: 01/10/2019
ms.author: hrasheed
ms.openlocfilehash: a44e53d7a32ab151fa951d1bc89b741390a70dfb
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55464782"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Usar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight

O Azure Data Lake Storage (Data Lake Storage) Gen2 é um conjunto de recursos dedicados à análise de Big Data, criado sobre o Armazenamento de Blobs do Azure. O Azure Data Lake Storage Gen2 é o resultado da combinação dos recursos do Armazenamento de Blobs do Azure com o Azure Data Lake Storage Gen1. O resultado é um serviço que oferece recursos do Azure Data Lake Storage Gen1, como semântica do sistema de arquivos, diretório e segurança e escala em nível de arquivo, além de armazenamento em camadas de baixo custo, alta disponibilidade/recursos de recuperação de desastre do Armazenamento de Blobs do Azure.

## <a name="data-lake-storage-gen2-availability"></a>Disponibilidade o Data Lake Storage Gen2

O Azure Data Lake Storage Gen2 está disponível como opção de armazenamento para quase todos os tipos de cluster do Azure HDInsight, como conta de armazenamento padrão e como conta de armazenamento adicional. O HBase, no entanto, só pode ter uma conta do Data Lake Storage Gen2.

> [!Note] 
> Depois de selecionar o Data Lake Storage Gen2 como o **tipo de armazenamento primário**, você não poderá selecionar uma conta do Data Lake Storage Gen1 como armazenamento adicional.

## <a name="creating-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Criando um cluster do HDInsight com o Data Lake Storage Gen2

## <a name="using-the-azure-portal"></a>Usando o portal do Azure

Para criar um cluster do HDInsight, que usa o Data Lake Storage Gen2 como armazenamento, use as etapas a seguir para criar uma conta do Data Lake Storage Gen2 configurada corretamente.

1. Crie uma identidade gerenciada atribuída pelo usuário, se ainda não tiver uma. Confira [Criar, listar, excluir ou atribuir uma função a uma identidade gerenciada atribuída pelo usuário usando o portal do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

    ![Criar uma identidade gerenciada atribuída ao usuário](./media/hdinsight-hadoop-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

1. Criar uma conta de armazenamento do Azure Data Lake Storage Gen2. Verifique se a opção **Sistema de arquivos hierárquico** está habilitada. Confira [Início Rápido: Crie uma conta de armazenamento do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md) para saber mais.

    ![Captura de tela mostrando a criação da conta de armazenamento no portal do Azure](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)
 
1. Atribua a identidade gerenciada à função **Colaborador de dados do blob de armazenamento (versão prévia)** na conta de armazenamento. Confira [Gerenciar direitos de acesso para Blobs do Azure e enfileirar dados com RBAC (versão prévia)](../storage/common/storage-auth-aad-rbac.md#assign-a-role-scoped-to-the-storage-account-in-the-azure-portal)

    1. No [Portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento.
    1. Selecione sua conta de armazenamento, em seguida, selecione **Controle de Acesso (IAM)** para exibir as configurações de controle de acesso para a conta. Selecione a guia **Atribuições de função** para ver as atribuições de função atuais.
    
        ![Captura de tela mostrando as configurações de controle de acesso de armazenamento](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
    1. Clique no botão **Adicionar atribuição de função** para adicionar uma nova função.
    1. Na janela **Adicionar atribuição de função**, selecione a função **Colaborador de dados de blob de armazenamento (versão prévia)**. Em seguida, selecione a assinatura que tem a conta de armazenamento e a identidade gerenciada. Em seguida, pesquise para localizar a identidade gerenciada atribuída pelo usuário que você criou anteriormente. Por fim, selecione a identidade gerenciada e ele estará listada em **Membros selecionados**.
    
        ![Captura de tela que mostra como atribuir uma função do RBAC](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role2.png)
    
    1. Clique em **Salvar**. A identidade atribuída pelo usuário que você selecionou agora está listada na função **Colaborador**.

    1. Ao concluir a configuração inicial, você poderá criar um cluster usando o portal. O cluster deve estar na mesma região do Azure da conta de armazenamento. Na seção **Armazenamento** do menu de criação do cluster, selecione as seguintes opções:
        
        * Em **Tipo de armazenamento primário**, clique em **Azure Data Lake Storage Gen2**.
        * Em **Selecione uma Conta de Armazenamento**, procure e selecione a conta de armazenamento do Data Lake Storage Gen2 recém-criada.
        
            ![Configurações do armazenamento para usar o Data Lake Storage Gen2 com o Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
        
        * Em **Identidade**, selecione a assinatura correta e a identidade gerenciada atribuída pelo usuário recém-criada.
        
            ![Configurações da identidade para usar o Data Lake Storage Gen2 com o Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)

### <a name="using-a-resource-manager-template-deployed-with-azure-cli"></a>Usar um modelo do Resource Manager implantado com a CLI do Azure

Você pode baixar um [arquivo de modelo de exemplo aqui](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) e um [arquivo de parâmetros de exemplo aqui](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Antes de usar o modelo, substitua sua ID de assinatura do Azure real pela cadeia de caracteres `<SUBSCRIPTION_ID>`. Além disso, substitua a senha escolhida pela cadeia de caracteres `<PASSWORD>` para definir a senha de logon que você usará para fazer logon em seu cluster, bem como a senha do SSH.

O snippet de código a seguir executa as seguintes etapas iniciais:

1. Faça logon em sua conta do Azure.
1. Definir a assinatura ativa em que as operações de criação serão executadas.
1. Criar um novo grupo de recursos para as novas atividades de implantação `hdinsight-deployment-rg`.
1. Criar uma MSI (Identidade de Serviço Gerenciada) `test-hdinsight-msi`.
1. Adicionar uma extensão à CLI do Azure para usar os recursos para o Data Lake Storage Gen2.
1. Criar uma nova conta do Data Lake Storage Gen2 `hdinsightadlsgen2`, usando o sinalizador `--hierarchical-namespace true`.

```azurecli
az login
az account set --subscription <subscription_id>

#create resource group
az group create --name hdinsight-deployment-rg --location eastus

# Create managed identity
az identity create -g hdinsight-deployment-rg -n test-hdinsight-msi

az extension add --name storage-preview

az storage account create --name hdinsightadlsgen2 \
    --resource-group hdinsight-deployment-rg \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Em seguida, faça logon no portal e adicione o novo MSI na função **Colaborador de Dados de Blob de Armazenamento (versão prévia)** na conta de armazenamento, conforme descrito na etapa 3 acima em [Usando o portal do Azure](hdinsight-hadoop-use-data-lake-storage-gen2.md#using-the-azure-portal).

Depois de concluir a atribuição de função do MSI no portal, passe à implantação do modelo usando o snippet de código abaixo.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group hdinsight-deployment-rg \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Controle de Acesso para Data Lake Storage Gen2 no HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>O Data Lake Storage Gen2 dá suporte a quais tipos de permissões?

O Azure Data Lake Storage Gen2 implementa um modelo de controle de acesso que oferece suporte ao Controle de Acesso Baseado em Função do Azure (RBAC) e listas de controle de acesso POSIX-like. O Data Lake Storage Gen1 só dava suporte a listas de controle de acesso para controlar o acesso aos dados.

O Controle de Acesso Baseado em Função do Azure (RBAC) usa as atribuições de função para aplicar efetivamente conjuntos de permissões a usuários, grupos e entidades de serviço para recursos do Azure. Normalmente, esses recursos do Azure são restritos a recursos de nível superior (por exemplo, contas de Armazenamento do Azure). Para o Armazenamento do Azure e o Azure Data Lake Storage Gen2, esse mecanismo foi estendido para o recurso do sistema de arquivos.

 Para obter mais informações sobre as permissões de arquivo com RBAC, consulte [RBAC (controle de acesso baseado em função) do Azure](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Para obter mais informações sobre as permissões de arquivo com ACLs, consulte [Listas de controle de acesso em arquivos e diretórios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).


### <a name="how-do-i-control-access-to-my-data-in-gen2"></a>Como fazer para controlar o acesso aos meus dados no Gen2?

A capacidade do seu cluster do HDInsight de acessar arquivos no Data Lake Storage Gen2 é controlada por meio de identidades gerenciadas. Uma identidade gerenciada é uma identidade registrada no Azure AD, cujas credenciais são gerenciadas pelo Azure. Não é necessário registrar entidades de serviço no Azure AD e manter credenciais, por exemplo, certificados.

Há dois tipos de Identidades Gerenciadas para serviços do Azure: atribuída pelo sistema e atribuída pelo usuário. O Azure HDInsight usa identidades gerenciadas atribuídas pelo usuário para acessar o Azure Data Lake Storage Gen2. Uma identidade gerenciada atribuída pelo usuário é criada como um recurso autônomo do Azure. Por meio de um processo de criação, o Microsoft Azure cria uma identidade no locatário do Azure AD confiado pela assinatura em uso. Depois que a identidade é criada, ela pode ser atribuída a uma ou mais instâncias de serviço do Azure. O ciclo de vida de uma identidade atribuída pelo usuário é gerenciado separadamente do ciclo de vida das instâncias de serviço do Azure a que ela é atribuída. Para obter mais informações sobre identidades gerenciadas, confira [Como funcionam as identidades gerenciadas em recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-using-hive-or-other-services"></a>Como fazer para definir permissões para usuários do Azure AD consultarem dados no Data Lake Storage Gen2 usando o Hive ou outros serviços?

Use grupos de segurança do Azure AD como a entidade de segurança atribuída nas ACLs. Não atribua permissões de acesso aos arquivos diretamente a usuários ou entidades de serviço individualmente. Quando você usa grupos de segurança do AD para controlar o fluxo de permissões, pode adicionar e remover usuários ou entidades de serviço sem reaplicar ACLs a uma estrutura inteira de um diretório. Você só precisa adicionar ou remover os usuários do grupo de segurança do Azure AD pertinente. As ACLs não são herdadas e, por isso, reaplicar as ACLs exige a atualização da ACL em cada arquivo e subdiretório.

## <a name="next-steps"></a>Próximas etapas

* [Usar a versão prévia do Azure Data Lake Storage Gen2 com clusters HDInsight do Azure](../storage/blobs/data-lake-storage-use-hdi-cluster.md)
* [Integração do Azure HDInsight com a versão prévia do Data Lake Storage Gen2 – ACL e atualização de segurança](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Introdução à Versão Prévia do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
