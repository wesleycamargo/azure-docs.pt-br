<properties linkid="manage-services-hdinsight-howto-blob-store" urlDisplayName="Armazenamento de Blob com o HDInsight" pageTitle="Usar o Armazenamento de Blob com o HDInsight | Azure" metaKeywords="" description="Saiba como o HDInsight usa o Armazenamento de Blob como repositório de dados subjacente para HDFS e como é possível consultar dados no repositório." metaCanonical="" services="storage,hdinsight" documentationCenter="" title="Usar o Armazenamento de Blob do Azure com o HDInsight" authors=""  solutions="" writer="jgao" manager="paulettm" editor="mollybos"  />




#Usar o armazenamento de Blob do Azure com o HDInsight




O Azure HDInsight oferece suporte ao HDFS (Sistema de Arquivos Distribuído) do Hadoop e ao Armazenamento de Blob do Azure para armazenar dados. O armazenamento de Blob é uma solução robusta de armazenamento do Azure para fins gerais. O Armazenamento de Blob fornece uma interface HDFS completa para uma experiência perfeita permitindo que o conjunto completo de componentes do ecossistema do Hadoop opere (por padrão) diretamente nos dados. O armazenamento de Blob não é apenas uma solução de baixo custo, o armazenamento de Blob permite que os clusters HDInsight usados para computação sejam excluídos com segurança sem que ocorra perda de dados do usuário. 

> [WACOM.NOTE]	A sintaxe *asv://* não é suportada em clusters HDInsight versão 3.0 e não terá suporte em versões posteriores. Isso significa que haverá falha em todos os trabalhos enviados para cluster HDInsight versão 3.0 que usem explicitamente a sintaxe "asv://". Em vez disso, a sintaxe *wasb://* deve ser usada. Além disso, haverá falha nos trabalhos enviados para qualquer cluster HDInsight versão 3.0 criados com um metastore existente que contém referências explícitas aos recursos usando a sintaxe asv://. Esses metastores precisarão ser recriados usando o wasb:// para endereçar recursos.

> [WACOM.NOTE] Atualmente, o HDInsight oferece suporte apenas a blobs de bloco.

> [WACOM.NOTE]
> A maioria dos comandos HDFS, como <b>ls</b>, <b>copyFromLocal</b>, <b>mkdir</b> e assim por diante, ainda funciona conforme o esperado. Apenas os comandos que são específicos à implementação nativa do HDFS (que é conhecida como DFS), como <b>fschk</b> e <b>dfsadmin</b>, mostrarão um comportamento diferente no armazenamento de Blob do Azure.

Para obter informações sobre como provisionar um cluster HDInsight, consulte [Introdução ao HDInsight][hdinsight-getting-started] ou [Provisionar clusters HDInsight][hdinsight-provision].

##Neste artigo

* [Arquitetura de armazenamento do HDInsight](#architecture)
* [Benefícios do Armazenamento de Blob do Azure](#benefits)
* [Preparar um contêiner para o armazenamento de Blob](#preparingblobstorage)
* [Arquivos de endereços no armazenamento de Blob](#addressing)
* [Acessar blob usando o PowerShell](#powershell)
* [Próximas etapas](#nextsteps)

##<a id="architecture"></a>Arquitetura de armazenamento do HDInsight
O diagrama a seguir fornece uma exibição abstrata da arquitetura de armazenamento do HDInsight:

![HDI.ASVArch](./media/hdinsight-use-blob-storage/HDI.ASVArch.png "HDInsight Storage Architecture")
  
O HDInsight fornece acesso ao sistema de arquivos distribuído que está anexado localmente aos nós de computação. Esse sistema de arquivos pode ser acessado usando o URI totalmente qualificado. Por exemplo: 

	hdfs://<namenodehost>/<path>

Além disso, o HDInsight fornece a capacidade de acessar dados armazenados no armazenamento de Blob. A sintaxe para acessar o armazenamento de Blob é:

	wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>


O Hadoop oferece suporte a uma noção do sistema de arquivos padrão. O sistema de arquivos padrão significa um esquema e autoridade padrão. Ele também pode ser usado para resolver caminhos relativos. Durante o processo de provisionamento do HDInsight, uma conta de Armazenamento do Azure e um contêiner de armazenamento de Blob específico dessa conta é designado como o sistema de arquivos padrão.

Além dessa conta de armazenamento, você pode adicionar mais contas de armazenamento da mesma assinatura do Azure ou de diferentes assinaturas do Azure durante o processo de provisionamento. Para obter instruções sobre como adicionar mais contas de armazenamento, consulte [Provisionar clusters HDInsight][hdinsight-provision]. 

* **Contêineres nas contas de armazenamento que estão conectadas a um cluster:** como o nome e a chave da conta são armazenados no *core-site.xml*, você tem acesso total aos blobs nesses contêineres.
* **Contêineres públicos ou blobs públicos nas contas de armazenamento que NÃO estão conectadas a um cluster:** você tem permissão somente leitura para os blobs nos contêineres.

	> [WACOM.NOTE]
        > Um contêiner público permite obter uma lista de todos os blobs disponíveis nesse contêiner e obter metadados do contêiner. Um blob público permite acessar os blobs somente se você souber a URL exata. Para obter mais informações, consulte <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/dd179354.aspx">Restringir o acesso a contêineres e Blobs (a página pode estar em inglês)</a>.

* **Contêineres privados nas contas de armazenamento que NÃO estão conectadas a um cluster:** não é possível acessar os blobs nos contêineres.


Os contêineres de armazenamento de blob armazenam dados como pares de chave/valor, e não há nenhuma hierarquia de diretório. No entanto, o caractere "/" pode ser usado dentro do nome de chave para parecer que um arquivo está armazenado em uma estrutura de diretório. Por exemplo, a chave de um blob pode ser *input/log1.txt*. Não existe nenhum diretório de *entrada* real, mas devido à presença do caractere "/" no nome da chave, ele parece um caminho de arquivo.










##<a id="benefits"></a>Benefícios do Armazenamento de Blob do Azure
O custo de desempenho implícito de não ter computação e armazenamento colocalizados é reduzido pela forma como os clusters de computação são provisionados próximos aos recursos da conta de armazenamento dentro do data center do Azure, onde a rede de alta velocidade torna os nós de computação muito eficientes para acessarem os dados dentro do armazenamento de Blob.

Há vários benefícios associados ao armazenamento de dados no armazenamento de Blob em vez de no HDFS:

* **Compartilhamento e reutilização de dados:** os dados no HDFS estão localizados dentro do cluster de computação. Apenas os aplicativos que têm acesso ao cluster de computação podem usar os dados usando a API HDFS. Os dados no armazenamento de Blob podem ser acessados por meio de APIs HDFS ou por meio de [APIs REST do armazenamento de Blob (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/dd135733.aspx). Assim, um conjunto maior de aplicativos (incluindo outros clusters HDInsight) e ferramentas podem ser usados para produzir e consumir os dados.
* **Arquivamento de dados:** o armazenamento de dados no armazenamento de Blob permite que os clusters HDInsight usados para cálculo sejam excluídos com segurança sem que ocorra perda de dados do usuário. 
* **Custo do armazenamento de dados:** o armazenamento de dados no DFS por longo prazo é mais caro do que o armazenamento de dados no armazenamento de Blob, uma vez que o custo de um cluster de computação é mais alto do que o custo de um contêiner de armazenamento de Blob. Além disso, como os dados não precisam ser recarregados para cada geração de cluster de computação, você está economizando em custos de carregamento de dados.
* **Expansão elástica:** embora o HDFS forneça um sistema de arquivos expandido, a escala é determinada pelo número de nós que você provisiona para seu cluster. A alteração da escala pode se tornar um processo mais complicado do que depender dos recursos de dimensionamento elástico do armazenamento de Blob que você obtém automaticamente.
* **Replicação Geográfica:** seus contêineres de armazenamento de Blob podem ser replicados geograficamente através do Portal do Azure. Embora isso forneça redundância de dados e recuperação geográfica, um failover para o local replicado geograficamente afetará seriamente o desempenho e poderá incorrer em custos adicionais. Portanto, nossa recomendação é escolher a replicação geográfica com sabedoria e somente se o valor dos dados compensar o custo adicional.

Determinados trabalhos e pacotes MapReduce podem criar resultados intermediários que você não deseja realmente armazenar no contêiner de armazenamento de Blob. Nesse caso, você ainda pode optar por armazenar os dados no HDFS local. Na verdade, o HDInsight usa o DFS para vários desses resultados intermediários em trabalhos Hive e outros processos. 





##<a id="preparingblobstorage"></a>Preparar um contêiner para armazenamento de Blob
Para usar blobs, primeiro você cria uma [Conta de Armazenamento do Azure](/pt-br/manage/services/storage/how-to-create-a-storage-account/). Como parte disso, você especifica um data center do Azure que armazenará os objetos criados usando essa conta. O cluster e a conta de armazenamento devem ser hospedados no mesmo data center (o banco de dados SQL do metastore Hive e o banco de dados SQL do metastore Oozie também devem estar localizados no mesmo data center). Onde quer que eles estejam, cada blob que você criar pertence a um contêiner na sua conta de armazenamento. Esse contêiner pode ser um contêiner de armazenamento de Blob existente criado fora do HDInsight, ou pode ser um contêiner criado para um cluster HDInsight. 



###Criar um contêiner de Blob para HDInsight usando o Portal de Gerenciamento

Ao provisionar um cluster HDInsight no Portal de Gerenciamento do Azure, há duas opções: *criação rápida* e *criação personalizada*. A opção de criação rápida requer a conta de Armazenamento do Azure criada anteriormente.  Para obter instruções, consulte [Como criar uma conta de armazenamento]( /pt-br/manage/services/storage/how-to-create-a-storage-account/). 

Usando a opção de criação rápida, você pode escolher uma conta de armazenamento existente. O processo de provisionamento cria um novo contêiner com o mesmo nome que o nome do cluster HDInsight. Esse contêiner é usado como o sistema de arquivos padrão.

![HDI.QuickCreate](./media/hdinsight-use-blob-storage/HDI.QuickCreateCluster.png "HDInsight Cluster Quick Create")
 
Usando a criação personalizada, você pode escolher um contêiner de armazenamento de Blob existente ou criar um contêiner padrão. O contêiner padrão tem o mesmo nome que o nome do cluster HDInsight.

![HDI.CustomCreateStorageAccount](./media/hdinsight-use-blob-storage/HDI.CustomCreateStorageAccount.png  "Custom Create Storage Account" )
  




### Criar um contêiner usando o PowerShell do Azure.
[O PowerShell do Azure][powershell-install] pode ser usado para criar contêineres de Blob. O seguinte é um exemplo de script do PowerShell:

	$subscriptionName = "<SubscriptionName>"
	$storageAccountName = "<WindowsAzureStorageAccountName>"
	$containerName="<BlobContainerToBeCreated>"

	Add-AzureAccount # The connection is good for 12 hours.
	Select-AzureSubscription $subscriptionName #only required if you have multiple subscriptions
	
	# Create a storage context object
	$storageAccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext 


##<a id="addressing"></a>Arquivos de endereços no armazenamento de Blob

O esquema de URI para acessar arquivos no armazenamento de Blob é: 

	wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


> [WACOM.NOTE] A sintaxe para endereçamento de arquivos no emulador de armazenamento (em execução no emulador do HDInsight) é <i>wasb://&lt;ContainerName&gt;@storageemulator</i>



O esquema de URI fornece acesso descriptografado com o prefixo *wasb:* e acesso criptografado SSL com *wasbs*. É recomendável usar *wasbs* sempre que possível, mesmo ao acessar dados que se residem dentro do mesmo data center do Azure.
	
O &lt;BlobStorageContainerName&gt; identifica o nome do contêiner de armazenamento de Blob.
O &lt;StorageAccountName&gt; identifica o nome da conta de Armazenamento do Azure. Um FQDN (nome de domínio totalmente qualificado) é necessário.
	
Se &lt;BlobStorageContainerName&gt; nem &lt;StorageAccountName&gt; for especificado, o sistema de arquivos padrão será usado. Para os arquivos no sistema de arquivos padrão, você pode usar um caminho absoluto ou um caminho relativo. Por exemplo, o arquivo hadoop-mapreduce-examples.jar fornecido com clusters HDInsight pode ser referenciado para usar um dos seguintes procedimentos:

	wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
	wasb:///example/jars/hadoop-mapreduce-examples.jar
	/example/jars/hadoop-mapreduce-examples.jar
	
> [WACOM.NOTE] O nome do arquivo é <i>hadoop-examples.jar</i> nos clusters HDInsight versão 1.6 e 2.1.


O &lt;caminho&gt; é o nome do caminho HDFS do arquivo ou do diretório. Como os contêineres de armazenamento de Blob são apenas um repositório de chave-valor, não há nenhum sistema de arquivos hierárquico verdadeiro. Um "/" dentro de uma chave de blob é interpretado como um separador de diretório. Por exemplo, o nome do blob para *hadoop-mapreduce-examples.jar* é:

	example/jars/hadoop-mapreduce-examples.jar
	

##<a id="powershell"></a>Acessar blob usando o PowerShell do Azure

Consulte [Instalar e configurar o PowerShell do Azure][powershell-install] para obter informações sobre como instalar e configurar o PowerShell do Azure na estação de trabalho. Você pode usar a janela de console do PowerShell do Azure ou o PowerShell_ISE para executar cmdlets do PowerShell. 

Use o comando a seguir para listar os cmdlets relacionados ao blob:

	Get-Command *blob*

![Blob.PowerShell.cmdlets][img-hdi-powershell-blobcommands]


**Exemplo do PowerShell para carregamento de um arquivo**

Consulte [Carregar dados no HDInsight][hdinsight-upload-data].

**Exemplo do PowerShell para download de um arquivo**

O script a seguir baixa blob de blocos na pasta atual. Antes de executar o script, altere o diretório para uma pasta onde você tenha permissão de gravação. 


	$storageAccountName = "<WindowsAzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
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

	$storageAccountName = "<WindowsAzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
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

	$storageAccountName = "<WindowsAzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
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

##<a id="nextsteps"></a>Próximas etapas

Neste artigo, você aprendeu a usar o armazenamento de Blob com o HDInsight, e que esse armazenamento de Blob é um componente fundamental do HDInsight. Isso permite que você crie soluções de arquivamento de longo prazo de aquisição de dados com o armazenamento de Blob do Azure e use o HDInsight para desbloquear as informações dentro dos dados armazenados.

Para saber mais, consulte os seguintes artigos:

* [Introdução ao Azure HDInsight][hdinsight-getting-started]
* [Carregar dados no HDInsight][hdinsight-upload-data]
* [Use o hive com o HDInsight][hdinsight-hive]
* [Use o Pig com o HDInsight][hdinsight-pig]

[Powershell-install]: ../install-configure-powershell/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-getting-started]: ../hdinsight-get-started/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-hive]: ../hdinsight-use-hive/
[hdinsight-pig]: ../hdinsight-use-pig/

[Powershell-install]: ../install-configure-powershell/

[img-hdi-powershell-blobcommands]: ./media/hdinsight-use-blob-storage/HDI.PowerShell.BlobCommands.png 


