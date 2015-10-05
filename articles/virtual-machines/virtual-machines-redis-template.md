<properties
	pageTitle="Modelo do Gerenciador de Recursos do Cluster Redis"
	description="Saiba como implantar com facilidade um novo Cluster Redis em VMs do Ubuntu usando o PowerShell ou a CLI do Azure e um modelo do Gerenciador de Recursos"
	services="virtual-machines"
	documentationCenter=""
	authors="timwieman"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2015"
	ms.author="twieman"/>

# Cluster Redis com um modelo do Gerenciador de Recursos

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Este artigo aborda como criar um cluster Redis com o modelo de implantação do Gerenciador de Recursos.

O Redis é um cache e repositório de chave-valor de código aberto, onde as chaves podem conter estruturas de dados como, por exemplo, cadeias de caracteres, hashes, listas, conjuntos e conjuntos classificados. O Redis dá suporte a um conjunto de operações atômicas nesses tipos de dados. Com o lançamento da versão 3.0 do Redis, o Cluster Redis agora está disponível na versão mais recente e estável do Redis. O Cluster Redis é uma implementação distribuída do Redis na qual os dados são fragmentados automaticamente em vários nós do Redis, com a capacidade de continuar as operações quando um subconjunto de nós apresentar falhas.

O Cache Redis do Microsoft Azure é um serviço de cache dedicado ao Redis gerenciado pela Microsoft, mas nem todos os clientes do Microsoft Azure desejam usar o Cache Redis do Azure. Alguns desejam manter seu Cache Redis por trás de uma sub-rede dentro de suas próprias implantações do Azure, enquanto outros preferirão hospedar seus próprios servidores Redis em máquinas virtuais do Linux para aproveitar por completo todos os recursos do Redis.

Este tutorial apresenta orientações sobre como usar um modelo de exemplo do Gerenciador de Recursos doAzure para implantar um Cluster Redis em VMs do Ubuntu em uma sub-rede em um Grupo de Recursos no Microsoft Azure. Além do Cluster Redis 3.0, esse modelo também oferece suporte à implantação do Redis 2.8 com o Redis Sentinel. Observe que este tutorial se concentrará na implementação do Cluster Redis 3.0.

O Cluster Redis é criado por trás de uma sub-rede e, portanto, não há acesso de IPs públicos ao Cluster Redis. Como parte da implantação, uma "jump box" opcional pode ser implantada. Essa “jump box” é uma VM do Ubuntu também implantada na sub-rede, mas que *expõe* um endereço IP público com uma porta SSH aberta em que você pode executar SSH. Em seguida, da “jump box”, é possível executar SSH para todas as VMs Redis da sub-rede.

Este modelo utiliza um conceito de "tamanho de camiseta" para especificar uma configuração "Pequena", "Média" ou "Grande" do Cluster Redis. Quando o idioma do modelo do Gerenciador de Recursos do Azure dá suporte a um dimensionamento de modelo mais dinâmico, isso poderia ser alterado para especificar o número de nós mestres do Cluster Redis, além dos nós subordinados, do tamanho da VM etc. Por enquanto, você pode ver o tamanho da VM e o número de mestres e subordinados definidos no arquivo azuredeploy.json nas variáveis `tshirtSizeSmall`, `tshirtSizeMedium` e `tshirtSizeLarge`.

O modelo do Cluster Redis para o tamanho de camiseta "médio" cria esta configuração:

![cluster-architecture](media/virtual-machines-redis-template/cluster-architecture.png)

Antes de lidar com mais detalhes relacionados ao Gerenciador de Recursos do Azure e ao modelo usado para essa implantação, verifique se você tem o PowerShell ou a CLI do Azure configurados corretamente.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## Implantar um Cluster Redis usando um modelo do Gerenciador de Recursos

Siga estas etapas para criar o Cluster Redis usando um modelo do Gerenciador de Recursos no repositório de modelos do GitHub. Cada etapa inclui instruções para o PowerShell do Azure e a CLI do Azure.

### Etapa 1-a: baixar os arquivos de modelo usando o PowerShell do Azure

Crie uma pasta local para o modelo JSON e outros arquivos associados (por exemplo, C:\\Azure\\Templates\\RedisCluster).

Substitua o nome da pasta local e execute os seguintes comandos:

```powershell
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy-parameters.json"
$filePath = $folderName + "\azuredeploy-parameters.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/empty-resources.json"
$filePath = $folderName + "\empty-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/jumpbox-resources.json"
$filePath = $folderName + "\jumpbox-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/node-resources.json"
$filePath = $folderName + "\node-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-cluster-install.sh"
$filePath = $folderName + "\redis-cluster-install.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-cluster-setup.sh"
$filePath = $folderName + "\redis-cluster-setup.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-sentinel-startup.sh"
$filePath = $folderName + "\redis-sentinel-startup.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/shared-resources.json"
$filePath = $folderName + "\shared-resources.json"
$webclient.DownloadFile($url,$filePath)
```

### Etapa 1-b: baixar os arquivos de modelo usando a CLI do Azure

Clone todo o repositório de modelos usando um cliente Git de sua escolha, por exemplo:

```
git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates
```

Depois de concluída a clonagem, procure a pasta **redis-high-availability** no diretório C:\\Azure\\Templates.

### Etapa 2 (opcional): compreender os parâmetros do modelo

Ao criar um Cluster Redis com um modelo, você deve especificar um conjunto de parâmetros de configuração. Para ver os parâmetros que você precisa especificar para o modelo em um arquivo JSON local antes de executar o comando para criar o Cluster Redis, abra o arquivo JSON em um editor de texto ou ferramenta de sua escolha.

Procure a seção "parâmetros" na parte superior do arquivo, que lista o conjunto de parâmetros necessários ao modelo para configurar o Cluster Redis. Aqui está a seção "parâmetros" do modelo azuredeploy.json:

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
	"storageAccountName": {
		"type": "string",
		"defaultValue": "",
		"metadata": {
			"Description": "Unique namespace for the Storage account where the virtual machine's disks will be placed"
		}
	},
	"location": {
		"type": "string",
		"metadata": {
			"Description": "Location where resources will be provisioned"
		}
	},
	"virtualNetworkName": {
		"type": "string",
		"defaultValue": "redisVirtNet",
		"metadata": {
			"Description": "The arbitrary name of the virtual network provisioned for the Redis cluster"
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
		"defaultValue": "redisSubnet1",
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
	"nodeAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.1",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"jumpbox": {
		"type": "string",
		"defaultValue": "Disabled",
		"allowedValues": [
			"Enabled",
			"Disabled"
		],
		"metadata": {
			"Description": "The flag allowing to enable or disable provisioning of the jump-box VM that can be used to access the Redis nodes"
		}
	},
	"tshirtSize": {
		"type": "string",
		"defaultValue": "Small",
		"allowedValues": [
			"Small",
			"Medium",
			"Large"
		],
		"metadata": {
			"Description": "T-shirt size of the Redis deployment"
		}
	},
	"redisVersion": {
		"type": "string",
		"defaultValue": "stable",
		"metadata": {
			"Description": "The version of the Redis package to be deployed on the cluster (or use 'stable' to pull in the latest and greatest)"
		}
	},
	"redisClusterName": {
		"type": "string",
		"defaultValue": "redis-cluster",
		"metadata": {
			"Description": "The arbitrary name of the Redis cluster"
		}
	}
},
```

Cada parâmetro tem detalhes como tipo de dados e valores permitidos. Isso permite a validação dos parâmetros passados durante a execução do modelo em um modo interativo (por exemplo, PowerShell do Azure ou CLI do Azure), bem como uma interface de usuário de autodescoberta que pode ser criada dinamicamente ao analisar a lista de parâmetros necessários e suas descrições.

### Etapa 3-a: Implantar um Cluster Redis usando um modelo por meio do PowerShell do Azure

Prepare um arquivo de parâmetros para a sua implantação criando um arquivo JSON que contém os valores de tempo de execução para todos os parâmetros. Em seguida, esse arquivo será passado como uma única entidade para o comando de implantação. Se você não incluir um arquivo de parâmetros, o PowerShell do Azure usará qualquer valor padrão especificado no modelo e solicitará que você preencha os valores restantes.

A seguir, um exemplo que você pode encontrar no arquivo azuredeploy-parameters.json. Observe que você precisa fornecer valores válidos para os parâmetros `storageAccountName`, `adminUsername` e `adminPassword`, além de qualquer personalização para os outros parâmetros:

```json
{
	"storageAccountName": {
		"value": "redisdeploy1"
	},
	"adminUsername": {
		"value": ""
	},
	"adminPassword": {
		"value": ""
	},
	"location": {
		"value": "West US"
	},
	"virtualNetworkName": {
		"value": "redisClustVnet"
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
	"nodeAddressPrefix": {
		"value": "10.0.0.1"
	},
	"redisVersion": {
		"value": "3.0.0"
	},
	"redisClusterName": {
		"value": "redis-arm-cluster"
	},
	"jumpbox": {
		"value": "Enabled"
	},
	"tshirtSize":  {
		"value": "Small"
	}
}
```

>[AZURE.NOTE]O parâmetro `storageAccountName` deve ser um nome exclusivo e inexistente de uma conta de Armazenamento que atenda aos requisitos de nomenclatura de uma conta do Armazenamento do Microsoft Azure (somente letras minúsculas e números). Essa conta de Armazenamento será criada como parte do processo de implantação.

Preencha o nome da implantação do Azure, o nome do grupo de recursos, o local do Azure e a pasta dos arquivos JSON salvos. Em seguida, execute estes comandos:

```powershell
$deployName="<deployment name>, such as TestDeployment"
$RGName="<resource group name>, such as TestRG"
$locName="<Azure location, such as West US>"
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$templateFile= $folderName + "\azuredeploy.json"
$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

New-AzureResourceGroup –Name $RGName –Location $locName

New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
```

>[AZURE.NOTE]`$RGName` deve ser exclusivo dentro de sua assinatura.

Quando você executar o comando **New-AzureResourceGroupDeployment**, isso extrairá valores de parâmetros do arquivo JSON (azuredeploy-parameters.json) e iniciará a execução do modelo de forma adequada. A definição e o uso de vários arquivos de parâmetros com seus diferentes ambientes (por exemplo, teste, produção etc.) promoverão a reutilização do modelo e simplificarão soluções complexas com vários ambientes.

Ao implantar, tenha em mente que uma nova conta de Armazenamento do Azure precisará ser criada. Portanto, o nome fornecido como o parâmetro de conta de Armazenamento precisa ser exclusivo e atender a todos os requisitos para uma conta de Armazenamento do Azure (somente letras minúsculas e números).

Durante a implantação, você verá algo assim:

	PS C:\> New-AzureResourceGroup –Name $RGName –Location $locName

	ResourceGroupName : TestRG
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

	ResourceId        : /subscriptions/1234abc1-abc1-1234-12a1-ab1ab12345ab/resourceGroups/TestRG

	PS C:\> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
	VERBOSE: 2:39:10 PM - Template is valid.
	VERBOSE: 2:39:14 PM - Create template deployment 'TestDeployment'.
	VERBOSE: 2:39:25 PM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is running
	VERBOSE: 2:40:04 PM - Resource Microsoft.Resources/deployments 'node-resources0' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'node-resources1' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is succeeded
	VERBOSE: 2:42:23 PM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is succeeded
	VERBOSE: 2:47:44 PM - Resource Microsoft.Resources/deployments 'node-resources1' provisioning status is succeeded
	VERBOSE: 2:47:57 PM - Resource Microsoft.Resources/deployments 'node-resources0' provisioning status is succeeded
	VERBOSE: 2:48:01 PM - Resource Microsoft.Resources/deployments 'lastnode-resources' provisioning status is running
	VERBOSE: 2:58:24 PM - Resource Microsoft.Resources/deployments 'lastnode-resources' provisioning status is succeeded

	DeploymentName    : TestDeployment
	ResourceGroupName : TestRG
	ProvisioningState : Succeeded
	Timestamp         : 4/28/2015 7:58:23 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     myadmin
                    adminPassword    SecureString
                    storageAccountName  String                     myuniqstgacct87
                    location         String                     West US
                    virtualNetworkName  String                     redisClustVnet
                    addressPrefix    String                     10.0.0.0/16
                    subnetName       String                     Subnet1
                    subnetPrefix     String                     10.0.0.0/24
                    nodeAddressPrefix  String                     10.0.0.1
                    jumpbox          String                     Enabled
                    tshirtSize       String                     Small
                    redisVersion     String                     3.0.0
                    redisClusterName  String                     redis-arm-cluster

	Outputs           :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    installCommand   String                     bash redis-cluster-install.sh -n redis-arm-cluster -v
                    3.0.0 -c 3 -m 3 -s 0 -p 10.0.0.1
                    setupCommand     String                     bash redis-cluster-install.sh -n redis-arm-cluster -v
                    3.0.0 -c 3 -m 3 -s 0 -p 10.0.0.1 -l
	...

Durante e após a implantação, você pode verificar todas as solicitações feitas durante o provisionamento, incluindo quaisquer erros ocorridos.

Para fazer isso, acesse o [Portal do Azure](https://portal.azure.com) e faça o seguinte:

- Na barra de navegação à esquerda, clique em **Procurar**, role para baixo e clique em **Grupos de Recursos**.
- Selecione o grupo de recursos que você acabou de criar, o que exibirá a folha "Grupo de Recursos".
- Na seção **Monitoramento**, selecione o gráfico de barras “Eventos”. Isso exibirá os eventos para sua implantação.
- Ao clicar em eventos individuais, você poderá fazer uma busca detalhada dos detalhes de cada operação feita em nome do modelo.

Se precisar remover esse grupo de recursos e todos os seus recursos (a conta de Armazenamento, a máquina virtual e a rede virtual) após os testes, use este comando:

```powershell
Remove-AzureResourceGroup –Name "<resource group name>"
```

### Etapa 3-b: Implantar um Cluster Redis usando um modelo por meio da CLI do Azure

Para implantar um Cluster Redis pela CLI do Azure, crie um grupo de recursos, especificando um nome e um local:

```powershell
azure group create TestRG "West US"
```

Passe o nome desse grupo de recursos, o local do arquivo de modelo JSON e o local do arquivo de parâmetros (consulte a seção acima sobre o PowerShell do Azure para saber mais) no comando abaixo:

```powershell
azure group deployment create TestRG -f .\azuredeploy.json -e .\azuredeploy-parameters.json
```

Você pode verificar o status das implantações de recursos individuais usando o seguinte comando:

```powershell
azure group deployment list TestRG
```

## Um tour da estrutura do modelo de Cluster Redis e da organização de arquivos

Para criar uma abordagem robusta e reutilizável para projetar modelos do Gerenciador de Recursos, é preciso preparação adicional para organizar a série de tarefas complexas e inter-relacionadas necessárias durante a implantação de uma solução complexa como o Cluster Redis. Ao utilizar os recursos de vinculação de modelos e loops de recursos do Gerenciador de Recursos e a execução de scripts por meio de extensões relacionadas, é possível implementar uma abordagem modular que pode ser reutilizada com praticamente qualquer implantação complexa com base em modelo.

Este diagrama descreve as relações entre todos os arquivos baixados do GitHub para esta implantação:

![redis-files](media/virtual-machines-redis-template/redis-files.png)

Esta seção orienta você pela estrutura do modelo azuredeploy.json para o Cluster Redis.

Se você não baixar uma cópia do arquivo de modelo, designe uma pasta local como o local para o arquivo e crie-o (por exemplo, C:\\Azure\\Templates\\RedisCluster). Preencha o nome da pasta e execute estes comandos:

```powershell
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)
```

Abra o modelo azuredeploy.json em um editor de texto ou na ferramenta de sua escolha. As informações a seguir descrevem a estrutura do arquivo de modelo e a finalidade de cada seção. Como alternativa, você pode ver o conteúdo do modelo em seu navegador clicando [aqui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json).

### Seção "parâmetros"

Já mencionamos a função do arquivo azuredeploy-parameters.json, que será usado para passar determinado conjunto de valores de parâmetros durante a execução do modelo. A seção "parâmetros" do arquivo azuredeploy.json especifica os parâmetros que são usados para inserir dados nesse modelo.

Veja a seguir um exemplo de um parâmetro para “tamanho de camiseta”:

```json
"tshirtSize": {
	"type": "string",
	"defaultValue": "Small",
	"allowedValues": [
		"Small",
		"Medium",
		"Large"
	],
	"metadata": {
		"Description": "T-shirt size of the Redis deployment"
	}
},
```

>[AZURE.NOTE]É importante lembrar que `defaultValue` pode ser especificado, bem como `allowedValues`.

### Seção "variáveis"

A seção "variáveis" especifica variáveis que podem ser usadas em todo esse modelo. Basicamente, ela contém vários campos (fragmentos ou tipos de dados JSON) que serão definidos como constantes ou valores calculados em tempo de execução. Veja a seguir alguns exemplos simples e mais complexos:

```json
"vmStorageAccountContainerName": "vhd-redis",
"vmStorageAccountDomain": ".blob.core.windows.net",
"vnetID": "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
...
"machineSettings": {
	"adminUsername": "[parameters('adminUsername')]",
	"machineNamePrefix": "redisnode-",
	"osImageReference": {
		"publisher": "[variables('osFamilyUbuntu').imagePublisher]",
		"offer": "[variables('osFamilyUbuntu').imageOffer]",
		"sku": "[variables('osFamilyUbuntu').imageSKU]",
		"version": "latest"
	}
},
...
"vmScripts": {
	"scriptsToDownload": [
		"[concat(variables('scriptUrl'), 'redis-cluster-install.sh')]",
		"[concat(variables('scriptUrl'), 'redis-cluster-setup.sh')]",
		"[concat(variables('scriptUrl'), 'redis-sentinel-startup.sh')]"
	],
	"installCommand": "[concat('bash ', variables('installCommand'))]",
	"setupCommand": "[concat('bash ', variables('installCommand'), ' -l')]"
}
```

As variáveis `vmStorageAccountContainerName` e `vmStorageAccountDomain` são exemplos de variáveis de nome/valor simples. `vnetID` é um exemplo de uma variável que é calculada no tempo de execução com as funções `resourceId` e `parameters`. `machineSettings` aprimora esses conceitos ainda mais aninhando o objeto JSON `osImageReference` na variável `machineSettings`. `vmScripts` contém uma matriz JSON, `scriptsToDownload`, que é calculada no tempo de execução com as funções `concat` e `variables`.

Se desejar personalizar o tamanho da implantação do Cluster Redis, é possível alterar as propriedades das variáveis `tshirtSizeSmall`, `tshirtSizeMedium` e `tshirtSizeLarge` no modelo azuredeploy.json.

```json
"tshirtSizeSmall": {
	"vmSizeMember": "Standard_A1",
	"numberOfMasters": 3,
	"numberOfSlaves": 0,
	"totalMemberCount": 3,
	"totalMemberCountExcludingLast": 2,
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
"tshirtSizeMedium": {
	"vmSizeMember": "Standard_A2",
	"numberOfMasters": 3,
	"numberOfSlaves": 3,
	"totalMemberCount": 6,
	"totalMemberCountExcludingLast": 5,
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
"tshirtSizeLarge": {
	"vmSizeMember": "Standard_A5",
	"numberOfMasters": 3,
	"numberOfSlaves": 6,
	"totalMemberCount": 9,
	"totalMemberCountExcludingLast": 8,
	"arbiter": "Enabled",
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
```

Observação: as propriedades `totalMemberCountExcludingLast` e `totalMemberCount` são necessárias, pois o idioma do modelo atualmente não tem operações de “matemática”.

Para saber mais sobre o idioma do modelo, consulte a MSDN em [Idioma do modelo do Gerenciador de Recursos do Azure](../resource-group-authoring-templates.md).

### Seção "recursos"

A seção "recursos" é onde acontece a maior parte da ação. Analisando cuidadosamente essa seção, você pode identificar imediatamente dois casos diferentes. O primeiro é um elemento definido do tipo `Microsoft.Resources/deployments` que basicamente invoca uma implantação aninhada dentro do elemento principal. O segundo é a propriedade `templateLink` (e a propriedade `contentVersion` relacionada), que torna possível a especificação de um arquivo de modelo vinculado que será invocado, passando um conjunto de parâmetros como entrada. Eles podem ser vistos neste fragmento do modelo:

```json
{
    "name": "shared-resources",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "properties": {
        "mode": "Incremental",
        "templateLink": {
            "uri": "[variables('sharedTemplateUrl')]",
            "contentVersion": "1.0.0.0"
        },
        "parameters": {
            "commonSettings": {
                "value": "[variables('commonSettings')]"
            },
            "storageSettings": {
                "value": "[variables('storageSettings')]"
            },
            "networkSettings": {
                "value": "[variables('networkSettings')]"
            }
        }
    }
},
```

Neste primeiro exemplo, fica claro como azuredeploy.json nesse cenário foi organizado como um mecanismo de orquestração, invocando vários outros arquivos de modelo. Cada arquivo é responsável por parte das atividades de implantação necessárias.

Em particular, os seguintes modelos vinculados serão usados para essa implantação:

- **shared-resource.json**: contém a definição de todos os recursos que serão compartilhados na implantação. Os exemplos são contas de Armazenamento usadas para armazenar os discos do sistema operacional da VM, redes virtuais e conjuntos de disponibilidade.
- **jumpbox-resources.json**: implanta a VM "jump box" e todos os recursos relacionados, como a interface de rede, o endereço IP público e o ponto de extremidade de entrada usados para executar SSH no ambiente.
- **nodes-resources.json**: implanta todas as VMs de nós do Cluster Redis e os recursos conectados (adaptadores de rede, IPs privados, etc). Este modelo também implanta extensões de VM (scripts personalizados para Linux) e invoca um script bash para instalar fisicamente e configurar o Redis em cada nó. O script a ser invocado é transmitido para esse modelo no parâmetro `machineSettings` da propriedade `commandToExecute`. Todos os nós do Cluster Redis, exceto um, podem ser implantados e programados em paralelo. Um nó deve ser reservado até o final porque a configuração do Cluster Redis só pode ser executada em um nó, e isso deve ser feito depois que todos os nós estiverem executando o servidor Redis. Isso ocorre porque o script de execução é passado para esse modelo; o último nó precisa executar um script ligeiramente diferente que não apenas instalará o servidor Redis, mas também configurará o Cluster Redis.

Vamos detalhar *como* esse último modelo, o node-resources.json é usado, pois ele é um dos mais interessantes em termos de desenvolvimento de modelo. Um conceito importante a ser realçado é como um único arquivo de modelo pode implantar várias cópias de um único tipo de recurso e, para cada instância, pode definir valores exclusivos para as configurações necessárias. Esse conceito é conhecido como **loop de recursos**.

Quando node-resources.json é invocado no arquivo principal azuredeploy.json, ele é invocado de dentro de um recurso que usa o elemento `copy` para criar um loop de tipos. Um recurso que usa o elemento `copy` criará cópias de si mesmo de acordo com o número de vezes especificado no parâmetro `count` do elemento `copy`. Para todas as configurações em que é necessário especificar valores exclusivos entre diferentes instâncias do recurso implantado, a função **copyindex()** pode ser usada para obter um valor numérico que indica o índice atual nessa criação de loop de recursos específica. No seguinte fragmento do azuredeploy.json, você pode ver esse conceito aplicado a várias VMs que estão sendo criadas para nós do Cluster Redis:

```json
{
    "type": "Microsoft.Resources/deployments",
    "name": "[concat('node-resources', copyindex())]",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
    ],
    "copy": {
        "name": "memberNodesLoop",
        "count": "[variables('clusterSpec').totalMemberCountExcludingLast]"
    },
    "properties": {
        "mode": "Incremental",
        "templateLink": {
            "uri": "[variables('clusterSpec').vmTemplate]",
            "contentVersion": "1.0.0.0"
        },
        "parameters": {
            "commonSettings": {
                "value": "[variables('commonSettings')]"
            },
            "storageSettings": {
                "value": "[variables('storageSettings')]"
            },
            "networkSettings": {
                "value": "[variables('networkSettings')]"
            },
            "machineSettings": {
                "value": {
                    "adminUsername": "[variables('machineSettings').adminUsername]",
                    "machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
                    "osImageReference": "[variables('machineSettings').osImageReference]",
                    "vmSize": "[variables('clusterSpec').vmSizeMember]",
                    "machineIndex": "[copyindex()]",
                    "vmScripts": "[variables('vmScripts').scriptsToDownload]",
                    "commandToExecute": "[concat(variables('vmScripts').installCommand, ' -i ', copyindex())]"
                }
            },
            "adminPassword": {
                "value": "[parameters('adminPassword')]"
            }
        }
    }
},
```

Outro conceito importante na criação de recursos é a capacidade de especificar dependências e precedências entre recursos, como você pode observar na matriz JSON `dependsOn`. Neste modelo específico, você pode ver que os nós do Cluster Redis dependem dos recursos compartilhados que estão sendo criados pela primeira vez.

Como mencionado anteriormente, o último nó precisa aguardar até que todos os outros nós do Cluster Redis tenham sido provisionados com o servidor Redis em execução. Isso é feito no azuredeploy.json com um recurso chamado `lastnode-resources` que depende do loop `copy` chamado `memberNodesLoop` do trecho do modelo acima. Depois de concluído o provisionamento do `memberNodesLoop`, o `lastnode-resources` poderá ser provisionado:

```json
{
	"name": "lastnode-resources",
	"type": "Microsoft.Resources/deployments",
	"apiVersion": "2015-01-01",
	"dependsOn": [
		"memberNodesLoop"
	],
	"properties": {
		"mode": "Incremental",
		"templateLink": {
			"uri": "[variables('clusterSpec').vmTemplate]",
			"contentVersion": "1.0.0.0"
		},
		"parameters": {
			"commonSettings": {
				"value": "[variables('commonSettings')]"
			},
			"storageSettings": {
				"value": "[variables('storageSettings')]"
			},
			"networkSettings": {
				"value": "[variables('networkSettings')]"
			},
			"machineSettings": {
				"value": {
					"adminUsername": "[variables('machineSettings').adminUsername]",
					"machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
					"osImageReference": "[variables('machineSettings').osImageReference]",
					"vmSize": "[variables('clusterSpec').vmSizeMember]",
					"machineIndex": "[variables('clusterSpec').totalMemberCountExcludingLast]",
					"vmScripts": "[variables('vmScripts').scriptsToDownload]",
					"commandToExecute": "[concat(variables('vmScripts').setupCommand, ' -i ', variables('clusterSpec').totalMemberCountExcludingLast)]"
				}
			},
			"adminPassword": {
				"value": "[parameters('adminPassword')]"
			}
		}
	}
}
```

Observe como o recurso `lastnode-resources` passa um `machineSettings.commandToExecute` ligeiramente diferente ao modelo vinculado. Isso ocorre porque, para o último nó, além do servidor Redis instalado, ele precisa chamar um script para configurar o Cluster Redis (que deve ser feito apenas uma vez depois que todos os servidores Redis estiverem em execução).

Outro fragmento interessante a explorar é o relacionado às extensões de VM do `CustomScriptForLinux`. Elas são instaladas como um tipo de recurso separado, com uma dependência em cada nó do cluster. Neste caso, isso é usado para instalar e configurar o Redis em cada nó de VM. Vamos examinar um trecho do modelo node-resources.json que usa esses recursos:

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat('vmMember', parameters('machineSettings').machineIndex, '/installscript')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('commonSettings').location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', 'vmMember', parameters('machineSettings').machineIndex)]"
    ],
    "properties": {
        "publisher": "Microsoft.OSTCExtensions",
        "type": "CustomScriptForLinux",
        "typeHandlerVersion": "1.2",
        "settings": {
            "fileUris": "[parameters('machineSettings').vmScripts]",
            "commandToExecute": "[parameters('machineSettings').commandToExecute]"
        }
    }
}
```

É possível ver que este recurso depende da VM de recurso que já está sendo implantada (`Microsoft.Compute/virtualMachines/vmMember<X>`, em que `<X>` é o parâmetro `machineSettings.machineIndex`, que é o índice da VM que foi transmitido para este script usando a função **copyindex()**).

Familiarizando-se com os outros arquivos incluídos nessa implantação, você poderá compreender todos os detalhes e as práticas recomendadas necessárias para organizar e orquestrar estratégias de implantação complexas para soluções com vários nós, com base em qualquer tecnologia, utilizando modelos do Gerenciador de Recursos do Azure. Embora não seja obrigatório, uma abordagem recomendada é estruturar seus arquivos de modelos conforme realçado pelo seguinte diagrama:

![redis-template-structure](media/virtual-machines-redis-template/redis-template-structure.png)

Essencialmente, essa abordagem sugere o seguinte:

- Definir o arquivo de modelo principal como um ponto central de orquestração para todas as atividades de implantação específicas, aproveitando a vinculação de modelos para invocar execuções de modelos de sub-rotina.
- Crie um arquivo de modelo específico que implantará todos os recursos compartilhados entre todas as outras tarefas de implantação específicas (por exemplo, contas de armazenamento, configuração de rede virtual etc.). Isso pode ser amplamente reutilizado entre implantações que tenham requisitos semelhantes em termos de infraestrutura comum.
- Inclua modelos de recursos opcionais para requisitos de spot específicos de determinado recurso.
- Para membros de um grupo de recursos idênticos (nós em um cluster etc.), crie modelos específicos que usam o loop de recursos para implantar várias instâncias com propriedades exclusivas.
- Para todas as tarefas pós-implantação (instalação de produtos, configurações etc.), use extensões de implantação de scripts e crie scripts específicos para cada tecnologia.

Para obter mais informações, consulte [Linguagem de modelo do Gerenciador de Recursos do Azure](../resource-group-authoring-templates.md).

<!---HONumber=Sept15_HO4-->