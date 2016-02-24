<properties
	pageTitle="Instalar e usar o Giraph nos clusters Hadoop no HDInsight | Microsoft Azure"
	description="Saiba como personalizar o cluster HDInsight com o Giraph, e como usar o Giraph."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="nitinme"/>

# Instalar e usar o Giraph no HDInsight


Saiba como personalizar o cluster HDInsight baseado em Windows com Giraph usando Ação de Ação, e como usar o Giraph para processar gráficos em larga escala. Para obter informações sobre como usar o Giraph com um cluster baseado no Linux, consulte [Instalar o Giraph em clusters Hadoop do HDInsight (Linux)](hdinsight-hadoop-giraph-install-linux.md).
 
Você pode instalar o Giraph em qualquer tipo de cluster (Hadoop, Storm, HBase, Spark) no Azure HDInsight usando a *Ação de Script*. Um script de exemplo para instalar o Giraph em um cluster HDInsight está disponível em um blob de armazenamento do Azure somente leitura em [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). O script de exemplo funciona apenas com o cluster HDInsight versão 3.1. Para obter mais informações sobre as versões do cluster HDInsight, consulte [Versões do cluster HDInsight](hdinsight-component-versioning.md).

**Artigos relacionados**

- [Instalar o Giraph em clusters Hadoop do HDInsight (Linux)](hdinsight-hadoop-giraph-install-linux.md)
- [Criar clusters Hadoop no HDInsight](hdinsight-provision-clusters.md): informações gerais sobre como criar clusters HDInsight.
- [Personalizar clusters HDInsight usando a Ação de Script][hdinsight-cluster-customize]\: informações gerais sobre como personalizar os clusters HDInsight usando a Ação de Script.
- [Desenvolver scripts de Ação de Script para o HDInsight](hdinsight-hadoop-script-actions.md).

## O que é Giraph?

O <a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> permite executar processamento de gráficos usando o Hadoop e pode ser usado com o Azure HDInsight. Os gráficos modelam as relações entre objetos, como as conexões entre roteadores em uma rede grande, como a Internet, ou as relações entre as pessoas nas redes sociais (às vezes chamados de gráficos sociais). O processamento de tabelas permite que você faça a análise das relações entre objetos em uma tabela, como:

- Identificar possíveis amigos com base em suas relações atuais.
- Identificar a menor rota entre dois computadores em uma rede.
- Calcular a ordem de classificação de página da Web.


## Instalar o Giraph usando o Portal

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Instalar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install-v1.md)

1. Comece a criar um cluster usando a opção **CRIAÇÃO PERSONALIZADA**, conforme descrito em [Criar clusters Hadoop no HDInsight](hdinsight-provision-clusters.md#portal).
2. Na página **Ações de Script** do assistente, clique em **Adicionar ação de script** para fornecer detalhes sobre a ação de script, como mostrado abaixo:

	![Usar Ação de Script para personalizar um cluster](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "Usar Ação de Script para personalizar um cluster")

	<table border='1'>
	<tr><th>Propriedade</th><th>Valor</th></tr>
	<tr><td>Nome</td>
		<td>Especifique um nome para a ação de script. Por exemplo, <b>Instalar o Giraph</b>.</td></tr>
	<tr><td>URI do script</td>
		<td>Especifique o URI (Uniform Resource Identifier) do script invocado para personalizar o cluster. Por exemplo, <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
	<tr><td>Tipo de nó</td>
		<td>Especifique os nós em que o script de personalização deve ser executado. Você pode escolher <b>Todos os nós</b>, <b>Somente nós principais</b> ou <b>Somente nós de trabalho</b>.
	<tr><td>Parâmetros</td>
		<td>Especifique os parâmetros, se exigido pelo script. O script para instalar o Giraph não requer nenhum parâmetro; você pode deixar em branco.</td></tr>
</table>Você pode adicionar mais de uma ação de script para instalar vários componentes no cluster. Depois de adicionar os scripts, clique na marca de seleção para começar a criar o cluster.

## Usar o Giraph

Usamos o exemplo SimpleShortestPathsComputation para demonstrar a implementação básica <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> para encontrar o caminho mais curto entre objetos em um gráfico. Use as etapas a seguir para carregar os dados de exemplo e o jar do exemplo, executar um trabalho usando o exemplo SimpleShortestPathsComputation e exibir os resultados.

1. Carregue um arquivo de dados de exemplo no armazenamento de Blob do Azure. Em sua estação de trabalho local, crie um novo arquivo chamado **tiny\_graph.txt**. Ele deve conter as seguintes linhas:

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	Carregue o arquivo tiny\_graph.txt no armazenamento primário para o seu cluster HDInsight. Para obter instruções sobre como carregar dados, consulte [Carregar dados de trabalhos do Hadoop no HDInsight](hdinsight-upload-data.md).

	Esses dados descrevem uma relação entre objetos em um gráfico direcionado, usando o formato [source\_id, source\_value,[[dest\_id], [edge\_value],...]]. Cada linha representa uma relação entre um objeto **source\_id** e um ou mais objetos **dest\_id**. O **edge\_value** (ou peso) pode ser pensado como a força ou a distância da conexão entre **source\_id** e **dest\_id**.

	Desenhado e utilizando o valor (ou peso) como distância entre os objetos, os dados acima podem se parecer com os demonstrados aqui:

	![tiny\_graph.txt Desenhado como círculos com linhas de distância variável entre](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)



4. Execute o exemplo SimpleShortestPathsComputation. Use os seguintes cmdlets do PowerShell do Azure para executar o exemplo, usando o arquivo tiny\_graph.txt como entrada. Isso requer que você tenha instalado e configurado o [PowerShell do Azure][powershell-install].

		$clusterName = "clustername"
		# Giraph examples jar
		$jarFile = "wasb:///example/jars/giraph-examples.jar"
		# Arguments for this job
		$jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
		                "-ca", "mapred.job.tracker=headnodehost:9010",
		                "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
		                "-vip", "wasb:///example/data/tiny_graph.txt",
		                "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
		                "-op",  "wasb:///example/output/shortestpaths",
		                "-w", "2"
		# Create the definition
		$jobDefinition = New-AzureHDInsightMapReduceJobDefinition
		  -JarFile $jarFile
		  -ClassName "org.apache.giraph.GiraphRunner"
		  -Arguments $jobArguments

		# Run the job, write output to the Azure PowerShell window
		$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
		Write-Host "Wait for the job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $job
		Write-Host "STDERR"
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

	No exemplo acima, substitua **clustername** pelo nome do cluster HDInsight que tem o Giraph instalado.

5. Exiba os resultados. Depois de concluir o trabalho, os resultados serão armazenados em dois arquivos de saída na pasta \_\___wasb:///example/out/shotestpaths__. Os arquivos se chamam __part-m-00001__ e __part-m-00002__. Execute as etapas a seguir para baixar e exibir a saída:

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		$storageAccountName = "<StorageAccountName>"   # Azure Storage account name
		$containerName = "<ContainerName>"             # Blob storage container name

		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Create the Storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force

	Isso criará a estrutura de diretório __example/output/shortestpaths__ no diretório atual em sua estação de trabalho e baixará os dois arquivos de saída nesse local.

	Use o cmdlet __Cat__ para exibir o conteúdo dos arquivos:

		Cat example/output/shortestpaths/part*

	A saída deve ter aparência similar à exibida a seguir:


		0	1.0
		4	5.0
		2	2.0
		1	0.0
		3	1.0

	O exemplo SimpleShortestPathComputation está codificado para começar com a ID do objeto 1 e localizar o caminho mais curto para os outros objetos. Logo, a saída deve ser lida como `destination_id distance`, em que a distância é o valor (ou peso) dos perímetros percorridos entre a ID do objeto 1 e a ID de destino.

	Visualizando isso, você pode verificar os resultados percorrendo os caminhos mais curtos entre a ID 1 e todos os outros objetos. Observe que o caminho mais curto entre a ID 1 e a ID 4 é 5. Essa é a distância total entre <span style="color:orange">ID 1 e 3</span> e, em seguida, entre <span style="color:red">ID 3 e 4</span>.

	![Desenho dos objetos como círculos com os percursos mais curtos entre](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)

## Instalar o Giraph usando o Azure PowerShell

Consulte [Personalizar os clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell). O exemplo demonstra como instalar o Spark usando o Azure PowerShell. Você precisa personalizar o script para usar [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## Instalar o Giraph usando o SDK do .NET

Consulte [Personalizar os clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). O exemplo demonstra como instalar o Spark usando o SDK do .NET. Você precisa personalizar o script para usar [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).


## Consulte também

- [Instalar o Giraph em clusters Hadoop do HDInsight (Linux)](hdinsight-hadoop-giraph-install-linux.md)
- [Criar clusters Hadoop no HDInsight](hdinsight-provision-clusters.md): informações gerais sobre como criar clusters HDInsight.
- [Personalizar clusters HDInsight usando a Ação de Script][hdinsight-cluster-customize]\: informações gerais sobre como personalizar os clusters HDInsight usando a Ação de Script.
- [Desenvolver scripts de Ação de Script para o HDInsight](hdinsight-hadoop-script-actions.md).
- [Instalar e usar o Spark em clusters HDInsight][hdinsight-install-spark]\: exemplo de Ação de Script sobre a instalação do Spark.
- [Instalar R em clusters HDInsight][hdinsight-install-r]\: exemplo de Ação de Script sobre a instalação do R.
- [Instalar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install.md): exemplo de Ação de Script sobre a instalação do Solr.



[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: ../powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md

<!---HONumber=AcomDC_0211_2016-->