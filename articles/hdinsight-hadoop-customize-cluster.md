<properties 
	pageTitle="Personalizar os Clusters HDInsight usando a ação de Script| Azure" 
	description="Saiba como personalizar os clusters HDInsight usando a ação de Script." 
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
	ms.date="01/15/2015" 
	ms.author="nitinme"/> 

# Personalizar os clusters HDInsight usando a Ação de Script

Você pode personalizar um cluster HDInsight do Azure para instalar software adicional em um cluster, ou para alterar a configuração de aplicativos no cluster. O HDInsight fornece uma opção de configuração chamada **ação de Script** que chama scripts personalizados, que definem a personalização para ser executada no cluster. Esses scripts podem ser usados para personalizar um cluster *as it is being deployed*.  

Os clusters HDInsight podem ser personalizados em diversas maneiras, como, incluindo contas de armazenamento adicional, alterando os arquivos de configuração do hadoop (core-site, hive-site, etc.) ou adicionando bibliotecas compartilhadas (p. ex.: Hive, Oozie) em locais comuns do cluster. Essas personalizações podem ser feitas usando o PowerShell do HDInsight, o SDK do .NET ou o Portal de Gerenciamento. Para obter mais informações, consulte [Provisionar clusters do Hadoop no HDInsight usando as opções personalizadas][hdinsight-provision-cluster].



> [AZURE.NOTE] Usar a ação de Script para personalizar um cluster tem suporte apenas no cluster HDInsight versão 3.1. Para obter mais informações sobre versões do cluster HDInsight, consulte [versões do cluster HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/).



## Neste artigo

- [Como o script é usado durante a criação do cluster?](#lifecycle)
- [Como escrever um script para personalização do cluster?](#writescript)
- [Como usar a ação de Script para personalizar o cluster?](#howto)
- [Exemplos de personalização do cluster](#example)
- [Suporte para software de código-fonte aberto usada no cluster HDInsight](#support)


## <a name="lifecycle"></a>Como o script é usado durante a criação do cluster?

Usando a ação de Script, um cluster HDInsight pode ser personalizado somente enquanto ele está sendo criado. Enquanto um cluster HDInsight está sendo criado, ele passa pelos seguintes estágios:

![HDInsight cluster customization and stages during cluster provisioning][img-hdi-cluster-states] 

O script é chamado após a criação de cluster completar o estágio *HDInsightConfiguration* e antes do estágio *ClusterOperational*. Cada cluster pode aceitar várias ações de script que são chamadas na ordem em que elas são especificadas.

> [AZURE.NOTE] A opção de personalizar os clusters HDInsight está disponível como parte das assinaturas padrões do HDInsight do Azure sem nenhum custo adicional.

### Como o script funciona?

Você tem a opção de executar o script no headnode, nos nós de trabalho ou em ambos. Quando o script é executado, o cluster entra no estágio *ClusterCustomization*. Nesse estágio, o script é executado na conta de administrador do sistema, em paralelo com todos os nós especificados no cluster e fornece privilégios totais de administrador nos nós. 

> [AZURE.NOTE] Por ter privilégios administrativos nos nós de cluster durante o estágio *Cluster customization*, você pode usar o script para executar operações como parar e iniciar serviços, incluindo serviços relacionados ao Hadoop. Como parte do script, você deve verificar que os serviços de Ambari e outros serviços relacionados ao Hadoop estão em funcionamento antes do script terminar a execução. Esses serviços são necessários para verificar a integridade e o estado do cluster enquanto ele estiver sendo criado. Se você alterar qualquer configuração no cluster que afete esses serviços, é necessário usar as funções auxiliares que são fornecidas. Para obter mais informações sobre funções auxiliares, consulte [desenvolvimento de ação de Script com o HDInsight][hdinsight-write-script].

O resultado, bem como os logs de erro do script são armazenados na conta de armazenamento padrão que você especificou para o cluster. Os logs são armazenados em uma tabela com o nome *u<\cluster-name-fragment><\time-stamp>setuplog*. Esses são logs de agregação de scripts executados em todos os nós (nós headnode e de trabalho) no cluster.

## <a name="writescript"></a>Como escrever um script para personalização do cluster?

Para obter informações sobre como escrever um script de personalização do cluster, consulte [desenvolvimento de ação de Script com o HDInsight][hdinsight-write-script]. 

## <a name="howto"></a>Como usar a ação de Script para personalizar o cluster?

Você pode usar a ação de Script por meio do Portal de Gerenciamento do Azure, cmdlets do PowerShell ou SDK do .NET do HDInsight para personalizar um cluster. 

**No Portal de gerenciamento**

1. Inicie o provisionamento de um cluster usando a opção **criação personalizada**, conforme descrito em [Provisionar um cluster usando opções personalizadas](http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/#portal). 
2. Na página ações de Script do assistente, clique em **Adicionar ação de script** para fornecer detalhes sobre a Ação de Script, como mostrado abaixo:

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CustomProvision.Page6.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>Propriedade</th><th>Valor</th></tr>
		<tr><td>Nome</td>
			<td>Especifique um nome para a ação de script.</td></tr>
		<tr><td>URI do script</td>
			<td>Especifique o URI para o script que é chamado para personalizar o cluster.</td></tr>
		<tr><td>Tipo de nó</td>
			<td>Especifica os nós em que o script de personalização é executado. Você pode escolher <b>Todos os nós</b>, <b>Somente nós headnode</b> ou <b>Somente nós de trabalho</b>.
		<tr><td>Parâmetros</td>
			<td>Especifique os parâmetros, se exigido pelo script.</td></tr>
	</table>

	Você pode adicionar mais de uma ação de script para instalar vários componentes no cluster. Depois de adicionar os scripts, clique na marca de seleção para iniciar o provisionamento do cluster. 
  
**Usando os cmdlets do PowerShell**

Use os comandos do PowerShell do HDInsight para executar uma única Ação de Script ou várias Ações de Script. Você pode usar o cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** para chamar scripts personalizados. Para usar esses cmdlets, você deve ter instalado e configurado o PowerShell do Azure. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do PowerShell do HDInsight, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

Use o seguinte comando do PowerShell para executar uma única Ação de Script ao implantar um cluster HDInsight:

	$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName -Uri http://uri.to/scriptaction.ps1 -Parameters MyScriptActionParameter -ClusterRoleCollection HeadNode,DataNode

	New-AzureHDInsightCluster -Config $config

Use o seguinte comando do PowerShell para executar várias Ações de Script ao implantar um cluster HDInsight:

	$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName1 -Uri http://uri.to/scriptaction1.ps1 -Parameters MyScriptAction1Parameters -ClusterRoleCollection HeadNode,DataNode | Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName2 -Uri http://uri.to/scriptaction2.ps1 -Parameters MyScriptAction2Parameters -ClusterRoleCollection HeadNode

	New-AzureHDInsightCluster -Config $config

**Usando o SDK do .NET do HDInsight**

O SDK do .NET do HDInsight fornece uma classe <a href="http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx" target="_blank">ScriptAction</a> para chamar scripts personalizados. Para usar o SDK do .NET do HDInsight:

1. Crie um aplicativo do Visual Studio e, em seguida, instale o SDK por meio do Nuget. No menu **Ferramentas**, clique em **Gerenciador de Pacotes Nuget** e, em seguida, clique em **Console do Gerenciador de Pacotes**. Execute os seguintes comandos no console para instalar o pacote:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

2. Crie um cluster usando o SDK. Para obter instruções, consulte [Provisionar cluster HDInsight usando o SDK do .NET](http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/#sdk).

3. Use a classe **ScriptAction** para chamar um script personalizado, como mostrado abaixo:

		
		var clusterInfo = new ClusterCreateParameters()
		{
			// PROVIDE THE CLUSTER INFORMATION LIKE
			// NAME, STORAGE ACCOUNT, CREDENTIALS,
			// CLUSTER SIZE, and VERSION		    
			...
			...
		};

		// ADD THE SCRIPT ACTION TO INSTALL SPARK
		clusterInfo.ConfigActions.Add(new ScriptAction(
	  		"MyScriptActionName", // Name of the config action
	  		new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install component on
	  		new Uri("http://uri.to/scriptaction.ps1"), // Location of the script to install the component
	  		"MyScriptActionParameter" //Parameters, if any, required by the script.
		));


## <a name="example"></a>Exemplos de personalização do cluster

Para começar, o HDInsight oferece scripts de exemplo para instalar os componentes a seguir em um cluster HDInsight.

- **Instalar Spark**. Consulte [Instalar Spark em clusters HDInsight][hdinsight-install-spark].
- **Instalar R**. Consulte [Instalar R em clusters HDInsight][hdinsight-install-r].
- **Instalar Solr**. [Instalar e usar o Solr em clusters HDInsight](../hdinsight-hadoop-solr-install)
- **Instalar Giraph**. [Instalar e usar o Giraph em clusters HDInsight](../hdinsight-hadoop-giraph-install)

## <a name="support"></a>Suporte para software de código-fonte aberto usada no cluster HDInsight
O serviço Microsoft Azure HDInsight é uma plataforma flexível que permite que você crie aplicativos de big data na nuvem usando o ecossistema das tecnologias de código-fonte aberto formado em torno do Hadoop. O Microsoft Azure fornece o nível geral de suporte para tecnologias de código-fonte aberto conforme discutido na <a href="http://azure.microsoft.com/support/faq/" target="_blank">seção Escopo de Suporte do site de perguntas Frequentes de suporte do Azure</a>. Além disso o serviço HDInsight fornece um nível adicional de suporte para alguns dos componentes, conforme descrito abaixo.

Há dois tipos de componentes de código-fonte aberto que estão disponíveis no serviço HDInsight:

- **Componentes internos**. Esses componentes são pré-instalados em clusters HDInsight e oferecem a funcionalidade principal do cluster. Por exemplo, Gerenciador de recursos de Yarn, linguagem de consulta de Hive e Biblioteca Mahout pertencem a essa categoria. A lista completa de componentes de cluster está disponível <a href="http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/" target="_blank">aqui</a>.
- **Componentes personalizados**. Você, como um usuário do cluster, pode instalar ou usar em sua carga de trabalho qualquer componente disponível na comunidade ou criado por você.

Componentes internos são totalmente compatíveis e o Suporte da Microsoft ajuda a isolar e resolver problemas relacionados a esses componentes.

Componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. Isso pode resultar na resolução do problema ou na solicitação de você buscar nos canais disponíveis as tecnologias de código-fonte aberto, onde é possível encontrar conhecimento aprofundado sobre essa tecnologia. Por exemplo, há muitos sites da comunidade que podem ser usados, como: <a href ="https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight" target="_blank">Fórum do MSDN para HDInsight</a>, <a href="http://stackoverflow.com" target="_blank">http://stackoverflow.com</a>. Além disso, projetos do Apache têm sites de projeto em <a href="http://apache.org" target="_blank">http://apache.org</a>, por exemplo: <a href="http://hadoop.apache.org/" target="_blank">Hadoop</a>, <a href="http://spark.apache.org/" target="_blank">Spark</a>.

O serviço HDInsight oferece várias maneiras de usar componentes personalizados. Independentemente de como o componente for usado ou instalado no cluster, aplica-se o mesmo nível de suporte. Abaixo está uma lista das maneiras mais comuns em que os componentes personalizados podem ser usados em clusters HDInsight.

1. Envio de trabalho. Hadoop ou outros tipos de trabalhos podem ser enviados para o cluster que executa ou usa componentes personalizados.
2. Personalização do cluster. Durante a criação do cluster, você pode especificar configurações adicionais e componentes personalizados que serão instalados nos nós do cluster.
3. Exemplos. Para componentes personalizados populares, a Microsoft e outros podem fornecer exemplos de como esses componentes podem ser usados nos clusters HDInsight. Esses exemplos são fornecidos sem suporte.


## Consulte também##
[Provisionar os clusters de Hadoop no HDInsight usando opções personalizadas][hdinsight-provision-cluster] fornece instruções sobre como provisionar um cluster HDInsight usando outras opções personalizadas.

[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-write-script]: ../hdinsight-hadoop-script-actions/
[hdinsight-provision-cluster]: ../hdinsight-provision-clusters/
[powershell-install-configure]: ../install-configure-powershell/


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Stages during cluster provisioning"
<!--HONumber=42-->
