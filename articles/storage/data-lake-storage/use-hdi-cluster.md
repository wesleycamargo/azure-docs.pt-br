---
title: Usar o Azure Data Lake Storage Gen2 Versão Prévia com clusters HDInsight do Azure
description: Aprenda a consultar dados do Azure Data Lake Storage Gen2 Versão Prévia para armazenar os resultados da sua análise.
keywords: hdfs, dados estruturados, dados não estruturados, data lake store, entrada do Hadoop, saída do Hadoop, armazenamento do hadoop, entrada do hdfs, saída do hdfs, armazenamento do hdfs, wasb do azure
services: hdinsight,storage
documentationcenter: ''
tags: azure-portal
author: jamesbak
manager: jahogg
ms.component: data-lake-storage-gen2
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.openlocfilehash: e9fd28ac21ce843655697c5d58849d940e305fce
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344947"
---
# <a name="use-azure-data-lake-storage-gen2-preview-with-azure-hdinsight-clusters"></a>Usar o Azure Data Lake Storage Gen2 Versão Prévia com clusters HDInsight do Azure

Para analisar dados no cluster HDInsight, é possível armazenar os dados em qualquer combinação do Armazenamento do Microsoft Azure, do Azure Data Lake Storage Gen1 ou d Azure Data Lake Storage Gen2 Versão Prévia. Todas as opções de armazenamento permitem que os clusters HDInsight usados para cálculo sejam excluídos com segurança sem que ocorra perda de dados do usuário.

O Hadoop dá suporte a uma noção do sistema de arquivos padrão. O sistema de arquivos padrão implica esquema e autoridade padrões. Ele também pode ser usado para resolver caminhos relativos. Durante o processo de criação do cluster HDInsight, você pode especificar um contêiner de blobs no Armazenamento do Azure ou no Azure Data Lake Storage como o sistema de arquivos padrão. Como alternativa, com o HDInsight 3.5, é possível selecionar o Armazenamento do Armazenamento do Microsoft Azure ou o Azure Data Lake Storage como o sistema de arquivos padrão com algumas exceções.

Neste artigo, você aprenderá como funciona o Azure Data Lake Storage Gen2 com clusters HDInsight. Para obter mais informações sobre a criação de um cluster HDInsight, consulte [Configurar clusters HDInsight usando o Azure Data Lake Storage com Hadoop, Spark, Kafka e mais](quickstart-create-connect-hdi-cluster.md).

O Armazenamento do Azure é uma solução de armazenamento de uso geral que se integra perfeitamente com o HDInsight. O HDInsight pode usar o Azure Data Lake Storage como o sistema de arquivos padrão para o cluster. Através de uma interface HDFS (Sistema de Arquivos Distribuído Hadoop), o conjunto completo de componentes em HDInsight pode operar diretamente sobre arquivos no Azure Data Lake Storage.

Não recomendamos o sistema de arquivos padrão para armazenar dados corporativos. É uma prática recomendada excluir o sistema de arquivos padrão após cada uso para reduzir o custo de armazenamento. Observe que o contêiner padrão contém os logs do aplicativo e do sistema. Certifique-se de recuperar os logs antes de excluir o contêiner.

Não há suporte para o compartilhamento de um sistema de arquivos para vários clusters.

## <a name="hdinsight-storage-architecture"></a>Arquitetura de armazenamento do HDInsight

O diagrama a seguir fornece uma exibição abstrata da arquitetura de armazenamento do HDInsight de uso do Armazenamento do Azure:

![Clusters Hadoop usam a API HDFS para acessar e armazenar dados estruturados e não estruturados no armazenamento de blobs.](./media/use-hdi-cluster/HDI.ABFS.Arch.png "Arquitetura de Armazenamento do HDInsight")

O HDInsight fornece acesso ao sistema de arquivos distribuídos que está anexado localmente aos nós de computação. Esse sistema de arquivos pode ser acessado usando o URI totalmente qualificado, por exemplo:

    hdfs://<NAME_NODE_HOST>/<PATH>

Além disso, o HDInsight permite que você acesse os dados armazenados no Azure Data Lake Storage. A sintaxe do é:

    abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<path>

Veja algumas considerações ao usar uma conta de Armazenamento do Azure com clusters HDInsight.

* **Arquivos nas contas de armazenamento que estejam conectadas a um cluster** têm o nome e a chave de conta associados com o cluster durante a criação. Essa configuração dá acesso completo aos arquivos no sistema de arquivos.

* **Arquivos públicos em contas de armazenamento que NÃO estão conectados a um cluster** expõem permissões somente leitura aos arquivos no sistema de arquivos.
  
  > [!NOTE]
  > Sistemas de arquivos públicos permitem que você obtenha uma lista de todos os arquivos disponíveis no sistema de arquivos e acesse metadados. Sistemas de arquivos públicos permitem acessar arquivos apenas se você souber a URL exata. Para mais informações, consulte [Acesso restrito a contêineres e blobs](http://msdn.microsoft.com/library/windowsazure/dd179354.aspx) (as regras para contêineres e blobs funcionam da mesma forma que para arquivos e sistema de arquivos).
 
* **Sistemas de arquivos privados nas contas de armazenamento que NÃO estão conectadas a um cluster** não permitem acessar arquivos no sistema de arquivos, a menos que você defina a conta de armazenamento ao enviar os trabalhos do WebHCat. Os motivos para essa restrição são explicados posteriormente neste artigo.

As contas de armazenamento definidas no processo de criação e suas chaves são armazenadas em *%HADOOP_HOME%/conf/core-site.xml* nos nós do cluster. O comportamento padrão do HDInsight é usar as contas de armazenamento definidas no arquivo *core-site.xml*. Você pode modificar essa configuração usando [Ambari](../../hdinsight/hdinsight-hadoop-manage-ambari.md)

Vários trabalhos do WebHCat, incluindo Hive, MapReduce, streaming de Hadoop e Pig, podem conter uma descrição de contas de armazenamento e metadados (normalmente funciona para Pig com contas de armazenamento, mas não para metadados). (Essa abordagem funciona atualmente com o Pig para contas de armazenamento, mas não para metadados.) Para obter mais informações, consulte [Usando um Cluster HDInsight com metastores e contas de armazenamento alternativas](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

## <a id="benefits"></a>Benefícios do Armazenamento do Azure

O custo de desempenho implícito de não ter clusters de cálculo e recursos de armazenamento colocalizados é reduzido pela forma como os clusters de cálculo são criados próximos aos recursos da conta de armazenamento na região do Azure, no qual a rede de alta velocidade torna os nós de computação eficientes para acessarem os dados no Armazenamento do Azure.

Há vários benefícios associados ao armazenamento de dados no Armazenamento do Azure em vez de no HDFS:

* **Compartilhamento e reutilização de dados:** os dados no HDFS estão localizados dentro do cluster de computação. Apenas os aplicativos que têm acesso ao cluster de computação podem usar os dados usando a API HDFS. Os dados no Armazenamento do Azure podem ser acessados por meio de APIs HDFS ou por meio de [APIs REST do Armazenamento de Blobs][blob-storage-restAPI]. Assim, um conjunto maior de aplicativos (incluindo outros clusters HDInsight) e ferramentas podem ser usados para produzir e consumir os dados.

* **Arquivamento de dados:** o armazenamento de dados no Armazenamento do Azure permite que os clusters HDInsight usados para cálculo sejam excluídos com segurança sem que ocorra perda de dados do usuário.

* **Custo do armazenamento de dados:** o armazenamento de dados no HDFS nativo a longo prazo é mais caro do que o armazenamento de dados no Armazenamento do Azure, uma vez que o custo de um cluster de computação é mais alto do que o custo do Armazenamento do Azure. Além disso, como os dados não precisam ser recarregados para cada geração de cluster de computação, você está economizando em custos de carregamento de dados.

* **Escala horizontal elástica:** embora o HDFS forneça um sistema de arquivos escalado horizontalmente, a escala é determinada pelo número de nós que você cria para o seu cluster. A alteração da escala pode se tornar um processo mais complicado do que depender dos recursos de dimensionamento elástico do Armazenamento do Azure que você obtém automaticamente.

* **Replicação geográfica:** seu dados de armazenamento do Azure pode ser replicado geograficamente. Embora esta função forneça redundância de dados e recuperação geográfica, um failover para o local replicado geograficamente afetará seriamente o desempenho e poderá resultar em custos adicionais. Portanto, escolha a replicação geográfica com sabedoria e somente se o valor dos dados compensar o custo adicional.

* **Gerenciamento de ciclo de vida de dados:** todos os dados em qualquer sistema de arquivos passa pelo seu próprio ciclo de vida. Dados geralmente começam sendo muito valiosos e acessados com frequência, fazem a transição para o que está sendo menos valiosos e exigindo menos acesso e, por fim, requerem o arquivo morto ou da exclusão. O Armazenamento do Microsoft Azure fornece políticas de camadas de dados e gerenciamento de ciclo de vida que colocam os dados em camada por aproximadamente seu estágio de ciclo de vida.

Determinados trabalhos e pacotes do MapReduce podem criar resultados intermediários que você não deseja realmente armazenar no contêiner de Armazenamento do Azure. Nesse caso, você ainda pode optar por armazenar os dados no HDFS local. Na verdade, o HDInsight usa a implementação de HDFS nativo (que é conhecida como DFS) para vários desses resultados intermediários em trabalhos Hive e outros processos.

> [!NOTE]
> A maioria dos comandos HDFS (por exemplo, `ls`, `copyFromLocal` e `mkdir`) ainda funciona conforme o esperado. Apenas os comandos específicos a DFS como `fschk` e `dfsadmin` mostram um comportamento diferente no Armazenamento do Azure.

## <a name="create-an-data-lake-storage-file-system"></a>Criar um sistema de arquivos do Data Lake Storage

Para usar o sistema de arquivos, primeiro você deve criar uma [conta de Armazenamento do Azure][azure-storage-create]. Como parte desse processo, especifique uma região do Azure na qual a conta de armazenamento será criada. O cluster e a conta de armazenamento devem ser hospedados na mesma região. O banco de dados SQL Server do metastore do Hive e o banco de dados SQL do metastore do Oozie também devem estar localizados na mesma região.

Independentemente de onde estiverem, cada blob que você criar pertencerá a um sistema de arquivos na sua conta do Azure Data Lake Storage. 

O sistema de arquivos do Data Lake Storage padrão armazena informações específicas do cluster, como o histórico do trabalho e logs. Não compartilhe um sistema de arquivos do Data Lake Storage padrão com vários clusters HDInsight. Isso pode corromper o histórico de trabalhos. É recomendável usar um sistema de arquivos diferente para cada cluster e colocar os dados compartilhados em uma conta de armazenamento vinculada especificada na implantação de todos os clusters relevantes, e não na conta de armazenamento padrão. Para obter mais informações sobre como configurar contas de armazenamento vinculadas, veja [Criar clusters HDInsight][hdinsight-creation]. No entanto, você pode reutilizar um sistema de arquivos de armazenamento padrão depois que o cluster HDInsight original for excluído. Para clusters HBase, você pode manter os dados e o esquema de tabela do HBase criando um novo cluster HBase com o contêiner de blobs padrão usado por um cluster HBase que foi excluído.

[!INCLUDE [secure-transfer-enabled-storage-account](../../../includes/hdinsight-secure-transfer.md)]

### <a name="use-the-azure-portal"></a>Use o Portal do Azure

Ao criar um cluster HDInsight no Portal, você tem as opções (como mostrado na captura de tela a seguir) para fornecer os detalhes da conta de armazenamento. Você também pode especificar se deseja uma conta de armazenamento adicional associada ao cluster e, em caso afirmativo, escolher qualquer uma das opções de armazenamento disponíveis como o armazenamento adicional.

![Fonte de dados de criação do hadoop do HDInsight](./media/use-hdi-cluster/create-storage-account.png)

> [!WARNING]
> Não há suporte para o uso de uma conta de armazenamento adicional em um local diferente do cluster HDInsight.

### <a name="use-azure-powershell"></a>Usar PowerShell do Azure

Se você tiver [instalado e configurado o Azure PowerShell][powershell-install], poderá usar o código a seguir no prompt do Azure PowerShell para criar uma conta de armazenamento e o contêiner:

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "WEST US 2"

    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"

    Connect-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionID

    # Create resource group
    New-AzureRmResourceGroup -name $ResourceGroupName -Location $Location

    # Create default storage account
    New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
      -Name StorageAccountName `
      -Location $Location `
      -SkuName Standard_LRS `
      -Kind StorageV2 
      -HierarchialNamespace $True

    # Create default blob containers
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer -Name $containerName -Context $destContext

> [!NOTE]
> Criar um contêiner é sinônimo de criar um sistema de arquivos no Azure Data Lake Storage.

### <a name="use-azure-cli"></a>Usar a CLI do Azure

[!INCLUDE [use-latest-version](../../../includes/hdinsight-use-latest-cli.md)]

Se você tiver [instalado e configurado a CLI do Azure](../../cli-install-nodejs.md), o comando a seguir pode ser usado para uma conta de armazenamento e o contêiner.

```bash
az storage account create \
    --name <STORAGE_ACCOUNT_NAME> \
    --resource-group <RESOURCE_GROUP_NAME> \
    --location westus2 \
    --sku Standard_LRS \
    --kind StorageV2 \
    --Enable-hierarchical-namespace true
```

> [!NOTE]
> Durante a versão prévia pública do Data Lake Storage Gen2 há suporte apenas para `--sku Standard_LRS`.

Você recebe uma solicitação para especificar a região geográfica na qual a conta de armazenamento é criada. Crie a conta de armazenamento na mesma região em que pretende criar o cluster HDInsight.

Assim que a conta de armazenamento for criada, use o seguinte comando para recuperar as chaves de conta de armazenamento:

    azure storage account keys list <STORAGE_ACCOUNT_NAME>

Para criar um novo contêiner, use o seguinte comando:

    azure storage container create <CONTAINER_NAME> --account-name <STORAGE_ACCOUNT_NAME> --account-key <STORAGE_ACCOUNT_KEY>

> [!NOTE]
> Criar um contêiner é sinônimo de criar um sistema de arquivos no Azure Data Lake Storage.

## <a name="address-files-in-azure-storage"></a>Arquivos de endereços no armazenamento do Azure

O esquema de URI para acessar arquivos no Armazenamento do Azure pelo HDInsight é:

    abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.widows.net/<PATH>

O esquema de URI fornece acesso sem criptografia (com o prefixo *abfs:*) e acesso criptografado SSL (com *abfss*). Recomendamos usar *abfss* sempre que possível, mesmo ao acessar dados que residem dentro da mesma região do Azure.

* O &lt;FILE_SYSTEM_NAME&gt; identifica o caminho do sistema de arquivos Azure Data Lake Storage.
* O &lt;ACCOUNT_NAME&gt; identifica o nome da conta do Armazenamento do Azure. Um FQDN (nome de domínio totalmente qualificado) é necessário.

    Se valores para &lt;FILE_SYSTEM_NAME&gt; ou &lt;ACCOUNT_NAME&gt; não tiverem sido especificados, o sistema de arquivos padrão é usado. Para os arquivos no sistema de arquivos padrão, você pode usar um caminho absoluto ou um caminho relativo. Por exemplo, o arquivo *hadoop-mapreduce-examples.jar* fornecido com clusters HDInsight pode ser referenciado para usar um dos caminhos a seguir:
    
        abfs://myfilesystempath@myaccount.dfs.core.widows.net/example/jars/hadoop-mapreduce-examples.jar
        abfs:///example/jars/hadoop-mapreduce-examples.jar
        /example/jars/hadoop-mapreduce-examples.jar

> [!NOTE]
> O nome do arquivo é *hadoop-examples.jar* em clusters HDInsight versões 2.1 e 1.6.

* O &lt;PATH&gt; é o nome do caminho do HDFS do arquivo ou do diretório.

> [!NOTE]
> Ao trabalhar com arquivos fora do HDInsight, a maioria dos utilitários não reconhecem o formato ABFS e, em vez disso, esperam um formato de caminho básico, como `example/jars/hadoop-mapreduce-examples.jar`.
 
## <a name="use-additional-storage-accounts"></a>Usar contas de armazenamento adicionais

Ao criar um cluster HDInsight, você especifica a conta de armazenamento do Azure que deseja associar a ele. Além dessa conta de armazenamento, você pode adicionar mais contas de armazenamento da mesma assinatura do Azure ou de diferentes assinaturas do Azure durante o processo de criação ou após a criação de um cluster. Para obter instruções sobre como adicionar mais contas de armazenamento, veja [Criar clusters HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]
> Não há suporte para o uso de uma conta de armazenamento adicional em um local diferente do cluster HDInsight.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a usar o armazenamento do Azure compatível com HDFS com o HDInsight. Essa abordagem permite que você crie soluções de aquisição de dados para arquivamento de longo prazo escalonáveis e use o HDInsight para desbloquear as informações nos dados armazenados estruturados e não estruturados. estruturados e não estruturados.

Para obter mais informações, consulte:

* [O driver ABFS Hadoop Filesystem para o Azure Data Lake Storage Gen2](abfs-driver.md)
* [Introdução ao Azure Data Lake Storage](introduction.md)
* [Configure clusters HDInsight usando o Azure Data Lake Storage com Hadoop, Spark, Kafka e mais](quickstart-create-connect-hdi-cluster.md)
* [Ingira dados no Azure Data Lake Storage usando distcp](use-distcp.md)

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: ../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/use-hdi-cluster/HDI.PowerShell.BlobCommands.png
