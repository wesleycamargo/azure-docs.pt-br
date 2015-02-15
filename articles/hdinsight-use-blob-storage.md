<properties 
	pageTitle="Consultar big data de armazenamento de blob compatível com Hadoop | Azure" 
	description="O HDInsight usa armazenamento de Blob compatível com Hadoop como armazenamento de big data para HDFS. Aprenda a consultar do armazenamento de Blob e armazenar os resultados da análise." 
	services="storage, hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="mollybos"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/12/2014" 
	ms.author="jgao"/>


# Consultar big data de armazenamento de blob compatível com Hadoop para análise no HDInsight

Armazenamento de blob de baixo custo é uma solução de armazenamento do Azure robusto, de propósito geral e compatível com Hadoop, que se integra perfeitamente com o HDInsight. Através de uma interface HDFS (Sistema de Arquivos Distribuído Hadoop), o conjunto completo de componentes em HDInsight pode operar diretamente sobre os dados do armazenamento de blob. Neste tutorial, aprenda a criar um contêiner para armazenamento de blob, e, em seguida, trate os dados dentro.

O armazenamento de dados no armazenamento de blob permite que os clusters HDInsight usados para cálculo sejam excluídos com segurança sem que ocorra perda de dados do usuário. 

> [AZURE.NOTE]	A sintaxe *asv://* não é suportada em clusters HDInsight versão 3.0 e não terá suporte em versões posteriores. Isso significa que haverá falha em todos os trabalhos enviados para o cluster HDInsight versão 3.0 que usarem explicitamente a sintaxe "asv://". Em vez disso, a sintaxe *wasb://* deve ser usada. Além disso, haverá falha nos trabalhos enviados para qualquer cluster HDInsight versão 3.0 criados com um metastore existente que contém referências explícitas aos recursos usando a sintaxe asv://. Esses metastores precisarão ser recriados usando o wasb:// para endereçar recursos.

> Atualmente, o HDInsight dá suporte apenas a blobs de bloco.

> A maioria dos comandos HDFS, como <b>ls</b>, <b>copyFromLocal</b>, <b>mkdir</b> e assim por diante, ainda funciona conforme o esperado. Apenas os comandos específicos à implementação nativa do HDFS (que é conhecida como DFS), como <b>fschk</b> e <b>dfsadmin</b> mostrarão um comportamento diferente no armazenamento de Blob do Azure.

Para obter informações sobre como provisionar um cluster HDInsight, consulte [Introdução ao HDInsight][hdinsight-get-started] ou [Provisionar clusters HDInsight][hdinsight-provision].

## Neste artigo

* [Arquitetura de armazenamento do HDInsight](#architecture)
* [Benefícios do armazenamento de Blob do Azure](#benefits)
* [Preparar um contêiner para o armazenamento de Blob](#preparingblobstorage)
* [Arquivos de endereços no armazenamento de Blob](#addressing)
* [Acessar blob usando o PowerShell](#powershell)
* [Próximas etapas](#nextsteps)

## <a id="architecture"></a>Arquitetura de armazenamento do HDInsight
O diagrama a seguir fornece uma exibição abstrata da arquitetura de armazenamento do HDInsight:

![Hadoop clusters in HDInsight access and store big data in cost-effective, scalable Hadoop-compatible Azure Blob storage in the cloud.](./media/hdinsight-use-blob-storage/HDI.ASVArch.png "HDInsight Storage Architecture")
  
O HDInsight fornece acesso ao sistema de arquivos distribuído que está anexado localmente aos nós de computação. Esse sistema de arquivos pode ser acessado usando o URI totalmente qualificado. Por exemplo: 

	hdfs://<namenodehost>/<path>

Além disso, o HDInsight fornece a capacidade de acessar dados armazenados no armazenamento de Blob. A sintaxe para acessar o armazenamento de blob é:

	wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>


O Hadoop dá suporte a uma noção do sistema de arquivos padrão. O sistema de arquivos padrão significa um esquema e autoridade padrão. Ele também pode ser usado para resolver caminhos relativos. Durante o processo de provisionamento do HDInsight, uma conta de Armazenamento do Azure e um contêiner de armazenamento de Blob específico dessa conta são designados como o sistema de arquivos padrão.

Além dessa conta de armazenamento, você pode adicionar mais contas de armazenamento da mesma assinatura do Azure ou de diferentes assinaturas do Azure durante o processo de provisionamento. Para obter instruções sobre como adicionar mais contas de armazenamento, consulte [Provisionar clusters HDInsight][hdinsight-provision]. 

- **Contêineres nas contas de armazenamento que estão conectadas a um  cluster:** Como o nome e a chave da conta são armazenados no *core-site.xml*, você tem acesso completo aos blobs nesses contêineres.
- **Contêineres públicos ou blobs públicos nas contas de armazenamento que NÃO estão conectadas a um cluster:** você tem permissão somente leitura para os blobs nos contêineres.

	> [AZURE.NOTE]
        > Um contêiner público permite obter uma lista de todos os blobs disponíveis nesse contêiner e obter metadados do contêiner. Um blob público permite  acessar os blobs somente se você souber a URL exata. Para obter mais informações, consulte <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/dd179354.aspx">Restringir o acesso aos contêineres e blobs</a>.

- **Contêineres privados nas contas de armazenamento que NÃO estão conectados a um cluster:** não é possível acessar os blobs nos contêineres, a menos que você defina a conta de armazenamento quando você envia os trabalhos WebHCat. Isso é explicado posteriormente neste artigo.


As contas de armazenamento definidas no processo de provisionamento e suas chaves são armazenadas em % HADOOP_HOME%/conf/core-site.xml.  O comportamento padrão do HDInsight é usar as contas de armazenamento definidas no arquivo core-site.xml. Não é recomendável editar o arquivo core-site.xml porque o headnode do cluster (mestre) poderá ter outra imagem criada ou ser migrado em qualquer momento, sendo que as alterações realizadas a esses arquivos serão perdidas.

Vários trabalhos do WebHCat, incluindo Hive, MapReduce, streaming de Hadoop e Pig, podem conter uma descrição de contas de armazenamento e metadados (normalmente funciona para Pig com contas de armazenamento, mas não para metadados). Na seção [Acessar blob usando o PowerShell](#powershell) deste artigo, há um exemplo desse recurso. Para obter mais informações, consulte [Usando um Cluster HDInsight com metastores e contas de armazenamento alternativas](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Os contêineres de armazenamento de blob armazenam dados como pares de chave/valor, e não há nenhuma hierarquia de diretório. No entanto, o caractere "/" pode ser usado dentro do nome de chave para parecer que um arquivo está armazenado em uma estrutura de diretório. Por exemplo, a chave de um blob pode ser *input/log1.txt*. Não existe nenhum diretório de *input* real, mas devido à presença do caractere "/" no nome da chave, ele parece um caminho de arquivo.










## <a id="benefits"></a>Benefícios do armazenamento de Blob do Azure
O custo de desempenho implícito de não ter computação e armazenamento colocalizados é reduzido pela forma como os clusters de computação são provisionados próximos aos recursos da conta de armazenamento no datacenter do Azure, onde a rede de alta velocidade torna os nós de computação muito eficientes para acessarem os dados no armazenamento de Blob.

Há vários benefícios associados ao armazenamento de dados no armazenamento de Blob em vez de no HDFS:

* **Compartilhamento e reutilização de dados:** os dados no HDFS estão localizados dentro do cluster de computação. Somente os aplicativos que têm acesso ao cluster de computação podem usar os dados usando a API HDFS. Os dados no armazenamento de Blob podem ser acessados por meio de APIs HDFS ou por meio de [APIs de REST do armazenamento de Blob][blob-storage-restAPI]. Assim, um conjunto maior de aplicativos (incluindo outros clusters HDInsight) e ferramentas pode ser usado para produzir e consumir os dados.
* **Arquivamento de dados:** o armazenamento de dados no armazenamento de Blob permite que os clusters HDInsight usados para cálculo sejam excluídos com segurança sem que ocorra perda de dados do usuário. 
* **Custo de armazenamento de dados:** armazenar dados no DFS a longo prazo é mais caro do que o armazenamento de dados no armazenamento de Blob, uma vez que o custo de um cluster de computação é maior do que o custo de um contêiner de armazenamento de Blob. Além disso, já que os dados não precisam ser recarregados para cada geração de cluster de computação, você está economizando em custos de carregamento de dados.
* **Expansão elástica:** enquanto o HDFS fornece um sistema de arquivos expandido, a escala é determinada pelo número de nós que você provisiona para seu cluster. A alteração da escala pode se tornar um processo mais complicado do que contar com recursos de dimensionamento elástico do armazenamento de Blob que você obtém automaticamente.
* **Replicação geográfica:** os contêineres de armazenamento de Blob podem ser replicados geograficamente por meio do Portal do Azure. Embora isso forneça redundância de dados e recuperação geográfica, um failover para o local replicado geograficamente afetará drasticamente o desempenho e pode incorrer em custos adicionais. Portanto, nossa recomendação é escolher a replicação geográfica com sabedoria e somente se o valor dos dados compensar o custo adicional.

Determinados trabalhos e pacotes MapReduce podem criar resultados intermediários que você não deseja realmente armazenar no contêiner de armazenamento de Blob. Nesse caso, você ainda pode optar por armazenar os dados no HDFS local. Na verdade, o HDInsight usa o DFS para vários desses resultados intermediários em trabalhos Hive e outros processos. 





## <a id="preparingblobstorage"></a>Preparar um contêiner para o armazenamento de Blob
Para usar blobs, primeiro você deve criar uma [Conta de armazenamento do Azure][azure-storage-create]. Como parte desse processo, você deve especificar um datacenter do Azure para armazenar os objetos criados usando essa conta. O cluster e a conta de armazenamento devem ser hospedados no mesmo data center (o banco de dados SQL do metastore Hive e o banco de dados SQL do metastore Oozie também devem estar localizados no mesmo data center). Onde quer que eles estejam, cada blob que você criar pertence a um contêiner na sua conta de armazenamento. Esse contêiner pode ser um contêiner de armazenamento de Blob existente criado fora do HDInsight, ou pode ser um contêiner criado para um cluster HDInsight. 



### Criar um contêiner de Blob para HDInsight usando o Portal de Gerenciamento

Ao provisionar um cluster HDInsight no Portal de Gerenciamento do Azure, há duas opções: *quick create* e *custom create*. A opção de criação rápida exige que a conta de Armazenamento do Azure já tenha sido criada.  Para obter instruções, consulte [Como criar uma conta de armazenamento][azure-storage-create]. 

Usando a opção de criação rápida, você pode escolher uma conta de armazenamento existente. O processo de provisionamento cria um novo contêiner com o mesmo nome que o nome do cluster HDInsight. Se já existir um contêiner com o mesmo nome, <clusterName>-<x> será utilizado. Por exemplo, myHDIcluster-1. Esse contêiner é usado como o sistema de arquivos padrão.

![Using Quick Create for a new Hadoop cluster in HDInsight in the Azure portal.][img-hdi-quick-create]
 
Ao usar a criação personalizada, você tem uma das opções a seguir para a conta de armazenamento padrão:

- Usar armazenamento existente
- Criar novo armazenamento
- Usar armazenamento de outra assinatura.

Você também tem a opção de criar seu próprio contêiner de Blob ou usar outro já existente.
 
![Option to use an existing storage account for your HDInsight cluster.][img-hdi-custom-create-storage-account]
  




### Criar um contêiner usando o PowerShell do Azure.
[O PowerShell do Azure][powershell-install] pode ser usado para criar contêineres de Blob. O seguinte é um exemplo de script do PowerShell:

	$subscriptionName = "<SubscriptionName>"	# Azure subscription name
	$storageAccountName = "<AzureStorageAccountName>" # The storage account that you will create
	$containerName="<BlobContainerToBeCreated>" # The Blob container name that you will create

	# Connect to your Azure account and selec the current subscription
	Add-AzureAccount # The connection will expire in 12 hours.
	Select-AzureSubscription $subscriptionName #only required if you have multiple subscriptions
	
	# Create a storage context object
	$storageAccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext 


## <a id="addressing"></a>Arquivos de endereços no armazenamento de blob

O esquema de URI para acessar arquivos no armazenamento de Blob é: 

	wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


> [AZURE.NOTE] A sintaxe para endereçamento de arquivos no emulador de armazenamento (em execução no emulador do HDInsight) é <i>wasb://&lt;ContainerName&gt;@storageemulator</i>.



O esquema de URI fornece acesso descriptografado com o prefixo *wasb:* e acesso criptografado SSL com *wasbs*. É recomendável usar *wasbs* sempre que possível, mesmo ao acessar dados que residem dentro do mesmo datacenter do Azure.
	
O &lt;BlobStorageContainerName&gt; identifica o nome do contêiner de armazenamento de Blob.
O &lt;StorageAccountName&gt; identifica o nome da conta de armazenamento do Azure. Um FQDN (nome de domínio totalmente qualificado) é necessário.
	
Se nem &lt;BlobStorageContainerName&gt; nem &lt;StorageAccountName&gt; forem especificados, o sistema de arquivos padrão será usado. Para os arquivos no sistema de arquivos padrão, você pode usar um caminho absoluto ou um caminho relativo. Por exemplo, o arquivo hadoop-mapreduce-examples.jar fornecido com clusters HDInsight pode ser referenciado para usar um dos seguintes procedimentos:

	wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
	wasb:///example/jars/hadoop-mapreduce-examples.jar
	/example/jars/hadoop-mapreduce-examples.jar
	
> [AZURE.NOTE] O nome do arquivo é <i>hadoop-examples.jar</i> nos clusters HDInsight versão 1.6 e 2.1.


O &lt;caminho&gt; é o nome do caminho HDFS do arquivo ou do diretório. Como os contêineres de armazenamento de Blob são apenas um repositório de chave-valor, não há nenhum sistema de arquivos hierárquico verdadeiro. Um "/" dentro de uma chave de blob é interpretado como um separador de diretório. Por exemplo, o nome do blob para *hadoop-mapreduce-examples.jar* é:

	example/jars/hadoop-mapreduce-examples.jar
	

## <a id="powershell"></a>Acessar blob usando o PowerShell do Azure

Consulte [Instalar e configurar o PowerShell do Azure][powershell-install] para obter informações sobre como instalar e configurar o PowerShell do Azure em sua estação de trabalho. Você pode usar a janela do console do PowerShell do Azure ou o PowerShell_ISE para executar cmdlets do PowerShell. 

Use o comando a seguir para listar os cmdlets relacionados ao blob:

	Get-Command *blob*

![List of Blob-related PowerShell cmdlets.][img-hdi-powershell-blobcommands]


**Exemplo do PowerShell para carregamento de um arquivo**

Consulte [Carregar dados no HDInsight][hdinsight-upload-data].

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

O script a seguir mostra como excluir um arquivo.
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
	
	Write-Host "Delete the blob ..." -ForegroundColor Green
	Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob 
	

**Exemplo do PowerShell para listagem dos arquivos de uma pasta**

O script a seguir mostra como listar arquivos dentro de uma "pasta". O exemplo a seguir mostra como usar o cmdlet Invoke-Hive para executar o comando dfs ls para uma lista ou pasta.

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
	
Este exemplo mostra como listar uma pasta por meio de uma conta de armazenamento que não é definida durante o processo de provisionamento.
	$clusterName = "<HDInsightClusterName>"
	
	$undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
	$undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"
	
	$undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }
	
	Use-AzureHDInsightCluster $clusterName
	
	$defines = @{}
	$defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

	Invoke-Hive -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"
 
## <a id="nextsteps"></a>Próximas etapas

Neste artigo, você aprendeu a usar o armazenamento de Blob com o HDInsight, e que esse armazenamento de Blob é um componente fundamental do HDInsight. Isso permite que você crie soluções de aquisição de dados para arquivamento de longo prazo com o armazenamento de Blob do Azure e use o HDInsight para desbloquear as informações nos dados armazenados.

Para saber mais, consulte os seguintes artigos:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Carregar dados no HDInsight][hdinsight-upload-data]
* [Usar o hive com o HDInsight][hdinsight-use-hive]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]

[Powershell-install]: ../install-configure-powershell/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/

[Powershell-install]: ../install-configure-powershell/
[blob-storage-restAPI]: http://msdn.microsoft.com/pt-br/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage-create-storage-account/

[img-hdi-powershell-blobcommands]: ./media/hdinsight-use-blob-storage/HDI.PowerShell.BlobCommands.png 
[img-hdi-quick-create]: ./media/hdinsight-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-use-blob-storage/HDI.CustomCreateStorageAccount.png  

<!--HONumber=42-->
