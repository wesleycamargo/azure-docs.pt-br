<properties linkid="manage-services-hdinsight-howto-blob-store" urlDisplayName="Blob Storage with  Hadoop in HDInsight" pageTitle="Use Blob storage with Hadoop in HDInsight | Azure" metaKeywords="" description="Learn how HDInsight uses Blob storage as the underlying data store for HDFS and how you can query data from the store." metaCanonical="" services="storage,hdinsight" documentationCenter="" title="Use Azure Blob storage with Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Usar o armazenamento de Blob do Azure com o Hadoop no HDInsight

O Azure HDInsight oferece suporte ao HDFS (Sistema de Arquivos Distribuídos do Hadoop) e ao armazenamento de Blob do Azure para armazenar dados. O armazenamento de Blob é uma solução de armazenamento do Azure robusta, para fins gerais. O Armazenamento de Blob fornece uma interface HDFS completa para uma experiência perfeita permitindo que o conjunto completo de componentes do ecossistema do Hadoop opere (por padrão) diretamente nos dados. O armazenamento de Blob não é apenas uma solução de baixo custo, o armazenamento de Blob permite que os clusters HDInsight usados para computação sejam excluídos com segurança sem que ocorra perda de dados do usuário.

> [WACOM.NOTE] A sintaxe *asv://* não é suportada em clusters HDInsight versão 3.0 e não terá suporte em versões posteriores. Isso significa que haverá falha em todos os trabalhos enviados para cluster HDInsight versão 3.0 que usarem explicitamente a sintaxe “asv://”. Em vez disso, a sintaxe *wasb://* deve ser usada. Além disso, haverá falha nos trabalhos enviados para qualquer cluster HDInsight versão 3.0 criados com um metastore existente que contém referências explícitas aos recursos usando a sintaxe asv://. Esses metastores precisarão ser recriados usando o wasb:// para endereçar recursos.

> [WACOM.NOTE] Atualmente, o HDInsight oferece suporte apenas a blobs de bloco.

> [WACOM.NOTE]
> A maioria dos comandos de HDFS, como **ls**, **copyFromLocal**, **mkdir** e assim por diante, ainda funciona conforme o esperado. Apenas os comandos específicos à implementação nativa do HDFS (que é conhecida como DFS), como **fschk** e **dfsadmin** mostrarão um comportamento diferente no armazenamento de Blob do Azure.

Para obter informações sobre como provisionar um cluster HDInsight, consulte [Introdução ao HDInsight][] ou [Provisionar clusters HDInsight][].

## Neste artigo

-   [Arquitetura de armazenamento do HDInsight][]
-   [Benefícios do armazenamento de Blob do Azure][]
-   [Preparar um contêiner para o armazenamento de Blob][]
-   [Arquivos de endereços no armazenamento de Blob][]
-   [Acessar blob usando o PowerShell][]
-   [Próximas etapas][]

## <span id="architecture"></span></a>Arquitetura de armazenamento do HDInsight

O diagrama a seguir fornece uma exibição abstrata da arquitetura de armazenamento do HDInsight:

![HDI.ASVArch][]

O HDInsight fornece acesso ao sistema de arquivos distribuídos que está anexado localmente aos nós de computação. Esse sistema de arquivos pode ser acessado usando o URI totalmente qualificado. Por exemplo:

    hdfs://<namenodehost>/<path>

Além disso, o HDInsight fornece a capacidade de acessar dados armazenados no armazenamento de Blob. A sintaxe para acessar o armazenamento de Blob é:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

O Hadoop oferece suporte a uma noção do sistema de arquivos padrão. O sistema de arquivos padrão significa um esquema e autoridade padrão. Ele também pode ser usado para resolver caminhos relativos. Durante o processo de provisionamento do HDInsight, uma conta de Armazenamento do Azure e um contêiner de armazenamento de Blob específico dessa conta são designados como o sistema de arquivos padrão.

Além dessa conta de armazenamento, você pode adicionar mais contas de armazenamento da mesma assinatura do Azure ou de diferentes assinaturas do Azure durante o processo de provisionamento. Para obter instruções sobre como adicionar mais contas de armazenamento, consulte [Provisionar clusters HDInsight][].

-   **Contêineres nas contas de armazenamento que estão conectadas a um cluster:** como o nome e a chave da conta são armazenados no *core-site.xml*, você tem acesso total aos blobs nesses contêineres.
-   **Contêineres públicos ou blobs públicos nas contas de armazenamento que NÃO estão conectadas a um cluster:** você tem permissão somente leitura para os blobs nos contêineres.

    > [WACOM.NOTE]
    >  \> Um contêiner público permite obter uma lista de todos os blobs disponíveis no contêiner e obter metadados do contêiner. Um blob público permite acessar os blobs somente se você souber a URL exata. Para obter mais informações, consulte [Restringir o acesso a contêineres e Blobs (a página pode estar em inglês)][].

-   **Contêineres privados nas contas de armazenamento que NÃO estão conectadas a um cluster:** não é possível acessar os blobs nos contêineres, a menos que você defina a conta de armazenamento quando enviar os trabalhos do WebHCat. Isso será explicado mais adiante neste artigo.

As contas de armazenamento definidas no processo de provisionamento e suas chaves são armazenadas em %HADOOP\_HOME%/conf/core-site.xml. O comportamento padrão do HDInsight é usar as contas de armazenamento definidas no arquivo core-site.xml. Não é recomendável editar o arquivo core-site.xml porque o headnode do cluster (mestre) poderá ter outra imagem criada ou ser migrado em qualquer momento, sendo que as alterações realizadas a esses arquivos serão perdidas.

Vários trabalhos do WebHCat, incluindo Hive, MapReduce, streaming de Hadoop e Pig, podem conter uma descrição de contas de armazenamento e metadados (normalmente funciona para Pig com contas de armazenamento, mas não para metadados). Na seção [Acessar blob usando o PowerShell][] deste artigo, há uma amostra desse recurso. Para obter mais informações, consulte [Usando um Cluster HDInsight com metastores e contas de armazenamento alternativas][].

Os contêineres de armazenamento de blob armazenam dados como pares de chave/valor, e não há nenhuma hierarquia de diretório. No entanto, o caractere "/" pode ser usado dentro do nome de chave para parecer que um arquivo está armazenado em uma estrutura de diretório. Por exemplo, a chave de um blob pode ser *input/log1.txt*. Não existe nenhum diretório de *entrada* real, mas devido à presença do caractere "/" no nome da chave, ele parece um caminho de arquivo.

## <span id="benefits"></span></a>Benefícios do armazenamento de Blob do Azure

O custo de desempenho implícito de não ter computação e armazenamento colocalizados é reduzido pela forma como os clusters de computação são provisionados próximos aos recursos da conta de armazenamento no datacenter do Azure, onde a rede de alta velocidade torna os nós de computação muito eficientes para acessarem os dados no armazenamento de Blob.

Há vários benefícios associados ao armazenamento de dados no armazenamento de Blob em vez de no HDFS:

-   **Compartilhamento e reutilização de dados:** os dados no HDFS estão localizados dentro do cluster de computação. Apenas os aplicativos que têm acesso ao cluster de computação podem usar os dados usando a API HDFS. Os dados no armazenamento de Blob podem ser acessados por meio de APIs HDFS ou por meio de [APIs REST do armazenamento de Blob (a página pode estar em inglês)][]. Assim, um conjunto maior de aplicativos (incluindo outros clusters HDInsight) e ferramentas podem ser usados para produzir e consumir os dados.
-   **Arquivamento de dados:** o armazenamento de dados no armazenamento de Blob permite que os clusters usados para cálculo sejam excluídos com segurança sem que ocorra perda de dados do usuário.
-   **Custo do armazenamento de dados:** o armazenamento de dados no DFS por longo prazo é mais caro do que o armazenamento de dados no armazenamento de Blob, uma vez que o custo de um cluster de computação é mais alto do que o custo de um contêiner de armazenamento de Blob. Além disso, como os dados não precisam ser recarregados para cada geração de cluster de computação, você está economizando em custos de carregamento de dados.
-   **Expansão elástica:** embora o HDFS forneça um sistema de arquivos de expansão, a escala é determinada pelo número de nós que você provisiona para seu cluster. A alteração da escala pode se tornar um processo mais complicado do que depender dos recursos de dimensionamento elástico do armazenamento de Blob que você obtém automaticamente.
-   **Replicação Geográfica:** seus contêineres de armazenamento de Blob podem ser replicados geograficamente pelo Portal do Azure. Embora isso forneça redundância de dados e recuperação geográfica, um failover para o local replicado geograficamente afetará seriamente o desempenho e poderá incorrer em custos adicionais. Portanto, nossa recomendação é escolher a replicação geográfica com sabedoria e somente se o valor dos dados compensar o custo adicional.

Determinados trabalhos e pacotes MapReduce podem criar resultados intermediários que você não deseja realmente armazenar no contêiner de armazenamento de Blob. Nesse caso, você ainda pode optar por armazenar os dados no HDFS local. Na verdade, o HDInsight usa o DFS para vários desses resultados intermediários em trabalhos Hive e outros processos.

## <span id="preparingblobstorage"></span></a>Preparar um contêiner para o armazenamento de Blob

Para usar blobs, primeiro você deve criar uma [conta de armazenamento do Azure][]. Como parte desse processo, você deve especificar um datacenter do Azure para armazenar os objetos criados usando essa conta. O cluster e a conta de armazenamento devem ser hospedados no mesmo data center (o banco de dados SQL do metastore Hive e o banco de dados SQL do metastore Oozie também devem estar localizados no mesmo data center). Onde quer que eles estejam, cada blob que você criar pertence a um contêiner na sua conta de armazenamento. Esse contêiner pode ser um contêiner de armazenamento de Blob existente criado fora do HDInsight, ou pode ser um contêiner criado para um cluster HDInsight.

### Criar um contêiner de Blob para HDInsight usando o Portal de Gerenciamento

Ao provisionar um cluster HDInsight no Portal de Gerenciamento do Azure, há duas opções: *criação rápida* e *criação personalizada*. A opção de criação rápida exige que a conta de Armazenamento do Azure já tenha sido criado. Para obter instruções, consulte [Como criar uma conta de armazenamento][conta de armazenamento do Azure].

Usando a opção de criação rápida, você pode escolher uma conta de armazenamento existente. O processo de provisionamento cria um novo contêiner com o mesmo nome que o nome do cluster HDInsight. Se já existir um contêiner com o mesmo nome, <clustername>-<x> será utilizado. Por exemplo, myHDIcluster-1. Esse contêiner é usado como o sistema de arquivos padrão.

![HDI.QuickCreate][]

Ao usar a criação personalizada, você tem uma das opções a seguir para a conta de armazenamento padrão:

-   Usar armazenamento existente
-   Criar novo armazenamento
-   Usar armazenamento de outra assinatura.

Você também tem a opção de criar seu próprio contêiner de Blob ou usar outro já existente.

![HDI.CustomCreateStorageAccount][]

### Criar um contêiner usando o PowerShell do Azure.

[O PowerShell do Azure][] pode ser usado para criar contêineres de Blob. O seguinte é um exemplo de script do PowerShell:

    $subscriptionName = "<SubscriptionName>"    # Azure subscription name
    $storageAccountName = "<AzureStorageAccountName>" # The storage account that you will create
    $containerName="<BlobContainerToBeCreated>" # The Blob container name that you will create

    # Connect to your Azure account and selec the current subscription
    Add-AzureAccount # The connection will expire in a few hours.
    Select-AzureSubscription $subscriptionName #only required if you have multiple subscriptions

    # Create a storage context object
    $storageAccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    # Create a Blob storage container
    New-AzureStorageContainer -Name $containerName -Context $destContext 

## <span id="addressing"></span></a>Arquivos de endereços no armazenamento de Blob

O esquema de URI para acessar arquivos no armazenamento de Blob é:

    wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>

> [WACOM.NOTE] A sintaxe para endereçamento de arquivos no emulador de armazenamento (em execução no emulador do HDInsight) é <i>wasb://\<ContainerName\>@storageemulator<i>.

O esquema de URI fornece acesso sem criptografia com o prefixo *wasb:* e acesso criptografado SSL com *wasbs*. É recomendável usar *wasbs* sempre que possível, mesmo ao acessar dados que residem dentro do mesmo datacenter do Azure.

O \<BlobStorageContainerName\> identifica o nome do contêiner de armazenamento de Blob.
O \<StorageAccountName\> identifica o nome da conta de armazenamento do Azure. Um FQDN (nome de domínio totalmente qualificado) é necessário.

Se nem \<BlobStorageContainerName\> nem \<StorageAccountName\> for especificado, o sistema de arquivos padrão será usado. Para os arquivos no sistema de arquivos padrão, você pode usar um caminho absoluto ou um caminho relativo. Por exemplo, o arquivo hadoop-mapreduce-examples.jar fornecido com clusters HDInsight pode ser referenciado para usar um dos seguintes procedimentos:

    wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
    wasb:///example/jars/hadoop-mapreduce-examples.jar
    /example/jars/hadoop-mapreduce-examples.jar

> [WACOM.NOTE] O nome do arquivo é *hadoop-examples.jar* nos clusters HDInsight versão 1.6 e 2.1.

O \<path\> é o nome do caminho do HDFS do arquivo ou do diretório. Como os contêineres de armazenamento de Blob são apenas um repositório de chave-valor, não há nenhum sistema de arquivos hierárquico verdadeiro. Um "/" dentro de uma chave de blob é interpretado como um separador de diretório. Por exemplo, o nome do blob para *hadoop-mapreduce-examples.jar* é:

    example/jars/hadoop-mapreduce-examples.jar

## <span id="powershell"></span></a>Acessar blob usando o PowerShell do Azure

Consulte [Instalar e configurar o PowerShell do Azure][O PowerShell do Azure] para obter informações sobre como instalar e configurar o PowerShell do Azure em sua estação de trabalho. Você pode usar a janela do console do PowerShell do Azure ou o PowerShell\_ISE para executar cmdlets do PowerShell.

Use o comando a seguir para listar os cmdlets relacionados ao blob:

    Get-Command *blob*

![Blob.PowerShell.cmdlets][]

**Exemplo do PowerShell para carregamento de um arquivo**

Consulte [Carregar dados no HDInsight][].

**Exemplo do PowerShell para download de um arquivo**

O script a seguir baixa blob de blocos na pasta atual. Antes de executar o script, altere o diretório de uma pasta na qual você tenha permissão de gravação.

    $storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.

    # Use Add-AzureAccount if you haven't connected to your Azure subscription
    #Add-AzureAccount # The connection is good for 12 hours

    # Use these two commands if you have multiple subscriptions
    #$subscriptionName = "<SubscriptionName>"       
    #Select-AzureSubscription $subscriptionName

    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force

    Write-Host "List the downloaded file ..." -ForegroundColor Green
    cat "./$blob"

**Exemplo do PowerShell para exclusão de um arquivo**

O script a seguir mostra como excluir uma tabela.
 $storageAccountName = "<azurestorageaccountname>" \# A Conta de armazenamento do Azure usada para o sistema de arquivos padrão especificado na provisão.
 $containerName = "<blobstoragecontainername>" \# O contêiner do sistema de arquivos padrão tem o mesmo nome que o cluster.
 $blob = "example/data/sample.log" \# O nome do Blob a ser baixado.

    # Use Add-AzureAccount if you haven't connected to your Azure subscription
    #Add-AzureAccount # The connection is good for 12 hours

    # Use these two commands if you have multiple subscriptions
    #$subscriptionName = "<SubscriptionName>"       
    #Select-AzureSubscription $subscriptionName

    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Write-Host "Delete the blob ..." -ForegroundColor Green
    Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob 

**Exemplo do PowerShell para listagem dos arquivos de uma pasta**

O script a seguir mostra como listar arquivos dentro de uma “pasta". O exemplo a seguir mostra como usar o cmdlet Invoke-Hive para executar o comando dfs ls para uma lista ou pasta.

    $storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blobPrefix = "example/data/"

    # Use Add-AzureAccount if you haven't connected to your Azure subscription
    #Add-AzureAccount # The connection is good for 12 hours

    # Use these two commands if you have multiple subscriptions
    #$subscriptionName = "<SubscriptionName>"       
    #Select-AzureSubscription $subscriptionName

    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Write-Host "List the files in $blobPrefix ..."
    Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix $blobPrefix

**Exemplo do PowerShell para acesso a uma conta de armazenamento não definida**

Este mostra como listar uma pasta por meio de uma conta de armazenamento que não é definida durante o processo de provisionamento.
 $clusterName = "<hdinsightclustername>"

    $undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
    $undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

    $undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }

    Use-AzureHDInsightCluster $clusterName

    $defines = @{}
    $defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

    Invoke-Hive -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"

## <span id="nextsteps"></span></a>Próximas etapas

Neste artigo, você aprendeu a usar o armazenamento de Blob com o HDInsight, e que esse armazenamento de Blob é um componente fundamental do HDInsight. Isso permite que você crie soluções de aquisição de dados para arquivamento de longo prazo com o armazenamento de Blob do Azure e use o HDInsight para desbloquear as informações nos dados armazenados.

Para saber mais, consulte os seguintes artigos:

-   [Introdução ao Azure HDInsight][Introdução ao HDInsight]
-   [Carregar dados no HDInsight][]
-   [Use o hive com o HDInsight][]
-   [Use o Pig com o HDInsight][]

  [Introdução ao HDInsight]: ../hdinsight-get-started/
  [Provisionar clusters HDInsight]: ../hdinsight-provision-clusters/
  [Arquitetura de armazenamento do HDInsight]: #architecture
  [Benefícios do armazenamento de Blob do Azure]: #benefits
  [Preparar um contêiner para o armazenamento de Blob]: #preparingblobstorage
  [Arquivos de endereços no armazenamento de Blob]: #addressing
  [Acessar blob usando o PowerShell]: #powershell
  [Próximas etapas]: #nextsteps
  [HDI.ASVArch]: ./media/hdinsight-use-blob-storage/HDI.ASVArch.png "Arquitetura de armazenamento do HDInsight"
  [Restringir o acesso a contêineres e Blobs (a página pode estar em inglês)]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179354.aspx
  [Usando um Cluster HDInsight com metastores e contas de armazenamento alternativas]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
  [APIs REST do armazenamento de Blob (a página pode estar em inglês)]: http://msdn.microsoft.com/en-us/library/windowsazure/dd135733.aspx
  [conta de armazenamento do Azure]: ../storage-create-storage-account/
  [HDI.QuickCreate]: ./media/hdinsight-use-blob-storage/HDI.QuickCreateCluster.png
  [HDI.CustomCreateStorageAccount]: ./media/hdinsight-use-blob-storage/HDI.CustomCreateStorageAccount.png
  [O PowerShell do Azure]: ../install-configure-powershell/
  [//\<ContainerName\>@storageemulator]: mailto://<ContainerName>@storageemulator
  [Blob.PowerShell.cmdlets]: ./media/hdinsight-use-blob-storage/HDI.PowerShell.BlobCommands.png
  [Carregar dados no HDInsight]: ../hdinsight-upload-data/
  [Use o hive com o HDInsight]: ../hdinsight-use-hive/
  [Use o Pig com o HDInsight]: ../hdinsight-use-pig/
