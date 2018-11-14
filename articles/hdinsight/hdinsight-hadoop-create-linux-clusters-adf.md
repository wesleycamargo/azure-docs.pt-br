---
title: 'Tutorial: criar clusters Apache Hadoop sob demanda no HDInsight do Azure usando o Data Factory '
description: Aprenda como criar clusters Apache Hadoop sob demanda no HDInsight usando o Azure Data Factory.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: hrasheed
ms.openlocfilehash: 76651874951255d9b01efdc6e91892f6852d948d
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036369"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Tutorial: criar clusters Apache Hadoop sob demanda no HDInsight usando o Azure Data Factory
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Neste artigo, você aprenderá como criar um cluster do Apache Hadoop, sob demanda, no Azure HDInsight usando o Azure Data Factory. Em seguida, usar pipelines de dados no Azure Data Factory para executar trabalhos de Hive e excluir o cluster. No final deste tutorial, você aprenderá a utilizar um trabalho de big data executado em que a criação do cluster, a execução do trabalho e a exclusão de cluster são executadas em um agendamento.

Este tutorial cobre as seguintes tarefas: 

> [!div class="checklist"]
> * Criar uma conta de armazenamento do Azure
> * Entender a atividade do Azure Data Factory
> * Criar um data factory usando o portal do Azure
> * Criar serviços vinculados
> * Criar um pipeline
> * Disparar um pipeline
> * Como monitorar um pipeline
> * Verificar a saída

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- PowerShell do Azure. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

- Uma entidade de serviço do Azure Active Directory. Depois de criar a entidade de serviço, certifique-se de recuperar o **ID do aplicativo** e **chave de autenticação** usando as instruções no artigo vinculado. Você precisa dos seguintes valores mais tarde neste tutorial. Além disso, verifique se a entidade de serviço é um membro da função de *Colaborador* da assinatura ou do grupo de recursos em que o cluster é criado. Para obter instruções para recuperar os valores necessários e atribuir as funções corretas, consulte [Criar uma entidade de serviço do Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure

Nesta seção, você deve criar uma conta de armazenamento que será usada como o armazenamento padrão para o cluster HDInsight que você criar sob demanda. Esta conta de armazenamento também contém o script HiveQL de exemplo (**hivescript.hql**) que você pode usar para simular um trabalho de Hive de exemplo que é executado no cluster.

Esta seção usa um script do Azure PowerShell para criar a conta de armazenamento e copie os arquivos necessários na conta de armazenamento. O exemplo de script do Azure PowerShell nesta seção executa as seguintes tarefas:

1. Logs no Azure.
1. Cria um grupo de recursos do Azure.
1. Cria uma conta de armazenamento do Azure.
1. Cria um contêiner de Blob na conta de armazenamento
1. Copia o script HiveQL de exemplo (**hivescript.hql**) o contêiner de Blob. O script está disponível em [https://hditutorialdata.blob.core.windows.net/adfv2hiveactivity/hivescripts/hivescript.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql). O script de exemplo já está disponível em outro contêiner de Blob público. O script do PowerShell a seguir faz uma cópia desses arquivos para a conta de Armazenamento do Azure, que ele cria.


**Para preparar a conta de armazenamento e copiar os arquivos usando o Azure PowerShell:**
> [!IMPORTANT]
> Especifique nomes para o grupo de recursos do Azure e a conta de armazenamento do Azure que será criada pelo script.
> Anote o **nome do grupo de recursos**, o **nome da conta de armazenamento**, e a **chave da conta de armazenamento** produzidos pelo script. Você precisa deles na próxima seção.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US 2"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfv2hiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
Login-AzureRmAccount
#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -type Standard_LRS `
    -Location $location

$destStorageAccountKey = (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzureStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous
$destContext = New-AzureStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzureStorageContainer -Name $destContainerName -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzureStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName

$blobs|Start-AzureStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzureStorageBlob -Context $destContext -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

**Para verificar a conta de armazenamento**

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Selecione **Grupos de recursos** no painel esquerdo.
1. Clique duas vezes no nome do grupo de recursos criado em seu script da CLI ou do PowerShell. Use o filtro se houver muitos grupos de recursos listados.
1. No bloco **Recursos**, você vê um recurso listado, a menos que compartilhe o grupo de recursos com outros projetos. Esse recurso é a conta de armazenamento com o nome especificado anteriormente. Selecione o nome da conta de armazenamento.
1. Selecione os blocos **Blobs**.
1. Selecione o contêiner **adfgetstarted**. Você verá uma pasta chamada **hivescripts**.
1. Abra a pasta e certificar-se de que ela contém o arquivo de script de exemplo, **hivescript.hql**.

## <a name="understand-the-azure-data-factory-activity"></a>Entender a atividade do Azure Data Factory

O [Azure Data Factory](../data-factory/introduction.md) orquestra e automatiza a movimentação e a transformação dos dados. O Azure Data Factory pode criar um cluster HDInsight Hadoop just-in-time para processar uma fatia de entrada de dados e excluir o cluster quando o processamento for concluído. 

No Azure Data Factory, um data factory pode ter um ou mais pipelines de dados. Um pipeline de dados tem uma ou mais atividades. Há dois tipos de atividades:

- [Atividades de Movimentação de Dados](../data-factory/copy-activity-overview.md) - Você pode usar as atividades de movimentação de dados para mover dados de um repositório de dados de origem para um repositório de dados de destino.
- [Atividades de transformação de dados](../data-factory/transform-data.md). Você pode usar as atividades de transformação de dados para transformar/processar dados. A atividade do HDInsight Hive é uma das atividades de transformação com suporte pelo Data Factory. Você pode usar a atividade de transformação do Hive neste tutorial.

Neste artigo, você deve configurar a atividade de Hive para criar um cluster Hadoop do HDInsight sob demanda. Quando a atividade é executada para processar dados, aqui está o que acontece:

1. Um cluster Hadoop do HDInsight é criado automaticamente para você just-in-time para processar a fatia. 

1. Os dados de entrada são processados executando um script HiveQL no cluster. Neste tutorial, o script HiveQL associado à atividade hive executa as seguintes ações:
    
    - Usa a tabela existente (*hivesampletable*) para criar outra tabela **HiveSampleOut**.
    - Preencha a tabela **HiveSampleOut** com apenas a colunas específicas do original *hivesampletable*.
    
1. O cluster HDInsight Hadoop é excluído depois que o processamento é concluído e o cluster está ocioso pelo período de tempo configurado (configuração timeToLive). Se a próxima fatia de dados estiver disponível para processamento nesse tempo ocioso timeToLive, o mesmo cluster será usado para processar a fatia.  

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Faça logon no [Portal do Azure](https://portal.azure.com/).

1. No Portal do Azure, selecione **Criar um recurso** > **Dados + Análise** > **Data Factory**.

    ![Azure Data Factory no portal](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "Azure Data Factory no portal")

1. Insira ou selecione os valores, conforme mostrado na captura de tela a seguir:

    ![Criar Azure Data Factory usando o portal do Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/create-data-factory-portal.png "criar Azure Data Factory usando o portal do Azure")

    Digite ou selecione os valores a seguir:
    
    |Propriedade  |DESCRIÇÃO  |
    |---------|---------|
    |**Nome** |  Insira um nome para o data factory. Esse nome deve ser globalmente exclusivo.|
    |**Assinatura**     |  Selecione sua assinatura do Azure. |
    |**Grupo de recursos**     | Selecione **Usar existente** e, em seguida, selecione o grupo de recursos que você criou usando o script do PowerShell. |
    |**Versão**     | Selecione **V2 (versão prévia)** |
    |**Localidade**     | O local é definido automaticamente para o local que você especificou ao criar o grupo de recursos anterior. Para este tutorial, o local é definido como **Leste dos EUA 2**. |
    

1. Escolha **Fixar no painel** e selecione **Criar**. Você verá um novo bloco chamado **Enviando a implantação** no painel do portal. Criar um data factory pode levar entre 2 a 4 minutos.

    ![Progresso da implantação de modelo](./media/hdinsight-hadoop-create-linux-clusters-adf/deployment-progress-tile.png "Progresso da implantação de modelo") 
 
1. Depois que o data factory é criado, o portal mostra a visão geral do data factory.

    ![Visão geral do Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "Visão geral do Azure Data Factory")

1. Selecione **Autor & Monitor** para iniciar o portal de criação e monitoramento do Azure Data Factory.

## <a name="create-linked-services"></a>Criar serviços vinculados

Nesta seção, você pode criar dois serviços vinculados no data factory.

- Um **serviço vinculado do Armazenamento do Azure** que vincula uma conta de Armazenamento do Azure ao data factory. Esse armazenamento é usado pelo cluster HDInsight sob demanda. Ele também contém o script Hive que é executado no cluster.
- Um **serviço vinculado do HDInsight sob demanda**. O Azure Data Factory cria automaticamente um cluster HDInsight e executa o script Hive. Em seguida, ele exclui o cluster HDInsight após o cluster ficar ocioso por um tempo pré-configurado.

###  <a name="create-an-azure-storage-linked-service"></a>Criar um serviço vinculado do Armazenamento do Azure

1. No painel esquerdo da página **Vamos começar**, selecione o ícone **Editar**.

    ![Criar um serviço vinculado do Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "Criar um serviço vinculado do Azure Data Factory")

1. Selecione **Conexões** do canto inferior esquerdo da janela e, depois, selecione **+ Novo**.

    ![Criar conexões no Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "Criar conexões no Azure Data Factory")

1. Na caixa de diálogo **Novo Serviço Vinculado**, selecione **Armazenamento de Blobs do Azure** e selecione **Continuar**.

    ![Criar serviço vinculado do Armazenamento do Azure para o Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "Criar serviço vinculado do Armazenamento do Azure para o Data Factory")

1. Forneça um nome para o serviço vinculado de armazenamento, selecione a conta de Armazenamento do Azure que você criou como parte do script do PowerShell e, em seguida, selecione **Concluir**.

    ![Forneça o nome para o serviço vinculado do Armazenamento do Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "Forneça o nome para o serviço vinculado do Armazenamento do Azure")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Criar um serviço vinculado do HDInsight sob demanda

1. Selecione o botão **+ Novo** novamente para criar outro serviço vinculado.

1. Na janela **Novo Serviço Vinculado**, selecione **Computação** > **Azure HDInsight** e selecione **Continuar**.

    ![Criar serviço vinculado do HDInsight para o Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "Criar serviço vinculado do HDInsight para o Azure Data Factory")

1. Na janela **Novo serviço vinculado**, forneça os valores necessários.

    ![Forneça valores para o serviço vinculado do HDInsight](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "Forneça valores para o serviço vinculado do HDInsight")

    Insira os seguintes valores e mantenha os demais como padrão.

    | Propriedade | Descrição |
    | --- | --- |
    | NOME | Insira um nome para o serviço vinculado do HDInsight |
    | Tipo | Selecione **HDInsight sob demanda** |
    | Serviço vinculado de armazenamento do Azure | Selecione o serviço vinculado de armazenamento criado anteriormente. |
    | Tipo de cluster | Selecione **hadoop** |
    | Vida útil | Forneça a duração para a qual você deseja que o cluster HDInsight esteja disponível antes de ser excluído automaticamente.|
    | ID da entidade de serviço | Forneça a ID do aplicativo da entidade de serviço do Azure Active Directory que você criou como parte dos pré-requisitos |
    | Chave da entidade de serviço | Forneça a chave de autenticação para a entidade de serviço do Azure Active Directory |
    | Prefixo do nome do cluster | Forneça um valor que será prefixado para todos os tipos de cluster que são criados pelo data factory |
    | Grupo de recursos | Selecione o grupo de recursos que você criou como parte do script do PowerShell que você usou anteriormente| 
    | Nome de usuário do cluster SSH | Insira um nome de usuário SSH |
    | Senha do cluster SSH | Forneça uma senha para o usuário do SSH |

    Selecione **Concluir**.

## <a name="create-a-pipeline"></a>Criar um pipeline

1. Selecione o botão **+** (mais) adição e, em seguida, selecione **Pipeline**.

    ![Criar um pipeline no Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "Criar um pipeline no Azure Data Factory")

1. Na caixa de ferramentas **Atividades**, expanda **HDInsight**e arraste a atividade **Hive** para a superfície do designer de pipeline. Na guia **Geral**, forneça um nome para a atividade.

    ![Adicionar atividades a pipeline do Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "Adicionar atividades a pipeline do Data Factory")

1. Certifique-se de ter a atividade de Hive selecionada, selecione a guia **grupo HDI** e da lista suspensa **Serviço vinculado HDInsight**, selecione o serviço vinculado que você criou anteriormente para HDInsight.

    ![Forneça detalhes do cluster HDInsight para o pipeline](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "Forneça detalhes do cluster HDInsight para o pipeline")

1. Selecione a guia **Script** e conclua as etapas a seguir:
    
    1. Para **Serviço vinculado de script**, selecione **HDIStorageLinkedService**. Este valor é o serviço vinculado de armazenamento criado anteriormente.
    
    1. Para **Caminho do arquivo**, selecione **Procurar armazenamento** e navegue até o local onde o script de Hive de exemplo está disponível. Se você executou o script do PowerShell anteriormente, esse local deve ser `adfgetstarted/hivescripts/hivescript.hql`.
    
        ![Forneça detalhes de script do Hive para o pipeline](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "Forneça detalhes de script do Hive para o pipeline")
    
    1. Em **Parâmetros** > **Avançado**, selecione **Preenchimento automático de script**. Essa opção procura todos os parâmetros de script do Hive que exigem valores em tempo de execução. O script que você usa (**hivescript.hql**) tem um parâmetro de **Saída**. Proporcione o valor no formato `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` para apontá-lo para uma pasta existente no seu Armazenamento do Azure. O caminho diferencia maiúsculas de minúsculas. Este é o caminho onde a saída do script será armazenada.
    
        ![Forneça parâmetros para o script do Hive](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "Forneça parâmetros para o script de Hive")

1. Selecione **Validar** para validar o pipeline. Selecione a **>>** (seta para a direita) para fechar a janela de validação.

    ![Validar o pipeline do Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "Validar o pipeline do Azure Data Factory")

1. Por fim, selecione **Publicar tudo** para publicar os artefatos no Azure Data Factory.

    ![Publicar o pipeline do Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "Publicar o pipeline do Azure Data Factory")

## <a name="trigger-a-pipeline"></a>Disparar um pipeline

1. Na barra de ferramentas na superfície de design, selecione **Disparar** > **Disparar agora**.

    ![Disparar o pipeline do Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "Disparar o pipeline do Azure Data Factory")

1. Selecione **Concluir** na barra lateral do pop-up.

## <a name="monitor-a-pipeline"></a>Como monitorar um pipeline

1. Alterne para a guia **Monitorar** à esquerda. Você vê uma execução de pipeline na lista **Execuções de Pipeline**. Observe o status da execução na coluna **Status**.

    ![Monitorar o pipeline do Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "Monitorar o pipeline do Azure Data Factory")

1. Selecione **Atualizar** para atualizar o status.

1. Você também pode monitorar o ícone **Exibir Execuções da Atividade** para ver a execução de atividade associada ao pipeline. Na captura de tela abaixo, você vê somente uma execução de atividade, já que há apenas uma atividade no pipeline que você criou. Para voltar para o modo de exibição anterior, selecione **Pipelines** no topo da página.

    ![Monitorar a atividade do pipeline do Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "Monitorar a atividade do pipeline do Azure Data Factory")


## <a name="verify-the-output"></a>Verificar a saída

1. Para verificar a saída, no portal do Azure, navegue até a conta de armazenamento que você usou para este tutorial. Você deve ver as seguintes pastas ou contêineres:

    - Você verá um **adfgerstarted/outputfolder** que contém a saída do script de Hive que foi executado como parte do pipeline.

    - Você verá um contêiner **adfhdidatafactory-\<linked-service-name>-\<timestamp >**. Esse contêiner é o local de armazenamento padrão do cluster HDInsight que foi criado como parte da execução do pipeline.

    - Você verá um contêiner **adfjobs** que possui logs de trabalho do Azure Data Factory.  

        ![Verificar a saída do pipeline do Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "Verificar a saída do pipeline do Azure Data Factory")


## <a name="clean-up-the-tutorial"></a>Limpar o tutorial

Com a criação do cluster HDInsight sob demanda, você não precisa excluir explicitamente o cluster HDInsight. O cluster é excluído com base na configuração fornecida durante a criação do pipeline. No entanto, mesmo depois que o cluster é excluído, as contas de armazenamento associadas ao cluster continuam a existir. Esse comportamento ocorre por design, para que você possa manter os dados intactos. No entanto, se você não deseja manter os dados, você pode excluir a conta de armazenamento que você criou.

Como alternativa, você pode excluir o grupo de recursos inteiro que você criou para este tutorial. Isso exclui a conta de armazenamento e o Azure Data Factory que você criou.

### <a name="delete-the-resource-group"></a>Exclua o grupo de recursos

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Selecione **Grupos de recursos** no painel esquerdo.
1. Selecione o nome do grupo de recursos criado em seu script do PowerShell. Use o filtro se houver muitos grupos de recursos listados. Ele abre o grupo de recursos.
1. No bloco **Recursos** , a conta de armazenamento padrão e o data factory deverão estar listados, a menos que você compartilhe o grupo de recursos com outros projetos.
1. Selecione **Excluir grupo de recursos**. Isso exclui a conta de armazenamento e os dados armazenados na conta de armazenamento.

    ![Excluir o grupo de recursos](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "Excluir o grupo de recursos")

1. Insira o nome do grupo de recursos para confirmar a exclusão e, em seguida, selecione **Excluir**.


## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar o Azure Data Factory para criar o cluster HDInsight sob demanda e executar trabalhos de Hive. Avance para o próximo artigo para aprender a criar clusters HDInsight com a configuração personalizada.

> [!div class="nextstepaction"]
>[Criar clusters HDInsight do Azure com configuração personalizada](hdinsight-hadoop-provision-linux-clusters.md)


