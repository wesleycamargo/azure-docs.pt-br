<properties 
	pageTitle="Usar o R no HDInsight para personalizar clusters| Microsoft Azure" 
	description="Saiba como instalar e usar R para personalizar os clusters do Hadoop." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="bradsev"/>

# Instalar e usar R em clusters Hadoop do HDInsight (visualização)

Você pode instalar R em qualquer tipo de cluster no Hadoop no HDInsight usando a personalização de cluster **Ação de Script**. Isso permite que os analistas e cientistas de dados usem R para implantar a eficiente estrutura de programação MapReduce/YARN para processar grandes quantidades de dados em clusters Hadoop implantados no HDInsight.

A ação de script permite executar scripts para personalizar um cluster somente quando o cluster está sendo criado. Para obter mais informações, consulte [Personalizar cluster HDInsight usando a ação de script][hdinsight-cluster-customize].


## <a name="whatIs"></a>O que é R?

O <a href="http://www.r-project.org/" target="_blank">Projeto R para computação de estatísticas</a> é uma linguagem de software livre e um ambiente de computação de estatísticas. O R fornece centenas de funções estatísticas de compilação e uma linguagem própria de programação que combina aspectos da programação funcional e orientada a objeto. Ele também fornece recursos abrangentes de gráficos. O R é o ambiente de programação preferencial para a maioria dos profissionais estatísticos e cientistas em uma ampla variedade de campos.

Os scripts R podem ser executados em clusters Hadoop no HDInsight que foram personalizados usando a ação de Script quando criado para instalar o ambiente de R. O R é compatível com o armazenamento de Blob do Azure (WASB) para que os dados armazenados nele possam ser processados usando R no HDInsight.

## <a name="install"></a>Como instalar o R?

Um [exemplo de script](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1) para instalar o R em um cluster HDInsight está disponível em um blob somente leitura no armazenamento do Azure. Esta seção fornece instruções sobre como usar o script de exemplo ao provisionar o cluster usando o portal do Azure.

> [AZURE.NOTE]O script de exemplo foi introduzido com o cluster HDInsight versão 3.1. Para obter mais informações sobre versões do cluster HDInsight, consulte [Versões do cluster HDInsight](../hdinsight-component-versioning/).

1. Comece provisionando um cluster usando a opção **CRIAÇÃO PERSONALIZADA**, como descrito em [Provisionando um cluster usando opções personalizadas](../hdinsight-provision-clusters/#portal). 
2. Na página **Ações de Script** do assistente, clique em **Adicionar ação de script** para fornecer detalhes sobre as ações de script, como mostrado a seguir:

	![Usar Ação de Script para personalizar um cluster](./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "Usar Ação de Script para personalizar um cluster")
	
	<table border='1'>
	<tr><th>Propriedade</th><th>Valor</th></tr>
	<tr><td>Nome</td>
		<td>Especifique um nome para a ação de script, por exemplo, <b>Instalar R</b>.</td></tr>
	<tr><td>URI do script</td>
		<td>Especifique o URI para o script que é chamado para personalizar o cluster, por exemplo, <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i></td></tr>
	<tr><td>Tipo de nó</td>
		<td>Especifique os nós em que o script de personalização deve ser executado. Você pode escolher <b>Todos os nós</b>, <b>Somente nós principais</b> ou somente <b>Nós de trabalho</b>.
	<tr><td>Parâmetros</td>
		<td>Especifique os parâmetros, se exigido pelo script. No entanto, o script para instalar o R não requer nenhum parâmetro, por isso você pode deixá-los em branco.</td></tr>
</table>Você pode adicionar mais de uma ação de script para instalar vários componentes no cluster. Depois de adicionar os scripts, clique na marca de seleção para iniciar o provisionamento do cluster.

Você também pode usar o script para instalar o R no HDInsight usando o PowerShell do Azure ou o SDK do .NET do HDInsight. Instruções para esses procedimentos são fornecidas posteriormente neste artigo.

## <a name="useR"></a>Como executar scripts R com o HDInsight?
Esta seção descreve como executar um script de R no cluster Hadoop com o HDInsight.

1. **Estabelecer uma conexão de área de trabalho remota para o cluster**: no portal do Azure, habilite a área de trabalho remota para o cluster que você criou com o R instalado e, em seguida, conecte-se a remotamente ao cluster. Para instruções, consulte <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Conectar-se a clusters HDInsight usando RDP</a>.

2. **Abrir o console do R**: a instalação de R coloca um link para o console de R na área de trabalho do nó principal. Clique para abrir o console de R.

3. **Executar o script de R**: o script de R pode ser executado diretamente do console de R colando-o, selecionando-o e pressionando ENTER. Este é um script de exemplo simples que gera os números de 1 a 100 e, em seguida, os multiplica por 2.

		library(rmr2)
		library(rhdfs)
		ints = to.dfs(1:100)
		calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
		from.dfs(calc)

As duas primeiras linhas chamam as bibliotecas R Hadoop que são instaladas com o R. A linha final imprime os resultados no console. O resultado deve ser assim:

	[1,]  1 2
	[2,]  2 4
	.
	.
	.
	[98,]  98 196
	[99,]  99 198
	[100,] 100 200

## <a name="usingPS"></a>Instalar o R no HDInsight usando o Azure PowerShell

Nesta seção, usamos o cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** para invocar scripts usando a Ação de Script para personalizar um cluster. Antes de prosseguir, verifique se você instalou e configurou o PowerShell do Azure. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do PowerShell do HDInsight, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

Execute as seguintes etapas:

1. Abra o console do PowerShell do Azure e declare as variáveis a seguir:

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
		$version = "<HDInsightClusterVersion>"          # HDInsight version, for example "3.1"
	
2. Especifique os valores de configuração (como nós no cluster) e o armazenamento padrão a ser usado.

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. Use o cmdlet **Add-AzureHDInsightScriptAction** para invocar o script de exemplo para instalar o R. Por exemplo:

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install R"  -ClusterRoleCollection HeadNode,DataNode -Uri https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1


	O cmdlet **Add-AzureHDInsightScriptAction** usa os parâmetros a seguir:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Parâmetro</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Definição</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Config</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Objeto de configuração em que as informações da ação de script são adicionadas</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome da ação de script</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Especifica os nós em que o script de personalização é executado. Os valores válidos são **HeadNode** (para instalar o nó principal) ou **DataNode** (para instalar em todos os nós de dados). Você pode usar um ou ambos os valores.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parâmetros</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parâmetros exigidos pelo script 
</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Especifica o URI para o script que é executado</td></tr>
</table>
	
4. Por fim, provisione o cluster personalizado para que R seja instalado.
	
		# PROVISION A CLUSTER WITH R INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

Quando solicitado, insira as credenciais para o cluster. Pode levar alguns minutos até que o cluster seja criado.


## <a name="usingSDK"></a>Instalar R no HDInsight usando o SDK do .NET

O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com o HDInsight em um aplicativo .NET.

Realize os procedimentos a seguir para provisionar um cluster HDInsight usando o SDK:

- [Instalar o SDK do .NET do HDInsight](#installSDK)
- [Criar um certificado autoassinado](#createCert)
- [Criar um aplicativo .NET no Visual Studio](#createApp)
- [Executar o aplicativo](#runApp)

As seções a seguir mostram como executar esses procedimentos.

### <a name="installSDK"></a>Para instalar o SDK do .NET do HDInsight

Você pode instalar a compilação publicada mais recentemente do SDK no [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). As instruções serão mostradas no próximo procedimento.

### <a name="createCert"></a>Para criar um certificado autoassinado

Crie um certificado autoassinado, instale-o em sua estação de trabalho e o carregue para sua assinatura do Azure. Para obter instruções, consulte [Criar um certificado autoassinado](http://go.microsoft.com/fwlink/?LinkId=511138).


### <a name="createApp"></a>Para criar um aplicativo .NET no Visual Studio

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
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateRCluster</td></tr>
</table>

4. Clique em **OK** para criar o projeto.

5. No menu **Ferramentas**, clique em **Gerenciador de Pacotes NuGet** e em **Console do Gerenciador de Pacotes**.

6. Execute o comando a seguir no console para instalar os pacotes.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Esse comando adiciona as bibliotecas .NET e referências a elas a partir do projeto atual do Visual Studio.

7. No **Gerenciador de Soluções**, clique duas vezes em **Program.cs** para abri-lo.

8. Adicione as seguintes instruções **using** à parte superior do arquivo:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. Na função **Main()**, copie e cole o código a seguir e forneça valores para as variáveis:
		
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

10. Acrescente o código a seguir à função **Main()** para usar a classe [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx), fazendo-a invocar um script personalizado para instalar o R.

		// ADD THE SCRIPT ACTION TO INSTALL R

        clusterInfo.ConfigActions.Add(new ScriptAction(
            "Install R",
            new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode },
            new Uri("https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1"), null
            ));

11. Por fim, crie o cluster:

		client.CreateCluster(clusterInfo);

11. Salve as alterações no aplicativo e crie a solução.

### <a name="runApp"></a>Para executar o aplicativo

Abra um console do Azure PowerShell, navegue até o local onde você salvou o projeto, navegue até a pasta \\bin\\debug do projeto e, em seguida, execute o seguinte comando:

	.\CreateRCluster <cluster-name>

Forneça um nome de cluster e pressione ENTER para provisionar um cluster com R instalado.

## <a name="seeAlso"></a>Consulte também

- [Instalar e usar Spark em clusters HDInsight][hdinsight-install-spark] para obter instruções sobre como usar o cluster de personalização para instalar e usar o Spark em clusters Hadoop do HDInsight. O Spark é uma estrutura de processamento paralelo de código-fonte aberto que oferece suporte ao processamento de memória para melhorar o desempenho dos aplicativos analíticos de big data.
- [Instalar o Giraph em clusters HDInsight](../hdinsight-hadoop-giraph-install). Use a personalização do cluster para instalar o Giraph em clusters de Hadoop do HDInsight. O Giraph permite que você realize processamento de tabelas usando o Hadoop, além de poder ser utilizado com o HDInsight do Azure.
- [Instalar o Solr em clusters HDInsight](../hdinsight-hadoop-solr-install). Use a personalização do cluster para instalar o Solr em clusters de Hadoop do HDInsight. O Solr permite que você execute operações de pesquisa poderosas nos dados armazenados.

[powershell-install-configure]: ../install-configure-powershell.md
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
 

<!---HONumber=July15_HO2-->