<properties 
	pageTitle="O modelo do Gerenciador de Recursos de farm do SharePoint de três servidores" 
	description="Percorra a estrutura do modelo do Gerenciador de Recursos do Azure para o farm do SharePoint de três servidores." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="josephd"/>

# O modelo do Gerenciador de Recursos de farm do SharePoint de três servidores

Este tópico orienta você quanto à estrutura do arquivo de modelo azuredeploy.json para o farm do SharePoint de três servidores. Você pode ver o conteúdo do modelo em seu navegador clicando [aqui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json).

Se preferir, para examinar uma cópia local do arquivo azuredeploy.json, designe uma pasta local para criar o arquivo (por exemplo, C:\Azure\Templates\SharePointFarm). Preencha o nome da pasta e execute esses comandos no prompt de comando do PowerShell do Azure.

	$folderName="<folder name, such as C:\Azure\Templates\SharePointFarm>"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json"	
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)

Abra o modelo azuredeploy.json em um editor de texto ou na ferramenta de sua escolha. O exemplo a seguir descreve a estrutura do arquivo de modelo e a finalidade de cada seção.

## Seção "parâmetros"

A seção "parâmetros" especifica os parâmetros que são usados para inserir dados nesse modelo. Podem ser definidos no máximo 50 parâmetros. Aqui está um exemplo de um parâmetro para o local do Azure:

	"deploymentLocation": {
		"type": "string",
		"allowedValues": [
			"West US",
			"East US",
			"West Europe",
			"East Asia",
			"Southeast Asia"
		],
		"metadata": {
			"Description": "The region to deploy the resources into"
		},
		"defaultValue":"West Europe"
	},

## Seção "variáveis"

A seção "variáveis" especifica variáveis que podem ser usadas em todo esse modelo. Podem ser definidas no máximo 100 variáveis. Estes são alguns exemplos:

	"LBFE": "LBFE",
	"LBBE": "LBBE",
	"RDPNAT": "RDP",
	"spWebNAT": "spWeb",
	"adSubnetName": "adSubnet",
	"sqlSubnetName": "sqlSubnet",
	"spSubnetName": "spSubnet",
	"adNicName": "adNic",
	"sqlNicName": "sqlNic",
	"spNicName": "spNic",

## Seção "recursos"

A seção **"recursos"** especifica as informações necessárias para implantar os recursos para o farm do SharePoint em um grupo de recursos. Podem ser definidos no máximo 250 recursos, e as dependências de recursos só podem ser definidas com cinco níveis de profundidade.

Essa seção contém os seguintes procedimentos:

### Microsoft.Storage/storageAccounts  

Essa seção cria uma nova conta de armazenamento para todos os recursos de disco e VHD para o farm. Aqui está o código JSON para a conta de armazenamento:

	{
	  "type": "Microsoft.Storage/storageAccounts",
	  "name": "[parameters('newStorageAccountName')]",
	  "apiVersion": "2015-05-01-preview",
	  "location": "[parameters('deploymentLocation')]",
	  "properties": {
		"accountType": "[parameters('storageAccountType')]"
	  }
	},

### Microsoft.Network/publicIPAddresses

Essas seções criam um conjunto de endereços IP públicos por meio dos quais cada máquina virtual pode ser conectada através da Internet. Aqui está um exemplo:

	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Network/publicIPAddresses",
		"name": "[variables('adpublicIPAddressName')]",
		"location": "[parameters('deploymentLocation')]",
		"properties": {
			"publicIPAllocationMethod": "[parameters('publicIPAddressType')]",
			"dnsSettings": {
				"domainNameLabel": "[parameters('adDNSPrefix')]"
			}
		}
	},

### Microsoft.Compute/availabilitySets

Essas seções criam três conjuntos de disponibilidade, um para cada camada da implantação:

- Controladores de domínio do Active Directory
- Cluster do SQL Server
- Servidores SharePoint

Veja um exemplo:

	{
		"type": "Microsoft.Compute/availabilitySets",
		"name": "[variables('spAvailabilitySetName')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('deploymentLocation')]"
	},

### Microsoft.Network/virtualNetworks

Essa seção cria uma rede virtual somente em nuvem com três sub-redes (um para cada camada da implantação), em que as máquinas virtuais são colocadas. Aqui está o código JSON:

	{
		"name": "[parameters('virtualNetworkName')]",
		"type": "Microsoft.Network/virtualNetworks",
		"location": "[parameters('deploymentLocation')]",
		"apiVersion": "2015-05-01-preview",
		"properties": {
			"addressSpace": {
			"addressPrefixes": [
				"[parameters('virtualNetworkAddressRange')]"
			]
			},
			"subnets": "[variables('subnets')]"
		}
	},


### Microsoft.Network/loadBalancers

Essas seções criam instâncias do balanceador de carga para cada máquina virtual para fornecer filtragem de tráfego e NAT para o tráfego de entrada da Internet. Para cada balanceador de carga, as configurações definem regras de NAT de entrada, back-end e front-end. Por exemplo, há regras de tráfego de Área de Trabalho Remota para cada máquina virtual e, para o servidor do SharePoint, uma regra para permitir o tráfego da Web de entrada (porta TCP 80) da Internet. Aqui está o exemplo do servidor do SharePoint:


	{
		"apiVersion": "2015-05-01-preview",
		"name": "[variables('spLBName')]",
		"type": "Microsoft.Network/loadBalancers",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[resourceId('Microsoft.Network/publicIPAddresses',variables('sppublicIPAddressName'))]"
		],
		"properties": {
			"frontendIPConfigurations": [
				{
					"name": "[variables('LBFE')]",
					"properties": {
						"publicIPAddress": {
							"id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('sppublicIPAddressName'))]"
						}
					}
				}
			],
			"backendAddressPools": [
				{
					"name": "[variables('LBBE')]"
				}
			],
			"inboundNatRules": [
				{
					"name": "[variables('RDPNAT')]",
					"properties": {
						"frontendIPConfiguration": {
							"id": "[variables('splbFEConfigID')]"
						},
						"protocol": "tcp",
						"frontendPort": "[parameters('RDPPort')]",
						"backendPort": 3389,
						"enableFloatingIP": false
					}
				},
				{
					"name": "[variables('spWebNAT')]",
					"properties": {
						"frontendIPConfiguration": {
							"id": "[variables('splbFEConfigID')]"
						},
						"protocol": "tcp",
						"frontendPort": 80,
						"backendPort": 80,
						"enableFloatingIP": false
					}
				}
			]
		}
	},

### Microsoft.Network/networkInterfaces

Essas seções criam uma interface de rede para cada máquina virtual e configuram um endereço IP estático para o controlador de domínio. Aqui está o exemplo da interface de rede do controlador de domínio:

	{
		"name": "[variables('adNicName')]",
		"type": "Microsoft.Network/networkInterfaces",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[parameters('virtualNetworkName')]",
			"[concat('Microsoft.Network/loadBalancers/',variables('adlbName'))]"
		],
		"apiVersion": "2015-05-01-preview",
		"properties": {
			"ipConfigurations": [
				{
					"name": "ipconfig1",
					"properties": {
						"privateIPAllocationMethod": "Static",
						"privateIPAddress": "[parameters('adNicIPAddress')]",
						"subnet": {
							"id": "[variables('adSubnetRef')]"
						},
						"loadBalancerBackendAddressPools": [
							{
								"id": "[variables('adBEAddressPoolID')]"
							}
						],
						"loadBalancerInboundNatRules": [
							{
								"id": "[variables('adRDPNATRuleID')]"
							}
						]
					}
				}
			]
		}
	},


### Microsoft.Compute/virtualMachines

Essas seções criam e configuram as três máquinas virtuais na implantação.

A primeira seção cria e configura o controlador de domínio que:

- Especifica a conta de armazenamento, o conjunto de disponibilidade, a interface de rede e a instância do balanceador de carga
- Adiciona mais um disco
- Executa um script do PowerShell para configurar o controlador de domínio

Aqui está o código JSON:

		{
			"apiVersion": "2015-05-01-preview",
			"type": "Microsoft.Compute/virtualMachines",
			"name": "[parameters('adVMName')]",
			"location": "[parameters('deploymentLocation')]",
			"dependsOn": [
				"[resourceId('Microsoft.Storage/storageAccounts',parameters('newStorageAccountName'))]",
				"[resourceId('Microsoft.Network/networkInterfaces',variables('adNicName'))]",
				"[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]",
				"[resourceId('Microsoft.Network/loadBalancers',variables('adlbName'))]"
			],
			"properties": {
				"hardwareProfile": {
					"vmSize": "[parameters('adVMSize')]"
				},
				"availabilitySet": {
					"id": "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]"
				},
				"osProfile": {
					"computername": "[parameters('adVMName')]",
					"adminUsername": "[parameters('adminUsername')]",
					"adminPassword": "[parameters('adminPassword')]"
				},
				"storageProfile": {
					"imageReference": {
						"publisher": "[parameters('adImagePublisher')]",
						"offer": "[parameters('adImageOffer')]",
						"sku": "[parameters('adImageSKU')]",
						"version": "latest"
					},
					"osDisk": {
						"name": "osdisk",
						"vhd": {
							"uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/',parameters('adVMName'),'-osdisk.vhd')]"
						},
						"caching": "ReadWrite",
						"createOption": "FromImage"
					},
					"dataDisks": [
						{
							"vhd": {
								"uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/', variables('adDataDisk'),'-1.vhd')]"
							},
							"name": "[concat(parameters('adVMName'),'-data-disk1')]",
							"caching": "None",
							"createOption": "empty",
							"diskSizeGB": "[variables('adDataDiskSize')]",
							"lun": 0
						}
					]
				},
				"networkProfile": {
					"networkInterfaces": [
						{
							"id": "[resourceId('Microsoft.Network/networkInterfaces',variables('adNicName'))]"
						}
					]
				}
			},
			"resources": [
				{
					"type": "Microsoft.Compute/virtualMachines/extensions",
					"name": "[concat(parameters('adVMName'),'/InstallDomainController')]",
					"apiVersion": "2015-05-01-preview",
					"location": "[parameters('deploymentLocation')]",
					"dependsOn": [
						"[resourceId('Microsoft.Compute/virtualMachines', parameters('adVMName'))]"
					],
					"properties": {
						"publisher": "Microsoft.Powershell",
						"type": "DSC",
						"typeHandlerVersion": "1.7",
						"settings": {
							"ModulesUrl": "[variables('adModulesURL')]",
							"ConfigurationFunction": "[variables('adConfigurationFunction')]",
							"Properties": {
								"DomainName": "[parameters('domainName')]",
								"AdminCreds": {
									"UserName": "[parameters('adminUserName')]",
									"Password": "PrivateSettingsRef:AdminPassword"
								}
							}
						},
						"protectedSettings": {
							"Items": {
								"AdminPassword": "[parameters('adminPassword')]"
							}
						}
					}
				}
			]
		},

Uma seção adicional para o controlador de domínio iniciando com **"nome": "UpdateVNetDNS"** configura o servidor DNS da rede virtual para usar o endereço IP do controlador de domínio.

A próxima seção **"tipo": "Microsoft.Compute/virtualMachines"** cria máquinas virtuais do SQL Server na implantação e:

- Especifica a conta de armazenamento, o conjunto de disponibilidade, o balanceador de carga, a rede virtual e a interface de rede
- Adiciona mais um disco

Seções adicionais **"Microsoft.Compute/virtualMachines/extensions"** chamam o script do PowerShell para configurar o SQL Server.

A próxima seção **"tipo": "Microsoft.Compute/virtualMachines"** cria a máquina virtual do SharePoint na implantação, especificando a conta de armazenamento, o conjunto de disponibilidade, o balanceador de carga, a rede virtual e a interface de rede. Uma outra seção **"Microsoft.Compute/virtualMachines/extensions"** chama um script do PowerShell para configurar o farm do SharePoint.

Observe a organização geral das subseções da seção **"recursos"** do arquivo JSON:

1.	Crie os elementos da infraestrutura do Azure que são necessários para dar suporte a várias máquinas virtuais (uma conta de armazenamento, endereços IP públicos, conjuntos de disponibilidade, uma rede virtual, interfaces de rede, instâncias de balanceador de carga).
2.	Crie a máquina virtual do controlador de domínio, que usa elementos comuns e específicos criados anteriormente de infraestrutura do Azure, adiciona um disco de dados e executa um script do PowerShell. Além disso, atualize a rede virtual para usar o endereço IP estático do controlador de domínio.
3.	Crie a máquina virtual do SQL Server, que usa elementos comuns criados anteriormente e elementos específicos da infraestrutura do Azure criados para o controlador de domínio, adiciona discos de dados e executa um script do PowerShell para configurar o SQL Server.
4.	Crie a máquina virtual de servidor SharePoint, que usa elementos comuns criados anteriormente e elementos específicos da infraestrutura do Azure e executa um script do PowerShell para configurar o farm do SharePoint.

Seu próprio modelo JSON para criar uma infraestrutura de várias camadas no Azure deve seguir as mesmas etapas:

1.	Crie os elementos comuns (conta de armazenamento, rede virtual), específicos da camada (conjuntos de disponibilidade) e específicos da máquina virtual (endereços IP públicos, conjuntos de disponibilidade, interfaces de rede e instâncias do balanceador de carga) da infraestrutura do Azure que são necessários para sua implantação.
2.	Para cada camada em seu aplicativo (por exemplo, autenticação, banco de dados, Web), crie e configure os servidores nessa camada usando os elementos comuns (conta de armazenamento, rede virtual), específicos da camada (conjunto de disponibilidade) e específicos da máquina virtual (endereços IP públicos, interfaces de rede, instâncias do balanceador de carga).

Para obter mais informações, consulte [Linguagem de modelo do Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).

## Recursos adicionais

[Computação do Azure, Provedores de Rede e Armazenamento no Gerenciador de Recursos do Azure](virtual-machines-azurerm-versus-azuresm.md)

[Visão Geral do Gerenciador de Recursos do Azure](../resource-group-overview.md)

[Criando modelos do Gerenciador de Recursos do Azure](../resource-group-authoring-templates.md)

[Documentação de máquinas virtuais](http://azure.microsoft.com/documentation/services/virtual-machines/)

<!---HONumber=58--> 