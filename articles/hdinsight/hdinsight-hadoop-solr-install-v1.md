<properties 
	pageTitle="Use a ação de Script no HDInsight para instalar o Solr em cluster do Hadoop | Microsoft Azure" 
	description="Saiba como personalizar o cluster HDInsight com o Solr. Você usará uma opção de configuração de Ação de Script para usar um script para instalar o Solr." 
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
	ms.date="08/07/2015" 
	ms.author="nitinme"/>

# Instalar e usar o Solr em clusters HDInsight do Hadoop

Você pode instalar o Solr em qualquer tipo de cluster no Hadoop no Azure HDInsight usando a personalização de cluster da **Ação de Script**. A Ação de Script permite executar scripts para personalizar um cluster somente quando o cluster está sendo criado. Para obter mais informações, consulte [Personalizar cluster HDInsight usando a ação de script][hdinsight-cluster-customize].

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Instalar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install.md)

Neste tópico, você aprenderá como instalar o Solr usando a Ação de Script. O Solr é uma plataforma de pesquisa poderosa e oferece recursos de pesquisa em nível corporativo para os dados gerenciados pelo Hadoop. Depois de instalar o Solr no cluster HDInsight, você também aprenderá como pesquisar dados usando o Solr.

> [AZURE.NOTE]O script de exemplo usado neste tópico cria um cluster Solr com uma configuração específica. Se você quiser configurar o cluster Solr com diferentes coleções, fragmentos, esquemas, réplicas, etc., você deve modificar o script e os binários do Solr adequadamente.


## <a name="whatis"></a>O que é Solr?

O <a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> é uma plataforma de pesquisa empresarial que habilita operações poderosas de pesquisa de texto completo nos dados. Enquanto o Hadoop permite armazenar e gerenciar grandes quantidades de dados, o Apache Solr oferece os recursos de pesquisa para recuperar rapidamente os dados. Este tópico fornece instruções sobre como personalizar um cluster HDInsight para instalar o Solr.

## <a name="install"></a>Como instalar o Solr?

Um exemplo de script para instalar o R em um cluster HDInsight está disponível em um blob de armazenamento do Azure somente leitura em [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1). Esta seção fornece instruções sobre como usar o script de exemplo ao provisionar o cluster usando o portal do Azure.


> [AZURE.NOTE]O script de exemplo funciona apenas com o cluster HDInsight versão 3.1. Para obter mais informações sobre as versões do cluster HDInsight, consulte [Versões do cluster HDInsight](hdinsight-component-versioning.md).


1. Comece provisionando um cluster usando a opção **CRIAÇÃO PERSONALIZADA**, como descrito em [Provisionando um cluster usando opções personalizadas](hdinsight-provision-clusters.md#portal). 
2. Na página **Ações de Script** do assistente, clique em **Adicionar ação de script** para fornecer detalhes sobre a ação de script, como mostrado abaixo:

	![Usar Ação de Script para personalizar um cluster](./media/hdinsight-hadoop-solr-install-v1/hdi-script-action-solr.png "Usar Ação de Script para personalizar um cluster")
	
	<table border='1'>
	<tr><th>Propriedade</th><th>Valor</th></tr>
	<tr><td>Nome</td>
		<td>Especifique um nome para a ação de script. Por exemplo, <b>Instalar o Solr</b>.</td></tr>
	<tr><td>URI do script</td>
		<td>Especifique o URI (Uniform Resource Identifier) do script invocado para personalizar o cluster. Por exemplo, <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
	<tr><td>Tipo de nó</td>
		<td>Especifique os nós em que o script de personalização deve ser executado. Você pode escolher <b>Todos os nós</b>, <b>Somente nós principais</b> ou <b>Somente nós de trabalho</b>.
	<tr><td>Parâmetros</td>
		<td>Especifique os parâmetros, se exigido pelo script. O script para instalar o Solr não requer nenhum parâmetro, portanto você pode deixá-los em branco.</td></tr>
</table>Você pode adicionar mais de uma ação de script para instalar vários componentes no cluster. Depois de adicionar os scripts, clique na marca de seleção para iniciar o provisionamento do cluster.

Você também pode usar o script para instalar o Solr no HDInsight usando o PowerShell do Azure ou o SDK do .NET do HDInsight. Instruções para esses procedimentos são fornecidas posteriormente neste tópico.

## <a name="usesolr"></a>Como usar o Solr no HDInsight?

Você deve começar com indexação Solr, com alguns arquivos de dados. Em seguida, você pode usar o Solr para executar consultas de pesquisa em dados indexados. Execute as seguintes etapas para usar o Solr em um cluster HDInsight:

1. **Usar protocolo RDP (desktop remoto) para remoto para o cluster HDInsight com o Solr instalado**. No Portal do Azure, habilite a Área de Trabalho Remota para o cluster criado com Solr instalado e, em seguida, acesse remotamente o cluster. Para instruções, consulte <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Conectar-se a clusters HDInsight usando RDP</a>.

2. **Indexar o Solr carregando arquivos de dados**. Quando você indexa o Solr, coloque os documentos que você pode precisar pesquisar. Para indexar o Solr, acesso o cluster remotamente via RDP, navegue até a área de trabalho, abra a linha de comando do Hadoop e navegue até **C:\\apps\\dist\\solr-4.7.2\\example\\exampledocs**. Execute o comando a seguir:
	
		java -jar post.jar solr.xml monitor.xml

	Você verá a saída a seguir no console:

		POSTing file solr.xml
		POSTing file monitor.xml
		2 files indexed.
		COMMITting Solr index changes to http://localhost:8983/solr/update..
		Time spent: 0:00:01.624

	O utilitário post.jar indexa o Solr com dois documentos de exemplo, **solr.xml** e **monitor.xml**. O utilitário post.jar e os documentos de exemplo estão disponíveis com a instalação do Solr.

3. **Use o painel do Solr para pesquisar nos documentos indexados**. Na sessão RDP ao cluster HDInsight, abra o Internet Explorer e inicie o painel do Solr em ****http://headnodehost:8983/solr/#/**. No painel esquerdo, na lista suspensa **Seletor de Núcleo**, selecione **coleção1** e, dentro dessa opção, clique em **Consulta**. Por exemplo, para selecionar e retornar todos os documentos em Solr, forneça os seguintes valores:
	1. Na caixa de texto **q**, digite ***:***. Isso retornará como resultado todos os documentos que são indexados em Solr. Se você quiser procurar uma cadeia de caracteres específica dentro dos documentos, você pode inserir essa cadeia de caracteres aqui.
	2. Na caixa de texto **wt**, selecione o formato de saída. O padrão é **json**. Clique em **Executar consulta**.

		![Usar Ação de Script para personalizar um cluster](./media/hdinsight-hadoop-solr-install-v1/hdi-solr-dashboard-query.png "Executar uma consulta no painel do Solr")
	
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
			          "30" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
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
   

4. **Recomendado: fazer backup dos dados indexados do Solr para o Blob de Armazenamento do Azure (WASB) associado ao cluster HDInsight**. Como uma prática recomendada, você deve fazer backup dos dados indexados de nós do cluster Solr no armazenamento de Blob do Azure. Execute as seguintes etapas para fazê-lo:

	1. Na sessão RDP, abra o Internet Explorer e insira a seguinte URL:

			http://localhost:8983/solr/replication?command=backup

		Você verá uma resposta semelhante a essa:

			<?xml version="1.0" encoding="UTF-8"?>
			<response>
			  <lst name="responseHeader">
			    <int name="status">0</int>
			    <int name="QTime">9</int>
			  </lst>
			  <str name="status">OK</str>
			</response>

	2. Na sessão remota, navegue até {HOME\_DO\_SOLR}\\{Coleção}\\data. Para o cluster criado usando o script de exemplo, esse caminho deve ser **C:\\apps\\dist\\solr-4.7.2\\example\\solr\\collection1\\data**. Nesse local, você deve ver uma pasta de capturas de tela criada com um nome semelhante a **snapshot.*timestamp***.
	
	3. Compacte a pasta de capturas de tela e carregue-la no armazenamento de Blob do Azure. Na linha de comando do Hadoop, navegue até o local da pasta de capturas de tela usando o seguinte comando:

			  hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

		Este comando copia a capturas de tela para /example/data/ sob o contêiner na conta de armazenamento padrão associada ao cluster.

## <a name="usingPS"></a>Instalar o Solr em clusters Hadoop do HDInsight usando o PowerShell do Azure

Nesta seção, usamos o cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** para invocar scripts, usando a Ação de Script para personalizar um cluster. Antes de prosseguir, verifique se você instalou e configurou o PowerShell do Azure. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do Windows PowerShell do HDInsight, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

Execute as seguintes etapas:

1. Abra uma janela do PowerShell do Azure e declare as variáveis a seguir:

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure Storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# Number of nodes in the HDInsight cluster
		$version = "<HDInsightClusterVersion>"          # For example, "3.1"
	
2. Especifique os valores de configuração como nós no cluster e o armazenamento padrão a ser usado.

		# Specify the configuration options
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. Use o cmdlet **Add-AzureHDInsightScriptAction** para adicionar uma ação de script à configuração do cluster. Mais tarde, quando o cluster estiver sendo criado, a ação de script será executada.

		# Add the script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Solr" -ClusterRoleCollection HeadNode,DataNode -Uri https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1

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
	
4. Finalmente, inicie o provisionamento de um cluster personalizado com o Solr instalado.
	
		# Start provisioning a cluster with Solr installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

Quando solicitado, insira as credenciais para o cluster. Pode levar alguns minutos até que o cluster seja criado.


## <a name="usingSDK"></a>Instalar o Solr em clusters HDInsight do Hadoop usando o SDK do .NET

O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com o HDInsight em um aplicativo .NET Framework. Esta seção fornece instruções sobre como usar a Ação de Script do SDK para provisionar um cluster com o Solr instalado. Os procedimentos a seguir devem ser executados:

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
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateSolrCluster</td></tr>
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

10. Acrescente o seguinte código para a função Main() para usar a classe [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx), fazendo-a invocar um script personalizado para instalar o Solr.

		// Add the script action to install Solr
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Solr", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode }, // List of nodes to install Solr on
          new Uri("https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1"), // Location of the script to install Solr
		  null //Because the script used does not require any parameters
        ));

11. E por último, crie o cluster.

		client.CreateCluster(clusterInfo);

11. Salve as alterações no aplicativo e crie a solução.

**Para executar o aplicativo**

Abra um console do Azure PowerShell ou Windows PowerShell, navegue até o local onde você salvou o projeto no Visual Studio, navegue até a pasta \\bin\\debug do projeto e, em seguida, execute o seguinte comando:

	.\CreateSolrCluster <cluster-name>

Forneça um nome de cluster e pressione ENTER para provisionar um cluster com o Solr instalado.


## Consulte também##
- [Instalar e usar o Spark em clusters HDInsight][hdinsight-install-spark]. Use a personalização do cluster para instalar o Spark em clusters de Hadoop do HDInsight. O Spark é uma estrutura de processamento paralelo de software livre que dá suporte a processamento na memória para melhorar o desempenho de aplicativos analíticos de Big Data.
- [Instalar o R em clusters HDInsight][hdinsight-install-r]. Use a personalização do cluster para instalar o R em clusters de Hadoop do HDInsight. R é uma linguagem e ambiente de software livre para computação estatística. Ele fornece centenas de funções estatísticas internas e sua própria linguagem de programação, que combina aspectos de programação funcional e de programação orientada a objetos. Ele também fornece recursos abrangentes de gráficos.
- [Instalar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install.md). Use a personalização do cluster para instalar o Giraph em clusters de Hadoop do HDInsight. O Giraph permite que você realize processamento de tabelas usando o Hadoop, além de poder ser usado com o HDInsight do Azure.



[powershell-install-configure]: ../install-configure-powershell.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
 

<!---HONumber=August15_HO8-->