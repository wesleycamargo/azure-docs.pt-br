<properties
	pageTitle="Personalizar os clusters HDInsight usando ações de script | Microsoft Azure"
	description="Saiba como adicionar componentes personalizados ao clusters HDInsight baseados em Linux usando Ações de Script. As Ações de Script são scripts Bash nos nós do cluster e podem ser usadas para personalizar a configuração do cluster ou adicionar outros serviços e utilitários, como Hue, Solr ou R."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="jhubbard"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="larryfr"/>

# Personalizar clusters HDInsight baseados em Linux usando a Ação de Script

O HDInsight fornece uma opção de configuração chamada **Ação de Script** que chama os scripts personalizados que personalizam o cluster. Estes scripts podem ser usados durante a criação do cluster ou em um cluster já em execução, e são usados para instalar componentes adicionais ou alterar as definições de configuração.

> [AZURE.NOTE] A capacidade de usar as ações de script em um cluster já em execução só está disponível para os clusters HDInsight baseados em Linux. Para obter informações sobre como usar as ações de script com clusters baseados no Windows, consulte [Personalizar clusters HDInsight usando a Ação de Script (Windows)](hdinsight-hadoop-customize-cluster.md).

Ações de script também podem ser publicadas no Azure Marketplace como um aplicativo do HDInsight. Alguns dos exemplos neste documento mostram como você pode instalar um aplicativo do HDInsight usando comandos de ação de script do PowerShell e o SDK do .NET. Para obter mais informações sobre aplicativos do HDInsight, consulte [Publicar aplicativos do HDInsight no Azure Marketplace](hdinsight-apps-publish-applications.md).

## Noções básicas sobre Ações de Script

Uma Ação de Script é simplesmente um script Bash para o qual você fornece uma URL e parâmetros e então é executada nos nós do cluster HDInsight. A seguir, as características e os recursos das ações de script.

* Deve estar armazenado em um URI que pode ser acessado do cluster HDInsight. Estes são os possíveis locais de armazenamento:

    * Uma conta de armazenamento de blobs que é a conta de armazenamento principal ou adicional para o cluster HDInsight. Como HDInsight recebe acesso a ambos os tipos de contas de armazenamento durante a criação do cluster, eles são uma maneira de usar uma ação de script não público.
    
    * Um URI publicamente legível, como um Blob do Azure, o GitHub, o OneDrive, o Dropbox, etc.
    
    Para obter exemplos de URI para os scripts armazenados no contêiner de blobs (publicamente legível), confira a seção [Scripts de exemplo de Ação de Script](#example-script-action-scripts).

* Podem ser restritos à __execução somente em determinados tipos de nó__, por exemplo, nos nós de cabeçalho ou nos nós de trabalho.

    > [AZURE.NOTE] Quando usado com HDInsight Premium, você pode especificar que o script deve ser usado no nó de borda.

* Pode ser __persistente__ ou __ad hoc__.

    Os scripts __persistentes__ são scripts aplicados aos nós de trabalho e serão executados automaticamente nos novos nós criados ao escalar verticalmente um cluster.

    Um script persistente também pode aplicar alterações a outro tipo de nó, como um nó de cabeçalho, mas da perspectiva da funcionalidade, o único motivo para manter um script é para ele ser aplicado aos novos nós de trabalho criados quando um cluster é expandido.

    > [AZURE.IMPORTANT] As ações de script persistentes devem ter um nome exclusivo.

    Os scripts __ad hoc__ não são persistentes, no entanto, você pode promover posteriormente um script ad hoc a um script persistente ou rebaixar um script persistente a um script ad hoc.

    > [AZURE.IMPORTANT] As ações de script usadas durante a criação do cluster são mantidas automaticamente.
    >
    > Os scripts que falham não são persistentes, mesmo que você indique especificamente que eles devem ser.

* Podem aceitar __parâmetros__ usados pelo script durante a execução.

* São executados com os __privilégios no nível da raiz__ nos nós do cluster.

* Podem ser usados por meio do __Portal do Azure__, do __Azure PowerShell__, da __CLI do Azure__ ou do __SDK do .NET do HDInsight__

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-cli-and-dotnet-sdk.md)]

Para ajudar a compreender quais scripts foram aplicados a um cluster e determinar a ID dos scripts para a promoção ou o rebaixamento, o cluster mantém um histórico de todos os scripts que foram executados.

> [AZURE.IMPORTANT] Não há nenhuma forma automática de desfazer as alterações feitas por uma ação de script. Se você precisar reverter os efeitos de um script, deverá entender quais alterações foram feitas e revertê-las manualmente (ou fornecer uma ação de script que os reverta).

### Ação de Script no processo de criação do cluster

As ações de script usadas durante a criação do cluster são ligeiramente diferentes das ações de script executadas em um cluster existente:

* O script é __automaticamente persistente__.

* Uma __falha__ no script pode fazer com que o processo de criação do cluster falhe.

O diagrama a seguir ilustra quando a Ação de Script é executada durante o processo de criação:

![Personalização do cluster HDInsight e estágios durante a criação de cluster][img-hdi-cluster-states]

O script é executado enquanto o HDInsight está sendo configurado. Nesse estágio, o script é executado em paralelo com todos os nós especificados no cluster e é executado com os privilégios de raiz nos nós.

> [AZURE.NOTE] Como o script é executado com o privilégio de nível raiz nos nós do cluster, você pode executar operações como parar e iniciar serviços, incluindo os serviços relacionados ao Hadoop. Se você parar quaisquer serviços, você deve garantir que os serviços Ambari e outros serviços relacionados ao Hadoop estejam funcionando antes do script terminar a execução. Esses serviços são necessários para determinar com êxito a integridade e o estado do cluster enquanto ele está sendo criado.

Durante a criação do cluster, você pode especificar várias ações de script que são chamadas na ordem em que foram especificadas.

> [AZURE.IMPORTANT] Ações de script devem ser concluídas em 60 minutos ou atingirão o tempo limite. Durante o provisionamento do cluster, o script é executado simultaneamente com outros processos de instalação e configuração. A competição por recursos, como tempo de CPU ou largura de banda rede, pode fazer com que o script leve mais tempo para ser concluído comparado ao seu tempo de conclusão no ambiente de desenvolvimento.
>
> Para minimizar o tempo necessário para executar o script, evite tarefas como baixar e compilar aplicativos da origem. Em vez disso, pré-compile o aplicativo e armazene o binário no armazenamento de Blob do Azure para ele possa ser rapidamente baixado para o cluster.

###Ação de script em um cluster em execução

Ao contrário das ações de script usadas durante a criação do cluster, uma falha em um script executado em um cluster já em execução não faz com o cluster mude automaticamente para um estado de falha. Quando um script é concluído, o cluster deve retornar a um estado "em execução".

> [AZURE.IMPORTANT] Isso não significa que o cluster em execução está imune aos scripts que fazem coisas ruins. Por exemplo, um script pode excluir os arquivos necessários para o cluster, alterar a configuração para que os serviços falhem etc.
>
> As ações de script são executadas com privilégios de raiz, portanto, você deve assegurar que entendeu o que faz um script antes de aplicá-lo ao seu cluster.

Ao aplicar um script a um cluster, o estado do cluster mudará de __Executando__ para __Aceito__, em seguida, __Configuração do HDInsight__ e, por fim, de volta para __Executando__ para os scripts bem-sucedidos. O status do script é registrado no histórico de ações do script e você pode usá-lo para determinar se o script teve êxito ou falhou. Por exemplo, o cmdlet `Get-AzureRmHDInsightScriptActionHistory` do PowerShell pode ser usado para exibir o status de um script. Isso retornará informações semelhantes às seguintes:

    ScriptExecutionId : 635918532516474303
    StartTime         : 2/23/2016 7:40:55 PM
    EndTime           : 2/23/2016 7:41:05 PM
    Status            : Succeeded

> [AZURE.NOTE] Se você alterou a senha de usuário (admin) do cluster depois que o cluster foi criado, isso poderá fazer com que ações de script executadas em relação a esse cluster falhem. Se você tiver ações de script persistente direcionadas para nós de trabalho, elas poderão falhar quando você adicionar nós ao cluster por meio de operações de redimensionamento.

## Scripts de exemplo de Ação de Script

Scripts da Ação de Script podem ser usados do Portal do Azure, do Azure PowerShell, da CLI do Azure ou do SDK do .NET do HDInsight. O HDInsight fornece scripts para instalar os seguintes componentes nos clusters do HDInsight:

Nome | Script
----- | -----
**Adicionar uma conta de armazenamento do Azure** | https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh. Consulte [Aplicar uma Ação de Script em um cluster em execução](#apply-a-script-action-to-a-running-cluster).
**Instalar o Hue** | https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. Veja [Instalar e usar o Hue em clusters HDInsight](hdinsight-hadoop-hue-linux.md).
**Instalar R** | https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh. Consulte [Instalar e usar o R em clusters HDInsight](hdinsight-hadoop-r-scripts-linux.md).
**Instalar Solr** | https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. Veja [Instalar e usar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install-linux.md).
**Instalar o Giraph** | https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. Consulte [Instalar e usar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md).
| **Pré-carregar bibliotecas Hive** | https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh. Consulte [Adicionar bibliotecas do Hive em clusters do HDInsight](hdinsight-hadoop-add-hive-libraries.md) |

## Usar uma Ação de Script durante a criação do cluster

Esta seção fornece exemplos sobre as diferentes maneiras como você pode usar ações de script ao criar um cluster HDInsight – no portal do Azure, usando um modelo do Azure Resource Manager, CMDlets do PowerShell e o SDK do .NET.

### Usar uma Ação de Script durante a criação do cluster no Portal do Azure

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

3. Clique em **Selecionar** para salvar a configuração e continuar com a criação do cluster.

### Usar uma Ação de Script em modelos do Gerenciador de Recursos do Azure

Nesta seção, usamos modelos do Azure Resource Manager para criar um cluster HDInsight e também usamos uma ação de script para instalar componentes personalizados (R, neste exemplo) no cluster. Esta seção fornece uma amostra de modelo para criar um cluster usando a ação de script.

> [AZURE.NOTE] As etapas nesta seção demonstram como criar um cluster usando uma ação de script. Para obter um exemplo de criação de um cluster de um modelo usando um aplicativo do HDInsight, consulte [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md).

#### Antes de começar

* Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do PowerShell do HDInsight, consulte [Instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).
* Para obter instruções sobre como criar modelos, confira [Criando modelos do Azure Resource Manager](../resource-group-authoring-templates.md).
* Se você ainda não utilizou o PowerShell do Azure com o Gerenciador de recursos, consulte [Uso do PowerShell do Azure com o Gerenciador de recursos do Azure](../powershell-azure-resource-manager.md).

#### Criar clusters usando a Ação de Script

1. Copie o modelo a seguir para um local no computador. Esse modelo instala Giraph no nó de cabeçalho, bem como nós de trabalho no cluster. Você também pode verificar se o modelo JSON é válido. Cole o conteúdo do modelo no [JSONLint](http://jsonlint.com/), uma ferramenta online para validação de JSON.

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
		                            "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
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
		                                    "name": "installGiraph",
		                                    "uri": "https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh",
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

    > [AZURE.NOTE] Você pode usar `Get-AzureRmSubscription` para obter uma lista de todas as assinaturas associadas à sua conta, o que inclui a Id de assinatura de cada uma delas.

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

### Usar uma Ação de Script durante a criação do cluster no Azure PowerShell

Nesta seção, usamos o cmdlet [Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) para invocar scripts usando a Ação de Script para personalizar um cluster. Antes de prosseguir, verifique se você instalou e configurou o PowerShell do Azure. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do PowerShell do HDInsight, confira [Instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

Execute as seguintes etapas:

1. Abra o console do Azure PowerShell e use o seguinte para fazer logon em sua assinatura do Azure e declarar algumas variáveis do PowerShell:

        # LOGIN TO ZURE
        Login-AzureRmAccount

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

3. Use o cmdlet **Add-AzureRmHDInsightScriptAction** para invocar o script. O seguinte exemplo usa um script que instala o Giraph no cluster:

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION FOR HEADNODE AND WORKERNODE
		$config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install Giraph"  -NodeType HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
        $config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install Giraph"  -NodeType WorkerNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

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

### Usar uma Ação de Script durante a criação do cluster no SDK do .NET do HDInsight

O SDK do .NET do HDInsight fornece bibliotecas de cliente que facilitam o trabalho com o HDInsight por meio de um aplicativo .NET. Para ver um exemplo de código, consulte [Criar clusters baseados em Linux no HDInsight usando o SDK do .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## Aplicar uma Ação de Script em um cluster em execução

Esta seção fornece exemplos sobre as diferentes maneiras como você pode aplicar as ações de script em um cluster HDInsight em execução: no portal do Azure, usando CMDlets do PowerShell, usando a CLI do Azure de plataforma cruzada e o SDK do .NET. A ação de script persistente usada nesta seção adiciona uma conta de armazenamento do Azure existente a um cluster em execução. Você também pode usar outras ações de script, veja [Scripts de exemplo de Ação de Script](#example-script-action-scripts).

### Aplicar uma Ação de Script em um cluster em execução no portal do Azure

1. No [Portal do Azure](https://portal.azure.com), selecione o cluster HDInsight.

2. Na folha do cluster HDInsight, selecione o bloco __Ações de Script__.

    ![Bloco Ações de script](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

    > [AZURE.NOTE] Você também pode selecionar __Todas as configurações__ e __Ações de Script__ na folha Configurações.

4. Na parte superior da folha Ações de Script, selecione __Enviar novas__.

    ![Ícone Enviar novo](./media/hdinsight-hadoop-customize-cluster-linux/newscriptaction.png)

5. Na folha Adicionar Ação de Script, insira as informações a seguir.

    * __Nome__: o nome amigável a usar para esta Ação de Script. Neste exemplo, `Add Storage account`.
    * __URI DO SCRIPT__: o URI para o script. Neste exemplo, `https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`
    * __Cabeçalho__, __Trabalho__ e __Zookeeper__: verifique os nós aos quais esse script deve ser aplicado. Neste exemplo, Cabeçalho, Trabalho e Zookeeper são verificados.
    * __PARÂMETROS__: se o script aceita parâmetros, insira-os aqui. Neste exemplo, digite o nome da conta de armazenamento e a chave da conta de armazenamento:

		![hdinsight, ação de script persistente, conta de armazenamento para clusters em execução](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-persisted-script-action-add-storage-account.png)

		Na captura de tela, `contosodata` é uma conta existente do Armazenamento do Azure, a segunda linha é a chave da conta de armazenamento.
    * __PERSISTENTE__: verifique esta entrada se você quiser manter o script para que ele seja aplicado aos novos nós de trabalho quando escalar verticalmente o cluster.

6. Por fim, use o botão __Criar__ para aplicar o script ao cluster.

### Aplicar uma Ação de Script em um cluster em execução no Azure PowerShell

Antes de prosseguir, verifique se você instalou e configurou o PowerShell do Azure. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do PowerShell do HDInsight, confira [Instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

1. Abra o console do Azure PowerShell e use o seguinte para fazer logon em sua assinatura do Azure e declarar algumas variáveis do PowerShell:

        # LOGIN TO ZURE
        Login-AzureRmAccount

		# PROVIDE VALUES FOR THESE VARIABLES
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
        $saName = "<ScriptActionName>"                  # Name of the script action
        $saURI = "<URI to the script>"                  # The URI where the script is located
        $nodeTypes = "headnode", "workernode"
        
    > [AZURE.NOTE] Se usar um cluster HDInsight Premium, você poderá usar um nodetype de `"edgenode"` para executar o script no nó de borda.

2. Use o comando a seguir para aplicar o script no cluster:

        Submit-AzureRmHDInsightScriptAction -ClusterName $clusterName -Name $saName -Uri $saURI -NodeTypes $nodeTypes -PersistOnSuccess

    Quando o trabalho concluir, você deverá receber informações semelhantes ao seguinte:

        OperationState  : Succeeded
        ErrorMessage    :
        Name            : Giraph
        Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
        Parameters      :
        NodeTypes       : {HeadNode, WorkerNode}

### Aplicar uma Ação de Script em um cluster em execução da CLI do Azure

Antes de prosseguir, certifique-se de ter instalado e configurado a CLI do Azure. Para obter mais informações, consulte [Instalar a CLI do Azure](../xplat-cli-install.md).

	[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

1. Abra um terminal, prompt de comando ou sessão do shell ou outra linha de comando para o seu sistema e use o comando a seguir para alternar para modo Azure Resource Manager.

        azure config mode arm

2. Use o comando a seguir para fazer logon em sua assinatura do Azure.

        azure login

3. Use o comando a seguir para aplicar uma ação de script a um cluster em execução

        azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>

    Se você omitir parâmetros para esse comando, você será solicitado a fornecê-los. Caso o script especificado com `-u` aceite parâmetros, você poderá especificá-los usando o parâmetro `-p`.

    __Nodetypes__ válidos são __headnode__, __workernode__ e __zookeeper__. Caso o script deva ser aplicado a vários tipos de nós, especifique os tipos separados por um ';'. Por exemplo: `-n headnode;workernode`.

    Para persistir o script, adicione `--persistOnSuccess`. Também é possível persistir o script em uma data posterior usando `azure hdinsight script-action persisted set`.
    
    Quando o trabalho for concluído, você receberá uma saída semelhante à seguinte.
    
        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### Aplicar uma Ação de Script em um cluster em execução usando a API REST

Consulte [Executar Ações de Script em um cluster em execução](https://msdn.microsoft.com/library/azure/mt668441.aspx).
### Aplicar uma Ação de Script em um cluster em execução no SDK do .NET do HDInsight

Para obter um exemplo de como usar o SDK do .NET para aplicar scripts a um cluster, consulte [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## Exibir o histórico, promover e rebaixar Ações de Script

### Usando o portal do Azure

1. No [Portal do Azure](https://portal.azure.com), selecione o cluster HDInsight.

2. Na folha de cluster do HDInsight, selecione __Configurações__.

    ![Ícone Configurações](./media/hdinsight-hadoop-customize-cluster-linux/settingsicon.png)

3. Na folha Configurações, selecione __Ações de Script__.

    ![Link Ações de Script](./media/hdinsight-hadoop-customize-cluster-linux/settings.png)

4. Uma lista de scripts persistentes, bem como um histórico dos scripts aplicados ao cluster, é exibida na folha Ações de Script. Na captura de tela abaixo, você pode ver que o script Solr foi executado nesse cluster, mas nenhuma ação de script persistiu.

    ![Folha Ações de Script](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionhistory.png)

5. A seleção de um script no histórico exibirá a folha Propriedades desse script. Na parte superior da folha, você pode executar novamente o script ou promovê-lo.

    ![Folha Propriedades das ações de script](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionproperties.png)

6. Também é possível usar __...__ à direita das entradas na folha Ações de Script para realizar ações como executar novamente, persistir ou (para as ações persistentes) excluir.

    ![Ações de script... uso](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### Usando o PowerShell do Azure

| Usar o seguinte... | Para... |
| ----- | ----- |
| Get-AzureRmHDInsightPersistedScriptAction | Recuperar informações sobre as ações de script persistentes |
| Get-AzureRmHDInsightScriptActionHistory | Recuperar um histórico das ações de script aplicadas no cluster ou detalhes de um script específico |
| Set-AzureRmHDInsightPersistedScriptAction | Promove uma ação de script ad hoc para uma ação de script persistente |
| Remove-AzureRmHDInsightPersistedScriptAction | Rebaixa uma ação de script persistente a uma ação ad hoc |

> [AZURE.IMPORTANT] Usar `Remove-AzureRmHDInsightPersistedScriptAction` não desfaz as ações executadas por um script, apenas remove o sinalizador persistente para que o script não seja executado nos novos nós de trabalho adicionados ao cluster.

O script de exemplo a seguir demonstra como usar os cmdlets para promover e depois rebaixar um script.

    # Get a history of scripts
    Get-AzureRmHDInsightScriptActionHistory -ClusterName mycluster

    # From the list, we want to get information on a specific script
    Get-AzureRmHDInsightScriptActionHistory -ClusterName mycluster -ScriptExecutionId 635920937765978529

    # Promote this to a persisted script
    # Note: the script must have a unique name to be promoted
    # if the name is not unique, you will receive an error
    Set-AzureRmHDInsightPersistedScriptAction -ClusterName mycluster -ScriptExecutionId 635920937765978529

    # Demote the script back to ad hoc
    # Note that demotion uses the unique script name instead of
    # execution ID.
    Remove-AzureRmHDInsightPersistedScriptAction -ClusterName mycluster -Name "Install Giraph"

### Usando a CLI do Azure

| Usar o seguinte... | Para... |
| ----- | ----- |
| `azure hdinsight script-action persisted list <clustername>` | Recuperar uma lista de ações de script persistente |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` | Recuperar informações sobre uma ação de script persistente específica |
| `azure hdinsight script-action history list <clustername>` | Recuperar um histórico das ações de script aplicadas ao cluster |
| `azure hdinsight script-action history show <clustername> <scriptname>` | Recuperar informações sobre uma ação de script específica |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` | Promove uma ação de script ad hoc para uma ação de script persistente |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` | Rebaixa uma ação de script persistente a uma ação ad hoc |

> [AZURE.IMPORTANT] Usar `azure hdinsight script-action persisted delete` não desfaz as ações executadas por um script, apenas remove o sinalizador persistente para que o script não seja executado nos novos nós de trabalho adicionados ao cluster.

### Usando o SDK do .NET do HDInsight

Para obter um exemplo de como usar o SDK do .NET para recuperar o histórico de scripts de um cluster, promover ou rebaixar scripts, consulte [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [AZURE.NOTE] Este exemplo também demonstra como instalar um aplicativo do HDInsight usando o SDK do .NET.

## Solucionar problemas

Você pode usar a interface do usuário da Web Ambari para exibir as informações registradas pelas ações de script. Se o script foi usado durante a criação do cluster e a criação falhou devido a um erro no script, os logs também estarão disponíveis na conta de armazenamento padrão associada ao cluster. Esta seção fornece informações sobre como recuperar logs usando ambas as opções.

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

	Nele, os logs são organizados separadamente em nó de cabeçalho, nó de trabalho e nós do zookeeper. Alguns exemplos incluem:
	* **Nó de cabeçalho** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`
	* **Nó de Trabalho** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`
	* **Nó Zookeeper** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Todos os stdout e stderr do host correspondentes são carregados para a conta de armazenamento. Há um **output-*.txt** e um **errors-*.txt** para cada ação de script. O arquivo *.txt de saída contém informações sobre o URI do script que foi executado no host. Por exemplo,

		'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* É possível criar repetidamente um cluster de ação de script com o mesmo nome. Nesse caso, você pode distinguir os logs relevantes com base no nome da pasta DATE. Por exemplo, a estrutura de pastas de um cluster (mycluster) criado em datas diferentes será:
	* `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04`
	* `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Se você criar um cluster de ação de script com o mesmo nome no mesmo dia, você poderá usar o prefixo exclusivo para identificar os arquivos de log relevantes.

* Se você criar um cluster no final do dia, é possível que os arquivos de log incluam dois dias. Nesses casos, você verá duas pastas de datas diferentes para o mesmo cluster.

* Carregar arquivos de log no contêiner padrão pode levar até 5 minutos, especialmente para clusters grandes. Portanto, se desejar acessar os logs, você não deverá imediatamente excluir o cluster se uma ação de script falhar.


## Suporte para software livre utilizado em clusters do HDInsight

O serviço Microsoft Azure HDInsight é uma plataforma flexível que permite compilar aplicativos Big Data na nuvem usando um ecossistema de tecnologias de software livre baseado no Hadoop. O Microsoft Azure fornece um nível geral de suporte para tecnologias de software livre, como discutimos na seção **Escopo do suporte** do [site de Perguntas frequentes sobre o Suporte do Azure](https://azure.microsoft.com/support/faq/). O serviço HDInsight fornece um nível adicional de suporte a alguns dos componentes, como descrito abaixo.

Há dois tipos de componentes de software livre disponíveis no serviço HDInsight:

- **Componentes internos**: estes componentes estão pré-instalado em clusters HDInsight e fornecem a funcionalidade básica do cluster. Por exemplo, o gerenciador de recursos YARN RM, o HiveQL (linguagem de consulta do Hive) e a biblioteca Mahout pertencem a esta categoria. Uma lista completa dos componentes de cluster está disponível em [Novidades nas versões do cluster Hadoop fornecidas pelo HDInsight](hdinsight-component-versioning.md).

- **Componentes personalizados**: como usuário do cluster, você pode instalar ou usar em sua carga de trabalho qualquer componente disponível na comunidade ou criado por você.

> [AZURE.WARNING] Há suporte total a componentes fornecidos com o cluster HDInsight e o Suporte da Microsoft ajudará a isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. Isso pode resultar na resolução do problema ou na solicitação de você buscar nos canais disponíveis as tecnologias de código-fonte aberto, onde é possível encontrar conhecimento aprofundado sobre essa tecnologia. Por exemplo, há muitos sites de comunidades que podem ser usados, como o [Fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/pt-BR/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Além disso, os projetos do Apache têm sites do projeto em [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/).

O serviço HDInsight possibilita o uso de componentes personalizados de várias formas. Seja como for usado ou instalado em um cluster, o mesmo nível de suporte se aplica ao componente. Mostramos, a seguir, uma lista das formas mais comuns de usar os componentes personalizados em clusters HDInsight:

1. Envio de trabalhos: trabalhos do Hadoop ou de outros tipos que executam ou usam componentes personalizados podem ser enviados para o cluster.

2. Personalização de clusters: durante a criação de clusters, você pode especificar configurações adicionais e componentes personalizados que serão instalados nos nós de cluster.

3. Exemplos: para componentes personalizados populares, a Microsoft e outras empresas podem fornecer exemplos de uso desses componentes em clusters HDInsight. Esses exemplos são fornecidos sem suporte.

##Solucionar problemas

###O histórico de não mostra os scripts usados durante a criação do cluster

Se o cluster tiver sido criado antes de 15 de março de 2016, você não verá uma entrada no histórico de Ação de Script para todos os scripts usados durante a criação do cluster. No entanto, se você redimensionar o cluster após 15 de março de 2016, os scripts usados durante a criação do cluster aparecerão no histórico, pois eles são aplicados aos novos nós no cluster como parte da operação de redimensionamento.

Há duas exceções:

* Se o cluster tiver sido criado antes de 1º de setembro de 2015. As Ações de Script foram introduzidas nessa data e, portanto, qualquer cluster criado antes não poderia ter usado as Ações de Script para a criação do cluster.

* Se você tiver usado várias Ações de Script durante a criação do cluster e se tiver usado o mesmo nome para vários scripts ou o mesmo nome, o mesmo URI, mas diferentes parâmetros para vários scripts. Nesses casos, você receberá o erro a seguir.

    Nenhuma nova ação de script pode ser executada nesse cluster devido aos nomes de script conflitantes nos scripts existentes. Os nomes de script fornecidos na criação do cluster devem ser exclusivos. Os scripts existentes ainda serão executados no redimensionamento.

## Próximas etapas

Consulte as informações e exemplos a seguir sobre como criar e usar scripts para personalizar um cluster:

- [Desenvolver scripts de Ação de Script para o HDInsight](hdinsight-hadoop-script-actions-linux.md)
- [Instalar e usar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install-linux.md)
- [Instalar e usar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md)



[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Estágios durante a criação de cluster"

<!---HONumber=AcomDC_0914_2016-->