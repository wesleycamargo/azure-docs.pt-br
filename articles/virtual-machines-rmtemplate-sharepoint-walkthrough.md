<properties 
	pageTitle="O modelo do Gerenciador de Recursos de farm do SharePoint de alta disponibilidade" 
	description="Percorra a estrutura do modelo do Gerenciador de Recursos do Azure para o farm do SharePoint de alta disponibilidade." 
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

# O modelo do Gerenciador de Recursos de farm do SharePoint de alta disponibilidade

Este tópico o orienta quanto à estrutura do arquivo de modelo azuredeploy.json para o farm do SharePoint de alta disponibilidade.

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

	"RDPNAT":"RDP", 
	"spWebLB":"spWeb", 
	"SQLAOListener":"SQLAlwaysOnEndPointListener", 
	"SQLAOProbe":"SQLAlwaysOnEndPointProbe", 
	"staticSubnetName": "staticSubnet", 
	"sqlSubnetName": "sqlSubnet", 
	"spwebSubnetName": "spwebSubnet", 
	"spappSubnetName": "spappSubnet", 

## Seção "recursos"

A seção **"recursos"** especifica as informações necessárias para implantar os recursos para o farm do SharePoint em um grupo de recursos. Podem ser definidos no máximo 250 recursos, e as dependências de recursos só podem ser definidas com cinco níveis de profundidade.

Essa seção contém os seguintes procedimentos:

### Microsoft.Storage/storageAccounts  

Essa seção cria uma nova conta de armazenamento para todos os recursos de disco e VHD para o farm. Aqui está o código JSON para a conta de armazenamento:

	{
	  "type": "Microsoft.Storage/storageAccounts",
	  "name": "[parameters('newStorageAccountName')]",
	  "apiVersion": "2014-12-01-preview",
	  "location": "[parameters('deploymentLocation')]",
	  "properties": {
		"accountType": "[parameters('storageAccountType')]"
	  }
	},

### Microsoft.Network/publicIPAddresses

Essas seções criam um conjunto de endereços IP públicos por meio dos quais cada máquina virtual pode ser conectada através da Internet. Aqui está um exemplo:

	{
		"apiVersion": "2014-12-01-preview",
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

Essas seções criam quatro conjuntos de disponibilidade, um para cada camada da implantação:

- Controladores de domínio do Active Directory
- Cluster do SQL Server
- Servidores da camada de aplicativo
- Servidores da camada da Web

Aqui está um exemplo:

	{
		"type": "Microsoft.Compute/availabilitySets",
		"name": "[variables('spAvailabilitySetName')]",
		"apiVersion": "2014-12-01-preview",
		"location": "[parameters('deploymentLocation')]"
	},

### Microsoft.Network/virtualNetworks

Essa seção cria uma rede virtual somente em nuvem com quatro sub-redes (um para cada camada da implantação), em que as máquinas virtuais são colocadas. Aqui está o código JSON:

	{
		"name": "[parameters('virtualNetworkName')]",
		"type": "Microsoft.Network/virtualNetworks",
		"location": "[parameters('deploymentLocation')]",
		"apiVersion": "2014-12-01-preview",
		"properties": {
			"addressSpace": {
			"addressPrefixes": [
				"[parameters('virtualNetworkPrefix')]"
			]
			},
			"subnets": "[variables('subnets')]"
		}
	},



### Microsoft.Network/loadBalancers

Essas seções criam instâncias do balanceador de carga para cada máquina virtual para fornecer filtragem de tráfego e NAT para o tráfego de entrada da Internet. Para cada balanceador de carga, as configurações definem regras de NAT de entrada, back-end e front-end. Por exemplo, há regras de tráfego de Área de Trabalho Remota para cada máquina virtual e uma regra para permitir o tráfego da Web de entrada (porta TCP 80) da Internet para os servidores da camada de Web. Aqui está um exemplo para o servidor da camada da Web:

        {
            "apiVersion": "2014-12-01-preview",
            "name": "[variables('splbName')]",
            "type": "Microsoft.Network/loadBalancers",
            "location": "[parameters('deploymentLocation')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses',variables('spIPAddressName'))]"
            ],
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "[variables('spLBFE')]",
                        "properties": {
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('spIPAddressName'))]"
                            },
                        }
                    }
                ],
                "backendAddressPools": [
                    {
                        "name": "[variables('spWebLBBE')]"
                    }
                ],
                "loadBalancingRules": [
                    {
                        "name": "[variables('spWebLB')]",
                        "properties": {
                        "frontendIPConfiguration": {
                            "id": "[variables('splbFEConfigID')]"
                        },
                        "probe": {
                            "id": "[variables('spwebProbeID')]"
                        },
                        "protocol": "tcp",
                        "frontendPort": 80,
                        "backendPort": 80,
                        "enableFloatingIP": false
                        }
                    }
                ],
                "probes": [
                    {
                        "name": "[variables('spWebProbe')]",
                        "properties": {
                            "protocol": "http",
                            "port": "[variables('spWebProbePort')]",
                            "intervalInSeconds": "15",
                            "numberOfProbes": "5",
                            "requestPath":"/"
                        }
                    }
                ]
            }
        },

### Microsoft.Network/networkInterfaces

Essas seções criam uma interface de rede para cada máquina virtual e configuram endereços IP estáticos para os controladores de domínio. Aqui está o exemplo da interface de rede para o controlador de domínio primário:

	{
		"name": "[variables('adPDCNicName')]",
		"type": "Microsoft.Network/networkInterfaces",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[parameters('virtualNetworkName')]",
			"[concat('Microsoft.Network/loadBalancers/',variables('rdpLBName'))]"
		],
		"apiVersion": "2014-12-01-preview",
		"properties": {
			"ipConfigurations": [
				{
					"name": "ipconfig1",
					"properties": {
						"privateIPAllocationMethod": "Static",
						"privateIPAddress" :"[parameters('adPDCNicIPAddress')]",
						"subnet": {
							"id": "[variables('staticSubnetRef')]"
						},
						"loadBalancerBackendAddressPools": [
							{
								"id":"[variables('adBEAddressPoolID')]"
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

Essas seções criam e configuram as nove máquinas virtuais na implantação.

As duas primeiras seções principais criam e configuram os controladores de domínio na implantação. Cada seção:

- Especifica a conta de armazenamento, o conjunto de disponibilidade, a interface de rede e a instância do balanceador de carga para cada máquina virtual do controlador de domínio
- Adiciona um disco extra a cada máquina virtual do controlador de domínio
- Executa o script do PowerShell para configurar as máquinas virtuais como controladores de domínio

Aqui está o exemplo para o controlador de domínio primário:

        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('adPDCVMName')]",
            "location": "[parameters('deploymentLocation')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts',parameters('newStorageAccountName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces',variables('adPDCNicName'))]",
                "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]",
                "[resourceId('Microsoft.Network/loadBalancers',variables('rdpLBName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('adVMSize')]"
                },
                "availabilitySet": {
                    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]"
                },
                "osProfile": {
                    "computername": "[variables('adPDCVMName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]",
                    "windowsProfile": {
                        "provisionVMAgent": "true"
                    }
                },
                "storageProfile": {
                    "sourceImage": {
                        "id": "[variables('adSourceImageName')]"
                    },
                    "destinationVhdsContainer": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/')]",
                    "dataDisks": [
                        {
                            "vhd": {
                                "uri":"[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/', variables('adPDCVMName'),'data-1.vhd')]"
                                },
                            "name":"[concat(variables('adPDCVMName'),'-data-disk1')]",
                            "caching" : "None",
                            "diskSizeGB": "[variables('adDataDiskSize')]",
                            "lun": 0
                        }
                    ]
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('adPDCNicName'))]"
                        }
                    ]
                }
            },
            "resources" :[
                {
                    "type": "Microsoft.Compute/virtualMachines/extensions",
                    "name": "[concat(variables('adPDCVMName'),'/InstallDomainController')]",
                    "apiVersion": "2014-12-01-preview",
                    "location": "[parameters('deploymentLocation')]",
                    "dependsOn":[
                        "[resourceId('Microsoft.Compute/virtualMachines', variables('adPDCVMName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "1.7",
                        "settings": {
                            "ModulesUrl": "[variables('adPDCModulesURL')]",
                            "ConfigurationFunction": "[variables('adPDCConfigurationFunction')]",
                            "Properties": {
                                "DomainName": "[parameters('domainName')]",
                                "AdminCreds":{
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


Uma seção adicional após cada controlador de domínio começando com **"nome": "UpdateVNetDNS"** configura os servidores DNS da rede virtual para usar os endereços IP estáticos dos dois controladores de domínio.

As próximas três seções **"tipo": "Microsoft.Compute/virtualMachines"** criam as máquinas virtuais do cluster do SQL Server na implantação. Cada seção:

- Especifica a conta de armazenamento, o conjunto de disponibilidade, o balanceador de carga, a rede virtual e a interface de rede para cada máquina virtual
- Adiciona dois discos extras para cada SQL Server

Seções adicionais **"Microsoft.Compute/virtualMachines/extensions"** chamam o script do PowerShell para configurar as máquinas virtuais do cluster do SQL Server e o cluster do SQL Server e habilitar Grupos de Disponibilidade do AlwaysOn.

As próximas quatro seções **"tipo": "Microsoft.Compute/virtualMachines"** criam as máquinas virtuais do farm do SharePoint na implantação. Cada seção especifica a conta de armazenamento, o conjunto de disponibilidade, o balanceador de carga, a rede virtual e a interface de rede para cada máquina virtual.

Seções adicionais **"Microsoft.Compute/virtualMachines/extensions"** chamam scripts do PowerShell para configurar os servidores do SharePoint como um farm do SharePoint.

Observe a organização geral das subseções da seção **"recursos"** do arquivo JSON:

1.	Crie os elementos da infraestrutura do Azure que são necessários para dar suporte a várias máquinas virtuais (uma conta de armazenamento, endereços IP públicos, conjuntos de disponibilidade, uma rede virtual, interfaces de rede, instâncias de balanceador de carga).
2.	Crie as máquinas virtuais do controlador de domínio, que usam com os elementos comuns e específicos criados anteriormente de infraestrutura do Azure, adicione discos de dados e execute scripts do PowerShell. Além disso, atualize a rede virtual para usar os endereços IP estáticos dos controladores de domínio.
3.	Crie as máquinas virtuais do cluster do SQL Server, que usam com os elementos comuns e específicos criados anteriormente da infraestrutura do Azure criados para os controladores de domínio, adicione discos de dados e execute scripts do PowerShell para configurar o cluster e os Grupos de Disponibilidade do AlwaysOn do SQL Server.
4.	Crie as máquinas virtuais de servidor do SharePoint que usam os elementos comuns e específicos criados anteriormente da infraestrutura do Azure, adicione discos de dados e execute scripts do PowerShell para configurar o farm do SharePoint.

Seu próprio modelo JSON para criar uma infraestrutura de várias camadas no Azure deve seguir as mesmas etapas:

1.	Crie os elementos comuns (conta de armazenamento, rede virtual), específicos da camada (conjuntos de disponibilidade) e específicos da máquina virtual (endereços IP públicos, conjuntos de disponibilidade, interfaces de rede e instâncias do balanceador de carga) da infraestrutura do Azure que são necessários para sua implantação.
2.	Para cada camada em seu aplicativo (por exemplo, autenticação, banco de dados, Web), crie e configure os servidores nessa camada usando os elementos comuns (conta de armazenamento, rede virtual), específicos da camada (conjunto de disponibilidade) e específicos da máquina virtual (endereços IP públicos, interfaces de rede, instâncias do balanceador de carga).

Para obter mais informações, consulte [Linguagem de modelo do Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).


## Recursos adicionais

[Computação do Azure, Provedores de Rede e Armazenamento no Gerenciador de Recursos do Azure](virtual-machines-azurerm-versus-azuresm.md)

[Visão Geral do Gerenciador de Recursos do Azure](resource-group-overview.md)

[Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md)

[Documentação de máquinas virtuais](http://azure.microsoft.com/documentation/services/virtual-machines/)


<!--HONumber=52-->
