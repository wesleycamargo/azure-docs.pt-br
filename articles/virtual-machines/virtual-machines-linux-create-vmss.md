<properties
	pageTitle="Dimensionar Automaticamente Conjuntos de Dimensionamento de Máquina Virtual | Microsoft Azure"
	description="Introdução à criação e ao gerenciamento dos seus primeiros Conjuntos de Escalas de Máquina Virtual do Azure usando a CLI do Azure"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="davidmu"/>

# Dimensionar automaticamente máquinas em um conjunto de escalas de máquina virtual



Os Conjuntos de Dimensionamento de Máquina Virtual tornam fácil de implantar e gerenciar máquinas virtuais idênticas como um conjunto. Os conjuntos de dimensionamento fornecem uma camada de computação altamente escalonável e personalizável para aplicativos de hiperescala e suporte a imagens da plataforma Windows, imagens da plataforma Linux, imagens personalizadas e extensões. Para obter mais informações sobre conjuntos de dimensionamento, confira [Conjuntos de Dimensionamento de Máquina Virtual](virtual-machines-linux-scale-sets-overview.md).

Este tutorial mostra como criar um Conjunto de Escalas de Máquinas Virtuais do Linux usando a versão corrigida mais recente do Ubuntu Linux e dimensionar automaticamente as máquinas no conjunto. Você pode fazer isso criando um modelo do Gerenciador de recursos do Azure e implantá-lo usando o Azure PowerShell. Para obter mais informações sobre modelos, confira [Criação de modelos do Gerenciador de Recursos do Azure](../resource-group-authoring-templates.md).

O modelo que você cria neste tutorial é semelhante a um modelo que pode ser encontrado na Galeria de modelos. Para saber mais, confira [Implantar um Conjunto de Escalas de VM simples com VMs do Linux e um Jumpbox](https://azure.microsoft.com/documentation/templates/201-vmss-linux-jumpbox/).

Antes de começar as etapas neste tutorial, [instale a CLI do Azure](../xplat-cli-install.md).

[AZURE.INCLUDE [virtual-machines-vmss-preview-cli](../../includes/virtual-machines-vmss-preview-cli-include.md)]

## Etapa 1: Criar um grupo de recursos e uma conta de armazenamento

1. **Entrar no Microsoft Azure** - Na interface de linha de comando (Bash, Terminal, Prompt de comando), verifique se você está no modo do Gerenciador de Recursos digitando `azure config mode arm` e, em seguida, [entre com sua ID corporativa ou de estudante](../xplat-cli-connect.md#use-the-log-in-method) digitando `azure login` e seguindo as instruções para obter uma experiência de logon interativa com sua conta do Azure.

	> [AZURE.NOTE] Se você tiver uma ID de trabalho ou escolar e se souber que não tem a autenticação de dois fatores habilitada, poderá usar `azure login -u` juntamente com a ID de trabalho ou escolar para efetuar logon sem uma sessão interativa. Se você não tiver uma ID de trabalho ou escolar, poderá [criar uma ID de trabalho ou escolar de sua conta da Microsoft pessoal](virtual-machines-windows-create-aad-work-id.md).

2. **Criar um grupo de recursos** – Todos os recursos devem ser implantados em um grupo de recursos. Para este tutorial, nomeie o grupo de recursos **vmsstest1**:

	```
	azure group create vmsstestrg1 westus
	```

3. **Implantar uma conta de armazenamento para o novo grupo de recursos** – Este tutorial usa várias contas de armazenamento para facilitar o conjunto de dimensionamento de máquina virtual. Crie uma conta de armazenamento chamada **vmsstestsa**. Mantenha a janela da interface de comando aberta para etapas posteriormente neste tutorial:

	```
	azure storage account create --type LRS -g vmsstestrg1 -l westus vmsstestsa
	```

## Etapa 2: Criar o modelo
Um modelo do Gerenciador de Recursos do Azure permite implantar e gerenciar recursos do Azure juntos usando uma descrição JSON dos recursos e parâmetros de implantação associados.

1. Em seu editor de texto favorito, crie o arquivo C:\\VMSSTemplate.json e adicione a estrutura inicial do JSON para suportar o modelo.

	```
	{
		"$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
		}
		"variables": {
		}
		"resources": [
		]
	}
	```

2. Os parâmetros nem sempre são necessários, mas eles facilitam o gerenciamento de modelos. Eles fornecem uma maneira de especificar valores para o modelo, descrevem o tipo do valor, o valor padrão, se necessário, e possivelmente os valores permitidos do parâmetro.

	Adicione esses parâmetros sob o elemento pai de parâmetros que você adicionou ao modelo:

	- Um nome para a máquina virtual jumpbox que é usado para acessar as máquinas no conjunto de dimensionamento.
	- Um nome para a conta de armazenamento onde o modelo é armazenado.
	- O número de instâncias de máquinas virtuais para criar inicialmente no conjunto de dimensionamento.
	- Um nome e senha da conta de administrador nas máquinas virtuais.
	- Um prefixo para os recursos criados no grupo de recursos.


	```
	"vmName": {
		"type": "string"
	},
	"vmSSName": {
		"type": "string"
	},
	"instanceCount": {
		"type": "string"
	},
	"adminUsername": {
		"type": "string"
	},
	"adminPassword": {
		"type": "securestring"
	},
	"resourcePrefix": {
		"type": "string"
	}
	```

3. As variáveis podem ser usadas em um modelo para especificar valores que podem ser alterados com frequência ou que precisam ser criados com base em uma combinação de valores de parâmetros.

	Adicione essas variáveis sob o elemento pai de variáveis que você adicionou ao modelo:

	- Nomes DNS que são usados pelas interfaces de rede.
	- O tamanho das máquinas virtuais usadas no conjunto de dimensionamento. Para obter mais informações sobre tamanhos de máquinas virtuais, confira [Tamanhos de máquinas virtuais](virtual-machines-linux-sizes.md).
	- As informações da imagem de plataforma para definir o sistema operacional que será executado nas máquinas virtuais no conjunto de dimensionamento. Para obter mais informações sobre a seleção de imagens, confira [Navegar e selecionar imagens da máquina virtual do Azure com o Windows PowerShell e a CLI do Azure](virtual-machines-linux-cli-ps-findimage.md).

	- Os nomes de endereços IP e prefixos para a rede virtual e sub-redes.
	- Os nomes e os identificadores da rede virtual, balanceador de carga e interfaces de rede.
	- Nomes de conta de armazenamento para as contas associadas com as máquinas no conjunto de dimensionamento.
	- Configurações para a Extensão de diagnóstico que é instalada nas máquinas virtuais. Para obter mais informações sobre a Extensão de diagnóstico, confira [Criar uma máquina virtual do Windows com monitoramento e diagnóstico usando o Modelo do Gerenciador de Recursos do Azure](virtual-machines-windows-extensions-diagnostics-template.md).

	```
	"apiVersion": "2015-06-15"
	"dnsName1": "[concat(parameters('resourcePrefix'),'dn1')] ",
	"dnsName2": "[concat(parameters('resourcePrefix'),'dn2')] ",
	"vmSize": "Standard_A0",
	"imagePublisher": "Canonical",
	"imageOffer": "UbuntuServer",
	"imageVersion": "15.10",
	"addressPrefix": "10.0.0.0/16",
	"subnetName": "Subnet",
	"subnetPrefix": "10.0.0.0/24",
	"publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
	"publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
	"loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
	"virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
	"nicName1": "[concat(parameters('resourcePrefix'),'nc1')]",
	"nicName2": "[concat(parameters('resourcePrefix'),'nc2')]",
	"vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
	"publicIPAddressID1": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIP1'))]",
	"publicIPAddressID2": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIP2'))]",
	"lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
	"nicId": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName2'))]",
	"frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
	"storageAccountType": "Standard_LRS",
	"storageAccountSuffix": [ "a", "g", "m", "s", "y" ],
	"diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
	"accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
	"wadlogs": "<WadCfg><DiagnosticMonitorConfiguration>",
	"wadperfcounter": "<PerformanceCounters scheduledTransferPeriod="PT1M"><PerformanceCounterConfiguration counterSpecifier="\\Processor\\PercentProcessorTime" sampleRate="PT15S" unit="Percent"><annotation displayName="CPU percentage guest OS" locale="pt-BR"/></PerformanceCounterConfiguration>",
	"wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId="')]",
	"wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
	"wadcfgxend": "[concat('"><MetricAggregation scheduledTransferPeriod="PT1H"/><MetricAggregation scheduledTransferPeriod="PT1M"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"
	```

4. Neste tutorial, você pode implantar os seguintes recursos e extensões:

 - Microsoft.Storage/storageAccounts
 - Microsoft.Network/virtualNetworks
 - Microsoft.Network/publicIPAddresses
 - Microsoft.Network/loadBalancers
 - Microsoft.Network/networkInterfaces
 - Microsoft.Compute/virtualMachines
 - Microsoft.Compute/virtualMachineScaleSets
 - Microsoft.Insights.VMDiagnosticsSettings
 - Microsoft.Insights/autoscaleSettings

	Para obter mais informações sobre os recursos do Gerenciador de recursos, confira [Computação do Azure, Rede e Provedores de Armazenamento no Gerenciador de Recursos do Azure](virtual-machines-windows-compare-deployment-models.md).

	Adicione o recurso de conta de armazenamento sob o elemento pai de recursos que você adicionou ao modelo. Este modelo usa um loop para criar as contas de armazenamento recomendadas 5 onde os discos do sistema operacional e os dados de diagnóstico estão armazenados. Este conjunto de contas pode oferecer suporte a até 100 máquinas virtuais em um conjunto de dimensionamento, que é o máximo atual. Cada conta de armazenamento é nomeada com um designador que foi definido nas variáveis combinadas com o sufixo que você fornecer nos parâmetros do modelo.

	```
	{
		"type": "Microsoft.Storage/storageAccounts",
		"name": "[concat(parameters('resourcePrefix'), parameters('storageAccountSuffix')[copyIndex()])]",
		"apiVersion": "2015-05-01-preview",
		"copy": {
			"name": "storageLoop",
			"count": 5
		},
		"location": "[resourceGroup().location]",
		"properties": {
			"accountType": "[variables('storageAccountType')]"
		}
	},
	```

5. Adicione o recurso de rede virtual. Confira [Provedor de Recurso de Rede](../virtual-network/resource-groups-networking.md) para obter mais informações.

	```
	{
		"apiVersion": "[variables('apiVersion')]",
		"type": "Microsoft.Network/virtualNetworks",
		"name": "[variables('virtualNetworkName')]",
		"location": "[resourceGroup().location]",
		"properties": {
			"addressSpace": {
				"addressPrefixes": [
					"[variables('addressPrefix')]"
				]
			},
			"subnets": [
				{
					"name": "[variables('subnetName')]",
					"properties": {
						"addressPrefix": "[variables('subnetPrefix')]"
					}
				}
			]
		}
	},
	```

6. Adicione os recursos de endereço IP público que são usados pelo balanceador de carga e interface de rede.

	```
	{
		"apiVersion": "[variables('apiVersion')]",
		"type": "Microsoft.Network/publicIPAddresses",
		"name": "[variables('publicIP1')]",
		"location": "[resourceGroup().location]",
		"properties": {
			"publicIPAllocationMethod": "Dynamic",
			"dnsSettings": {
				"domainNameLabel": "[variables('dnsName1')]"
			}
		}
	},
	{
		"apiVersion": "[variables('apiVersion')]",
		"type": "Microsoft.Network/publicIPAddresses",
		"name": "[variables('publicIP2')]",
		"location": "[resourceGroup().location]",
		"properties": {
			"publicIPAllocationMethod": "Dynamic",
			"dnsSettings": {
				"domainNameLabel": "[variables('dnsName2')]"
			}
		}
	},
	```

7. Adicione o recurso de balanceador de carga que é usado pelo conjunto de dimensionamento. Para obter mais informações, confira [Suporte do Gerenciador de Recursos do Azure para Balanceador de Carga](../load-balancer/load-balancer-arm.md).

	```
	{
		"apiVersion": "[variables('apiVersion')]",
		"name": "[variables('loadBalancerName')]",
		"type": "Microsoft.Network/loadBalancers",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
		],
		"properties": {
			"frontendIPConfigurations": [
				{
					"name": "loadBalancerFrontEnd",
					"properties": {
						"publicIPAddress": {
							"id": "[variables('publicIPAddressID1')]"
						}
					}
				}
			],
			"backendAddressPools": [
				{
					"name": "bepool1"
				}
			],
			"inboundNatPools": [
				{
					"name": "natpool1",
					"properties": {
						"frontendIPConfiguration": {
							"id": "[variables('frontEndIPConfigID')]"
						},
						"protocol": "tcp",
						"frontendPortRangeStart": 50000,
						"frontendPortRangeEnd": 50500,
						"backendPort": 3389
					}
				}
			]
		}
	},
	```

8. Adicione o recurso de interface de rede que é usado pela máquina virtual de jumpbox. Como máquinas em um conjunto de dimensionamento de máquina virtual não são diretamente acessíveis usando um endereço IP público, uma máquina virtual de jumpbox é criada na mesma rede virtual como o conjunto de dimensionamento e é usada para acessar remotamente as máquinas no conjunto.

	```
	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Network/networkInterfaces",
		"name": "[variables('nicName1')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP2'))]",
			"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
		],
		"properties": {
			"ipConfigurations": [
				{
					"name": "ipconfig1",
					"properties": {
						"privateIPAllocationMethod": "Dynamic",
						"publicIPAddress": {
							"id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIP2'))]"
						},
						"subnet": {
							"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/',variables('subnetName'))]"
						}
					}
				}
			]
		}
	},
	```


9. Adicione o jumpbox à mesma rede que o conjunto de escalas.

	```
	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Compute/virtualMachines",
		"name": "[parameters('vmName')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"storageLoop",
			"[concat('Microsoft.Network/networkInterfaces/', variables('nicName1'))]"
		],
		"properties": {
			"hardwareProfile": {
				"vmSize": "[variables('vmSize')]"
			},
			"osProfile": {
				"computername": "[parameters('vmName')]",
				"adminUsername": "[parameters('adminUsername')]",
				"adminPassword": "[parameters('adminPassword')]"
			},
			"storageProfile": {
				"imageReference": {
					"publisher": "[variables('imagePublisher')]",
					"offer": "[variables('imageOffer')]",
					"sku": "[variables('imageVersion')]",
					"version": "latest"
				},
				"osDisk": {
					"name": "osdisk1",
					"vhd": {
						"uri":  "[concat('https://',parameters('resourcePrefix'),'saa.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'osdisk1.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"
				}
			},
			"networkProfile": {
				"networkInterfaces": [
					{
						"id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName1'))]"
					}
				]
			}
		}
	},
	```

10.	Adicione o recurso do conjunto de dimensionamento de máquina virtual e especifique a extensão de Diagnóstico que é instalada em todas as máquinas virtuais no conjunto de dimensionamento. Muitas das configurações desse recurso são semelhantes ao recurso de máquina virtual. Aqui estão as diferenças principais:

	- **capacity** - especifica quantas máquinas virtuais devem ser inicializadas no conjunto de dimensionamento. Você pode definir esse valor especificando um valor para o parâmetro instanceCount.

	- **upgradePolicy** – especifica como as atualizações são feitas em máquinas virtuais no conjunto de dimensionamento. Manual especifica que apenas novas máquinas virtuais são afetadas pelas alterações em um modelo quando ele for reimplantado. Automático especifica que todas as máquinas no conjunto de dimensionamento são atualizadas e reiniciadas.

	O conjunto de dimensionamento de máquina virtual não é criado até que todas as contas de armazenamento são criadas conforme especificado com o elemento dependsOn.

	```
	{
		"type": "Microsoft.Compute/virtualMachineScaleSets",
		"apiVersion": "[variables('apiVersion')]",
		"name": "[parameters('vmSSName')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"storageLoop",
			"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
			"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
		],
		"sku": {
			"name": "[variables('vmSize')]",
			"tier": "Standard",
			"capacity": "[parameters('instanceCount')]"
		}
		"properties": {
			"upgradePolicy": {
				"mode": "Manual"
			},
			"virtualMachineProfile": {
				"storageProfile": {
					"osDisk": {
						"vhdContainers": [
							"[concat('https://', parameters('resourcePrefix'), 'saa.blob.core.windows.net/vmss')]",
							"[concat('https://', parameters('resourcePrefix'), 'sag.blob.core.windows.net/vmss')]",
							"[concat('https://', parameters('resourcePrefix'), 'sam.blob.core.windows.net/vmss')]",
							"[concat('https://', parameters('resourcePrefix'), 'sas.blob.core.windows.net/vmss')]",
							"[concat('https://', parameters('resourcePrefix'), 'say.blob.core.windows.net/vmss')]"
						],
						"name": "vmssosdisk",
						"caching": "ReadOnly",
						"createOption": "FromImage"
					},
					"imageReference": {
						"publisher": "[variables('imagePublisher')]",
						"offer": "[variables('imageOffer')]",
						"sku": "[variables('imageVersion')]",
						"version": "latest"
					}
				},
				"osProfile": {
					"computerNamePrefix": "[parameters('vmSSName')]",
					"adminUsername": "[parameters('adminUsername')]",
					"adminPassword": "[parameters('adminPassword')]"
				},
				"networkProfile": {
					"networkInterfaceConfigurations": [
						{
							"name": "[variables('nicName2')]",
							"properties": {
								"primary": "true",
								"ipConfigurations": [
									{
										"name": "ip1",
										"properties": {
											"subnet": {
												"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/',variables('subnetName'))]"
											},
											"loadBalancerBackendAddressPools": [
												{
													"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/backendAddressPools/bepool1')]"
												}
											],
											"loadBalancerInboundNatPools": [
												{
													"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/inboundNatPools/natpool1')]"
												}
											]
										}
									}
								]
							}
						}
					]
				},
				"extensionProfile": {
					"extensions": [
						{
							"name":"LinuxDiagnostic",
							"properties": {
								"publisher":"Microsoft.OSTCExtensions",
								"type":"LinuxDiagnostic",
								"typeHandlerVersion":"2.1",
								"autoUpgradeMinorVersion":false,
								"settings": {
									"xmlCfg":"[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
									"storageAccount":"[variables('diagnosticsStorageAccountName')]"
								},
								"protectedSettings": {
									"storageAccountName":"[variables('diagnosticsStorageAccountName')]",
									"storageAccountKey":"[listkeys(variables('accountid'), variables('apiVersion')).key1]",
									"storageAccountEndPoint":"https://core.windows.net"
								}
							}
						}
					]
				}
			}
		}
	},
	```

11.	Adicione o recurso autoscaleSettings, que define como o conjunto de escalas se ajusta com base no uso do processador nas máquinas no conjunto. Para este tutorial, estes são os valores importantes:

 - **metricName** - Esse é o mesmo que o contador de desempenho que definimos na variável wadperfcounter. Usando essa variável, a extensão do Diagnóstico coleta o contador **Processor\\PercentProcessorTime**.
 - **metricResourceUri** - Este é o identificador de recurso do conjunto de dimensionamento de máquina virtual.
 - **timeGrain** – Esta é a granularidade das métricas que são coletadas. Neste modelo, ele é definido como 1 minuto.
 - **statistic** – Isso determina como as métricas são combinadas para acomodar a ação de dimensionamento automático. Os valores possíveis são: Média, Mín, Máx. Neste modelo estamos procurando o uso médio de CPU total entre as máquinas virtuais no conjunto de dimensionamento.
 - **timeWindow** – Esta, se o intervalo de tempo em que os dados de instância são coletados. Deve estar entre 5 minutos e 12 horas.
 - **timeAggregation** – Determina como os dados coletados devem ser combinados ao longo do tempo. O valor padrão é Average. Os valores possíveis são: Média, Mínimo, Máximo, Último, Total, Contagem.
 - **operator** – Este é o operador usado para comparar os dados de métrica e o limite. Os valores possíveis são: Equals, NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual.
 - **threshold** – Este é o valor que dispara a ação de dimensionamento. Neste modelo, máquinas são adicionadas ao conjunto de dimensionamento quando o uso médio de CPU entre máquinas no conjunto é mais de 50%.
 - **direction** – Determina a ação que é executada quando o valor de limite for atingido. Os valores possíveis são Aumentar ou Diminuir. Neste modelo, o número de máquinas virtuais no conjunto de dimensionamento é aumentado se o limite for acima de 50% na janela de tempo definido.
 - **type** – Este é o tipo de ação que deve ocorrer e deve ser definido como ChangeCount.
 - **value** – Este é o número de máquinas virtuais que são adicionadas ou removidas do conjunto de dimensionamento. Este valor deve ser 1 ou maior. O valor padrão é 1. Neste modelo, o número de máquinas no conjunto de dimensionamento aumenta em 1 quando o limite é atingido.
 - **cooldown** – Esta é a quantidade de tempo de espera desde a última ação de dimensionamento antes que ocorra a próxima ação. Isso deve estar entre 1 minuto e 1 semana.

	```
	{
		"type": "Microsoft.Insights/autoscaleSettings",
		"apiVersion": "2015-04-01",
		"name": "[concat(parameters('resourcePrefix'),'as1')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
		],
		"properties": {
			"enabled": true,
			"name": "[concat(parameters('resourcePrefix'),'as1')]",
			"profiles": [
				{
					"name": "Profile1",
					"capacity": {
						"minimum": "1",
						"maximum": "10",
						"default": "1"
					},
					"rules": [
						{
							"metricTrigger": {
								"metricName": "\\Processor\\PercentProcessorTime",
								"metricNamespace": "",
								"metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]",
								"timeGrain": "PT1M",
								"statistic": "Average",
								"timeWindow": "PT5M",
								"timeAggregation": "Average",
								"operator": "GreaterThan",
								"threshold": 50.0
							},
							"scaleAction": {
								"direction": "Increase",
								"type": "ChangeCount",
								"value": "1",
								"cooldown": "PT5M"
							}
						}
					]
				}
			],
			"targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
		}
	}
	```

12.	Salvar o arquivo de modelo.    

## Etapa 3: Carregar o modelo para armazenamento

O modelo pode ser carregado da sua interface de linha de comando, desde que você saiba o nome da conta e a chave primária da conta de armazenamento que você criou na etapa 1.

1. Em sua interface de linha de comando (Bash, Terminal, Prompt de comando), execute esses comandos para definir as variáveis de ambiente necessárias para acessar a conta de armazenamento:

		export AZURE_STORAGE_ACCOUNT={account_name}
		export AZURE_STORAGE_ACCESS_KEY={key}

	Você pode obter a chave clicando no ícone de chave ao exibir o recurso de conta de armazenamento no Portal do Azure. Ao usar um prompt de comando do Windows, digite **set** em vez de exportar.

2. Crie o contêiner para armazenar o modelo:

		azure storage container create -p Blob templates

3. Carregue o arquivo de modelo para o novo contêiner.

		azure storage blob upload VMSSTemplate.json templates VMSSTemplate.json

## Etapa 4: Implantar o modelo

Agora que você criou o modelo, pode começar a implantar os recursos. Use este comando para iniciar o processo:

	azure group deployment create --template-uri https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json vmsstestrg1 vmsstestdp1

Quando você pressiona a tecla enter, é solicitado a fornecer valores para as variáveis que você atribuiu. Forneça esses valores:

	vmName: vmsstestvm1
	vmSSName: vmsstest1
	instanceCount: 5
	adminUserName: vmadmin1
	adminPassword: VMpass1
	resourcePrefix: vmsstest

Deve levar cerca de 15 minutos para todos os recursos serem implantados com êxito.

>[AZURE.NOTE]Você também pode fazer uso da capacidade do portal para implantar os recursos. Para fazer isso, use este link: https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>

## Etapa 5: monitorar recursos

Você pode obter informações sobre os conjuntos de dimensionamento de máquina virtual usando estes métodos:

 - O portal do Azure - Atualmente você pode obter uma quantidade limitada de informações usando o portal.
 - O [Azure Resource Explorer](https://resources.azure.com/) - Esta é a melhor ferramenta para explorar o estado atual de seu conjunto de dimensionamento. Siga este caminho e você deverá ver a exibição da instância do conjunto de dimensionamento que você criou:

		subscriptions > {your subscription} > resourceGroups > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines

 - CLI do Azure - use esse comando para obter algumas informações:

		azure resource show -n vmsstest1 -r Microsoft.Compute/virtualMachineScaleSets -o 2015-06-15 -g vmsstestrg1

 - Conecte-se à máquina virtual de jumpbox exatamente como faria com qualquer outra máquina e, em seguida, você pode acessar remotamente as máquinas virtuais no conjunto de dimensionamento para monitorar os processos individuais.

>[AZURE.NOTE]Uma API REST completa para obter informações sobre conjuntos de escala pode ser encontrada nos [Conjuntos de escala de Máquinas Virtuais](https://msdn.microsoft.com/library/mt589023.aspx).

## Etapa 6: remover os recursos

Como você é cobrado pelos recursos usados no Azure, sempre é uma boa prática excluir os recursos que não são mais necessários. Você não precisa excluir cada recurso separadamente de um grupo de recursos. Você pode excluir o grupo de recursos, e todos os seus recursos serão excluídos automaticamente.

		azure group delete vmsstestrg1

## Próximas etapas

Confira o modelo [Escalar automaticamente um Conjunto de Escalas de VM que executa um aplicativo Ubuntu/Apache/PHP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale) que configura uma pilha LAMP para praticar a funcionalidade de colocação em escala automática dos Conjuntos de Escalas de Máquina Virtual.

<!---HONumber=AcomDC_0406_2016-->