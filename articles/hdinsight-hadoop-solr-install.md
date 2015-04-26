<properties 
	pageTitle="Use a ação de Script no HDInsight para instalar o Solr em cluster do Hadoop| Azure" 
	description="Saiba como personalizar o cluster HDInsight para instalar o Solr. Você usará uma opção de configuração de Ação de Script para usar um script para instalar o Solr" 
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
	ms.date="12/19/2014" 
	ms.author="nitinme"/>

# Instalar e usar o Solr em clusters HDInsight do Hadoop

Você pode instalar Solr em qualquer tipo de cluster no Hadoop no HDInsight usando a personalização de cluster **Ação de Script**. A ação de script permite executar scripts para personalizar um cluster somente quando o cluster está sendo criado. Para obter mais informações, consulte [Personalizar cluster HDInsight usando a ação de script][hdinsight-cluster-customize].

Neste tópico, você aprenderá como instalar o Solr usando a Ação de Script. O Solr é uma plataforma de pesquisa poderosa e oferece recursos de pesquisa em nível corporativo para os dados gerenciados pelo Hadoop. Depois de instalar o Solr no cluster HDInsight, você também aprenderá como pesquisar dados usando o Solr.

> [AZURE.NOTE] O script de exemplo usado neste tópico cria um cluster Solr com uma configuração específica. Se você quiser configurar o cluster Solr com diferentes coleções, fragmentos, esquemas, réplicas, etc., você deve modificar o script e os binários do Solr adequadamente.


## Neste artigo

- [O que é Solr?](#whatis)
- [Como instalar o Solr?](#install)
- [Como usar o Solr no HDInsight?](#usesolr)
- [Instalar o Solr em clusters HDInsight do Hadoop usando o PowerShell](#usingPS)
- [Instalar o Solr em clusters HDInsight do Hadoop usando o SDK do .NET](#usingSDK) 


## <a name="whatis"></a>O que é Solr?

<a href="http://lucene.apache.org/solr/features.html" target="_blank">O Apache Solr</a> é uma plataforma de pesquisa empresarial que permite operações poderosas de pesquisa de texto completo nos dados. Enquanto o Hadoop permite armazenar e gerenciar grandes quantidades de dados, o Apache Solr oferece os recursos de pesquisa para recuperar rapidamente os dados. Este tópico fornece instruções sobre como personalizar um cluster HDInsight para instalar o Solr.   

## <a name="install"></a>Como instalar o Solr?

Um exemplo de script para instalar o R em um cluster HDInsight está disponível em um blob de armazenamento do Azure somente para leitura em [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1). Esta seção fornece instruções sobre como usar o script de exemplo ao provisionar o cluster usando o Portal de Gerenciamento do Azure. 


> [AZURE.NOTE] O script de exemplo funciona apenas com o cluster HDInsight versão 3.1.  Para obter mais informações sobre versões do cluster HDInsight, consulte [versões do cluster HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/).


1. Inicie o provisionamento de um cluster usando a opção **criação personalizada**, conforme descrito em [Provisionar um cluster usando opções personalizadas](http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/#portal). 
2. Na página **Ações de Script** do assistente, clique em **Adicionar ação de script** para fornecer detalhes sobre a Ação de Script, como mostrado abaixo:

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>Propriedade</th><th>Valor</th></tr>
		<tr><td>Nome</td>
			<td>Especifique um nome para a ação de script. Por exemplo, <b>Instalar o Solr</b>.</td></tr>
		<tr><td>URI do script</td>
			<td>Especifique o URI para o script que é chamado para personalizar o cluster. Por exemplo, <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
		<tr><td>Tipo de nó</td>
			<td>Especifica os nós em que o script de personalização é executado. Você pode escolher <b>Todos os nós</b>, <b>Somente nós headnode</b> ou <b>Somente nós de trabalho</b>.
		<tr><td>Parâmetros</td>
			<td>Especifique os parâmetros, se exigido pelo script. O script para instalar o Solr não requer nenhum parâmetro, portanto você pode deixá-los em branco.</td></tr>
	</table>	

	Você pode adicionar mais de uma ação de script para instalar vários componentes no cluster. Depois de adicionar os scripts, clique na marca de seleção para iniciar o provisionamento do cluster.

Você também pode usar o script para instalar o Solr no HDInsight usando o PowerShell ou o SDK do .NET do HDInsight. Instruções para esses procedimentos são fornecidas posteriormente neste tópico.

## <a name="usesolr"></a>Como usar o Solr no HDInsight?

Você deve começar com indexação Solr, com alguns arquivos de dados. Em seguida, você pode usar o Solr para executar consultas de pesquisa em dados indexados. Execute as seguintes etapas para usar o Solr em um cluster HDInsight:

1. **Acesso por RDP ao HDInsight cluster com Solr instalado**. No Portal de Gerenciamento do Azure, habilite a área de trabalho remota para o cluster criado com Solr instalado e, em seguida, acesse remotamente o cluster. Para obter instruções, consulte <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Conectar aos clusters HDInsight usando o RDP</a>.

2. **Indexar o Solr carregando arquivos de dados**. Quando você indexa o Solr, coloque os documentos que você pode precisar pesquisar. Para indexar o Solr, faça acesso por RDP ao cluster, navegue até a área de trabalho, abra a linha de comando do Hadoop e navegue até **C:\apps\dist\solr-4.7.2\example\exampledocs**. Execute o comando a seguir: 
	
		java -jar post.jar solr.xml monitor.xml

	You'll see the following output on the console.

		POSTing file solr.xml
		POSTing file monitor.xml
		2 files indexed.
		COMMITting Solr index changes to http://localhost:8983/solr/update..
		Time spent: 0:00:01.624

	O utilitário post.jar indexa o Solr com dois documentos de exemplo, **solr.xml** e **monitor.xml**. O utilitário post.jar e os documentos de exemplo estão disponíveis com a instalação do Solr.

3. **Use o painel do Solr para pesquisar nos documentos indexados**. Na sessão de acesso por RDP ao cluster HDInsight, abra o Internet Explorer e inicie o painel do Solr em **http://headnodehost:8983/solr/#/**. No painel esquerdo, na lista suspensa Seletor de Núcleo, selecione **coleção1** e, dentro dessa opção, clique em **Consulta**. Por exemplo, para selecionar e retornar todos os documentos em Solr, forneça os seguintes valores:
	1. Na caixa de texto **q**, digite **\*:**\*. Isso retornará como resultado todos os documentos que são indexados em Solr. Se você quiser procurar uma cadeia de caracteres específica dentro dos documentos, você pode inserir essa cadeia de caracteres aqui.
	2. Na caixa de texto **wt**, selecione o formato de saída. O padrão é **json**. Clique em **Executar consulta**.

		![Use Script Action to customize a cluster](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Run a query on Solr dashboard")
	
	A saída retorna os dois documentos que foram usados para indexação do Solr. A saída é semelhante ao seguinte:

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }
   

4. **Recomendável: Fazer backup dos dados indexados do Solr para o Blob de Armazenamento do Azure (WASB) associado ao cluster HDInsight**. Como uma prática recomendada, você deve fazer backup dos dados indexados de nós do cluster Solr no WASB. Execute as seguintes etapas para fazê-lo:

	1. Na sessão RDP, abra o Internet Explorer e insira a seguinte URL:

			http://localhost:8983/solr/replication?command=backup

		You should see a response like this

			<?xml version="1.0" encoding="UTF-8"?>
			<response>
			  <lst name="responseHeader">
			    <int name="status">0</int>
			    <int name="QTime">9</int>
			  </lst>
			  <str name="status">OK</str>
			</response>

	2. Na sessão remota, navegue até {HOME_DO_SOLR}\{Coleção}\data. Para o cluster criado usando o script de exemplo, esse caminho deve ser **C:\apps\dist\solr-4.7.2\example\solr\collection1\data**. Nesse local, você deve ver uma pasta de capturas de tela criando com um nome semelhante a **snapshot.*data_e_hora***.
	
	3. Compacte a pasta de capturas de tela e carregá-la no WASB. Na linha de comando do Hadoop, navegue até o local da pasta de capturas de tela usando o seguinte comando:

			  hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

		Este comando copia a capturas de tela para /example/data/ sob o contêiner na conta de armazenamento padrão associada ao cluster.

## <a name="usingPS"></a>Instalar o Solr em clusters HDInsight do Hadoop usando o PowerShell

Nesta seção, usamos o cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** para chamar scripts usando a Ação de Script para personalizar um cluster. Antes de prosseguir, certifique-se de ter instalado e configurado o PowerShell. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do PowerShell do HDInsight, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

Execute as seguintes etapas:

1. Abra uma janela do PowerShell do Azure e declare as variáveis a seguir:

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# The HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# The location of the HDInsight cluster. It must in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
		$version = "<HDInsightClusterVersion>"          # For example "3.1"
	
2. Especifique os valores de configuração como nós no cluster e o armazenamento padrão a ser usado.

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. Use o cmdlet **Add-AzureHDInsightScriptAction** para adicionar a ação de Script na configuração do cluster. Posteriormente, quando o cluster estiver sendo criado, a Ação de Script será executada. 

		# ADD SCRIPT ACTION TO CLUSTER CONFIGURATION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Solr" -ClusterRoleCollection HeadNode,DataNode -Uri https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1

	O cmdlet **Add-AzureHDInsightScriptAction** usa os seguintes parâmetros:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Parâmetro</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Definição</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Config</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">O objeto de configuração em que a informações da ação de script é adicionada</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome da ação de script</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Especifica os nós em que o script de personalização é executado. Os valores válidos são HeadNode (para instalar o headnode) ou DataNode (para instalar em todos os datanodes). Você pode usar um ou ambos os valores.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Especifica o URI para o script que é executado</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parâmetros</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parâmetros exigidos pelo script. O script de exemplo usado neste tópico não requer nenhum parâmetro e, portanto, você não vir esse parâmetro no trecho acima.
	</td></tr>
	</table>
	
4. Finalmente, inicie o provisionamento de um cluster personalizado com o Solr instalado.  
	
		# START PROVISIONING A CLUSTER WITH SOLR INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

Quando solicitado, insira as credenciais para o cluster. Pode levar alguns minutos até que o cluster seja criado.


## <a name="usingSDK"></a>Instalar o Solr em clusters HDInsight do Hadoop usando o SDK do .NET

O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com o HDInsight em um aplicativo .NET. Esta seção fornece instruções sobre como usar a Ação de Script do SDK para provisionar um cluster com o Solr instalado. Os procedimentos a seguir devem ser executados:

- Instale o SDK do HDInsight .NET
- Criar um certificado autoassinado
- Criar um aplicativo de console
- Executar o aplicativo


**Para instalar o SDK do .NET do HDInsight**

Você pode instalar a compilação publicada mais recentemente do SDK no [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). As instruções serão mostradas no próximo procedimento.

**Para criar um certificado autoassinado**

Crie um certificado autoassinado, instale-o em sua estação de trabalho e o carregue para sua assinatura do Azure. Para obter instruções, consulte [Criar um certificado autoassinado](http://go.microsoft.com/fwlink/?LinkId=511138). 


**Para criar um aplicativo do Visual Studio**

1. Abra o Visual Studio 2013.

2. No menu Arquivo, clique em **Novo** e **Projeto**.

3. No Novo Projeto, digite ou selecione os seguintes valores:
	
	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propriedade</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valor</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Categoria</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Modelos/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Modelo</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Console Aplicativo</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateSolrCluster</td></tr>
	</table>

4. Clique em **OK** para criar o projeto.

5. No menu **Ferramentas**, clique em **Gerenciador de Pacotes Nuget** e, em seguida, clique em **Console do Gerenciador de Pacotes**.

6. Execute os seguintes comandos no console para instalar os pacotes.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Esse comando adiciona as bibliotecas .NET e referências a elas por meio do projeto atual do Visual Studio.

	
7. No Gerenciador de Soluções, clique duas vezes com o botão direito do mouse em **Program.cs** para abri-lo.

8. Adicione o seguinte usando as instruções na parte superior do arquivo :

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. Na função Main (), copie e cole o seguinte código e forneça valores para as variáveis:
		
        var clusterName = args[0];

        // PROVIDE VALUES FOR THE VARIABLES
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // PROVIDE THE CERTIFICATE THUMBPRINT TO RETRIEVE THE CERTIFICATE FROM THE CERTIFICATE STORE 
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // CREATE AN HDINSIGHT CLIENT OBJECT
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;
        
        // PROVIDE THE CLUSTER INFORMATION
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

10. Acrescente o seguinte código para a função Main () para usar a classe [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) para chamar um script personalizado para instalar o Solr.

		// ADD THE SCRIPT ACTION TO INSTALL Solr
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Solr", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode }, // List of nodes to install Solr on
          new Uri("https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1"), // Location of the script to install Solr
		  null //because the script used does not require any parameters.
        ));

11. E por último, crie o cluster.

		client.CreateCluster(clusterInfo);

11. Salve as alterações no aplicativo e crie a solução. 

**Para executar o aplicativo**

Abra um console do PowerShell, navegue até o local onde você salvou o projeto no Visual Studio, navegue até a pasta \bin\debug do projeto e, em seguida, execute o seguinte comando:

	.\CreateSolrCluster <cluster-name>

Forneça um nome de cluster e pressione ENTER para provisionar um cluster com o Solr instalado.


## Consulte também##
- [Instalar e usar o Spark em clusters HDInsight (visualização)][hdinsight-install-spark]. Use a personalização do cluster para instalar o Spark em clusters de Hadoop do HDInsight. O Spark é uma estrutura de processamento paralelo de código-fonte aberto que oferece suporte ao processamento de memória para melhorar o desempenho dos aplicativos de grandes volumes de dados analíticos.
- [Instalar o R em clusters HDInsight][hdinsight-install-r]. Use a personalização do cluster para instalar o R em clusters de Hadoop do HDInsight. R é um ambiente de computação de estatísticas e linguagem de fonte aberta e fornece centenas de funções estatísticas de compilação e sua própria linguagem de programação que combina aspectos da programação funcional e orientada a objeto. Ele também fornece recursos abrangentes de gráficos.
- [Instalar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install.md). Use a personalização do cluster para instalar o Giraph em clusters de Hadoop do HDInsight. O Giraph permite que você realize processamento de tabelas usando o Hadoop, além de poder ser utilizado com o HDInsight do Azure.




[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster

<!--HONumber=42-->
