<properties
	pageTitle="Personalizar os clusters HDInsight usando ações de script | Microsoft Azure"
	description="Saiba como personalizar os clusters HDInsight usando a ação de Script."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/21/2015"
	ms.author="larryfr"/>

# Personalizar os clusters HDInsight usando a Ação de Script

O HDInsight fornece uma opção de configuração chamada **Ação de Script**; ela invoca scripts personalizados que definem a personalização a ser executada no cluster durante o processo de provisionamento. Esses scripts podem ser usados para instalar software adicional em um cluster ou para alterar a configuração de aplicativos em um cluster.

> [AZURE.NOTE]As informações neste artigo são específicas de clusters HDInsight baseados em Linux. Para obter uma versão deste artigo específica aos clusters baseados no Windows, veja [Personalizar clusters HDInsight usando a Ação de Script (Windows)](hdinsight-hadoop-customize-cluster.md)

## Ação de script no processo de provisionamento do cluster

A Ação de Script só é usada enquanto um cluster está sendo criado. O diagrama a seguir ilustra quando a Ação de Script é executada durante o processo de provisionamento:

![Personalização do cluster HDInsight e estágios durante o provisionamento de cluster][img-hdi-cluster-states]

O script é executado enquanto o HDInsight está sendo configurado. Nesse estágio, o script é executado em paralelo com todos os nós especificados no cluster e é executado com privilégios de raiz nos nós.

> [AZURE.NOTE]Por ter privilégios raiz nos nós de cluster quando o script é executado, você pode executar operações como parar e iniciar serviços, incluindo serviços relacionados ao Hadoop. Se você parar quaisquer serviços, você deve garantir que os serviços Ambari e outros serviços relacionados ao Hadoop estejam funcionando antes do script terminar a execução. Esses serviços são necessários para verificar a integridade e o estado do cluster enquanto ele estiver sendo criado.

Cada cluster pode aceitar várias ações de script, que são invocadas na ordem em que elas são especificadas. Um script pode ser executado nos nós de cabeçalho, nos nós de trabalho ou em ambos.

> [AZURE.IMPORTANT]Ações de script devem ser concluídas em 15 minutos ou atingirão o tempo limite.

## Scripts de exemplo de Ação de Script

Scripts de Ação de Script podem ser usados no Portal de Visualização do Azure, no Azure PowerShell ou no SDK do .NET HDInsight. Este artigo mostra como usar a Ação de Script a partir do portal. Para saber como usar o PowerShell e o SDK do .NET para usar a Ação de Script, veja os exemplos listados na tabela a seguir.

O HDInsight fornece vários scripts para instalar os seguintes componentes em clusters do HDInsight:

Nome | Script
----- | -----
**Instalar o Hue** | https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/install-hue-uber-v01.sh. Consulte [Instalar e usar o Hue em clusters HDInsight](hdinsight-hadoop-hue-linux.md).
**Instalar Spark** | https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv01/spark-installer-v01.sh. Consulte [Instalar e usar o Spark em clusters HDInsight](hdinsight-hadoop-spark-install-linux.md).
**Instalar R** | https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh. Consulte [Instalar e usar o R em clusters HDInsight](hdinsight-hadoop-r-scripts-linux.md).
**Instalar Solr** | https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. Consulte [Instalar e usar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install-linux.md).
**Instalar o Giraph** | https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. Consulte [Instalar e usar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md).

##Use uma Ação de Script no Portal de Visualização do Azure

1. Inicie o provisionamento de um cluster descrito em [Provisionar um cluster usando opções personalizadas](hdinsight-provision-clusters.md#portal).

2. Em __Configuração Opcional__, para a folha **Ações de Script**, clique em **adicionar ação de script** para fornecer detalhes sobre a ação de script, como mostrado abaixo:

	![Usar Ação de Script para personalizar um cluster](./media/hdinsight-hadoop-customize-cluster-linux/HDI.CreateCluster.8.png "Usar Ação de Script para personalizar um cluster")

	| Propriedade | Valor |
	| -------- | ----- |
	| Nome | Especifique um nome para a ação de script. |
	| URI do script | Especifique o URI para o script que é chamado para personalizar o cluster. |
	| Cabeçalho/Trabalho | Especifique os nós (de **Cabeçalho**, de **Trabalho** ou **ZooKeeper**) nos quais o script de personalização é executado. |
	| Parâmetros | Especifique os parâmetros, se exigido pelo script. |

	Pressione ENTER para adicionar mais de uma ação de script para instalar vários componentes no cluster.

3. Clique em **Selecionar** para salvar a configuração da ação de script e continuar com o provisionamento do cluster.

##Usar uma Ação de Script em modelos do Gerenciador de Recursos do Azure

Nesta seção, usamos modelos ARM (Gerenciador de Recursos do Azure) para provisionar um cluster HDInsight e também usar uma ação de script para instalar componentes personalizados (R, neste exemplo) no cluster. Esta seção fornece uma amostra de modelo ARM para provisionar um cluster usando uma ação de script.

### Antes de começar

* Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do PowerShell do HDInsight, consulte [Instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).
* Para obter instruções sobre como criar modelos ARM, consulte [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md).
* Se você ainda não utilizou o PowerShell do Azure com o Gerenciador de recursos, consulte [Uso do PowerShell do Azure com o Gerenciador de recursos do Azure](powershell-azure-resource-manager).

### Provisionar cluster usando a ação de script

1. Copie o modelo a seguir para um local no computador. Esse modelo instala R no nó de cabeçalho, bem como nós de trabalho no cluster. Você também pode verificar se o modelo JSON é válido. Cole o modelo de conteúdo em [JSONLint](http://jsonlint.com/), uma ferramenta online para validação de JSON.

			{
		    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
		    "contentVersion": "1.0.0.0",
		    "parameters": {
		        "clusterLocation": {
		            "type": "string",
		            "defaultValue": "West US",
		            "allowedValues": [ "West US" ]
		        },
		        "clusterName": {
		            "type": "string"
		        },
		        "clusterUserName": {
		            "type": "string",
		            "defaultValue": "admin"
		        },
		        "clusterUserPassword": {
		            "type": "securestring"
		        },
		        "sshUserName": {
		            "type": "string",
		            "defaultValue": "hdiuser"
		        },
		        "sshPassword": {
		            "type": "securestring"
		        },
		        "clusterStorageAccountName": {
		            "type": "string"
		        },
		        "clusterStorageAccountResourceGroup": {
		            "type": "string"
		        },
		        "clusterStorageType": {
		            "type": "string",
		            "defaultValue": "Standard_LRS",
		            "allowedValues": [
		                "Standard_LRS",
		                "Standard_GRS",
		                "Standard_ZRS"
		            ]
		        },
		        "clusterStorageAccountContainer": {
		            "type": "string"
		        },
		        "clusterHeadNodeCount": {
		            "type": "int",
		            "defaultValue": 1
		        },
		        "clusterWorkerNodeCount": {
		            "type": "int",
		            "defaultValue": 2
		        }
		    },
		    "variables": {
		    },
		    "resources": [
		        {
		            "name": "[parameters('clusterStorageAccountName')]",
		            "type": "Microsoft.Storage/storageAccounts",
		            "location": "[parameters('clusterLocation')]",
		            "apiVersion": "2015-05-01-preview",
		            "dependsOn": [ ],
		            "tags": { },
		            "properties": {
		                "accountType": "[parameters('clusterStorageType')]"
		            }
		        },
		        {
		            "name": "[parameters('clusterName')]",
		            "type": "Microsoft.HDInsight/clusters",
		            "location": "[parameters('clusterLocation')]",
		            "apiVersion": "2015-03-01-preview",
		            "dependsOn": [
		                "[concat('Microsoft.Storage/storageAccounts/', parameters('clusterStorageAccountName'))]"
		            ],
		            "tags": { },
		            "properties": {
		                "clusterVersion": "3.2",
		                "osType": "Linux",
		                "clusterDefinition": {
		                    "kind": "hadoop",

		                    "configurations": {
		                        "gateway": {
		                            "restAuthCredential.isEnabled": true,
		                            "restAuthCredential.username": "[parameters('clusterUserName')]",
		                            "restAuthCredential.password": "[parameters('clusterUserPassword')]"
		                        }
		                    }
		                },
		                "storageProfile": {
		                    "storageaccounts": [
		                        {
		                            "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
		                            "isDefault": true,
		                            "container": "[parameters('clusterStorageAccountContainer')]",
		                            "key": "[listKeys(resourceId(parameters('clusterStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).key1]"
		                        }
		                    ]
		                },
		                "computeProfile": {
		                    "roles": [
		                        {
		                            "name": "headnode",
		                            "targetInstanceCount": "[parameters('clusterHeadNodeCount')]",
		                            "hardwareProfile": {
		                                "vmSize": "Large"
		                            },
		                            "osProfile": {
		                                "linuxOperatingSystemProfile": {
		                                    "username": "[parameters('sshUserName')]",
		                                    "password": "[parameters('sshPassword')]"
		                                }
		                            },
		                            "scriptActions": [
		                                {
		                                    "name": "installR",
		                                    "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
		                                    "parameters": ""
		                                }
		                            ]
		                        },
		                        {
		                            "name": "workernode",
		                            "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
		                            "hardwareProfile": {
		                                "vmSize": "Large"
		                            },
		                            "osProfile": {
		                                "linuxOperatingSystemProfile": {
		                                    "username": "[parameters('sshUserName')]",
		                                    "password": "[parameters('sshPassword')]"
		                                }
		                            },
		                            "scriptActions": [
		                                {
		                                    "name": "installR",
		                                    "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
		                                    "parameters": ""
		                                }
		                            ]
		                        }
		                    ]
		                }
		            }
		        }
		    ],
		    "outputs": {
		        "cluster":{
		            "type" : "object",
		            "value" : "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
		        }
		    }
		}



2. Inicie o Azure PowerShell e faça logon em sua conta do Azure. Depois de fornecer suas credenciais, o comando retornará informações sobre sua conta.

		Add-AzureAccount

		Id                             Type       ...
		--                             ----
		someone@example.com            User       ...

3. Se você tiver várias assinaturas, forneça a ID da assinatura que deseja usar para implantação.

		Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

4. Alterne para o módulo Gerenciador de Recursos do Azure.

		Switch-AzureMode AzureResourceManager

5. Se você não tiver um grupo de recursos existente, crie um novo grupo de recursos. Forneça o nome do grupo de recursos e o local necessários para sua solução. Um resumo do novo grupo de recursos é retornado.

		New-AzureResourceGroup -Name myresourcegroup -Location "West US"

		ResourceGroupName : myresourcegroup
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		            		Actions  NotActions
		            		=======  ==========
		            		*
		ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup


6. Para criar uma nova implantação para seu grupo de recursos, execute o comando **New-AzureResourceGroupDeployment** e forneça os parâmetros necessários. Os parâmetros incluirão um nome para sua implantação, o nome do seu grupo de recursos e o caminho ou a URL para o modelo criado. Se seu modelo exige quaisquer parâmetros, você deve passar esses parâmetros também. Nesse caso, a ação de script para instalar o R no cluster não exige nenhum parâmetro.


		New-AzureResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>


	Você será solicitado a fornecer valores para os parâmetros definidos no modelo.

7. Quando o grupo de recursos tiver sido implantado, você verá um resumo da implantação.

		  DeploymentName    : mydeployment
		  ResourceGroupName : myresourcegroup
		  ProvisioningState : Succeeded
		  Timestamp         : 8/17/2015 7:00:27 PM
		  Mode              : Incremental
		  ...

8. Se sua implantação falhar, você poderá usar os cmdlets a seguir para obter informações sobre as falhas.

		Get-AzureResourceGroupLog -ResourceGroup myresourcegroup -Status Failed

	Para obter informações detalhadas sobre as falhas de implantação, use o cmdlet a seguir.

		Get-AzureResourceGroupLog -ResourceGroup myresourcegroup -Status Failed -DetailedOutput

##Use uma Ação de Script por meio do Azure PowerShell

Nesta seção, usamos o cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** para invocar scripts usando a Ação de Script para personalizar um cluster. Antes de prosseguir, verifique se você instalou e configurou o PowerShell do Azure. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do PowerShell do HDInsight, consulte [Instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).

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

3. Use o cmdlet **Add-AzureHDInsightScriptAction** para invocar o script. O exemplo a seguir usa o script para instalar o R no cluster:

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install R"  -ClusterRoleCollection HeadNode,WorkerNode,ZookeeperNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh


	O cmdlet **Add-AzureHDInsightScriptAction** usa os seguintes parâmetros:

	| Parâmetro | Definição |
	| --------- | ---------- |
	| Config | Objeto de configuração ao qual as informações da ação de script são adicionadas. |
	| Nome | Nome da ação de script. |
	| ClusterRoleCollection | Especifica os nós em que o script de personalização é executado. Os valores válidos são **HeadNode** (para instalar no nó de cabeçalho), **WorkerNode** (para instalar em todos os nós de dados) ou **ZookeeperNode** (para instalar no nó zookeeper). Você pode usar um ou todos esses valores. |
	| Parâmetros | Parâmetros exigidos pelo script. |
	| Uri | Especifica o URI para o script que é executado. |

4. Por último, provisione o cluster:

		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

Quando solicitado, insira as credenciais para o cluster. Pode levar alguns minutos até que o cluster seja criado.

##Usar uma ação de Script por meio do SDK do .NET do HDInsight

O SDK do .NET do HDInsight fornece bibliotecas de cliente que facilitam o trabalho com o HDInsight por meio de um aplicativo .NET. As etapas a seguir demonstram como usar um script para personalizar um cluster por meio do SDK do .NET do HDInsight.


> [AZURE.IMPORTANT]Crie um certificado autoassinado, instale-o em sua estação de trabalho e carregue-o para sua assinatura do Azure. Para obter instruções, consulte [Criar um certificado autoassinado](http://go.microsoft.com/fwlink/?LinkId=511138).


###Criar um projeto do Visual Studio

1. Abra o Visual Studio 2013 ou 2015.

2. No menu **Arquivo**, clique em **Novo** e em **Projeto**.

3. Em **Novo Projeto**, digite ou selecione os seguintes valores:

	| Propriedade | Valor |
	| -------- | ----- |
	| Categoria | Modelos/Visual C#/Windows |
	| Modelo | Aplicativo de console |
	| Nome | ScriptActionCluster |

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

10. Acrescente o seguinte código à função **Main()**. Esse código invoca uma Ação de Script; neste exemplo, trata-se do script para instalar o R no cluster:

		// ADD THE SCRIPT ACTION TO INSTALL R

        clusterInfo.ConfigActions.Add(new ScriptAction(
            "Install R",
            new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode },
            new Uri("https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh"), null
            ));

11. Por fim, crie o cluster:

		client.CreateCluster(clusterInfo);

11. Salve as alterações no aplicativo e crie a solução.

###Executar o aplicativo

Abra um console do Azure PowerShell, navegue até o local onde você salvou o projeto, navegue até a pasta \\bin\\debug do projeto e, em seguida, execute o seguinte comando:

	.\ScriptActionCluster <cluster-name>

Forneça um nome de cluster e pressione ENTER para provisionar um cluster.

## Suporte para software livre utilizado em clusters do HDInsight

O serviço Microsoft Azure HDInsight é uma plataforma flexível que permite compilar aplicativos Big Data na nuvem usando um ecossistema de tecnologias de software livre baseado no Hadoop. O Microsoft Azure fornece um nível geral de suporte para tecnologias de software livre, conforme discutimos na seção **Escopo do suporte** do [Site Perguntas Frequentes de Suporte do Azure](http://azure.microsoft.com/support/faq/). O serviço HDInsight fornece um nível adicional de suporte a alguns dos componentes, como descrito abaixo.

Há dois tipos de componentes de software livre disponíveis no serviço HDInsight:

- **Componentes internos**: estes componentes estão pré-instalado em clusters HDInsight e fornecem a funcionalidade básica do cluster. Por exemplo, o gerenciador de recursos YARN RM, o HiveQL (linguagem de consulta do Hive) e a biblioteca Mahout pertencem a esta categoria. Uma lista completa dos componentes de cluster está disponível em [O que há de novo nas versões de cluster de Hadoop fornecidas pelo HDInsight?](hdinsight-component-versioning.md)

- **Componentes personalizados**: como usuário do cluster, você pode instalar ou usar em sua carga de trabalho qualquer componente disponível na comunidade ou criado por você.

> [AZURE.WARNING]Há suporte total a componentes fornecidos com o cluster HDInsight e o Suporte da Microsoft ajudará a isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. Isso pode resultar na resolução do problema ou na solicitação de você buscar nos canais disponíveis as tecnologias de código-fonte aberto, onde é possível encontrar conhecimento aprofundado sobre essa tecnologia. Por exemplo, há muitos sites de comunidades que podem ser usados, como o [Fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/pt-BR/home?forum=hdinsight) e [http://stackoverflow.com](http://stackoverflow.com). Além disso, projetos Apache têm sites de projetos em [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/) e [Spark](http://spark.apache.org/).

O serviço HDInsight possibilita o uso de componentes personalizados de várias formas. Seja como for usado ou instalado em um cluster, o mesmo nível de suporte se aplica ao componente. Mostramos, a seguir, uma lista das formas mais comuns de usar os componentes personalizados em clusters HDInsight:

1. Envio de trabalhos: trabalhos do Hadoop ou de outros tipos que executam ou usam componentes personalizados podem ser enviados para o cluster.

2. Personalização de clusters: durante a criação de clusters, você pode especificar configurações adicionais e componentes personalizados que serão instalados nos nós de cluster.

3. Exemplos: para componentes personalizados populares, a Microsoft e outras empresas podem fornecer exemplos de uso desses componentes em clusters HDInsight. Esses exemplos são fornecidos sem suporte.

##Solucionar problemas

Você pode usar a interface do usuário da Web do Ambari para exibir informações registradas em log pelo scripts durante o provisionamento do cluster.

1. Em seu navegador, navegue até https://CLUSTERNAME.azurehdinsight.net. Substitua CLUSTERNAME com o nome do cluster HDInsight.

	Quando solicitado, insira o nome de conta de administrador (admin) e a senha correspondente para o cluster. Você precisará reinserir as credenciais de administrador em um formulário da Web.

2. Na barra na parte superior da página, selecione a entrada __ops__. Isso mostrará uma lista das operações atuais e anteriores realizadas no cluster por meio do Ambari.

	![Barra de interface do usuário da Web do Ambari com o item “ops” selecionado](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Localize as entradas nas quais __run\_customscriptaction__ consta na coluna __Operações__. Elas são criadas quando as Ações de Script são executadas.

	![Captura de tela de operações](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

	Selecione esta entrada e aprofunde-se por meio de links para exibir a saída STDOUT e STDERR gerada quando o script foi executado no cluster.

## Próximas etapas

Consulte as informações e exemplos a seguir sobre como criar e usar scripts para personalizar um cluster:

- [Desenvolver scripts de Ação de Script para o HDInsight](hdinsight-hadoop-script-actions-linux.md)
- [Instalar e usar o Spark em clusters HDInsight](hdinsight-hadoop-spark-install-linux.md)
- [Instalar e usar R em clusters do HDInsight](hdinsight-hadoop-r-scripts-linux.md)
- [Instalar e usar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install-linux.md)
- [Instalar e usar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md)



[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Estágios durante o provisionamento de cluster"

<!---HONumber=Sept15_HO4-->