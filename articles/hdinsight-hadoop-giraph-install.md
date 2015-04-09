<properties 
	pageTitle="Use a ação de Script no HDInsight para instalar o Giraph em cluster do Hadoop| Azure" 
	description="Saiba como personalizar o cluster HDInsight para instalar o Giraph. Você usará uma opção de configuração de Ação de Script para usar um script para instalar o Giraph" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/03/2015" 
	ms.author="nitinme"/>

# Instalar e usar o Giraph em clusters Hadoop no HDInsight

Você pode instalar Giraph em qualquer tipo de cluster no Hadoop no Azure HDInsight usando a personalização de cluster **Ação de Script**. A Ação de script permite executar scripts para personalizar um cluster somente quando o cluster está sendo criado. Para obter mais informações, consulte [Personalizar clusters HDInsight usando a Ação de Script][hdinsight-cluster-customize].

Neste tópico, você aprenderá como instalar o Giraph usando a Ação de Script. Uma vez instalado o Giraph, você também aprenderá a usá-lo para aplicações mais comuns, ou seja, processamento de gráficos em grande escala.


## <a name="whatis"></a>O que é Giraph?

<a href="http://giraph.apache.org/" target="_blank">O Apache Giraph</a> permite que você realize processamento de tabelas usando o Hadoop, além de poder ser utilizado com o HDInsight do Azure. Relações de modelo de tabelas entre objetos, como as conexões entre roteadores em uma rede grande como a Internet, ou relações entre pessoas em redes sociais (algumas vezes mencionadas como uma tabela social). O processamento de gráficos permite que você faça a análise das relações entre objetos em um gráfico, como:

- Identificando aliados em potencial com base em seus relacionamentos atuais.
- Identificando a rota mais curta entre dois computadores em uma rede.
- Calculando a classificação de página das páginas da Web.

   
## <a name="install"></a>Como instalar o Giraph?

Um exemplo de script para instalar o Giraph em um cluster HDInsight está disponível em um blob de armazenamento do Azure somente para leitura em [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). Esta seção fornece instruções sobre como usar o script de exemplo ao provisionar o cluster usando o portal do Azure. 

> [AZURE.NOTE] O script de exemplo funciona apenas com o cluster HDInsight versão 3.1. Para obter mais informações sobre versões do cluster HDInsight, consulte [versões do cluster HDInsight](hdinsight-component-versioning.md).

1. Inicie o provisionamento de um cluster usando a opção **criação personalizada**, conforme descrito em [Provisionar um cluster usando opções personalizadas](hdinsight-provision-clusters.md#portal). 
2. Na página **Ações de Script** do assistente, clique em **adicionar ação de script** para fornecer detalhes sobre a ação de script, como mostrado abaixo:

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>Propriedade</th><th>Valor</th></tr>
		<tr><td>Nome</td>
			<td>Especifique um nome para a ação de script. Por exemplo, <b>Instalar Giraph</b>.</td></tr>
		<tr><td>URI do script</td>
			<td>Especifique o URI (Uniform Resource Identifier) para o script que é chamado para personalizar o cluster. Por exemplo, <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
		<tr><td>Tipo de nó</td>
			<td>Especifique os nós em que o script de personalização é executado. Você pode escolher <b>Todos os nós</b>, <b>Somente nós headnode</b> ou <b>Somente nós de trabalho</b>.
		<tr><td>Parâmetros</td>
			<td>Especifique os parâmetros, se exigido pelo script. O script para instalar o Giraph não requer nenhum parâmetro, portanto você pode deixá-los em branco.</td></tr>
	</table>	

	Você pode adicionar mais de uma ação de script para instalar vários componentes no cluster. Depois de adicionar os scripts, clique na marca de seleção para iniciar o provisionamento do cluster.

Você também pode usar o script para instalar o Giraph no HDInsight usando o PowerShell do Azure ou o SDK do .NET do HDInsight. Instruções para esses procedimentos são fornecidas posteriormente neste tópico.

## <a name="usegiraph"></a>Como usar o Giraph no HDInsight?

Usamos o exemplo de SimpleShortestPathsComputation para demonstrar a implementação básica do <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> para encontrar o caminho mais curto entre objetos em um gráfico. Use as etapas a seguir para carregar os dados amostrais e o jar de exemplo, execute um trabalho usando o exemplo SimpleShortestPathsComputation e, então, visualize os resultados.

1. Carregue um arquivo de dados de exemplo no Armazenamento de Blob do Azure. Em sua estação de trabalho local, crie um novo arquivo chamado **tiny_graph.txt**. Ele deve conter as seguintes linhas:

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	Carregue o arquivo tiny_graph.txt no armazenamento primário para o seu cluster HDInsight. Para obter instruções sobre como carregar dados, consulte [Carregar dados para trabalhos de Hadoop no HDInsight](hdinsight-upload-data.md).

	Esses dados descrevem uma relação entre objetos em uma tabela direcionada, usando o formato [source\_id, source\_value,[[dest\_id], [edge\_value],...]]. Cada linha representa uma relação entre um objeto **source\_id** e um ou mais objetos **dest\_id**. O **edge\_value** (ou peso) pode ser pensada como a intensidade ou a distância da conexão entre **source\_id** e **dest\_id**.

	Desenhado e utilizando o valor (ou peso) como distância entre os objetos, os dados acima podem se parecer com os demonstrados aqui:

	![tiny_graph.txt drawn as circles with lines of varying distance between](.\media\hdinsight-hadoop-giraph-install\giraph-graph.png)

	

4. Execute o exemplo SimpleShortestPathsComputation. Use os cmdlets do PowerShell do Azure a seguir para executar o exemplo usando o arquivo tiny_graph.txt como entrada. Isso requer que você tenha instalado e configurado o [PowerShell do Azure][powershell-install-configure].

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

	No exemplo acima, substitua **clustername** pelo nome do seu cluster HDInsight que tem o Giraph instalado.

5. Exiba os resultados. Assim que o trabalho tiver sido concluído, os resultados serão armazenados em dois arquivos de saída na pasta __wasb:///example/out/shotestpaths__. Os arquivos são chamados __part-m-00001__ e __part-m-00002__. Execute as etapas a seguir para baixar e exibir a saída:

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

	Isso criará a estrutura de diretórios __example/output/shortestpaths__ no diretório atual em sua estação de trabalho e baixará os dois arquivos de saída para esse local.

	Use o cmdlet __Cat__ para exibir o conteúdo dos arquivos: 

		Cat example/output/shortestpaths/part*

	A saída deve ter aparência similar à exibida a seguir:


		0	1.0
		4	5.0
		2	2.0
		1	0.0
		3	1.0

	O exemplo SimpleShortestPathComputation é programado em código fixo para começar com a ID de objeto 1 e encontrar o caminho mais curto para outros objetos. Portanto, a saída deve ser lida como `destination_id distance`, onde a distância é o valor (ou peso) dos limites entre os quais se percorre entre a ID de objeto 1 e a ID de destino.
	
	Visualizando isso, você pode verificar os resultados percorrendo os caminhos mais curtos entre a ID 1 e todos os outros objetos. Observe que o caminho mais curto entre a ID 1 e a ID 4 é 5. Essa é a distância padrão entra a <span style="color:orange">ID 1 e 3</span>e entre a <span style="color:red">ID 3 e 4</span>.

	![Drawing of objects as circles with shortest paths drawn between](.\media\hdinsight-hadoop-giraph-install\giraph-graph-out.png) 


## <a name="usingPS"></a>Instalar o Giraph em clusters Hadoop do HDInsight usando o PowerShell do Azure

Nesta seção, usamos o cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** para chamar scripts usando a Ação de Script para personalizar um cluster. Antes de prosseguir, certifique-se de ter instalado e configurado o PowerShell do Azure. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do PowerShell do Azure para HDInsight, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

Execute as seguintes etapas:

1. Abra uma janela do PowerShell do Azure e declare as variáveis a seguir:

		# Provide values for these variables
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure Storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# Number of nodes in the HDInsight cluster
		$version = "<HDInsightClusterVersion>"          # For example, "3.1"
	
2. Especifique os valores de configuração como nós no cluster e o armazenamento padrão a ser usado:

		# Specify the configuration options
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. Use o cmdlet **AzureHDInsightScriptAction adicionar** para adicionar uma ação de script à configuração do cluster. Posteriormente, quando o cluster estiver sendo criado, a ação de script será executada. 

		# Add a script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Giraph" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1

	O cmdlet **Add-AzureHDInsightScriptAction** usa os seguintes parâmetros:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Parâmetro</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Definição</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Config</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">O objeto de configuração em que as informações da ação de script são adicionadas.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome da ação de script.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nós em que o script de personalização é executado. Os valores válidos são HeadNode (para instalar o nó de cabeçalho) ou DataNode (para instalar em todos os nós de dados). Você pode usar um ou ambos os valores.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">URI para o script que é executado.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parâmetros</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parâmetros exigidos pelo script. O script de exemplo usado neste tópico não requer nenhum parâmetro e, portanto, você não vir esse parâmetro no trecho acima.
	</td></tr>
	</table>
	
4. Finalmente, inicie o provisionamento de um cluster personalizado com o Giraph instalado:  
	
		# Start provisioning a cluster with Giraph installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

Quando solicitado, insira as credenciais para o cluster. Pode levar alguns minutos até que o cluster seja criado.


## <a name="usingSDK"></a>Instalar o Giraph em clusters Hadoop do HDInsight Hadoop usando o SDK do .NET

O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com o HDInsight em um aplicativo .NET Framework. Esta seção fornece instruções sobre como usar a Ação de Script do SDK para provisionar um cluster com o Giraph instalado. Os procedimentos a seguir devem ser executados:

- Instalar o SDK do .NET do HDInsight
- Crie um certificado autoassinado
- Criar um aplicativo de console
- Executar o aplicativo


**Para instalar o SDK do .NET do HDInsight**

Você pode instalar a compilação publicada mais recentemente do SDK no [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). As instruções serão mostradas no próximo procedimento.

**Para criar um certificado autoassinado**

Crie um certificado autoassinado, instale-o em sua estação de trabalho e o carregue para sua assinatura do Azure. Para obter instruções, consulte [Criar um certificado autoassinado](http://go.microsoft.com/fwlink/?LinkId=511138). 


**Para criar um aplicativo do Visual Studio**

1. Abra o Visual Studio 2013.

2. No menu **Arquivo**, clique em **Novo** e **Projeto**.

3. Em **Novo Projeto**, digite ou selecione os seguintes valores:
	
	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propriedade</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valor</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Categoria</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Modelos/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Modelo</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Aplicativo de console</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateGiraphCluster</td></tr>
	</table>

4. Clique em **OK** para criar o projeto.

5. No menu **Ferramentas**, clique em **Gerenciador de Pacotes Nuget** e, em seguida, clique em **Console do Gerenciador de Pacotes**.

6. Execute o seguinte comando no console para instalar os pacotes:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Esse comando adiciona as bibliotecas .NET e referências a elas a partir do projeto atual do Visual Studio.

7. No Gerenciador de Soluções, clique duas vezes com o botão direito do mouse em **Program.cs** para abri-lo.

8. Adicione o seguinte usando as instruções na parte superior do arquivo :

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. Na função Main(), copie e cole o seguinte código e forneça valores para as variáveis:
		
        var clusterName = args[0];

        // Provide values for the variables
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Provide the certificate thumbprint to retrieve the certificate from the certificate store 
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsight client object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;
        
        // Provide the cluster information
		var clusterInfo = new ClusterCreateParameters()
        {
            Name = clusterName,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = clusterName,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize,
            Version = "3.1"
        };        

10. Acrescente o seguinte código para a função Main() para usar a classe [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) para invocar um script personalizado para instalar o Giraph:

		// Add the script action to install Giraph
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Giraph", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install Giraph on
          new Uri("https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1"), // Location of the script to install Giraph
		  null //Because the script used does not require any parameters
        ));

11. Por último, crie o cluster:

		client.CreateCluster(clusterInfo);

12. Salve as alterações no aplicativo e crie a solução. 

**Para executar o aplicativo**

Abra um console do PowerShell do Azure, navegue até o local onde você salvou o projeto no Visual Studio, navegue até a pasta \bin\debug do projeto e, em seguida, execute o seguinte comando:

	.\CreateGiraphCluster <cluster-name>

Forneça um nome de cluster e pressione ENTER para provisionar um cluster com o Giraph instalado.


## Consulte também##
- [Instalar e usar Spark em clusters HDInsight][hdinsight-install-spark] para obter instruções sobre como usar o cluster de personalização para instalar e usar Spark em clusters Hadoop do HDInsight. O Spark é uma estrutura de processamento paralelo de código-fonte aberto que oferece suporte ao processamento de memória para melhorar o desempenho dos aplicativos analíticos de big data.
- [Instalar R em clusters HDInsight][hdinsight-install-r] fornece instruções sobre como usar a personalização do cluster para instalar e usar o R em clusters de Hadoop do HDInsight. R é uma linguagem de software livre e um ambiente de computação de estatísticas. O R fornece centenas de funções estatísticas internas e uma linguagem própria de programação que combina aspectos da programação funcional e orientada a objeto. Ele também fornece recursos abrangentes de gráficos.
- [Instalar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install.md). Use a personalização do cluster para instalar o Solr em clusters de Hadoop do HDInsight. O Solr permite que você execute operações poderosas nos dados armazenados.



[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md

<!--HONumber=49-->