<properties
	pageTitle="Spark no modelo do Gerenciador de Recursos do Ubuntu"
	description="Saiba como implantar com facilidade um novo cluster Spark em VMs Ubuntu usando o PowerShell ou a CLI do Azure e um modelo do Gerenciador de Recursos"
	services="virtual-machines"
	documentationCenter=""
	authors="paolosalvatori"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="05/16/2015"
	ms.author="paolosalvatori"/>

# Spark no Ubuntu com um modelo do Gerenciador de Recursos

Apache Spark é um mecanismo rápido para processamento de dados em grande escala. O Spark tem um mecanismo de execução DAG avançado que oferece suporte ao fluxo de dados cíclicos e computação na memória, e pode acessar várias fontes de dados, incluindo HDFS, Spark, HBase e S3.

Além de executar nos gerenciadores de cluster Mesos ou YARN, o Spark fornece um modo simples de implantação autônoma. Este tutorial mostrará como usar um exemplo de modelo do Gerenciador de Recursos do Azure para implantar um cluster Spark em VMs Ubuntu por meio do [Azure PowerShell](../powershell-install-configure.md) ou da [CLI do Azure](../xplat-cli.md).

Esse modelo implanta um cluster Spark em máquinas virtuais Ubuntu. Também fornece uma conta de armazenamento, rede virtual, conjuntos de disponibilidade, endereços IP públicos e interfaces de rede necessários para a instalação. O cluster Spark é criado por trás de uma sub-rede e, portanto, não há acesso de IP público ao cluster. Como parte da implantação, uma "jump box" opcional pode ser implantada. Essa “jump box” é uma VM Ubuntu implantada na sub-rede, mas que *expõe* um endereço IP público com uma porta SSH aberta com qual você pode se conectar. Em seguida, da “jump box”, é possível executar SSH em todas as VMs Spark na sub-rede.

Este modelo utiliza um conceito de "tamanho de camiseta" para especificar uma configuração de cluster Spark "pequena", "média" ou "grande". Quando o idioma do modelo oferece suporte a um dimensionamento de modelo mais dinâmico, é possível fazer alterações para especificar o número de nós mestres do cluster Spark, além dos nós subordinados, tamanho da VM, etc. Por enquanto, você pode ver o tamanho da VM e o número de mestres e subordinados definidos no arquivo azuredeploy.json nas variáveis **tshirtSizeS**, **tshirtSizeM** e **tshirtSizeL**:

- P: 1 mestre, 1 subordinado
- M: 1 mestre, 4 subordinados
- G: 1 mestre, 6 subordinados

Clusters recém-implantados com base nesse modelo terão a topologia descrita no diagrama a seguir, embora outras topologias possam ser facilmente obtidas personalizando-se o modelo apresentado neste artigo:

![cluster-architecture](media/virtual-machines-spark-template/cluster-architecture.png)

Conforme mostra a imagem acima, a topologia de implantação é composta pelos elementos a seguir:

-	Uma nova conta de armazenamento hospedando o disco de SO de máquinas virtuais criadas recentemente.
-	Uma rede virtual com uma sub-rede. Todas as máquinas virtuais criadas pelo modelo são provisionadas dentro dessa rede virtual.
-	Um conjunto de disponibilidade para nós mestre e subordinados.
-	Um nó mestre no conjunto de disponibilidade recém-criado.
-	Quatro nós subordinados em execução na mesma sub-rede virtual, e conjunto de disponibilidade como o nó mestre.
-	Uma VM jump box localizada na mesma rede virtual e sub-rede que pode ser usada para acessar o cluster.

O Spark versão 3.0.0 é a versão padrão e pode ser alterada para qualquer binário pré-criado disponível no repositório do Spark. Há também uma cláusula no script para remover os comentários da compilação do código-fonte. Um endereço IP estático será atribuído a cada nó mestre do Spark: 10.0.0.10. Um endereço IP estático será atribuído a cada nó escravo do Spark para contornar a limitação atual de não ser possível criar dinamicamente uma lista de endereços IP de dentro do modelo. (Por padrão, o primeiro nó será atribuído ao endereço IP privado 10.0.0.30, o segundo nó será atribuído ao 10.0.0.31 e assim por diante.) Para verificar erros de implantação, acesse o novo portal do Azure e procure **Grupo de Recursos** > **Última Implantação** > **Verificar Detalhes da Operação**.

Antes de lidar com mais detalhes relacionados ao Gerenciador de Recursos do Azure e ao modelo usado para essa implantação, verifique se você tem o PowerShell ou a CLI do Azure configurados corretamente.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## Criar um cluster Spark usando um modelo do Gerenciador de Recursos

Execute estas etapas para criar um cluster Spark usando um modelo do Gerenciador de Recursos do repositório de modelos do GitHub, usando o Azure PowerShell ou a CLI do Azure.

### Etapa 1-a: baixar os arquivos de modelo usando o PowerShell do Azure

Crie uma pasta local para o modelo JSON e outros arquivos associados (por exemplo, C:\\Azure\\Templates\\Spark).

Substitua o nome da pasta local e execute os seguintes comandos:

```powershell
# Define variables
$folderName="C:\Azure\Templates\Spark"
$baseAddress = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/spark-on-ubuntu/"
$webclient = New-Object System.Net.WebClient
$files = $("azuredeploy.json", `
         "azuredeploy-parameters.json", `
         "jumpbox-resources-disabled.json", `
         "jumpbox-resources-enabled.json",
         "metadata.json", `
         "shared-resources.json", `
         "spark-cluster-install.sh", `
         "jumpbox-resources-enabled.json")

# Download files
foreach ($file in $files)
{
    $url = $baseAddress + $file
    $filePath = $folderName + $file
    $webclient.DownloadFile($baseAddress + $file, $folderName + $file)
    Write-Host $url "downloaded to" $filePath
}
```

### Etapa 1-b: baixar os arquivos de modelo usando a CLI do Azure

Clone todo o repositório de modelos usando um cliente Git de sua escolha, por exemplo:

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

Quando a clonagem for concluída, procure a pasta **spark-on-ubuntu** no diretório C:\\Azure\\Templates.

### Etapa 2 (opcional): compreender os parâmetros do modelo

Ao criar um cluster Spark usando um modelo, é preciso especificar um conjunto de parâmetros de configuração para lidar com várias configurações necessárias. Declarando esses parâmetros na definição do modelo, é possível especificar valores durante a execução da implantação por meio de um arquivo externo ou em uma linha de comando.

Na seção "parâmetros" no início do arquivo azuredeploy.json, você encontrará o conjunto de parâmetros necessários ao modelo para configurar um cluster Spark. Veja um exemplo da seção "parâmetros" desse arquivo de modelo azuredeploy.json:

```json
"parameters": {
	"adminUsername": {
		"type": "string",
		"metadata": {
			"Description": "Administrator user name used when provisioning virtual machines"
		}
	},
	"adminPassword": {
		"type": "securestring",
		"metadata": {
			"Description": "Administrator password used when provisioning virtual machines"
		}
	},
	"imagePublisher": {
		"type": "string",
		"defaultValue": "Canonical",
		"metadata": {
			"Description": "Image publisher"
		}
	},
	"imageOffer": {
		"type": "string",
		"defaultValue": "UbuntuServer",
		"metadata": {
			"Description": "Image offer"
		}
	},
	"imageSKU": {
		"type": "string",
		"defaultValue": "14.04.2-LTS",
		"metadata": {
			"Description": "Image SKU"
		}
	},
	"storageAccountName": {
		"type": "string",
		"defaultValue": "uniquesparkstoragev12",
		"metadata": {
			"Description": "Unique namespace for the Storage account where the virtual machine's disks will be placed"
		}
	},
	"region": {
		"type": "string",
		"defaultValue": "West US",
		"metadata": {
			"Description": "Location where resources will be provisioned"
		}
	},
	"virtualNetworkName": {
		"type": "string",
		"defaultValue": "myVNETspark",
		"metadata": {
			"Description": "The arbitrary name of the virtual network provisioned for the cluster"
		}
	},
	"dataDiskSize": {
		"type": "int",
		"defaultValue": 100,
		"metadata": {
			"Description": "Size of the data disk attached to Spark nodes (in GB)"
		}
	},
	"addressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.0/16",
		"metadata": {
			"Description": "The network address space for the virtual network"
		}
	},
	"subnetName": {
		"type": "string",
		"defaultValue": "Subnet-1",
		"metadata": {
			"Description": "Subnet name for the virtual network that resources will be provisioned into"
		}
	},
	"subnetPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.0/24",
		"metadata": {
			"Description": "Address space for the virtual network subnet"
		}
	},
	"sparkVersion": {
		"type": "string",
		"defaultValue": "stable",
		"metadata": {
			"Description": "The version of the Spark package to be deployed on the cluster (or use 'stable' to pull in the latest and greatest)"
		}
	},
	"sparkClusterName": {
		"type": "string",
		"metadata": {
			"Description": "The arbitrary name of the Spark cluster (maps to cluster's configuration file name)"
		}
	},
	"sparkNodeIPAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.1",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"sparkSlaveNodeIPAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.3",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"jumpbox": {
		"type": "string",
		"defaultValue": "enabled",
		"allowedValues": [
		"enabled",
		"disabled"
		],
		"metadata": {
			"Description": "The flag allowing to enable or disable provisioning of the jump-box VM that can be used to access the Spark nodes"
		}
	},
	"tshirtSize": {
		"type": "string",
		"defaultValue": "S",
		"allowedValues": [
		"S",
		"M",
		"L"
		],
		"metadata": {
			"Description": "T-shirt size of the Spark cluster"
		}
	}
}
```

Cada parâmetro tem detalhes como tipo de dados e valores permitidos. Isso permite a validação dos parâmetros passados durante a execução do modelo em um modo interativo (por exemplo, PowerShell do Azure ou CLI do Azure), bem como uma interface de usuário de autodescoberta que pode ser criada dinamicamente ao analisar a lista de parâmetros necessários e suas descrições.

### Etapa 3-a: Implantar um cluster Spark usando um modelo por meio do Azure PowerShell

Prepare um arquivo de parâmetros para a sua implantação criando um arquivo JSON que contém os valores de tempo de execução para todos os parâmetros. Em seguida, esse arquivo será passado como uma única entidade para o comando de implantação. Se você não incluir um arquivo de parâmetros, o PowerShell do Azure usará qualquer valor padrão especificado no modelo e solicitará que você preencha os valores restantes.

Veja um exemplo de conjunto de parâmetros do arquivo azuredeploy-parameters.json. Observe que você precisa fornecer valores válidos para os parâmetros **storageAccountName**, **adminUsername** e **adminPassword**, além de qualquer personalização para os outros parâmetros:

```json
{
  "storageAccountName": {
    "value": "paolosspark"
  },
  "adminUsername": {
    "value": "paolos"
  },
  "adminPassword": {
    "value": "Passw0rd!"
  },
  "region": {
    "value": "West US"
  },
  "virtualNetworkName": {
    "value": "sparkClustVnet"
  },
  "subnetName": {
    "value": "Subnet1"
  },
  "addressPrefix": {
    "value": "10.0.0.0/16"
  },
  "subnetPrefix": {
    "value": "10.0.0.0/24"
  },
  "sparkVersion": {
    "value": "3.0.0"
  },
  "sparkClusterName": {
    "value": "spark-arm-cluster"
  },
  "sparkNodeIPAddressPrefix": {
		"value": "10.0.0.1"
  },
  "sparkSlaveNodeIPAddressPrefix": {
    "value": "10.0.0.3"
  },
  "jumpbox": {
    "value": "enabled"
  },
  "tshirtSize": {
    "value": "M"
  }
}
```
Preencha o nome da implantação do Azure, o nome do grupo de recursos, o local do Azure e a pasta em que você salvou os arquivos de implantação JSON. Em seguida, execute estes comandos:

```powershell
$deployName="<deployment name>"
$RGName="<resource group name>"
$locName="<Azure location, such as West US>"
$folderName="<folder name, such as C:\Azure\Templates\Spark>"
$templateFile= $folderName + "\azuredeploy.json"
$templateParameterFile= $folderName + "\azuredeploy-parameters.json"
New-AzureResourceGroup -Name $RGName -Location $locName
New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParamterFile $templateParameterFile -TemplateFile $templateFile
```
> [AZURE.NOTE]**$RGName** deve ser exclusivo dentro de sua assinatura.

Quando você executar o comando **New-AzureResourceGroupDeployment**, isso extrairá valores de parâmetros do arquivo de parâmetros JSON e iniciará a execução do modelo de forma adequada. A definição e o uso de vários arquivos de parâmetros com seus diferentes ambientes (por exemplo, teste, produção etc.) promoverão a reutilização do modelo e simplificarão soluções complexas com vários ambientes.

Ao implantar, tenha em mente que uma nova conta de Armazenamento do Azure precisará ser criada. Portanto, o nome fornecido como o parâmetro de conta de Armazenamento precisa ser exclusivo e atender a todos os requisitos para uma conta de Armazenamento do Azure (somente letras minúsculas e números).

Durante a implantação, você verá algo assim:

```powershell
PS C:> New-AzureResourceGroup -Name $RGName -Location $locName

ResourceGroupName : SparkResourceGroup
Location          : westus
ProvisioningState : Succeeded
Tags              :
Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

ResourceId        : /subscriptions/2018abc3-dbd9-4437-81a8-bb3cf56138ed/resourceGroups/sparkresourcegroup

PS C:> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
VERBOSE: 10:08:28 AM - Template is valid.
VERBOSE: 10:08:28 AM - Create template deployment 'SparkTestDeployment'.
VERBOSE: 10:08:37 AM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is running
VERBOSE: 10:09:11 AM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is succeeded
VERBOSE: 10:09:13 AM - Resource Microsoft.Network/networkInterfaces 'nicsl0' provisioning status is succeeded
VERBOSE: 10:09:16 AM - Resource Microsoft.Network/networkInterfaces 'nic0' provisioning status is succeeded
VERBOSE: 10:09:16 AM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is running
VERBOSE: 10:09:24 AM - Resource Microsoft.Compute/virtualMachines 'mastervm0' provisioning status is running
VERBOSE: 10:11:04 AM - Resource Microsoft.Compute/virtualMachines/extensions 'mastervm0/installsparkmaster'
provisioning status is running
VERBOSE: 10:11:04 AM - Resource Microsoft.Compute/virtualMachines 'mastervm0' provisioning status is succeeded
VERBOSE: 10:11:11 AM - Resource Microsoft.Compute/virtualMachines 'slavevm0' provisioning status is running
VERBOSE: 10:11:42 AM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is succeeded
VERBOSE: 10:13:10 AM - Resource Microsoft.Compute/virtualMachines 'slavevm0' provisioning status is succeeded
VERBOSE: 10:13:15 AM - Resource Microsoft.Compute/virtualMachines/extensions 'slavevm0/installsparkslave' provisioning
status is running
VERBOSE: 10:16:58 AM - Resource Microsoft.Compute/virtualMachines/extensions 'mastervm0/installsparkmaster'
provisioning status is succeeded
VERBOSE: 10:19:05 AM - Resource Microsoft.Compute/virtualMachines/extensions 'slavevm0/installsparkslave' provisioning
status is succeeded


DeploymentName    : SparkTestDeployment
ResourceGroupName : SparkResourceGroup
ProvisioningState : Succeeded
Timestamp         : 5/5/2015 5:19:05 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     Paolos
                    adminPassword    SecureString
                    imagePublisher   String                     Canonical
                    imageOffer       String                     UbuntuServer
                    imageSKU         String                     14.04.2-LTS
                    storageAccountName  String                  paolosspark
                    region           String                     West US
                    virtualNetworkName  String                  sparkClustVnet
                    addressPrefix    String                     10.0.0.0/16
										subnetName       String                     Subnet1
                    subnetPrefix     String                     10.0.0.0/24
                    sparkVersion     String                     3.0.0
                    sparkClusterName  String                    spark-arm-cluster
                    sparkNodeIPAddressPrefix  String            10.0.0.1
                    sparkSlaveNodeIPAddressPrefix  String       10.0.0.3
                    jumpbox          String                     enabled
                    tshirtSize       String                     M
```

Durante e após a implantação, você pode verificar todas as solicitações feitas durante o provisionamento, incluindo quaisquer erros ocorridos.

Para fazer isso, acesse o [portal do Azure](https://portal.azure.com) e siga este procedimento:

- Clique em **Procurar** na barra de navegação à esquerda, role para baixo e clique em **Grupos de Recursos**.
- Clique no grupo de recursos que você acabou de criar, o que exibirá a folha "Grupo de Recursos".
- Clicando no gráfico de barras **Eventos** na parte **Monitoramento** da folha "Grupo de Recursos", será possível ver os eventos de sua implantação.
- Ao clicar em eventos individuais, será possível fazer uma busca detalhada dos detalhes de cada operação individual feita em nome do modelo.

![portal-events](media/virtual-machines-spark-template/portal-events.png)

Depois dos testes, se você precisar remover esse grupo de recursos e todos os seus recursos (a conta de armazenamento, a máquina virtual e a rede virtual), use este comando:

```powershell
Remove-AzureResourceGroup -Name "<resource group name>" -Force
```

### Etapa 3-b: Implantar um cluster Spark usando um modelo por meio da CLI do Azure

Para implantar um cluster Spark pela CLI do Azure, primeiro crie um grupo de recursos especificando um nome e um local:

	azure group create SparkResourceGroup "West US"

Passe o nome desse grupo de recursos, o local do arquivo de modelo JSON e o local do arquivo de parâmetros (consulte a seção acima sobre o PowerShell do Azure para saber mais) no comando abaixo:

	azure group deployment create SparkResourceGroup -f .\azuredeploy.json -e .\azuredeploy-parameters.json

É possível verificar o status das implantações de recursos individuais usando o seguinte comando:

	azure group deployment list SparkResourceGroup

## Um tour pela estrutura do modelo do Spark e da organização de arquivos

Para criar um modelo robusto e reutilizável do Gerenciador de Recursos, é preciso realizar preparação adicional para organizar a série de tarefas complexas e inter-relacionadas necessárias durante a implantação de uma solução complexa como o Spark. Aproveitando os loops de recursos e a vinculação de modelos do Gerenciador de Recursos, além da execução de scripts por meio de extensões relacionadas, é possível implementar uma abordagem modular que pode ser reutilizada com praticamente qualquer implantação complexa com base no modelo.

Este diagrama descreve as relações entre todos os arquivos baixados do GitHub para essa implantação:

![spark-files](media/virtual-machines-spark-template/spark-files.png)

Esta seção percorre a estrutura do arquivo azuredeploy.json para o cluster Spark.

Se você ainda não baixou uma cópia do arquivo de modelo, indique uma pasta local como o local para o arquivo e crie-o (por exemplo, C:\\Azure\\Templates\\Spark). Preencha o nome da pasta e execute estes comandos:

```powershell
$folderName="<folder name, such as C:\Azure\Templates\Spark>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)
```

Abra o modelo azuredeploy.json em um editor de texto ou na ferramenta de sua escolha. As informações a seguir descrevem a estrutura do arquivo de modelo e a finalidade de cada seção. Como alternativa, você pode ver o conteúdo do modelo em seu navegador clicando [aqui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/azuredeploy.json).

### Seção "parâmetros"

A seção "parâmetros" do arquivo azuredeploy.json especifica os parâmetros modificáveis que são usados nesse modelo. O arquivo azuredeploy-parameters.json mencionado anteriormente é usado para passar valores para a seção "parâmetros" de azuredeploy.json durante a execução do modelo.

Veja a seguir um exemplo de um parâmetro para “tamanho de camiseta”:

```json
"tshirtSize": {
    "type": "string",
    "defaultValue": "S",
    "allowedValues": [
        "S",
        "M",
        "L"
    ],
    "metadata": {
        "Description": "T-shirt size of the Spark deployment"
    }
},
```

> [AZURE.NOTE]Observe que **defaultValue** pode ser especificado, bem como **allowedValues**.

### Seção "variáveis"

A seção "variáveis" especifica variáveis que podem ser usadas em todo esse modelo. Ela contém vários campos (fragmentos ou tipos de dados JSON) que serão definidos como constantes ou valores calculados em tempo de execução. Veja a seguir alguns exemplos simples e mais complexos:

```json
"variables": {
	"vmStorageAccountContainerName": "vhd",
	"vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
	"subnetRef": "[concat(variables('vnetID'),'/subnets/',parameters('subnetName'))]",
	"computerNamePrefix": "sparknode",
	"scriptUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/",
	"templateUrl": "[variables('scriptUrl')]",
	"sharedTemplateName": "shared-resources",
	"jumpboxTemplateName": "jumpbox-resources-",
	"tshirtSizeS": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 1,
		"vmSize": "Standard_A2"
	},
	"tshirtSizeM": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 4,
		"vmSize": "Standard_A4"
	},
	"tshirtSizeL": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 6,
		"vmSize": "Standard_A7"
	},
	"numberOfMasterInstances": "[variables(concat('tshirtSize', parameters('tshirtSize'))).numberOfMasterInstances]",
	"numberOfSlavesInstances": "[variables(concat('tshirtSize', parameters('tshirtSize'))).numberOfSlavesInstances]",
	"vmSize": "[variables(concat('tshirtSize', parameters('tshirtSize'))).vmSize]"
},
```

A variável **vmStorageAccountContainerName** é um exemplo de uma variável simples de nome/valor. **vnetID** é um exemplo de uma variável calculada durante a execução usando as funções **resourceId** e **parameters**. O valor das variáveis **numberOfMasterInstances** e **vmSize** é calculado durante a execução usando as funções **concat**, **variables** e **parameters**.

Se você quiser personalizar o tamanho da implantação do cluster Spark, altere as propriedades das variáveis **tshirtSizeS**, **tshirtSizeM** e **tshirtSizeL** no modelo azuredeploy.json.

Para saber mais sobre o idioma do modelo, consulte a MSDN em [Idioma do modelo do Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).


### Seção "recursos"

A seção "recursos" é onde acontece a maior parte da ação. Analisando cuidadosamente essa seção, você pode identificar imediatamente dois casos diferentes. O primeiro é um elemento definido do tipo `Microsoft.Resources/deployments` que essencialmente invoca uma implantação aninhada dentro do elemento principal. O segundo é a propriedade **templateLink** (e a propriedade **contentVersion** relacionada), que torna possível a especificação de um arquivo de modelo vinculado que será invocado, passando um conjunto de parâmetros como entrada. Eles podem ser vistos neste fragmento do modelo:

```json
"resources": [
{
	"name": "shared-resources",
	"type": "Microsoft.Resources/deployments",
	"apiVersion": "2015-01-01",
	"properties": {
		"mode": "Incremental",
		"templateLink": {
			"uri": "[concat(variables('templateUrl'), variables('sharedTemplateName'), '.json')]",
			"contentVersion": "1.0.0.0"
		},
		"parameters": {
			"region": {
				"value": "[parameters('region')]"
			},
			"storageAccountName": {
				"value": "[parameters('storageAccountName')]"
			},
			"virtualNetworkName": {
				"value": "[parameters('virtualNetworkName')]"
			},
			"addressPrefix": {
				"value": "[parameters('addressPrefix')]"
			},
			"subnetName": {
				"value": "[parameters('subnetName')]"
			},
			"subnetPrefix": {
				"value": "[parameters('subnetPrefix')]"
			}
		}
	}
},
```

Neste primeiro exemplo, fica claro como azuredeploy.json nesse cenário foi organizado como um mecanismo de orquestração, invocando vários outros arquivos de modelo. Cada arquivo é responsável por parte das atividades de implantação necessárias.

Em particular, os seguintes modelos vinculados serão usados para essa implantação:

-	**shared-resource.json**: contém a definição de todos os recursos que serão compartilhados na implantação. Os exemplos são contas de Armazenamento usadas para armazenar as redes virtuais e os discos do SO da VM.
-	**jumpbox-resources-enabled.json**: implanta a VM "jump box" e todos os recursos relacionados, como a interface de rede, o endereço IP público e o ponto de extremidade de entrada usados para executar SSH no ambiente.

Depois de invocar esses dois modelos, o azuredeploy.json provisiona todas as VMs de nó do cluster Spark e os recursos conectados (adaptadores de rede, IPs privados etc.). Este modelo também implanta extensões de VM (scripts personalizados para Linux) e invoca um script bash (spark-cluster-install.sh) para instalar fisicamente e configurar o Spark em cada nó.

Vamos detalhar *como* esse último modelo, azuredeploy.json, é usado, pois ele é um dos mais interessantes em termos de desenvolvimento de modelo. Um conceito importante a ser realçado é como um único arquivo de modelo pode implantar várias cópias de um único tipo de recurso e, para cada instância, pode definir valores exclusivos para as configurações necessárias. Este conceito é conhecido como **loop de recursos**.

Um recurso que usa o elemento **copy** criará cópias de si mesmo de acordo com o número de vezes especificado no parâmetro **count** do elemento **copy**. Para todas as configurações em que é necessário especificar valores exclusivos entre diferentes instâncias do recurso implantado, a função **copyindex()** pode ser usada para obter um valor numérico que indica o índice atual nessa criação de loop de recursos específica. No fragmento a seguir do azuredeploy.json, é possível ver esse conceito aplicado a várias adaptadores de rede, VMs e extensões de VM que estão sendo criadas para o cluster Spark:

```json
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Network/networkInterfaces",
	"name": "[concat('nic', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "nicLoop",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
	],
	"properties": {
		"ipConfigurations": [
		{
			"name": "ipconfig1",
			"properties": {
				"privateIPAllocationMethod": "Static",
				"privateIPAddress": "[concat(parameters('sparkNodeIPAddressPrefix'), copyindex())]",
				"subnet": {
					"id": "[variables('subnetRef')]"
				}
			}
		}
		]
	}
},
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Network/networkInterfaces",
	"name": "[concat('nicsl', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "nicslLoop",
		"count": "[variables('numberOfSlavesInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
	],
	"properties": {
		"ipConfigurations": [
		{
			"name": "ipconfig1",
			"properties": {
				"privateIPAllocationMethod": "Static",
				"privateIPAddress": "[concat(parameters('sparkSlaveNodeIPAddressPrefix'), copyindex())]",
				"subnet": {
					"id": "[variables('subnetRef')]"
				}
			}
		}
		]
	}
},
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Compute/virtualMachines",
	"name": "[concat('mastervm', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineLoopMaster",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]",
	"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
	],
	"properties": {
		"availabilitySet": {
			"id": "[resourceId('Microsoft.Compute/availabilitySets', 'sparkCluserAS')]"
		},
		"hardwareProfile": {
			"vmSize": "[variables('vmSize')]"
		},
		"osProfile": {
			"computername": "[concat(variables('computerNamePrefix'), copyIndex())]",
			"adminUsername": "[parameters('adminUsername')]",
			"adminPassword": "[parameters('adminPassword')]",
			"linuxConfiguration": {
				"disablePasswordAuthentication": "false"
			}
		},
		"storageProfile": {
			"imageReference": {
				"publisher": "[parameters('imagePublisher')]",
				"offer": "[parameters('imageOffer')]",
				"sku" : "[parameters('imageSKU')]",
				"version":"latest"
			},
			"osDisk" : {
				"name": "osdisk",
				"vhd": {
					"uri": "[concat('https://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/','osdisk', copyindex() ,'.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"
				}
			},
			"networkProfile": {
				"networkInterfaces": [
				{
					"id": "[resourceId('Microsoft.Network/networkInterfaces',concat('nic', copyindex()))]"
				}
				]
			}
		}
	},
	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Compute/virtualMachines",
		"name": "[concat('slavevm', copyindex())]",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineLoop",
			"count": "[variables('numberOfSlavesInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Resources/deployments/', 'shared-resources')]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]",
		"virtualMachineLoopMaster"
		],
		"properties": {
			"availabilitySet": {
				"id": "[resourceId('Microsoft.Compute/availabilitySets', 'sparkCluserAS')]"
			},
			"hardwareProfile": {
				"vmSize": "[variables('vmSize')]"
			},
			"osProfile": {
				"computername": "[concat(variables('computerNamePrefix'),'sl', copyIndex())]",
				"adminUsername": "[parameters('adminUsername')]",
				"adminPassword": "[parameters('adminPassword')]",
				"linuxConfiguration": {
					"disablePasswordAuthentication": "false"
				}
			},
			"storageProfile": {
				"imageReference": {
					"publisher": "[parameters('imagePublisher')]",
					"offer": "[parameters('imageOffer')]",
					"sku" : "[parameters('imageSKU')]",
					"version":"latest"
				},
				"osDisk" : {
					"name": "osdisksl",
					"vhd": {
						"uri": "[concat('https://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/','osdisksl', copyindex() ,'.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"
				}
			},
			"networkProfile": {
				"networkInterfaces": [
				{
					"id": "[resourceId('Microsoft.Network/networkInterfaces',concat('nicsl', copyindex()))]"
				}
				]
			}
		}
	},
	{
		"type": "Microsoft.Compute/virtualMachines/extensions",
		"name": "[concat('mastervm', copyindex(), '/installsparkmaster')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineExtensionsLoopMaster",
			"count": "[variables('numberOfMasterInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'mastervm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
		],
		"properties": {
			"publisher": "Microsoft.OSTCExtensions",
			"type": "CustomScriptForLinux",
			"typeHandlerVersion": "1.2",
			"settings": {
				"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
				],
				"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -d ', reference('nic0').ipConfigurations[0].properties.privateIPAddress,' -s ',variables('numberOfSlavesInstances'),' -m ', ' 1 ')]"
			}
		}
	},
	{
		"type": "Microsoft.Compute/virtualMachines/extensions",
		"name": "[concat('slavevm', copyindex(), '/installsparkslave')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineExtensionsLoopSlave",
			"count": "[variables('numberOfSlavesInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'slavevm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]"
		],
		"properties": {
			"publisher": "Microsoft.OSTCExtensions",
			"type": "CustomScriptForLinux",
			"typeHandlerVersion": "1.2",
			"settings": {
				"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
				],
				"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -m ', ' 0 ')]"
			}
		}
	}
```

Outro conceito importante na criação de recursos é a capacidade de especificar dependências e precedências entre recursos, como você pode ver na matriz JSON **dependsOn**. Neste modelo específico, é possível ver que os nós do cluster Spark dependem dos recursos compartilhados e do recursos **networkInterfaces** que estão sendo criados primeiro.

Outro fragmento interessante a ser explorado é aquele relacionado às extensões de VM **CustomScriptForLinux**. Elas são instaladas como um tipo de recurso separado, com uma dependência em cada nó do cluster. Neste caso, isso é usado para instalar e configurar o Spark em cada nó de VM. Vamos examinar um trecho do modelo azuredeploy.json que usa esses recursos:

```json
{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat('mastervm', copyindex(), '/installsparkmaster')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineExtensionsLoopMaster",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'mastervm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
			],
			"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -d ', reference('nic0').ipConfigurations[0].properties.privateIPAddress,' -s ',variables('numberOfSlavesInstances'),' -m ', ' 1 ')]"
		}
	}
},
{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat('slavevm', copyindex(), '/installsparkslave')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineExtensionsLoopSlave",
		"count": "[variables('numberOfSlavesInstances')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'slavevm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
			],
			"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -m ', ' 0 ')]"
		}
	}
}
```

Observe que a extensão dos recursos de nó mestre e subordinado executa comandos diferentes, definidos na propriedade **commandToExecute** como parte do processo de provisionamento.

Se você examinar o trecho JSON da extensão mais recente de máquina virtual, poderá ver que esse recurso depende do recurso da máquina virtual e sua interface de rede. Isso indica que esses dois recursos já precisam ser implantados antes de provisionar e executar essa extensão de VM. Observe também o uso da função **copyindex()** para repetir esta etapa para cada máquina virtual escrava.

Familiarizando-se com os outros arquivos incluídos nessa implantação, você poderá compreender todos os detalhes e as práticas recomendadas necessárias para organizar e orquestrar estratégias de implantação complexas para soluções com vários nós, com base em qualquer tecnologia, utilizando modelos do Gerenciador de Recursos do Azure. Embora não seja obrigatório, uma abordagem recomendada é estruturar seus arquivos de modelos conforme realçado pelo seguinte diagrama:

![spark-template-structure](media/virtual-machines-spark-template/spark-template-structure.png)

Essencialmente, essa abordagem sugere o seguinte:

-	Definir o arquivo de modelo principal como um ponto central de orquestração para todas as atividades de implantação específicas, aproveitando a vinculação de modelos para invocar execuções de modelos de sub-rotina.
-	Crie um arquivo de modelo específico que implantará todos os recursos compartilhados entre todas as outras tarefas de implantação específicas (contas de armazenamento, configuração de rede virtual, etc.). Isso pode ser amplamente reutilizado entre implantações que tenham requisitos semelhantes em termos de infraestrutura comum.
-	Inclua modelos de recursos opcionais para requisitos de spot específicos de determinado recurso.
-	Para membros de um grupo de recursos idênticos (nós em um cluster etc.), crie modelos específicos que usam o loop de recursos para implantar várias instâncias com propriedades exclusivas.
-	Para todas as tarefas pós-implantação (instalação de produtos, configurações etc.), use extensões de implantação de scripts e crie scripts específicos para cada tecnologia.

Para obter mais informações, consulte [Linguagem de modelo do Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).

## Próximas etapas

Leia mais detalhes em [implantando um modelo](../resource-group-template-deploy.md).

Descubra mais [estruturas de aplicativo](virtual-machines-app-frameworks.md).

[Solucionar problemas de implantações de modelo](resource-group-deploy-debug.md).

<!---HONumber=July15_HO4-->