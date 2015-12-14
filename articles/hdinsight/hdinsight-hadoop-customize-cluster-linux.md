<properties
	pageTitle="Personalizar os clusters HDInsight usando ações de script | Microsoft Azure"
	description="Saiba como adicionar componentes personalizados ao clusters HDInsight baseados em Linux usando Ações de Script. Ações de Script são scripts Bash executados durante a criação do cluster e que podem ser usados para personalizar a configuração do cluster ou adicionar outros serviços e utilitários, como Hue, Solr ou R."
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
	ms.date="11/20/2015"
	ms.author="larryfr"/>

# Personalizar os clusters HDInsight usando a Ação de Script (Linux)

O HDInsight fornece uma opção de configuração chamada **Ação de Script**. Ela invoca scripts personalizados que definem a personalização a ser executada no cluster durante o processo de criação. Esses scripts podem ser usados para instalar software adicional em um cluster ou para alterar a configuração de aplicativos em um cluster.

> [AZURE.NOTE]As informações neste artigo são específicas de clusters HDInsight baseados em Linux. Para obter uma versão deste artigo específica aos clusters baseados no Windows, veja [Personalizar clusters HDInsight usando a Ação de Script (Windows)](hdinsight-hadoop-customize-cluster.md)

## Ação de Script no processo de criação do cluster

A Ação de Script só é usada enquanto um cluster está sendo criado. O diagrama a seguir ilustra quando a Ação de Script é executada durante o processo de criação:

![Personalização do cluster HDInsight e estágios durante a criação de cluster][img-hdi-cluster-states]

O script é executado enquanto o HDInsight está sendo configurado. Nesse estágio, o script é executado em paralelo com todos os nós especificados no cluster e é executado com privilégios de raiz nos nós.

> [AZURE.NOTE]Por ter privilégios raiz nos nós de cluster quando o script é executado, você pode executar operações como parar e iniciar serviços, incluindo serviços relacionados ao Hadoop. Se você parar quaisquer serviços, você deve garantir que os serviços Ambari e outros serviços relacionados ao Hadoop estejam funcionando antes do script terminar a execução. Esses serviços são necessários para verificar a integridade e o estado do cluster enquanto ele estiver sendo criado.

Cada cluster pode aceitar várias ações de script, que são invocadas na ordem em que elas são especificadas. Um script pode ser executado nos nós de cabeçalho, nos nós de trabalho ou em ambos.

> [AZURE.IMPORTANT]Ações de script devem ser concluídas em 60 minutos ou atingirão o tempo limite. Durante o provisionamento de nó, o script é executado simultaneamente com outros processos de instalação e configuração. A competição por recursos, como tempo de CPU ou largura de banda rede, pode fazer com que o script leve mais tempo para ser concluído comparado ao seu tempo de conclusão no ambiente de desenvolvimento.
> 
> Para minimizar o tempo necessário para executar o script, evite tarefas como baixar e compilar aplicativos da origem. Em vez disso, pré-compile o aplicativo e armazene o binário no armazenamento de Blob do Azure para ele possa ser rapidamente baixado para o cluster.


## Scripts de exemplo de Ação de Script

Scripts da Ação de Script podem ser usados no Portal do Azure, Azure PowerShell ou SDK do .NET do HDInsight. Este artigo mostra como usar a Ação de Script a partir do portal. Para saber como usar o PowerShell e o SDK do .NET para usar a Ação de Script, veja os exemplos listados na tabela a seguir.

O HDInsight fornece vários scripts para instalar os seguintes componentes em clusters do HDInsight:

Nome | Script
----- | -----
**Instalar o Hue** | https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/install-hue-uber-v01.sh. Veja [Instalar e usar o Hue em clusters HDInsight](hdinsight-hadoop-hue-linux.md).
**Instalar Spark** | https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh. Veja [Instalar e usar o Spark em clusters HDInsight](hdinsight-hadoop-spark-install-linux.md).
**Instalar R** | https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh. Veja [Instalar e usar o R em clusters HDInsight](hdinsight-hadoop-r-scripts-linux.md).
**Instalar Solr** | https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. Veja [Instalar e usar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install-linux.md).
**Instalar o Giraph** | https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. Veja [Instalar e usar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md).

## Usar uma Ação de Script no Portal do Azure

1. Comece criando um cluster conforme descrito em [Criar clusters Hadoop no HDInsight](hdinsight-provision-clusters.md#portal).

2. Em __Configuração Opcional__, na folha **Ações de Script**, clique em **adicionar ação de script** para fornecer detalhes sobre a ação de script, como mostrado abaixo:

	![Usar Ação de Script para personalizar um cluster](./media/hdinsight-hadoop-customize-cluster-linux/HDI.CreateCluster.8.png)

	| Propriedade | Valor |
	| -------- | ----- |
	| Nome | Especifique um nome para a ação de script. |
	| URI do script | Especifique o URI para o script que é chamado para personalizar o cluster. |
	| Cabeçalho/Trabalho | Especifique os nós (de **Cabeçalho**, de **Trabalho** ou **ZooKeeper**) nos quais o script de personalização é executado. |
	| Parâmetros | Especifique os parâmetros, se exigido pelo script. |

	Pressione ENTER para adicionar mais de uma ação de script para instalar vários componentes no cluster.

3. Clique em **Selecionar** para salvar a configuração da ação de script e continuar com a criação do cluster.

## Usar uma Ação de Script em modelos do Gerenciador de Recursos do Azure

Nesta seção, usamos modelos do ARM (Gerenciador de Recursos do Azure) para criar um cluster HDInsight e também usamos uma ação de script para instalar componentes personalizados (R, neste exemplo) no cluster. Esta seção fornece uma amostra de modelo ARM para criar um cluster usando a ação de script.

### Antes de começar

* Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do PowerShell do HDInsight, consulte [Instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).
* Para obter instruções sobre como criar modelos ARM, confira [Criando modelos do Gerenciador de Recursos do Azure](../resource-group-authoring-templates.md).
* Se você ainda não utilizou o PowerShell do Azure com o Gerenciador de recursos, consulte [Uso do PowerShell do Azure com o Gerenciador de recursos do Azure](../powershell-azure-resource-manager.md).

### Criar clusters usando a ação de script

1. Copie o modelo a seguir para um local no computador. Esse modelo instala R no nó de cabeçalho, bem como nós de trabalho no cluster. Você também pode verificar se o modelo JSON é válido. Cole o modelo de conteúdo em [JSONLint](http://jsonlint.com/), uma ferramenta online para validação do JSON.

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
		            "defaultValue": "username"
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

		Add-AzureRmAccount

		Id                             Type       ...
		--                             ----
		someone@example.com            User       ...

3. Se você tiver várias assinaturas, forneça a ID da assinatura que deseja usar para implantação.

		Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

    > [AZURE.NOTE]Você pode usar `Get-AzureRmSubscription` para obter uma lista de todas as assinaturas associadas à sua conta, que inclui a ID de assinatura de cada uma delas.

5. Se você não tiver um grupo de recursos existente, crie um novo grupo de recursos. Forneça o nome do grupo de recursos e o local necessários para sua solução. Um resumo do novo grupo de recursos é retornado.

		New-AzureRmResourceGroup -Name myresourcegroup -Location "West US"

		ResourceGroupName : myresourcegroup
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		            		Actions  NotActions
		            		=======  ==========
		            		*
		ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup


6. Para criar uma nova implantação para seu grupo de recursos, execute o comando **New-AzureRmResourceGroupDeployment** e forneça os parâmetros necessários. Os parâmetros incluirão um nome para sua implantação, o nome do seu grupo de recursos e o caminho ou a URL para o modelo criado. Se seu modelo exige quaisquer parâmetros, você deve passar esses parâmetros também. Nesse caso, a ação de script para instalar o R no cluster não exige nenhum parâmetro.


		New-AzureRmResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>


	Você será solicitado a fornecer valores para os parâmetros definidos no modelo.

7. Quando o grupo de recursos tiver sido implantado, você verá um resumo da implantação.

		  DeploymentName    : mydeployment
		  ResourceGroupName : myresourcegroup
		  ProvisioningState : Succeeded
		  Timestamp         : 8/17/2015 7:00:27 PM
		  Mode              : Incremental
		  ...

8. Se sua implantação falhar, você poderá usar os cmdlets a seguir para obter informações sobre as falhas.

		Get-AzureRmResourceGroupDeployment -ResourceGroupName myresourcegroup -ProvisioningState Failed

## Use uma Ação de Script por meio do Azure PowerShell

Nesta seção, usamos o cmdlet [Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) para invocar scripts usando a Ação de Script para personalizar um cluster. Antes de prosseguir, verifique se você instalou e configurou o PowerShell do Azure. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do PowerShell do HDInsight, confira [Instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

Execute as seguintes etapas:

1. Abra o console do PowerShell do Azure e declare as variáveis a seguir:

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionId = "<SubscriptionId>"		# ID of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
        $resourceGroupName = "<ResourceGroupName>"      # The resource group that the HDInsight cluster will be created in

2. Especifique os valores de configuração (como nós no cluster) e o armazenamento padrão a ser usado.

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureRmSubscription -SubscriptionId $subscriptionId
		$config = New-AzureRmHDInsightClusterConfig
		$config.DefaultStorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccountKey=$storageAccountKey

3. Use o cmdlet **Add-AzureRmHDInsightScriptAction** para invocar o script. O seguinte exemplo usa um script que instala o R no cluster:

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION FOR HEADNODE AND WORKERNODE
		$config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install R"  -NodeType HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
        $config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install R"  -NodeType WorkerNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh

	O cmdlet **Add-AzureRmHDInsightScriptAction** usa os seguintes parâmetros:

	| Parâmetro | Definição |
	| --------- | ---------- |
	| Config | Objeto de configuração ao qual as informações da ação de script são adicionadas. |
	| Nome | Nome da ação de script. |
	| NodeType | Especifica os nós em que o script de personalização é executado. Os valores válidos são **HeadNode** (a ser instalado no nó de cabeçalho), **WorkerNode** (a ser instalado em todos os nós de dados) ou **ZookeeperNode** (a ser instalado no nó do Zookeeper). |
	| Parâmetros | Parâmetros exigidos pelo script. |
	| Uri | Especifica o URI para o script que é executado. |

4. Defina o usuário do administrador/HTTPS para o cluster:

        $httpCreds = get-credential
        
    Quando solicitado, insira 'admin' como o nome e forneça uma senha.

5. Defina as credenciais SSH:

        $sshCreds = get-credential
    
    Quando solicitado, insira o nome de usuário do SSH e a senha. Se quiser proteger a conta SSH com um certificado em vez de uma senha, use uma senha em branco e defina `$sshPublicKey` para o conteúdo da chave pública do certificado que você deseja utilizar. Por exemplo:
    
        $sshPublicKey = Get-Content .\path\to\public.key -Raw
    
4. Por fim, crie o cluster:
        
        New-AzureRmHDInsightCluster -config $config -clustername $clusterName -DefaultStorageContainer $containerName -Location $location -ResourceGroupName $resourceGroupName -ClusterSizeInNodes $clusterNodes -HttpCredential $httpCreds -SshCredential $sshCreds -OSType Linux
    
    Se estiver usando uma chave pública para proteger sua conta SSH, você também deverá especificar `-SshPublicKey $sshPublicKey` como um parâmetro.

Pode levar alguns minutos até que o cluster seja criado.

## Usar uma ação de Script por meio do SDK do .NET do HDInsight

O SDK do .NET do HDInsight fornece bibliotecas de cliente que facilitam o trabalho com o HDInsight por meio de um aplicativo .NET. As etapas a seguir demonstram como usar um script para personalizar um cluster por meio do SDK do .NET do HDInsight.

> [AZURE.IMPORTANT]Crie um certificado autoassinado, instale-o em sua estação de trabalho e carregue-o para sua assinatura do Azure. Para obter instruções, consulte [Criar um certificado autoassinado](http://go.microsoft.com/fwlink/?LinkId=511138).


### Criar um projeto do Visual Studio


1. No Visual Studio, crie um aplicativo de console C#.
2. No **Console do Gerenciador de Pacotes** Nuget, execute os seguintes comandos:

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre

	Esses comandos adicionam bibliotecas .NET e referências a elas no projeto atual do Visual Studio.

3. Abra **Program.cs** e adicione as seguintes instruções using:

		using System;
		using System.Security;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;

4. Substitua o código na classe pelo seguinte:

        private static HDInsightManagementClient _hdiManagementClient;

        private static Guid SubscriptionId = new Guid("<AZURE SUBSCRIPTION ID>");
        private const string ResourceGroupName = "<AZURE RESOURCEGROUP NAME>";

        private const string NewClusterName = "<HDINSIGHT CLUSTER NAME>";
        private const int NewClusterNumNodes = <NUMBER OF NODES>;
        private const string NewClusterLocation = "<LOCATION>";  // Must match the Azure Storage account location
        private const string NewClusterVersion = "3.2";
        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
        private const OSType NewClusterOSType = OSType.Linux;

        private const string ExistingStorageName = "<STORAGE ACCOUNT NAME>.blob.core.windows.net";
        private const string ExistingStorageKey = "<STORAGE ACCOUNT KEY>";
        private const string ExistingContainer = "<DEFAULT CONTAINER NAME>"; 

        private const string NewClusterUsername = "admin";
        private const string NewClusterPassword = "<HTTP USER PASSWORD>";

        private const string NewClusterSshUserName = "sshuser";
        private const string NewClusterSshPublicKey = @"---- BEGIN SSH2 PUBLIC KEY ----
			Comment: ""rsa-key-20150731""
			AAAAB3NzaC1yc2EAAAABJQAAAQEA4QiCRLqT7fnmUA5OhYWZNlZo6lLaY1c+IRsp
			gmPCsJVGQLu6O1wqcxRqiKk7keYq8bP5s30v6bIljsLZYTnyReNUa5LtFw7eauGr
			yVt3Pve6ejfWELhbVpi0iq8uJNFA9VvRkz8IP1JmjC5jsdnJhzQZtgkIrdn3w0e6
			WVfu15kKyY8YAiynVbdV51EB0SZaSLdMZkZQ81xi4DDtCZD7qvdtWEFwLa+EHdkd
			pzO36Mtev5XvseLQqzXzZ6aVBdlXoppGHXkoGHAMNOtEWRXpAUtEccjpATsaZhQR
			zZdZlzHduhM10ofS4YOYBADt9JohporbQVHM5w6qUhIgyiPo7w==
			---- END SSH2 PUBLIC KEY ----"; //replace the public key with your own

        private static void Main(string[] args)
        {
            var tokenCreds = GetTokenCloudCredentials();
            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);

            CreateCluster();
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
                SshUserName = NewClusterSshUserName,
        		SshPublicKey = NewClusterSshPublicKey
            };

            ScriptAction rScriptAction = new ScriptAction("Install R",
                new Uri("https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode,new System.Collections.Generic.List<ScriptAction> { rScriptAction});
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { rScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }
		
6. Substitua os valores de membro de classe.

7. Pressione **F5** para executar o aplicativo. Uma janela de console deve ser aberta e exibir o status do aplicativo. Você também será solicitado a inserir suas credenciais de conta do Azure. Pode levar vários minutos para criar um cluster HDInsight.


## Solucionar problemas

Você pode usar a interface do usuário da Web do Ambari para exibir informações registradas em log pelos scripts durante a criação do cluster. No entanto, se a criação de cluster falhou devido a um erro no script, os logs também estarão disponíveis na conta de armazenamento padrão associada ao cluster. Esta seção fornece informações sobre como recuperar logs usando ambas as opções.

### Usando a interface do usuário da Web do Ambari

1. Em seu navegador, navegue até https://CLUSTERNAME.azurehdinsight.net. Substitua CLUSTERNAME com o nome do cluster HDInsight.

	Quando solicitado, insira o nome de conta de administrador (admin) e a senha correspondente para o cluster. Você precisará reinserir as credenciais de administrador em um formulário da Web.

2. Na barra na parte superior da página, selecione a entrada __ops__. Isso mostrará uma lista das operações atuais e anteriores realizadas no cluster por meio do Ambari.

	![Barra de interface do usuário da Web do Ambari com o item "ops" selecionado](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Localize as entradas com __run\_customscriptaction__ na coluna __Operações__. Elas são criadas quando as Ações de Script são executadas.

	![Captura de tela de operações](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

	Selecione esta entrada e aprofunde-se por meio de links para exibir a saída STDOUT e STDERR gerada quando o script foi executado no cluster.

### Acessar logs na conta de armazenamento padrão

Se a criação de cluster falhou devido a um erro na ação de script, os logs de ação de script ainda poderão ser acessados diretamente na conta de armazenamento padrão associada ao cluster.

* Os logs de armazenamento estão disponíveis em `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`. 

	![Captura de tela de operações](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

	Nele, os logs são organizados separadamente em nó de cabeçalho, nó de trabalho e nós do Zookeeper. Alguns exemplos são: * **Nó de cabeçalho** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net` * **Nó de Trabalho** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net` * **Nó Zookeeper** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Todos os stdout e stderr do host correspondentes são carregados para a conta de armazenamento. Há um **output-*.txt** e um **errors-*.txt** para cada ação de script. O arquivo *.txt de saída contém informações sobre o URI do script que foi executado no host. Por exemplo,

		'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* É possível criar repetidamente um cluster de ação de script com o mesmo nome. Nesse caso, você pode distinguir os logs relevantes com base no nome da pasta DATE. Por exemplo, a estrutura de pastas de um cluster (mycluster) criado em datas diferentes será:
	* `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04`
	* `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Se você criar um cluster de ação de script com o mesmo nome no mesmo dia, você poderá usar o prefixo exclusivo para identificar os arquivos de log relevantes.

* Se você criar um cluster no final do dia, é possível que os arquivos de log incluam dois dias. Nesses casos, você verá duas pastas de datas diferentes para o mesmo cluster.

* Carregar arquivos de log no contêiner padrão pode levar até 5 minutos, especialmente para clusters grandes. Portanto, se desejar acessar os logs, você não deverá imediatamente excluir o cluster se uma ação de script falhar.


## Suporte para software livre utilizado em clusters do HDInsight

O serviço Microsoft Azure HDInsight é uma plataforma flexível que permite compilar aplicativos Big Data na nuvem usando um ecossistema de tecnologias de software livre baseado no Hadoop. O Microsoft Azure fornece um nível geral de suporte para tecnologias de software livre, como discutimos na seção **Escopo do suporte** do [site de Perguntas frequentes sobre o Suporte do Azure](http://azure.microsoft.com/support/faq/). O serviço HDInsight fornece um nível adicional de suporte a alguns dos componentes, como descrito abaixo.

Há dois tipos de componentes de software livre disponíveis no serviço HDInsight:

- **Componentes internos**: estes componentes estão pré-instalado em clusters HDInsight e fornecem a funcionalidade básica do cluster. Por exemplo, o gerenciador de recursos YARN RM, o HiveQL (linguagem de consulta do Hive) e a biblioteca Mahout pertencem a esta categoria. Uma lista completa dos componentes de cluster está disponível em [Novidades nas versões do cluster Hadoop fornecidas pelo HDInsight](hdinsight-component-versioning.md).

- **Componentes personalizados**: como usuário do cluster, você pode instalar ou usar em sua carga de trabalho qualquer componente disponível na comunidade ou criado por você.

> [AZURE.WARNING]Há suporte total a componentes fornecidos com o cluster HDInsight e o Suporte da Microsoft ajudará a isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. Isso pode resultar na resolução do problema ou na solicitação de você buscar nos canais disponíveis as tecnologias de código-fonte aberto, onde é possível encontrar conhecimento aprofundado sobre essa tecnologia. Por exemplo, há muitos sites de comunidades que podem ser usados, como o [fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/pt-BR/home?forum=hdinsight) e o [http://stackoverflow.com](http://stackoverflow.com). Além disso, os projetos do Apache têm sites do projeto em [http://apache.org](http://apache.org) como, por exemplo, [Hadoop](http://hadoop.apache.org/) e [Spark](http://spark.apache.org/).

O serviço HDInsight possibilita o uso de componentes personalizados de várias formas. Seja como for usado ou instalado em um cluster, o mesmo nível de suporte se aplica ao componente. Mostramos, a seguir, uma lista das formas mais comuns de usar os componentes personalizados em clusters HDInsight:

1. Envio de trabalhos: trabalhos do Hadoop ou de outros tipos que executam ou usam componentes personalizados podem ser enviados para o cluster.

2. Personalização de clusters: durante a criação de clusters, você pode especificar configurações adicionais e componentes personalizados que serão instalados nos nós de cluster.

3. Exemplos: para componentes personalizados populares, a Microsoft e outras empresas podem fornecer exemplos de uso desses componentes em clusters HDInsight. Esses exemplos são fornecidos sem suporte.

## Próximas etapas

Consulte as informações e exemplos a seguir sobre como criar e usar scripts para personalizar um cluster:

- [Desenvolver scripts de Ação de Script para o HDInsight](hdinsight-hadoop-script-actions-linux.md)
- [Instalar e usar o Spark em clusters HDInsight](hdinsight-hadoop-spark-install-linux.md)
- [Instalar e usar R em clusters do HDInsight](hdinsight-hadoop-r-scripts-linux.md)
- [Instalar e usar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install-linux.md)
- [Instalar e usar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md)



[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Estágios durante a criação de cluster"

<!---HONumber=AcomDC_1203_2015-->