<properties
	pageTitle="DataStax no modelo do Gerenciador de Recursos do Ubuntu"
	description="Aprenda a implantar facilmente um novo cluster do DataStax em VMs do Ubuntu usando o PowerShell ou a CLI do Azure e um modelo do Gerenciador de Recursos"
	services="multiple"
	documentationCenter=""
	authors="karthmut"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="karthmut"/>

# DataStax no modelo do Gerenciador de Recursos do Ubuntu

O DataStax é um dos líderes reconhecidos do setor para o desenvolvimento e fornecimento de soluções com base em Apache Cassandra™, com suporte comercial e pronto para empresas, a tecnologia de banco de dados distribuído NoSQL de software livre amplamente reconhecida como ágil, sempre ativa e escalonável de forma previsível para qualquer porte. O DataStax está disponível como os tipos Enterprise \(DSE\) e Community \(DSC\), fornecendo recursos como computação na memória, segurança de nível corporativo, análise integrada rápida e eficiente e pesquisa empresarial.

Além do que já estava disponível no Azure Marketplace, agora você também pode implantar facilmente um novo cluster do DataStax em VMs do Ubuntu usando o PowerShell ou a CLI do Azure e um modelo do Gerenciador de Recursos.

Clusters recém-implantados com base nesse modelo terão a topologia descrita no diagrama a seguir, embora outras topologias possam ser facilmente obtidas personalizando-se a abordagem apresentada:

![cluster-architecture](media/virtual-machines-datastax-template/cluster-architecture.png)

Basicamente, por meio de um parâmetro, você pode definir o número de nós que serão implantados no novo cluster do Apache Cassandra e, além disso, uma instância de serviço do DataStax Operation Center também será implantada em uma VM autônoma na mesma VNET, oferecendo a capacidade de monitorar o status do cluster e de todos os nós individuais, adicionar ou remover nós e executar todas as tarefas administrativas relacionadas ao cluster.

Depois que a implantação for concluída, você poderá acessar a instância da VM do Datastax Operations Center usando o endereço DNS configurado. A VM OpsCenter tem a porta SSH 22 habilitada, bem como a porta 8443 para HTTPS. O endereço DNS para o centro de operações incluirá o dnsName e a região inseridos como parâmetros ao ser criada uma implantação com base nesse modelo no formato `{dnsName}.{region}.cloudapp.azure.com`. Se tiver criado uma implantação com o parâmetro `dnsName` definido como "datastax" na região "Oeste dos EUA", você poderá acessar a máquina virtual do Datastax Operations Center para a implantação em `https://datastax.westus.cloudapp.azure.com:8443`.

> [AZURE.NOTE]O certificado usado na implantação é um certificado autoassinado que criará um aviso no navegador. Você pode seguir o processo no site do [Datastax](http://www.datastax.com/) para substituir o certificado por seu próprio certificado SSL.

Antes de lidar com mais detalhes relacionados ao Gerenciador de Recursos do Azure e ao modelo usado para essa implantação, verifique se que você tem o PowerShell e a CLI do Azure configurados e prontos para serem usados.

[AZURE.INCLUDE [arm-getting-setup-powershell](../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../includes/xplat-getting-set-up-arm.md)]

## Criar um cluster do Cassandra baseado em DataStax com um modelo do Gerenciador de Recursos e o PowerShell do Azure

Siga estas etapas para criar um cluster do Apache Cassandra, com base no DataStax, usando um modelo do Gerenciador de Recursos no repositório de modelos do Github com o PowerShell do Azure.

### Etapa 1: baixe o arquivo JSON para o modelo e outros arquivos.

Designe uma pasta local como a localização para o modelo JSON e outros arquivos e crie-a \(por exemplo, C:\\Azure\\Templates\\DataStax\).

Preencha o nome da pasta e execute estes comandos:

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

Como alternativa, também é possível clonar o repositório de modelos usando um cliente git de sua escolha, por exemplo:

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

Ao concluir, procure a pasta datastax-on-ubuntu em C:\\Azure\\Templates.

### Etapa 2 \(opcional\): familiarize-se com os parâmetros do modelo.

Ao implantar soluções não triviais, como um cluster do Apache Cassandra com base no DataStax, você deve especificar um conjunto de parâmetros de configuração para lidar com várias configurações necessárias. Declarando esses parâmetros na definição do modelo, é possível especificar valores durante a execução da implantação por meio de um arquivo externo ou na linha de comando.

Se procurar a seção "parâmetros" no início do arquivo azuredeploy.json, você encontrará o conjunto de parâmetros necessários ao modelo para configurar um cluster do DataStax. Eis um exemplo dessa seção no modelo azuredeploy.json:

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
				"Description": "Unique namespace for the Storage Account where the Virtual Machine's disks will be placed"
			}
		},
		"dnsName": {
			"type": "string",
			"metadata" : {
				"Description": "DNS subname for the opserations center public IP"
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

Descrevendo os parâmetros necessários, incluindo detalhes como tipos de dados, valores permitidos e assim por diante, fica claro que essa seção será muito útil para qualquer tarefa de validação relacionada a valores de parâmetro passados na execução do modelo em um modo interativo \(por exemplo, PowerShell ou CLI do Azure\), mas também para qualquer interface do usuário de autodescoberta que possa ser criada dinamicamente analisando a lista de parâmetros necessários e sua descrição.

### Etapa 3: implante novo cluster do DataStax com o modelo.

Preparar um arquivo de parâmetro para sua implantação significa criar um arquivo JSON que contém os valores de tempo de execução para todos os parâmetros, que serão então passados como uma única entidade ao comando de implantação.

Aqui está um exemplo que você pode encontrar no arquivo azuredeploy-parameters.json:

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

Preencha o nome da implantação do Azure, o nome do grupo de recursos, o local do Azure e a pasta para o arquivo JSON salvo e, em seguida, execute estes comandos:

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\DataStax>"
	$templateFile= $folderName + "\azuredeploy.json"
	$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

	New-AzureResourceGroup –Name $RGName –Location $locName

	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

Quando você executar o comando **New-AzureResourceGroupDeployment**, isso extrairá valores de parâmetros do arquivo JSON e iniciará a execução do modelo de forma adequada. A definição e o uso de vários arquivos de parâmetros com seus diferentes ambientes \(por exemplo, Teste, Produção etc.\) promoverão a reutilização do modelo e simplificarão soluções complexas com vários ambientes.

Ao implantar, tenha em mente que uma nova Conta de Armazenamento do Azure precisa ser criada. Portanto, o nome fornecido como o parâmetro de conta de armazenamento precisa ser exclusivo e atender a todos os requisitos para uma Conta de Armazenamento do Azure.

Durante e após a implantação, você pode verificar todas as solicitações feitas durante o provisionamento, incluindo quaisquer erros ocorridos.

Para fazer isso, vá para o [Portal do Azure](https://portal.azure.com) e faça o seguinte:

- Clique em "Procurar" na barra de navegação à esquerda, role para baixo e clique em "Grupos de Recursos".  
- Depois que você clicar no Grupo de Recursos que acabou de criar, será mostrada a folha "Grupo de Recursos".  
- Clicando no gráfico de barras "Eventos" na parte "Monitoramento" da folha "Grupo de Recursos", você pode ver os eventos de sua implantação:
- Se clicar em eventos individuais, você poderá fazer uma busca detalhada dos detalhes de cada operação individual feita em nome do modelo

Depois dos testes, se você precisar remover esse grupo de recursos e todos os seus recursos \(a conta de armazenamento, a máquina virtual e a rede virtual\), use este comando:

	Remove-AzureResourceGroup –Name "<resource group name>" -Force

### Etapa 3-b: implante um cluster do DataStax com um modelo do Gerenciador de Recursos usando a CLI do Azure

Funcionalmente equivalente à abordagem do PowerShell listada acima, a implantação de um cluster do Cassandra Apache via CLI do Azure requer que você crie primeiro um Grupo de Recursos, especificando o nome e o local:

	azure group create dsc "West US"

Subsequentemente, invocando a criação de uma implantação e passando o nome do Grupo de Recursos, o arquivo de parâmetros e o modelo real, conforme mostrado abaixo:

	azure group deployment create dsc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

Também é possível verificar o status de implantações individuais invocando o seguinte comando:

	azure group deployment list dsc

## Um tour da organização dos arquivos e da de estrutura do modelo criado para implantar o DataStax no Ubuntu

Para criar uma abordagem robusta e reutilizável para projetar modelos do Gerenciador de Recursos, é preciso realizar preparação adicional para organizar a série de tarefas complexas e interrelacionadas necessárias durante a implantação de uma solução complexa como o DataStax. Aproveitando os recursos de **vinculação de modelos** e **loops de recursos** do ARMm além da execução de scripts por meio de extensões relacionadas, é possível implementar uma abordagem modular que pode ser reutilizada com praticamente qualquer implantação complexa com base em modelo.

Este diagrama descreve as relações entre todos os arquivos baixados do GitHub para essa implantação:

![datastax-files](media/virtual-machines-datastax-template/datastax-files.png)

Já mencionamos a função de **azuredeploy-parameters.json**, que será usado para passar determinado conjunto de valores de parâmetros durante a execução do modelo, mas a essência dessa abordagem de implantação está contida em **azuredeploy.json**. Ignorando a seção de parâmetros, conforme descrito anteriormente neste documento, a seção a seguir é representada por **"variáveis"**. Basicamente, ela contém vários campos \(fragmentos ou tipos de dados JSON\) que serão definidos como constantes ou valores calculados em tempo de execução, como você pode ver no seguinte exemplo:

	"variables": {
	"templateBaseUrl": "https://raw.githubusercontent.com/trentmswanson/azure-quickstart-templates/master/datastax-on-ubuntu/",
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

Fazendo uma busca detalhada neste exemplo, você pode ver as duas abordagens diferentes. No primeiro fragmento, a variável "osSettings" será definida como um elemento JSON aninhado que contém pares de valores com quatro chaves:

	"osSettings": {
	      "imageReference": {
	        "publisher": "Canonical",
	        "offer": "UbuntuServer",
	        "sku": "14.04.2-LTS",
	        "version": "latest"
	      },

	 
No segundo fragmento, em vez disso, a variável "scripts" é uma matriz JSON em que elementos únicos serão calculados em tempo de execução usando uma função de linguagem de modelo \(concat\) e o valor de outra variável mais constantes de cadeia de caracteres:

	      "scripts": [
	        "[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
	        "[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
	        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
	      ]

A seção **"recursos"** é onde acontece a maior parte da ação. Analisando cuidadosamente essa seção, você pode identificar imediatamente dois casos diferentes: o primeiro é um elemento definido do tipo `Microsoft.Resources/deployments` que, basicamente, significa a invocação de uma implantação aninhada dentro da principal. Por meio do elemento `templateLink` \(e da propriedade de versão relacionada\), é possível especificar um arquivo de modelo vinculado que será invocado passando um conjunto de parâmetros como entrada, como você pode perceber neste fragmento:

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

Neste primeiro exemplo, fica claro como **azuredeploy.json** nesse cenário foi organizado como um mecanismo de orquestração, invocando vários outros arquivos de modelo, cada um deles responsável por parte das atividades de implantação necessárias.

Em particular, os seguintes modelos vinculados serão usados para essa implantação:

-	**shared-resource.json**: contém a definição de todos os recursos que serão compartilhados na implantação. Os exemplos são contas de armazenamento usadas para armazenar os discos do sistema operacional da VM ou redes virtuais.
-	**opscenter-resources.json**: implanta a VM OpsCenter e todos os recursos relacionados, como interface de rede, endereço IP público e assim por diante.
-	**opscenter-install-resources.json**: implanta a extensão de VM OpsCenter \(script personalizado para Linux\) que invocará o arquivo de script bash específico \(\*\*opscenter.sh\*\*\) necessário para configurar o serviço OpsCenter na VM.
-	**ephemeral-nodes-resources.json**: implanta todas as VMs de nós de cluster e recursos conectados \(por exemplo, placas de rede, IPs privados etc.\). Esse modelo também implanta extensões de VM \(scripts personalizados para Linux\) e invoca um script bash \(\*\*dsenode.sh\*\*\) para instalar fisicamente partes do Apache Cassandra em cada nó.

Vamos fazer uma busca detalhada desse último modelo, pois ele é um dos mais interessantes em termos de desenvolvimento de modelo. Um conceito importante a ser realçado é como um único arquivo de modelo pode implantar várias cópias de um único tipo de recurso e, para cada instância, pode definir valores exclusivos para as configurações necessárias. Esse conceito é conhecido como **lLoop de Recursos**.

Quando **ephemeral-nodes-resources.json** é invocado no arquivo principal **azuredeploy.json**, de fato, um parâmetro chamado **nodeCount** é fornecido como parte da lista de parâmetros. No modelo filho, esse parâmetro \(número de nós a serem implantados no cluster\) será usado no elemento **"copiar"** de cada recurso que precisa ser implantado em várias cópias, como realçado no fragmento a seguir. Para todas as configurações em que é necessário especificar valores exclusivos entre diferentes instâncias do recurso implantado, a função **copyindex\(\)** pode ser usada para obter um valor numérico que indica o índice atual nessa criação de loop de recursos específica. No fragmento a seguir, você pode ver esse conceito aplicado à criação de várias VMs para nós de cluster:

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

Outro conceito importante na criação de recursos é a capacidade de especificar dependências e precedências entre recursos, como você pode observar na matriz JSON **dependsOn**. Nesse modelo específico, cada nó também terá um disco de 1 TB anexado \(consulte `dataDisks`\) que pode ser usado para hospedar backups e instantâneos da instância do Apache Cassandra.

Os discos anexados são formatados como parte das atividades de preparação do nó disparadas pela execução do arquivo de script **dsenode.sh**. Na verdade, a primeira linha desse script invoca outro script:

	bash vm-disk-utils-0.1.sh

vm-disk-utils-0.1.sh faz parte da pasta **shared\_scripts\\ubuntu**, no repositório do github azure-quickstart-templates, e contém funções muito úteis para a montagem de discos, a formatação e a distribuição, que podem ser reutilizadas sempre que você precisar executar tarefas semelhantes como parte da criação de modelo.

Outro fragmento interessante para explorar é aquele relacionado às extensões de VM CustomScriptForLinux. Elas são instaladas como um tipo separado de recurso, com uma dependência em cada nó do cluster \(e na instância de OpsCenter\), aproveitando o mesmo mecanismo de loop de recursos descrito para máquinas virtuais:

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

Em vez disso, todos os outros arquivos de modelo necessários para essa implantação criam instâncias únicas de todos os recursos necessários, assim, podem ser considerados uma versão simples desse arquivo **ephemeral-nodes-resources.json**.

Familiarizando-se com os outros arquivos incluídos nessa implantação, você poderá compreender todos os detalhes e as práticas recomendadas necessárias para organizar e orquestrar estratégias de implantação complexas para soluções com vários nós, com base em qualquer tecnologia, utilizando modelos do Gerenciador de Recursos do Azure. Embora não seja obrigatório, uma abordagem recomendada é estruturar seus arquivos de modelos conforme realçado pelo seguinte diagrama:

![datastax-template-structure](media/virtual-machines-datastax-template/datastax-template-structure.png)

Essencialmente, essa abordagem sugere o seguinte:

-	Definir o arquivo de modelo principal como um ponto central de orquestração para todas as atividades de implantação específicas, aproveitando a vinculação de modelos para invocar execuções de modelos de sub-rotina
-	Criar arquivos de modelos específicos que implantarão todos os recursos compartilhados entre todas as outras tarefas de implantação específicas \(por exemplo, contas de armazenamento, configuração de vnet etc.\). Isso pode ser amplamente reutilizado entre implantações que tenham requisitos semelhantes em termos de infraestrutura comum.
-	Incluir modelos de recursos opcionais para requisitos de spot específicos de determinado recurso
-	Para membros de um grupo de recursos idênticos \(nós em um cluster etc.\), crie modelos específicos que usam o loop de recursos para implantar várias instâncias com propriedades exclusivas
-	Para todas as tarefas pós-implantação \(por exemplo, instalação de produtos, configurações etc.\), use extensões de implantação de scripts e crie scripts específicos para cada tecnologia

Para obter mais informações, consulte [Linguagem de modelo do Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).

<!--HONumber=52-->
