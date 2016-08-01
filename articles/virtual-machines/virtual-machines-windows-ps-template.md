<properties
	pageTitle="Criar uma VM com o modelo do Resource Manager | Microsoft Azure"
	description="Use um modelo do Resource Manager e o PowerShell para criar facilmente uma nova máquina virtual do Windows."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2016"
	ms.author="davidmu"/>

# Criar uma máquina virtual do Windows com um modelo do Gerenciador de Recursos

Este artigo apresenta um modelo do Azure Resource Manager e mostra como usar o PowerShell para implantá-lo. O modelo implanta uma única máquina virtual que executa o Windows Server em uma nova rede virtual com uma única sub-rede.

Deve levar cerca de 20 minutos para executar as etapas neste artigo.

> [AZURE.IMPORTANT] Se você quiser que sua VM seja parte de um conjunto de disponibilidade, você precisará adicioná-la ao conjunto ao criar a VM. Atualmente não há uma maneira de adicionar uma VM a um conjunto de disponibilidade após ela ter sido criada.

## Etapa 1: Criar o arquivo do modelo

Você pode criar seu próprio modelo usando as informações encontradas em [Criando modelos do Azure Resource Manager](../resource-group-authoring-templates.md). Você também pode implantar modelos que foram criados para você de [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).

1. Abra seu editor de texto favorito e copie essas informações do JSON em um novo arquivo chamado *VirtualMachineTemplate.json*:

        {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": {
              "type": "string",
              "metadata": {
                "Description": "The name of the storage account where the VM disk is stored."
              }
            },
            "adminUsername": {
              "type": "string",
              "metadata": {
                "Description": "The name of the administrator account on the VM."
              }
            },
            "adminPassword": {
              "type": "securestring",
              "metadata": {
                "Description": "The administrator account password on the VM."
              }
            },
            "dnsNameForPublicIP": {
              "type": "string",
              "metadata": {
                "Description": "The name of the public IP address used to access the VM."
              }
            }
          },
          "variables": {
            "location": "Central US",
            "imagePublisher": "MicrosoftWindowsServer",
            "imageOffer": "WindowsServer",
            "windowsOSVersion": "2012-R2-Datacenter",
            "OSDiskName": "osdisk1",
            "nicName": "nc1",
            "addressPrefix": "10.0.0.0/16",
            "subnetName": "sn1",
            "subnetPrefix": "10.0.0.0/24",
            "storageAccountType": "Standard_LRS",
            "publicIPAddressName": "ip1",
            "publicIPAddressType": "Dynamic",
            "vmStorageAccountContainerName": "vhds",
            "vmName": "vm1",
            "vmSize": "Standard_A0",
            "virtualNetworkName": "vn1",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[parameters('newStorageAccountName')]",
              "apiVersion": "2015-06-15",
              "location": "[variables('location')]",
              "properties": {
                "accountType": "[variables('storageAccountType')]"
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "[variables('publicIPAddressName')]",
              "location": "[variables('location')]",
              "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                  "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "[variables('virtualNetworkName')]",
              "location": "[variables('location')]",
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
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "[variables('nicName')]",
              "location": "[variables('location')]",
              "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
              ],
              "properties": {
                "ipConfigurations": [
                  {
                    "name": "ipconfig1",
                    "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                      },
                      "subnet": {
                        "id": "[variables('subnetRef')]"
                      }
                    }
                  }
                ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "[variables('vmName')]",
              "location": "[variables('location')]",
              "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
                "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
              ],
              "properties": {
                "hardwareProfile": {
                  "vmSize": "[variables('vmSize')]"
                },
                "osProfile": {
                  "computername": "[variables('vmName')]",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "[variables('imagePublisher')]",
                    "offer": "[variables('imageOffer')]",
                    "sku" : "[variables('windowsOSVersion')]",
                    "version":"latest"
                  },
                  "osDisk" : {
                    "name": "osdisk",
                    "vhd": {
                      "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces": [
                    {
                      "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                    }
                  ]
                }
              }
            }
          ]
        }
        
    >[AZURE.NOTE] Este artigo cria uma máquina virtual executando uma versão do sistema operacional do Windows Server. Para saber mais sobre a seleção de outras imagens, veja [Navegar e selecionar imagens de máquina virtual do Azure com o Windows PowerShell e a CLI do Azure](virtual-machines-linux-cli-ps-findimage.md).
    
2. Salvar o arquivo de modelo.

## Etapa 2: Criar o arquivo de parâmetros

Para especificar valores para os parâmetros de recursos que foram definidos no modelo, você cria um arquivo de parâmetros que contém os valores e o envia ao Gerenciador de Recursos com o modelo.

1. No editor de texto, copie essas informações de JSON para um novo arquivo chamado *Parameters.json*:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "value": "mytestsa1" },
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" },
            "dnsNameForPublicIP": { "value": "mytestdns1" }
          }
        }

2. Salve o arquivo de parâmetros.

## Etapa 3: Instalar o Azure PowerShell

Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar a versão mais recente do Azure PowerShell, selecionar a assinatura que você deseja usar e entrar na sua conta do Azure.

## Etapa 4: Criar um grupo de recursos

Todos os recursos devem estar implantados em um grupo de recursos. Consulte a [Visão geral do Azure Resource Manager](../resource-group-overview.md) para obter mais informações.

1. Obtenha uma lista dos locais disponíveis nos quais os recursos podem ser criados.

	    Get-AzureRmLocation | sort DisplayName | Select DisplayName

2. Substitua o valor **$locName** por uma localização na lista, por exemplo, **EUA Central**. Crie a variável.

        $locName = "location name"
        
3. Substitua o valor **$rgName** pelo nome do novo grupo de recursos. Crie a variável e o grupo de recursos.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    Você deverá ver algo assim:
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1

### Etapa 5: Criar os recursos com o modelo e os parâmetros

1. Substitua o valor **$deployName** pelo nome da implantação. Substitua o valor de **$templatePath** pelo caminho e nome do arquivo do modelo. Substitua o valor de **$parameterFile** pelo caminho e nome do arquivo de parâmetros. Crie as variáveis.

        $deployName="deployment name"
        $templatePath = "template path"
        $parameterFile = "parameter file"

2. Implante o modelo.

        New-AzureRmResourceGroupDeployment -ResourceGroupName "davidmurg6" -TemplateFile $templatePath -TemplateParameterFile $parameterFile

    Você verá algo semelhante a:

        DeploymentName    : VirtualMachineTemplate
        ResourceGroupName : myrg1
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2016 8:11:37 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            newStorageAccountName  String                     mytestsa1
                            adminUsername    String                     mytestacct1
                            adminPassword    SecureString
                            dnsNameForPublicIP  String                     mytestdns1

        Outputs           :

    >[AZURE.NOTE] Você também pode implantar os modelos e os parâmetros de uma conta de armazenamento do Azure. Para saber mais, consulte [Usando o Azure PowerShell com o Armazenamento do Azure](../storage-powershell-guide-full.md).

## Próximas etapas

- Se houver problemas com a implantação, uma próxima etapa será examinar [Solucionando os problemas de implantações do grupo de recursos com o Portal do Azure](../resource-manager-troubleshoot-deployments-portal.md)
- Saiba como gerenciar a máquina virtual que você acabou de criar examinando [Gerenciar as máquinas virtuais usando o Azure Resource Manager e o PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0720_2016-->