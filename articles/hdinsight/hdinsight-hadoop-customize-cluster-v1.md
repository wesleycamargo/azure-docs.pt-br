<properties 
	pageTitle="Personalizar os clusters HDInsight usando ações de script | Microsoft Azure" 
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
	ms.date="11/29/2015" 
	ms.author="nitinme"/>

# Personalizar os clusters HDInsight usando a Ação de Script

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Personalizar os clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md)

O HDInsight oferece uma opção de configuração chamada **Ação de Script**; ela invoca scripts personalizados que definem a personalização a ser executada no cluster durante o processo de provisionamento. Esses scripts podem ser usados para instalar software adicional em um cluster ou para alterar a configuração de aplicativos em um cluster.


> [AZURE.NOTE]Há suporte para Ação de Script apenas no cluster do HDInsight versão 3.1 ou superior com o sistema operacional Windows. Para obter mais informações sobre as versões do cluster HDInsight, consulte [Versões do cluster HDInsight](hdinsight-component-versioning.md).
> 
> A Ação de Script está disponível como parte das assinaturas do Azure HDInsight padrão sem custo adicional.

Clusters HDInsight também podem ser personalizados de várias outras formas, como incluir contas adicionais do Armazenamento do Azure, alterar os arquivos de configuração do Hadoop (core-site.xml, hive-site.xml, etc.) ou adicionar bibliotecas compartilhadas (p.ex., Hive, Oozie) em locais comuns no cluster. Essas personalizações podem ser feitas por meio do Azure PowerShell, SDK do .NET do Azure HDInsight ou Portal Clássico do Azure. Para obter mais informações, consulte [Provisionar clusters Hadoop no HDInsight usando opções personalizadas][hdinsight-provision-cluster].

## Ação de script no processo de provisionamento do cluster

A Ação de Script só é usada enquanto um cluster está sendo criado. O diagrama a seguir ilustra quando a Ação de Script é executada durante o processo de provisionamento:

![Personalização do cluster HDInsight e estágios durante o provisionamento de cluster][img-hdi-cluster-states]

Quando o script é executado, o cluster entra no estágio **ClusterCustomization**. Nesse estágio, o script é executado na conta de administrador do sistema, em paralelo com todos os nós especificados no cluster e fornece privilégios totais de administrador nos nós.

> [AZURE.NOTE]Por ter privilégios administrativos nos nós de cluster durante o estágio **ClusterCustomization**, você pode usar o script para executar operações como parar e iniciar serviços, inclusive serviços relacionados ao Hadoop. Logo, como parte do script, você deve garantir que os serviços Ambari e outros serviços relacionados ao Hadoop estejam funcionando antes do script terminar a execução. Esses serviços são necessários para verificar a integridade e o estado do cluster enquanto ele estiver sendo criado. Se você alterar qualquer configuração no cluster que afete esses serviços, é necessário usar as funções auxiliares que são fornecidas. Para obter mais informações sobre funções auxiliares, confira [Desenvolver scripts de Ação de Script para o HDInsight][hdinsight-write-script].

Os logs de saída e de erros do script são armazenados na conta padrão do Armazenamento que você especificou para o cluster. Os logs são armazenados em uma tabela de nome **u<\\fragmento-do-nome-do-cluster><\\carimbo-de-data-e-hora>setuplog**. São logs agregados dos scripts executados em todos os nós (nó de cabeçalho e nós de trabalho) no cluster.


Cada cluster pode aceitar várias ações de script, que são invocadas na ordem em que elas são especificadas. Um script pode ser executado no nó principal, nos nós de trabalho ou em ambos.

## Scripts de Ação de Chamar Script

Scripts da Ação de Script podem ser usados no Portal Clássico do Azure, Azure PowerShell ou SDK do .NET do HDInsight.

O HDInsight fornece vários scripts para instalar os seguintes componentes em clusters do HDInsight:

Nome | Script
----- | -----
**Instalar Spark** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. Confira [Instalar e usar o Spark em clusters HDInsight][hdinsight-install-spark].
**Instalar R** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. Confira [Instalar e usar o R em clusters HDInsight][hdinsight-install-r].
**Instalar Solr** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. Confira [Instalar e usar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install.md).
- **Instalar o Giraph** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. Confira [Instalar e usar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install.md).



**No Portal Clássico do Azure**

1. Comece provisionando um cluster usando a opção **CRIAÇÃO PERSONALIZADA**, como descrito em [Provisionando um cluster usando opções personalizadas](hdinsight-provision-clusters.md#portal). 
2. Na página **Ações de Script** do assistente, clique em **Adicionar ação de script** para fornecer detalhes sobre a ação de script, como mostrado abaixo:

	![Usar Ação de Script para personalizar um cluster](./media/hdinsight-hadoop-customize-cluster-v1/HDI.CustomProvision.Page6.png "Usar Ação de Script para personalizar um cluster")
	
	<table border='1'>
	<tr><th>Propriedade</th><th>Valor</th></tr>
	<tr><td>Nome</td>
		<td>Especifique um nome para a ação de script.</td></tr>
	<tr><td>URI do script</td>
		<td>Especifique o URI para o script que é chamado para personalizar o cluster. s</td></tr>
	<tr><td>Tipo de nó</td>
		<td>Especifique os nós em que o script de personalização deve ser executado. Você pode escolher <b>Todos os Nós</b>, <b>Somente nós principais</b> ou <b>Somente nós de trabalho</b>.
	<tr><td>Parâmetros</td>
		<td>Especifique os parâmetros, se exigido pelo script.</td></tr>
</table>Você pode adicionar mais de uma ação de script para instalar vários componentes no cluster.

3. Clique na marca de seleção para começar a provisionar o cluster.
  
**De cmdlets do Azure PowerShell**

Use comandos do PowerShell do Azure para HDInsight para executar uma ação de script única ou várias ações de script. Você pode usar o cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** para invocar scripts personalizados. Para usar esses cmdlets, você deve ter instalado e configurado o PowerShell do Azure. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do PowerShell do Azure para HDInsight, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

Use os seguintes comandos do PowerShell do Azure para executar uma ação de script única ao implantar um cluster HDInsight:

	$config = New-AzureHDInsightClusterConfig –ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config –Name MyScriptActionName –Uri http://uri.to/scriptaction.ps1 –Parameters MyScriptActionParameter -ClusterRoleCollection HeadNode,DataNode

	New-AzureHDInsightCluster -Config $config

Use os seguintes comandos do PowerShell do Azure para executar várias ações de script ao implantar um cluster HDInsight:

	$config = New-AzureHDInsightClusterConfig –ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config –Name MyScriptActionName1 –Uri http://uri.to/scriptaction1.ps1 –Parameters MyScriptAction1Parameters -ClusterRoleCollection HeadNode,DataNode | Add-AzureHDInsightScriptAction -Config $config –Name MyScriptActionName2 –Uri http://uri.to/scriptaction2.ps1 -Parameters MyScriptAction2Parameters -ClusterRoleCollection HeadNode

	New-AzureHDInsightCluster -Config $config

**Do HDInsight .NET SDK**

O HDInsight .NET SDK fornece uma classe <a href="http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx" target="_blank">ScriptAction</a> para invocar scripts personalizados. Para usar o SDK do .NET do HDInsight:

1. Crie um aplicativo do Visual Studio e instale o SDK por meio do NuGet. No menu **Ferramentas**, clique em **Gerenciador de Pacotes NuGet** e em **Console do Gerenciador de Pacotes**. Execute o seguinte comando no console para instalar o pacote:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

2. Crie um cluster usando o SDK. Para obter instruções, consulte [Provisionar um cluster HDInsight usando o .NET SDK](hdinsight-provision-clusters.md#sdk).

3. Use a classe **ScriptAction** para invocar um script personalizado, como mostrado abaixo:

		
		var clusterInfo = new ClusterCreateParameters()
		{
			// Provide the cluster information, like
			// name, Storage account, credentials,
			// cluster size, and version		    
			...
			...
		};

		// Add the script action to install Spark
		clusterInfo.ConfigActions.Add(new ScriptAction(
	  		"MyScriptActionName", // Name of the config action
	  		new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install the component on
	  		new Uri("http://uri.to/scriptaction.ps1"), // Location of the script to install the component
	  		"MyScriptActionParameter" //Parameters, if any, required by the script
		));



## Suporte para software livre utilizado em clusters do HDInsight
O serviço Microsoft Azure HDInsight é uma plataforma flexível que permite compilar aplicativos Big Data na nuvem usando um ecossistema de tecnologias de software livre baseado no Hadoop. O Microsoft Azure fornece um nível geral de suporte para tecnologias de software livre, conforme discutimos na seção **Escopo do suporte** do <a href="http://azure.microsoft.com/support/faq/" target="_blank">site Perguntas frequentes de suporte do Azure</a>. O serviço HDInsight fornece um nível adicional de suporte a alguns dos componentes, como descrito abaixo.

Há dois tipos de componentes de software livre disponíveis no serviço HDInsight:

- **Componentes internos**: estes componentes estão pré-instalado em clusters HDInsight e fornecem a funcionalidade básica do cluster. Por exemplo, o gerenciador de recursos YARN RM, o HiveQL (linguagem de consulta do Hive) e a biblioteca Mahout pertencem a esta categoria. Uma lista completa dos componentes de cluster está disponível em [Novidades nas versões do cluster Hadoop fornecidas pelo HDInsight](hdinsight-component-versioning.md).
- **Componentes personalizados**: como usuário do cluster, você pode instalar ou usar em sua carga de trabalho qualquer componente disponível na comunidade ou criado por você.

Componentes internos são totalmente compatíveis e o Suporte da Microsoft ajudará a isolar e resolver problemas relacionados a esses componentes.

Componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. Ou seja, talvez você tenha que resolver o problema sozinho ou contatar os canais disponíveis para a obtenção das competências especializadas nessas tecnologias de software livre. Por exemplo, há muitos sites de comunidades que podem ser usados, como o <a href ="https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight" target="_blank">Fórum do MSDN para o HDInsight</a> e o <a href="http://stackoverflow.com" target="_blank">Stack Overflow</a>. Além disso, projetos Apache têm sites de projetos em <a href="http://apache.org" target="_blank">Apache.org</a>; por exemplo, <a href="http://hadoop.apache.org/" target="_blank">Hadoop</a> e <a href="http://spark.apache.org/" target="_blank">Spark</a>.

O serviço HDInsight possibilita o uso de componentes personalizados de várias formas. Seja como for usado ou instalado em um cluster, o mesmo nível de suporte se aplica ao componente. Mostramos, a seguir, uma lista das formas mais comuns de usar os componentes personalizados em clusters HDInsight:

1. Envio de trabalhos: trabalhos do Hadoop ou de outros tipos que executam ou usam componentes personalizados podem ser enviados para o cluster.
2. Personalização de clusters: durante a criação de clusters, você pode especificar configurações adicionais e componentes personalizados que serão instalados nos nós de cluster.
3. Exemplos: para componentes personalizados populares, a Microsoft e outras empresas podem fornecer exemplos de uso desses componentes em clusters HDInsight. Esses exemplos são fornecidos sem suporte.

## Desenvolver scripts de Ação de script

Confira [Desenvolver scripts de Ação de Script para o HDInsight][hdinsight-write-script].


## Consulte também

- [Provisionar clusters Hadoop no HDInsight usando opções personalizadas][hdinsight-provision-cluster] fornece instruções sobre como provisionar um cluster HDInsight usando outras opções personalizadas.
- [Desenvolver scripts de Ação de Script para o HDInsight][hdinsight-write-script]
- [Instalar e usar o Spark em clusters HDInsight][hdinsight-install-spark]
- [Instalar e usar R em clusters do HDInsight][hdinsight-install-r]
- [Instalar e usar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install.md).
- [Instalar e usar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: ../install-configure-powershell.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-v1/HDI-Cluster-state.png "Estágios durante o provisionamento de cluster"
 

<!---HONumber=AcomDC_1203_2015-->