<properties
	pageTitle="Personalizar os clusters HDInsight usando ações de script | Microsoft Azure"
	description="Saiba como personalizar os clusters HDInsight usando a ação de Script."
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
	ms.date="10/02/2015"
	ms.author="nitinme"/>

# Personalizar os Clusters HDInsight usando a Ação de Script (Windows)

O HDInsight fornece uma opção de configuração chamada **Ação de Script**. Ela invoca scripts personalizados que definem a personalização a ser executada no cluster durante o processo de criação. Esses scripts podem ser usados para instalar software adicional em um cluster ou para alterar a configuração de aplicativos em um cluster.

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Personalizar os clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-v1.md)

> [AZURE.NOTE]As informações contidas neste artigo são específicas aos clusters HDInsight baseados no Windows. Para obter uma versão deste artigo específica aos clusters baseados no Linux, veja [Personalizar clusters HDInsight usando a Ação de Script (Linux)](hdinsight-hadoop-customize-cluster-linux.md)

Clusters HDInsight também podem ser personalizados de várias outras formas, como incluir contas adicionais do Armazenamento do Azure, alterar os arquivos de configuração do Hadoop (core-site.xml, hive-site.xml, etc.) ou adicionar bibliotecas compartilhadas (p.ex., Hive, Oozie) em locais comuns no cluster. Essas personalizações podem ser feitas por meio do Azure PowerShell, do SDK do Azure HDInsight .NET ou do Portal de Visualização do Azure. Para obter mais informações, consulte [Criar clusters Hadoop no HDInsight][hdinsight-provision-cluster].

## Ação de Script no processo de criação do cluster

A Ação de Script só é usada enquanto um cluster está sendo criado. O diagrama a seguir ilustra quando a Ação de Script é executada durante o processo de criação:

![Personalização do cluster HDInsight e estágios durante a criação de cluster][img-hdi-cluster-states]

Quando o script é executado, o cluster entra no estágio **ClusterCustomization**. Nesse estágio, o script é executado na conta de administrador do sistema, em paralelo com todos os nós especificados no cluster e fornece privilégios totais de administrador nos nós.

> [AZURE.NOTE]Por ter privilégios administrativos nos nós de cluster durante o estágio **ClusterCustomization**, você pode usar o script para executar operações como parar e iniciar serviços, inclusive serviços relacionados ao Hadoop. Logo, como parte do script, você deve garantir que os serviços Ambari e outros serviços relacionados ao Hadoop estejam funcionando antes do script terminar a execução. Esses serviços são necessários para verificar a integridade e o estado do cluster enquanto ele estiver sendo criado. Se você alterar qualquer configuração no cluster que afete esses serviços, é necessário usar as funções auxiliares que são fornecidas. Para obter mais informações sobre funções auxiliares, confira [Desenvolver scripts de Ação de Script para o HDInsight][hdinsight-write-script].

Os logs de saída e de erros do script são armazenados na conta padrão do Armazenamento que você especificou para o cluster. Os logs são armazenados em uma tabela de nome **u<\\fragmento-do-nome-do-cluster><\\carimbo-de-data-e-hora>setuplog**. São logs agregados dos scripts executados em todos os nós (nó de cabeçalho e nós de trabalho) no cluster.

Cada cluster pode aceitar várias ações de script, que são invocadas na ordem em que elas são especificadas. Um script pode ser executado no nó principal, nos nós de trabalho ou em ambos.

O HDInsight fornece vários scripts para instalar os seguintes componentes em clusters do HDInsight:

Nome | Script
----- | -----
**Instalar Spark** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. Consulte [Instalar e usar o Spark em clusters HDInsight][hdinsight-install-spark].
**Instalar R** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. Consulte [Instalar e usar o R em clusters HDInsight][hdinsight-install-r].
**Instalar Solr** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. Consulte [Instalar e usar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install.md).
- **Instalar o Giraph** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. Consulte [Instalar e usar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install.md).



## Chamar scripts usando o Portal de Visualização do Azure

**No Portal de Visualização do Azure**

1. Comece criando um cluster conforme descrito em [Criar clusters Hadoop no HDInsight](hdinsight-provision-clusters.md#portal).
2. Em Configuração Opcional, para a folha **Ações de Script**, clique em **adicionar ação de script** para fornecer detalhes sobre a ação de script, como mostrado abaixo:

	![Usar Ação de Script para personalizar um cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "Usar Ação de Script para personalizar um cluster")

	<table border='1'>
	<tr><th>Propriedade</th><th>Valor</th></tr>
	<tr><td>Nome</td>
		<td>Especifique um nome para a ação de script.</td></tr>
	<tr><td>URI do script</td>
		<td>Especifique o URI para o script que é chamado para personalizar o cluster. s</td></tr>
	<tr><td>Cabeçalho/Trabalho</td>
		<td>Especificar os nós (**Cabeçalho** ou **Trabalho**) nos quais o script de personalização é executado.</b>.
	<tr><td>Parâmetros</td>
		<td>Especifique os parâmetros, se exigido pelo script.</td></tr>
</table>Pressione ENTER para adicionar mais de uma ação de script para instalar vários componentes no cluster.

3. Clique em **Selecionar** para salvar a configuração da ação de script e continuar com a criação do cluster.

## Chamar scripts usando o Azure PowerShell

Esse script PowerShell a seguir demonstra como instalar o Spark no cluster HDInsight baseado em Windows. Para instalar outros softwares, você precisará substituir o arquivo de script no script:

Nesta seção, usamos o cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** para invocar scripts usando a Ação de Script para personalizar um cluster. Antes de prosseguir, verifique se você instalou e configurou o PowerShell do Azure. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do PowerShell do Azure para HDInsight, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

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
		$version = "<HDInsightClusterVersion>"          # For example, "3.2"

2. Especifique os valores de configuração como nós no cluster e o armazenamento padrão a ser usado.

		# Specify the configuration options
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName

3. Use o cmdlet **Add-AzureHDInsightScriptAction** para adicionar uma Ação de Script à configuração do cluster. Mais tarde, quando o cluster estiver sendo criado, a ação de script será executada.

		# Add a script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1

	O cmdlet **Add-AzureHDInsightScriptAction** usa os parâmetros a seguir:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Parâmetro</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Definição</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Config</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">O objeto de configuração em que a informações da ação de script é adicionada.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome da ação de script.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Especifica os nós em que o script de personalização é executado. Os valores válidos são HeadNode (para instalar no nó principal) ou DataNode (para instalar em todos os nós de dados). Você pode usar um ou ambos os valores.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Especifica o URI para o script que é executado.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parâmetros</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parâmetros exigidos pelo script. O script de exemplo usado neste tópico não requer nenhum parâmetro e, portanto, você não vir esse parâmetro no trecho acima.
</td></tr>
</table>

4. Finalmente, inicie a criação de um cluster personalizado com o Spark instalado.

		# Start creating a cluster with Spark installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

Quando solicitado, insira as credenciais para o cluster. Pode levar alguns minutos até que o cluster seja criado.

## Scripts de chamada usando o SDK do .NET 

A amostra a seguir demonstra como instalar o Spark no cluster HDInsight baseado em Windows. Para instalar outros softwares, você precisará substituir o arquivo de script no código.

**Para criar um cluster HDInsight com Spark**

1. No Visual Studio, crie um aplicativo de console C#.
2. No Console do Gerenciador de Pacotes NuGet, execute o comando a seguir.

		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Common.Authentication -Pre

2. Use as seguintes instruções using no arquivo Program.cs:

		using System;
		using System.Security;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;
		
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;

3. Substitua o código na classe pelo seguinte:

        private static HDInsightManagementClient _hdiManagementClient;

        private static Guid SubscriptionId = new Guid("<YourAzureSubscriptionID>");
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumNodes = <NumberOfClusterNodes>;
        private const string NewClusterLocation = "East US";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            var tokenCreds = GetTokenCloudCredentials();
            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);

            CreateCluster();

        }

        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,

                DefaultStorageAccountName = ExistingStorageName,
                DefaultStorageAccountKey = ExistingStorageKey,
                DefaultStorageContainer = ExistingContainer,

                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }


        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
        {
            var authFactory = new AuthenticationFactory();

            var account = new AzureAccount { Type = AzureAccount.AccountType.User };

            if (username != null && password != null)
                account.Id = username;

            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

            var accessToken =
                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
                    .AccessToken;

            return new TokenCloudCredentials(accessToken);
        }

        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
        {
            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
        }

4. Pressione **F5** para executar o aplicativo.


## Suporte para software livre utilizado em clusters do HDInsight
O serviço Microsoft Azure HDInsight é uma plataforma flexível que permite compilar aplicativos Big Data na nuvem usando um ecossistema de tecnologias de software livre baseado no Hadoop. O Microsoft Azure fornece um nível geral de suporte para tecnologias de software livre, conforme discutimos na seção **Escopo do suporte** do <a href="http://azure.microsoft.com/support/faq/" target="_blank">site Perguntas frequentes de suporte do Azure</a>. O serviço HDInsight fornece um nível adicional de suporte a alguns dos componentes, como descrito abaixo.

Há dois tipos de componentes de software livre disponíveis no serviço HDInsight:

- **Componentes internos**: estes componentes estão pré-instalado em clusters HDInsight e fornecem a funcionalidade básica do cluster. Por exemplo, o gerenciador de recursos YARN RM, o HiveQL (linguagem de consulta do Hive) e a biblioteca Mahout pertencem a esta categoria. Uma lista completa dos componentes de cluster está disponível em <a href="http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/" target="_blank">O que há de novo nas versões de cluster Hadoop fornecidas pelo HDInsight?</a>
- **Componentes personalizados**: como usuário do cluster, você pode instalar ou usar em sua carga de trabalho qualquer componente disponível na comunidade ou criado por você.

Componentes internos são totalmente compatíveis e o Suporte da Microsoft ajudará a isolar e resolver problemas relacionados a esses componentes.

> [AZURE.WARNING]Há suporte total a componentes fornecidos com o cluster HDInsight e o Suporte da Microsoft ajudará a isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. Isso pode resultar na resolução do problema ou na solicitação de você buscar nos canais disponíveis as tecnologias de código-fonte aberto, onde é possível encontrar conhecimento aprofundado sobre essa tecnologia. Por exemplo, há muitos sites de comunidades que podem ser usados, como o [Fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/pt-BR/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Além disso, projetos Apache têm sites de projetos em [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/) e [Spark](http://spark.apache.org/).

O serviço HDInsight possibilita o uso de componentes personalizados de várias formas. Seja como for usado ou instalado em um cluster, o mesmo nível de suporte se aplica ao componente. Mostramos, a seguir, uma lista das formas mais comuns de usar os componentes personalizados em clusters HDInsight:

1. Envio de trabalhos: trabalhos do Hadoop ou de outros tipos que executam ou usam componentes personalizados podem ser enviados para o cluster.
2. Personalização de clusters: durante a criação de clusters, você pode especificar configurações adicionais e componentes personalizados que serão instalados nos nós de cluster.
3. Exemplos: para componentes personalizados populares, a Microsoft e outras empresas podem fornecer exemplos de uso desses componentes em clusters HDInsight. Esses exemplos são fornecidos sem suporte.

## Desenvolver scripts de Ação de script

Confira [Desenvolver scripts de Ação de Script para o HDInsight][hdinsight-write-script].


## Consulte também

- [Criar clusters Hadoop no HDInsight][hdinsight-provision-cluster] fornece instruções sobre como criar um cluster HDInsight usando outras opções personalizadas.
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


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Estágios durante a criação de cluster"

<!---HONumber=Oct15_HO2-->