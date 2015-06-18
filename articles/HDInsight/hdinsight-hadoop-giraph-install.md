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

Você pode instalar o Giraph em qualquer tipo de cluster em Hadoop no Azure HDInsight usando a **Ação de Script** para personalizar clusters. A Ação de Script permite executar scripts para personalizar um cluster somente quando o cluster está sendo criado. Para obter mais informações, consulte [Personalizar clusters HDInsight usando a Ação de Script][hdinsight-cluster-customize].

Neste tópico, você aprenderá a instalar o Giraph usando a Ação de Script. Depois de instalar o Giraph, você também aprenderá a usar o Giraph em suas aplicações mais comuns: processar gráficos em grande escala.


## <a name="whatis"></a>O que é o Giraph?

O <a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> permite executar processamento de gráficos usando o Hadoop e pode ser usado com o Azure HDInsight. Os gráficos modelam as relações entre objetos, como as conexões entre roteadores em uma rede grande, como a Internet, ou as relações entre as pessoas nas redes sociais (às vezes chamados de gráficos sociais). O processamento de tabelas permite que você faça a análise das relações entre objetos em uma tabela, como:

- Identificar possíveis amigos com base em suas relações atuais.
- Identificar a menor rota entre dois computadores em uma rede.
- Calcular a ordem de classificação de página da Web.

   
## <a name="install"></a>Como instalar o Giraph?

Um script de exemplo para instalar o Giraph em um cluster HDInsight está disponível em um blob de armazenamento do Azure somente leitura em [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). Esta seção fornece instruções sobre como usar o script de exemplo ao provisionar o cluster usando o portal do Azure.

> [AZURE.NOTE]O script de exemplo funciona apenas com o cluster HDInsight versão 3.1. Para obter mais informações sobre as versões do cluster HDInsight, consulte [Versões do cluster HDInsight](hdinsight-component-versioning.md).

1. Comece provisionando um cluster usando a opção **CRIAÇÃO PERSONALIZADA**, como descrito em [Provisionando um cluster usando opções personalizadas](hdinsight-provision-clusters.md#portal). 
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
</table>Você pode adicionar mais de uma ação de script para instalar vários componentes no cluster. Depois de adicionar os scripts, clique na marca de seleção para iniciar o provisionamento do cluster.

Você também pode usar o script para instalar o Giraph no HDInsight usando o PowerShell do Azure ou o HDInsight .NET SDK. Instruções para esses procedimentos são fornecidas posteriormente neste tópico.

## <a name="usegiraph"></a>Como usar o Giraph no HDInsight?

Usamos o exemplo SimpleShortestPathsComputation para demonstrar a implementação básica <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> para encontrar o caminho mais curto entre objetos em um gráfico. Use as etapas a seguir para carregar os dados de exemplo e o jar do exemplo, executar um trabalho usando o exemplo SimpleShortestPathsComputation e exibir os resultados.

1. Carregue um arquivo de dados de exemplo no armazenamento de Blob do Azure. Em sua estação de trabalho local, crie um novo arquivo chamado **tiny_graph.txt**. Ele deve conter as seguintes linhas:

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	Carregue o arquivo tiny_graph.txt no armazenamento primário para o seu cluster HDInsight. Para obter instruções sobre como carregar dados, consulte [Carregar dados de trabalhos do Hadoop no HDInsight](hdinsight-upload-data.md).

	Esses dados descrevem uma relação entre objetos em um gráfico direcionado, usando o formato [source_id, source_value,[[dest_id], [edge_value],...]]. Cada linha representa uma relação entre um objeto **source_id** e um ou mais objetos **dest_id**. O **edge_value** (ou peso) pode ser pensado como a força ou a distância da conexão entre **source_id** e **dest_id**.

	Se desenharmos usando o valor (ou peso) como distância entre os objetos, os dados acima poderiam se assemelhar a este esquema:

	![tiny_graph.txt Desenhado como círculos com linhas de distância variável entre](.\media\hdinsight-hadoop-giraph-install\giraph-graph.png)

	

4. Execute o exemplo SimpleShortestPathsComputation. Use os seguintes cmdlets do PowerShell do Azure para executar o exemplo, usando o arquivo tiny_graph.txt como entrada. Isso requer que você tenha instalado e configurado o [PowerShell do Azure][powershell-install-configure].

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

5. Exibir os resultados. Concluído o trabalho, os resultados serão armazenados em dois arquivos de saída na pasta __wasb:///example/out/shotestpaths__. Os arquivos se chamam __part-m-00001__ e __part-m-00002__. Execute as seguintes etapas para baixar e exibir a saída:

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

	![Desenho dos objetos como círculos com os percursos mais curtos entre](.\media\hdinsight-hadoop-giraph-install\giraph-graph-out.png)


## <a name="usingPS"></a>Instalar o Giraph em clusters HDInsight Hadoop usando o PowerShell do Azure

Nesta seção, usamos o cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** para invocar scripts usando a Ação de Script para personalizar um cluster. Antes de prosseguir, verifique se você instalou e configurou o PowerShell do Azure. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do Powershell do Azure para HDInsight, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

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
	
2. Especifique os valores de configuração, como nós no cluster e o armazenamento padrão a ser usado:

		# Specify the configuration options
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. Use o cmdlet **Add-AzureHDInsightScriptAction** para adicionar uma ação de script à configuração do cluster. Mais tarde, quando o cluster estiver sendo criado, a ação de script será executada.

		# Add a script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Giraph" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1

	O cmdlet **Add-AzureHDInsightScriptAction** usa os seguintes parâmetros:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Parâmetro</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Definição</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Config</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Objeto de configuração ao qual as informações da ação de script são adicionadas.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome da ação de script.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nós nos quais o script de personalização é executado. Os valores válidos são HeadNode (para instalar no nó principal) ou DataNode (para instalar em todos os nós de dados). Você pode usar um ou ambos os valores.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">URI para o script que é executado.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parâmetros</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parâmetros exigidos pelo script. O script de exemplo usado neste tópico não requer nenhum parâmetro e, portanto, você não vir esse parâmetro no trecho acima.
</td></tr>
</table>
	
4. Por fim, comece a provisionar um cluster personalizado com Giraph instalado:
	
		# Start provisioning a cluster with Giraph installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

Quando solicitado, insira as credenciais para o cluster. Pode levar alguns minutos até que o cluster seja criado.


## <a name="usingSDK"></a>Instalar o Giraph em clusters HDInsight Hadoop usando o .NET SDK

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

2. No menu **Arquivo**, clique em **Novo** e em **Projeto**.

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

5. No menu **Ferramentas**, clique em **Gerenciador de Pacotes NuGet** e em **Console do Gerenciador de Pacotes**.

6. Execute o seguinte comando no console para instalar o pacote:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Esse comando adiciona as bibliotecas .NET e referências a elas a partir do projeto atual do Visual Studio.

7. No Gerenciador de Soluções, clique duas vezes em **Program.cs** para abri-lo.

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

10. Acrescente o seguinte código à função Main () para usar a classe [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) para invocar um script personalizado para instalar o Giraph:

		// Add the script action to install Giraph
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Giraph", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install Giraph on
          new Uri("https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1"), // Location of the script to install Giraph
		  null //Because the script used does not require any parameters
        ));

11. Por fim, crie o cluster:

		client.CreateCluster(clusterInfo);

12. Salve as alterações no aplicativo e crie a solução.

**Para executar o aplicativo**

Abra um console do PowerShell do Azure, navegue até o local onde você salvou o projeto do Visual Studio, navegue até a pasta \\bin\\debug do projeto e execute o seguinte comando:

	.\CreateGiraphCluster <cluster-name>

Forneça um nome de cluster e pressione ENTER para provisionar um cluster com o Giraph instalado.


## Consulte também##
- [Instalar e usar o Spark em clusters HDInsight][hdinsight-install-spark] para obter instruções sobre como usar o cluster de personalização para instalar e usar o Spark em clusters Hadoop no HDInsight. O Spark é uma estrutura de processamento paralelo de software livre que dá suporte a processamento na memória para melhorar o desempenho de aplicativos analíticos de Big Data.
- [Instalar o R em clusters HDInsight][hdinsight-install-r] fornece instruções sobre como usar a personalização do cluster para instalar e usar o R em clusters Hadoop no HDInsight. R é uma linguagem e ambiente de software livre para computação estatística. Ele fornece centenas de funções estatísticas internas e sua própria linguagem de programação, que combina aspectos de programação funcional e de programação orientada a objetos. Ele também fornece recursos abrangentes de gráficos.
- [Instalar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install.md). Use a personalização do cluster para instalar o Solr em clusters de Hadoop do HDInsight. O Solr permite executar operações de pesquisa avançadas nos dados armazenados.



[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md

<!--HONumber=54--> 