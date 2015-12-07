<properties
	pageTitle="DataStax no Ubuntu com um modelo do Gerenciador de Recursos | Microsoft Azure"
	description="Saiba como implantar facilmente um novo cluster DataStax em VMs do Ubuntu usando o Azure PowerShell ou a CLI do Azure e um modelo do Gerenciador de Recursos"
	services="virtual-machines"
	documentationCenter=""
	authors="scoriani"
	manager="timlt"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="scoriani"/>

# DataStax no Ubuntu com um modelo do Gerenciador de Recursos

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.


O DataStax é um dos líderes reconhecidos da indústria em desenvolvimento e entrega de soluções baseadas no Apache Cassandra – a tecnologia de banco de dados distribuído NoSQL, com suporte comercial e pronta para empresas que é amplamente reconhecida como ágil, sempre ativa e escalonável de forma previsível para qualquer tamanho. O DataStax oferece as opções Enterprise (DSE) e Community (DSC). Ele também fornece recursos como computação na memória, segurança de nível corporativo, análise integrada rápida e eficiente e pesquisa empresarial.

Além do que já está disponível no Azure Marketplace, agora você também pode implantar facilmente um novo cluster DataStax em VMs do Ubuntu usando um modelo do Gerenciador de Recursos implantado por meio do [Azure PowerShell](../powershell-install-configure.md) ou da [CLI do Azure](../xplat-cli-install.md).

Clusters recém-implantados com base nesse modelo terão a topologia descrita no diagrama a seguir, embora outras topologias possam ser facilmente obtidas personalizando-se o modelo apresentado neste artigo:

![cluster-architecture](media/virtual-machines-datastax-template/cluster-architecture.png)

Usando parâmetros, você pode definir o número de nós que serão implantados no novo cluster Apache Cassandra. Uma instância de serviço do DataStax Operation Center também será implantada em uma VM autônoma na mesma rede virtual, oferecendo a capacidade de monitorar o status do cluster e de todos os nós individuais, adicionar ou remover nós e executar todas as tarefas administrativas relacionadas ao cluster.

Depois de concluída a implantação, você poderá acessar a instância VM do Centro de Operações do DataStax usando o endereço DNS configurado. A VM OpsCenter tem a porta SSH 22 habilitada, bem como a porta 8443 para HTTPS. O endereço DNS para o centro de operações incluirá *dnsName* e *region* inseridos como parâmetros, resultando no formato `{dnsName}.{region}.cloudapp.azure.com`. Se você criou uma implantação com o parâmetro *dnsName* definido como “datastax” na região “Oeste dos EUA”, é possível acessar a VM do Centro de Operações do DataStax para a implantação em `https://datastax.westus.cloudapp.azure.com:8443`.

> [AZURE.NOTE]O certificado usado na implantação é um certificado autoassinado que criará um aviso no navegador. Você pode seguir o processo no site do [DataStax](http://www.datastax.com/) para substituir o certificado pelo seu próprio certificado SSL.

Antes de lidar com mais detalhes relacionados ao Gerenciador de Recursos do Azure e ao modelo usado para essa implantação, verifique se você tem o PowerShell ou a CLI do Azure configurados corretamente.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## Criar um cluster do Cassandra baseado no Datastax usando um modelo do Gerenciador de Recursos

Siga estas etapas para criar um cluster do Apache Cassandra, baseado no DataStax, usando um modelo do Gerenciador de Recursos do repositório de modelos do GitHub. Cada etapa inclui instruções para o PowerShell do Azure e a CLI do Azure.

### Etapa 1-a: baixar os arquivos de modelo usando o PowerShell do Azure

Crie uma pasta local para o modelo JSON e outros arquivos associados (por exemplo, C:\\Azure\\Templates\\DataStax).

Substitua o nome da pasta local e execute os seguintes comandos:

	$folderName="C:\Azure\Templates\DataStax"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/azuredeploy.json"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/azuredeploy-parameters.json"
	$filePath = $folderName + "\azuredeploy-parameters.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/dsenode.sh"
	$filePath = $folderName + "\dsenode.sh"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/ephemeral-nodes-resources.json"
	$filePath = $folderName + "\ephemeral-nodes-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/metadata.json"
	$filePath = $folderName + "\metadata.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter-install-resources.json"
	$filePath = $folderName + "\opscenter-install-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter-resources.json"
	$filePath = $folderName + "\opscenter-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter.sh"
	$filePath = $folderName + "\opscenter.sh"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/shared-resources.json"
	$filePath = $folderName + "shared-resources.json"
	$webclient.DownloadFile($url,$filePath)

### Etapa 1-b: baixar os arquivos de modelo usando a CLI do Azure

Clone todo o repositório de modelos usando um cliente Git de sua escolha, por exemplo:

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

Depois de concluída a clonagem, procure a pasta **datastax-on-ubuntu** no diretório C:\\Azure\\Templates.

### Etapa 2 (opcional): compreender os parâmetros do modelo

Ao implantar soluções não triviais, como um cluster do Apache Cassandra com base no DataStax, você deve especificar um conjunto de parâmetros de configuração para lidar com várias configurações necessárias. Ao declarar esses parâmetros na definição do modelo, é possível especificar valores durante a implantação por meio de um arquivo externo ou na linha de comando.

Na seção “parâmetros” no início do arquivo azuredeploy.json, você encontrará o conjunto de parâmetros necessários ao modelo para configurar um cluster DataStax. Veja um exemplo da seção "parâmetros" desse arquivo de modelo azuredeploy.json:

	"parameters": {
		"region": {
			"type": "string",
			"defaultValue": "West US",
			"metadata": {
				"Description": "Location where resources will be provisioned"
			}
		},
		"storageAccountPrefix": {
			"type": "string",
			"defaultValue": "uniqueStorageAccountName",
			"metadata": {
				"Description": "Unique namespace for the storage account where the virtual machine's disks will be placed"
			}
		},
		"dnsName": {
			"type": "string",
			"metadata" : {
				"Description": "DNS subname for the operations center public IP"
			}
		},
		"virtualNetworkName": {
			"type": "string",
			"defaultValue": "myvnet",
			"metadata": {
				"Description": "Name of the virtual network provisioned for the cluster"
			}
		},
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
		"opsCenterAdminPassword": {
			"type": "securestring",
			"metadata": {
				"Description": "Sets the operations center admin user password"
			}
		},
		"clusterVmSize": {
			"type": "string",
			"defaultValue": "Standard_D3",
			"allowedValues": [
				"Standard_D1",
				"Standard_D2",
				"Standard_D3",
				"Standard_D4",
				"Standard_D11",
				"Standard_D12",
				"Standard_D13",
				"Standard_D14"
			],
			"metadata": {
				"Description": "The size of the virtual machines used when provisioning cluster nodes"
			}
		},
		"clusterNodeCount": {
			"type": "int",
			"metadata": {
				"Description": "The number of nodes provisioned in the cluster"
			}
		},
		"clusterName": {
			"type": "string",
			"metadata": {
				"Description": "The name of the cluster provisioned"
			}
		}
	}

Cada parâmetro tem detalhes como tipo de dados e valores permitidos. Isso permite a validação dos parâmetros passados durante a execução do modelo em um modo interativo (por exemplo, PowerShell do Azure ou CLI do Azure), bem como uma interface de usuário de autodescoberta que pode ser criada dinamicamente ao analisar a lista de parâmetros necessários e suas descrições.

### Etapa 3-a: implantar um cluster DataStax usando um modelo por meio do Azure PowerShell

Prepare um arquivo de parâmetros para a sua implantação criando um arquivo JSON que contém os valores de tempo de execução para todos os parâmetros. Em seguida, esse arquivo será passado como uma única entidade para o comando de implantação. Se você não incluir um arquivo de parâmetros, o PowerShell do Azure usará qualquer valor padrão especificado no modelo e solicitará que você preencha os valores restantes.

Veja um exemplo de conjunto de parâmetros do arquivo azuredeploy parameters.json:

	{
		"storageAccountPrefix": {
			"value": "scorianisa"
		},
		"dnsName": {
			"value": "scorianids"
		},
		"virtualNetworkName": {
			"value": "datastax"
		},
		"adminUsername": {
			"value": "scoriani"
		},
		"adminPassword": {
			"value": ""
		},
		"region": {
			"value": "West US"
		},
		"opsCenterAdminPassword": {
			"value": ""
		},
		"clusterVmSize": {
			"value": "Standard_D3"
		},
		"clusterNodeCount": {
			"value": 3
		},
		"clusterName": {
			"value": "cl1"
		}
	}

Preencha o nome da implantação do Azure, o nome do grupo de recursos, o local do Azure e a pasta em que você salvou os arquivos de implantação JSON. Em seguida, execute estes comandos:

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\DataStax>"
	$templateFile= $folderName + "\azuredeploy.json"
	$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

	New-AzureResourceGroup –Name $RGName –Location $locName

	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

Quando você executar o comando **New-AzureResourceGroupDeployment**, isso extrairá valores de parâmetros do arquivo de parâmetros JSON e iniciará a execução do modelo de forma adequada. A definição e o uso de vários arquivos de parâmetros com seus diferentes ambientes (por exemplo, teste, produção etc.) promoverão a reutilização do modelo e simplificarão soluções complexas com vários ambientes.

Durante a implantação, tenha em mente de que será necessário criar uma nova conta de armazenamento do Azure. Portanto, o nome fornecido como o parâmetro de conta de armazenamento precisa ser exclusivo e atender a todos os requisitos para uma conta de armazenamento do Azure (somente letras minúsculas e números).

Durante e após a implantação, você pode verificar todas as solicitações feitas durante o provisionamento, incluindo quaisquer erros ocorridos.

Para fazê-lo, vá para o [portal de visualização do Azure](https://portal.azure.com) e faça o seguinte:

- Clique em **Procurar** na barra de navegação à esquerda, role para baixo e clique em **Grupos de Recursos**.  
- Clique no grupo de recursos que você acabou de criar, o que exibirá a folha “Grupo de Recursos”.  
- Ao clicar no gráfico de barras “Eventos” na parte **Monitoramento** da folha “Grupo de Recursos”, você poderá ver os eventos de sua implantação.
- Ao clicar em eventos individuais, você poderá fazer uma busca detalhada dos detalhes de cada operação feita em nome do modelo.

Depois dos testes, se você precisar remover esse grupo de recursos e todos os seus recursos (a conta de armazenamento, a máquina virtual e a rede virtual), use este comando:

	Remove-AzureResourceGroup –Name "<resource group name>" -Force

### Etapa 3-b: implantar um cluster DataStax usando um modelo por meio da CLI do Azure

Para implantar um cluster DataStax pela CLI do Azure, primeiro crie um grupo de recursos especificando um nome e um local:

	azure group create dsc "West US"

Passe o nome desse grupo de recursos, o local do arquivo de modelo JSON e o local do arquivo de parâmetros (consulte a seção acima sobre o PowerShell do Azure para saber mais) no comando abaixo:

	azure group deployment create dsc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

É possível verificar o status das implantações de recursos individuais usando o seguinte comando:

	azure group deployment list dsc

## Um tour pela estrutura do modelo do DataStax e da organização de arquivos

Para criar um modelo robusto e reutilizável do Gerenciador de Recursos, é preciso realizar preparação adicional para organizar a série de tarefas complexas e inter-relacionadas necessárias durante a implantação de uma solução complexa como o DataStax. Ao aproveitar os loops de recursos e a vinculação de modelos do Gerenciador de Recursos, além da execução de scripts por meio de extensões relacionadas, é possível implementar uma abordagem modular que pode ser reutilizada com praticamente qualquer implantação complexa baseada em modelo.

Esta seção apresenta a estrutura do arquivo azuredeploy.json para o cluster DataStax.

### Seção "parâmetros"

A seção "parâmetros" do arquivo azuredeploy.json especifica os parâmetros modificáveis que são usados nesse modelo. O arquivo azuredeploy-parameters.json mencionado anteriormente é usado para passar valores para a seção "parâmetros" de azuredeploy.json durante a execução do modelo.

### Seção "variáveis"

A seção "variáveis" especifica variáveis que podem ser usadas em todo esse modelo. Ela contém vários campos (fragmentos ou tipos de dados JSON) que serão definidos como constantes ou valores calculados em tempo de execução. Veja a seção “variáveis” desse modelo do DataStax:

	"variables": {
	"templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/datastax-on-ubuntu/",
	"sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
	"clusterNodesTemplateUrl": "[concat(variables('templateBaseUrl'), 'ephemeral-nodes-resources.json')]",
	"opsCenterTemplateUrl": "[concat(variables('templateBaseUrl'), 'opscenter-resources.json')]",
	"opsCenterInstallTemplateUrl": "[concat(variables('templateBaseUrl'), 'opscenter-install-resources.json')]",
	"opsCenterVmSize": "Standard_A1",
	"networkSettings": {
		"virtualNetworkName": "[parameters('virtualNetworkName')]",
		"addressPrefix": "10.0.0.0/16",
		"subnet": {
			"dse": {
				"name": "dse",
				"prefix": "10.0.0.0/24",
				"vnet": "[parameters('virtualNetworkName')]"
			}
		},
		"statics": {
			"clusterRange": {
				"base": "10.0.0.",
				"start": 5
			},
			"opsCenter": "10.0.0.240"
		}
	},
	"osSettings": {
		"imageReference": {
			"publisher": "Canonical",
			"offer": "UbuntuServer",
			"sku": "14.04.2-LTS",
			"version": "latest"
		},
		"scripts": [
			"[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
			"[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
			"https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
		]
	},
	"sharedStorageAccountName": "[concat(parameters('storageAccountPrefix'),'cmn')]",
	"nodeList": "[concat(variables('networkSettings').statics.clusterRange.base, variables('networkSettings').statics.clusterRange.start, '-', parameters('clusterNodeCount'))]"
	},

Fazendo uma busca detalhada neste exemplo, você pode ver as duas abordagens diferentes. No primeiro fragmento, a variável **osSettings** é um elemento JSON aninhado que contém quatro pares de chave-valor:

	"osSettings": {
	      "imageReference": {
	        "publisher": "Canonical",
	        "offer": "UbuntuServer",
	        "sku": "14.04.2-LTS",
	        "version": "latest"
	      },

	 
No segundo fragmento, a variável **scripts** é uma matriz JSON em que cada elemento será calculado no tempo de execução por meio de uma função de linguagem de modelo (concat) e do valor de outra variável, além de constantes da cadeia de caracteres:

	      "scripts": [
	        "[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
	        "[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
	        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
	      ]

### Seção "recursos"

A seção "recursos" é onde acontece a maior parte da ação. Analisando cuidadosamente essa seção, você pode identificar imediatamente dois casos diferentes. O primeiro é um elemento definido do tipo `Microsoft.Resources/deployments` que basicamente significa a invocação de uma implantação aninhada dentro do elemento principal. Por meio do elemento **templateLink** (e da propriedade de versão relacionada), é possível especificar um arquivo de modelo vinculado que será invocado pela transmissão de um conjunto de parâmetros como entrada, como visto neste fragmento:

	{
	      "name": "shared",
	      "type": "Microsoft.Resources/deployments",
	      "apiVersion": "2015-01-01",
	      "properties": {
	        "mode": "Incremental",
	        "templateLink": {
	          "uri": "[variables('sharedTemplateUrl')]",
	          "contentVersion": "1.0.0.0"
	        },
	        "parameters": {
	          "region": {
	            "value": "[parameters('region')]"
	          },
	          "networkSettings": {
	            "value": "[variables('networkSettings')]"
	          },
	          "storageAccountName": {
	            "value": "[variables('sharedStorageAccountName')]"
	          }
	        }
	      }
	    },

Neste primeiro exemplo, fica claro como azuredeploy.json nesse cenário foi organizado como um mecanismo de orquestração, invocando vários outros arquivos de modelo. Cada arquivo é responsável por parte das atividades de implantação necessárias.

Em particular, os seguintes modelos vinculados serão usados para essa implantação:

-	**shared-resource.json**: contém a definição de todos os recursos que serão compartilhados na implantação. Alguns exemplos são as contas de armazenamento usadas para armazenar as redes virtuais e os discos do SO de uma VM.
-	**opscenter-resources.json**: implanta uma VM do OpsCenter e todos os recursos relacionados, incluindo uma interface de rede e um endereço IP público.
-	**opscenter-install-resources.json**: implanta a extensão de VM do OpsCenter (script personalizado para Linux) que invocará o arquivo de script Bash específico (opscenter.sh) necessário para configurar o serviço do OpsCenter nessa VM.
-	**ephemeral-nodes-resources.json**: implanta todas as VMs de nó de cluster e os recursos conectados (placas de rede, IPs privados, etc.). Esse modelo também implantará extensões de VM (scripts personalizados para Linux) e invoca um script bash (dsenode.sh) para instalar fisicamente partes do Apache Cassandra em cada nó.

Vejamos detalhadamente como este último modelo é usado, pois ele é um dos mais interessantes em termos de desenvolvimento de modelo. Um conceito importante a ser realçado é como um único arquivo de modelo pode implantar várias cópias de um único tipo de recurso e, para cada instância, pode definir valores exclusivos para as configurações necessárias. Esse conceito é conhecido como **loop de recursos**.

Quando ephemeral-nodes-resources.json é invocado no arquivo principal azuredeploy.json, um parâmetro chamado *nodeCount* é fornecido como parte da lista de parâmetros. No modelo filho, *nodeCount* (o número de nós a serem implantados no cluster) será usado no elemento **copy** de cada recurso que precisa ser implantado em várias cópias, como realçado no fragmento a seguir. Para todas as configurações em que é necessário especificar valores exclusivos para diferentes instâncias do recurso implantado, a função **copyindex()** pode ser usada para obter um valor numérico que indica o índice atual nessa criação de loop de recursos específica. No seguinte fragmento, você pode ver esse conceito aplicado a várias VMs que estão sendo criadas para nós do cluster DataStax:

			   {
			      "apiVersion": "2015-05-01-preview",
			      "type": "Microsoft.Compute/virtualMachines",
			      "name": "[concat(parameters('namespace'), 'vm', copyindex())]",
			      "location": "[parameters('region')]",
			      "copy": {
			        "name": "[concat(parameters('namespace'), 'vmLoop')]",
			        "count": "[parameters('nodeCount')]"
			      },
			      "dependsOn": [
			        "[concat('Microsoft.Network/networkInterfaces/', parameters('namespace'), 'nic', copyindex())]",
			        "[concat('Microsoft.Compute/availabilitySets/', parameters('namespace'), 'set')]",
			        "[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]"
			      ],
			      "properties": {
			        "availabilitySet": {
			          "id": "[resourceId('Microsoft.Compute/availabilitySets', concat(parameters('namespace'), 'set'))]"
			        },
			        "hardwareProfile": {
			          "vmSize": "[parameters('vmSize')]"
			        },
			        "osProfile": {
			          "computername": "[concat(parameters('namespace'), 'vm', copyIndex())]",
			          "adminUsername": "[parameters('adminUsername')]",
			          "adminPassword": "[parameters('adminPassword')]"
			        },
			        "storageProfile": {
			          "imageReference": "[parameters('osSettings').imageReference]",
			          "osDisk": {
			            "name": "osdisk",
			            "vhd": {
			              "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/vhds/', variables('vmName'), copyindex(), '-osdisk.vhd')]"
			            },
			            "caching": "ReadWrite",
			            "createOption": "FromImage"
			          },
			          "dataDisks": [
			            {
			              "name": "datadisk1",
			              "diskSizeGB": 1023,
			              "lun": 0,
			              "vhd": {
			                "Uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/','vhds/', variables('vmName'), copyindex(), 'DataDisk1.vhd')]"
			              },
			              "caching": "None",
			              "createOption": "Empty"
			            }
			          ]
			        },
			        "networkProfile": {
			          "networkInterfaces": [
			            {
			              "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('namespace'), 'nic', copyindex()))]"
			            }
			          ]
			        }
			      }
			    },

Outro conceito importante na criação de recursos é a capacidade de especificar dependências e precedências entre recursos, como você pode ver na matriz JSON **dependsOn**. Nesse modelo específico, cada nó também terá um disco de 1 TB anexado (consulte “dataDisks”) que pode ser usado para hospedar backups e instantâneos da instância do Apache Cassandra.

Os discos anexados são formatados como parte das atividades de preparação do nó disparadas pela execução do arquivo de script dsenode.sh. A primeira linha desse script invoca outro script:

	bash vm-disk-utils-0.1.sh

O arquivo vm-disk-utils-0.1.sh faz parte da pasta **shared\_scripts\\ubuntu** no repositório azure-quickstart-templates do GitHub, e contém funções muito úteis para a montagem, formatação e distribuição de discos. Essas funções podem ser usadas em todos os modelos no repositório.

Outro fragmento interessante para explorar é aquele relacionado às extensões de VM CustomScriptForLinux. Elas são instaladas como um tipo de recurso separado, com uma dependência em cada nó de cluster (e a instância OpsCenter). Elas usam o mesmo mecanismo de loop de recursos descrito para máquinas virtuais:

	{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat(parameters('namespace'), 'vm', copyindex(), '/installdsenode')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "[concat(parameters('namespace'), 'vmLoop')]",
		"count": "[parameters('nodeCount')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', parameters('namespace'), 'vm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', parameters('namespace'), 'nic', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": "[parameters('osSettings').scripts]",
			"commandToExecute": "bash dsenode.sh"
		}
	}
	}

Ao se familiarizar com os outros arquivos incluídos nessa implantação, você poderá compreender todos os detalhes e as práticas recomendadas necessárias para organizar e orquestrar estratégias de implantação complexas para soluções com vários nós, baseadas em qualquer tecnologia, utilizando os modelos do Gerenciador de Recursos do Azure. Embora não seja obrigatório, uma abordagem recomendada é estruturar seus arquivos de modelos conforme realçado pelo seguinte diagrama:

![datastax-template-structure](media/virtual-machines-datastax-template/datastax-template-structure.png)

Essencialmente, essa abordagem sugere o seguinte:

-	Definir o arquivo de modelo principal como um ponto central de orquestração para todas as atividades de implantação específicas, aproveitando a vinculação de modelos para invocar execuções de modelos de sub-rotina.
-	Crie um arquivo de modelo específico que implantará todos os recursos compartilhados entre todas as outras tarefas de implantação específicas (contas de armazenamento, configuração de rede virtual, etc.). Isso pode ser amplamente reutilizado entre implantações que tenham requisitos semelhantes em termos de infraestrutura comum.
-	Inclua modelos de recursos opcionais para requisitos de spot específicos de determinado recurso.
-	Para membros de um grupo de recursos idênticos (nós em um cluster etc.), crie modelos específicos que usam o loop de recursos para implantar várias instâncias com propriedades exclusivas.
-	Para todas as tarefas pós-implantação (instalação de produtos, configurações etc.), use extensões de implantação de scripts e crie scripts específicos para cada tecnologia.

Para obter mais informações, consulte [Linguagem de modelo do Gerenciador de Recursos do Azure](../resource-group-authoring-templates.md).

<!---HONumber=AcomDC_1125_2015-->