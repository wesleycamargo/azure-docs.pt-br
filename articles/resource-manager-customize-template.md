<properties
	pageTitle="Personalizar o modelo do Gerenciador de Recursos exportado | Microsoft Azure"
	description="Adicione parâmetros a um modelo do Azure Resource Manager exportado e reimplante-o por meio do Azure PowerShell ou da CLI do Azure."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/01/2016"
	ms.author="tomfitz"/>

# Personalizar um modelo exportado do Azure Resource Manager

Este artigo mostra como modificar um modelo exportado para que você possa passar valores adicionais como parâmetros. Ele se baseia nas etapas executadas no artigo [Exportar modelo do Resource Manager](resource-manager-export-template.md), mas não é essencial que você conclua esse artigo primeiro. Você pode encontrar o modelo e os scripts requeridos no artigo.

## Exibir um modelo exportado

Se você concluiu o [Exportar modelo do Gerenciador de Recursos](resource-manager-export-template.md), abra o modelo que baixou. O modelo é denominado **template.json**. Se você tiver o Visual Studio ou o Visual Code, poderá usar qualquer um para editar o modelo. Caso contrário, poderá usar qualquer editor de texto ou editor JSON.

Se você não tiver concluído o passo a passo anterior, crie um arquivo chamado **template.json** e adicione o conteúdo do modelo exportado a seguir para o arquivo.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
        },
        "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "comments": "Generalized from resource in walkthrough.",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetworks_VNET_name')]",
            "apiVersion": "2015-06-15",
            "location": "northeurope",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "10.0.0.0/16"
                    ]
                },
                "subnets": [
                    {
                        "name": "default",
                        "properties": {
                            "addressPrefix": "10.0.0.0/24"
                        }
                    }
                ]
            },
            "dependsOn": []
        },
        {
            "comments": "Generalized from resource in walkthrough.",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccounts_storagetf05092016_name')]",
            "apiVersion": "2015-06-15",
            "location": "northeurope",
            "tags": {},
            "properties": {
                "accountType": "Standard_RAGRS"
            },
            "dependsOn": []
        }
    ]
}
```

O modelo template.json acima funcionará bem se você quiser criar o mesmo tipo de conta de armazenamento na mesma região com uma rede virtual que usa os mesmos prefixos do endereço e da sub-rede para cada implantação. No entanto, o Resource Manager fornece opções para que você possa implantar modelos com muito mais flexibilidade. Por exemplo, durante a implantação, talvez você queira especificar o tipo de conta de armazenamento a criar ou os valores a usar para os prefixos do endereço e da sub-rede da rede virtual.

## Personalizar o modelo

Nesta seção, você adicionará parâmetros ao modelo exportado para poder reutilizar o modelo ao implantar esses recursos em outros ambientes. Você também adicionará alguns recursos ao modelo que reduzem a probabilidade de encontrar um erro ao implantá-lo. Você não terá mais que adivinhar um nome exclusivo para sua conta de armazenamento. Ao contrário, o modelo criará um nome exclusivo. Você restringirá os valores que podem ser especificados para o tipo da conta de armazenamento apenas para as opções válidas.

1. Para habilitar a passagem dos valores que talvez você queira especificar durante a implantação, substitua a seção **parâmetros** pelas seguintes definições de parâmetro. Observe os valores de **allowedValues** para **storageAccount\_accountType**. Se você fornecer acidentalmente um valor inválido, esse erro será reconhecido antes da implantação iniciar. E mais: observe também que você está fornecendo apenas um prefixo para o nome da conta de armazenamento e o prefixo é limitado a 11 caracteres. Ao limitar o prefixo a 11 caracteres, você garante que o nome completo não excederá o número máximo de caracteres para uma conta de armazenamento. O prefixo permite que você aplique uma convenção de nomenclatura em suas contas de armazenamento. Você verá como criar um nome exclusivo na próxima etapa.

        "parameters": {
          "storageAccount_prefix": {
            "type": "string",
            "maxLength": 11
          },
          "storageAccount_accountType": {
            "defaultValue": "Standard_RAGRS",
            "type": "string",
            "allowedValues": [
              "Standard_LRS",
              "Standard_ZRS",
              "Standard_GRS",
              "Standard_RAGRS",
              "Premium_LRS"
            ]
          },
          "virtualNetwork_name": {
            "type": "string"
          },
          "addressPrefix": {
            "defaultValue": "10.0.0.0/16",
            "type": "string"
          },
          "subnetName": {
            "defaultValue": "subnet-1",
            "type": "string"
          },
          "subnetAddressPrefix": {
            "defaultValue": "10.0.0.0/24",
            "type": "string"
          }
        },

2. A seção **variáveis** do modelo está vazia atualmente. Substitua essa seção pelo código a seguir. Na seção **variáveis**, você, como autor do modelo, pode criar valores que simplificam a sintaxe para o resto do modelo. A variável **storageAccount\_name** concatena o prefixo do parâmetro a uma cadeia de caracteres exclusiva gerada com base no identificador do grupo de recursos.

        "variables": {
          "storageAccount_name": "[concat(parameters('storageAccount_prefix'), uniqueString(resourceGroup().id))]"
        },

3. Para usar os parâmetros e a variável nas definições do recurso, substitua a seção **recursos** pelas definições a seguir. Observe que muito pouco realmente mudou nas definições do recurso além do valor que é atribuído à propriedade do recurso. As propriedades são exatamente iguais às propriedades do modelo exportado. Você está simplesmente atribuindo propriedades aos valores do parâmetro, em vez de valores codificados. O local dos recursos é definido para usar o mesmo local do grupo de recursos por meio da expressão **resourceGroup ().location**. A variável criada para o nome da conta de armazenamento é referenciada por meio da expressão **variables**.

        "resources": [
          {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetwork_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "properties": {
              "addressSpace": {
                "addressPrefixes": [
                  "[parameters('addressPrefix')]"
                ]
              },
              "subnets": [
                {
                  "name": "[parameters('subnetName')]",
                  "properties": {
                    "addressPrefix": "[parameters('subnetAddressPrefix')]"
                  }
                }
              ]
            },
            "dependsOn": []
          },
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccount_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "tags": {},
            "properties": {
                "accountType": "[parameters('storageAccount_accountType')]"
            },
            "dependsOn": []
          }
        ]

## Corrigir o arquivo de parâmetros

O arquivo de parâmetros baixado não corresponde mais aos parâmetros em seu modelo. Você não precisa usar um arquivo de parâmetro, mas ele pode simplificar o processo na reimplantação de um ambiente. Você usará os valores padrão definidos no modelo para muitos parâmetros, de modo que o arquivo de parâmetros só precise de dois valores.

Substitua o conteúdo do arquivo parameters.json por:

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccount_prefix": {
      "value": "storage"
    },
    "virtualNetwork_name": {
      "value": "VNET"
    }
  }
}
```

O arquivo de parâmetros atualizado fornece apenas os valores para os parâmetros que não têm um valor padrão. Você poderá fornecer valores para outros parâmetros quando quiser que um valor seja diferente do valor padrão.

## Implantar o modelo

Você pode usar o Azure PowerShell ou a CLI (interface de linha de comando) do Azure para implantar o modelo personalizado e os arquivos de parâmetro. Se necessário, instale o [Azure PowerShell](powershell-install-configure.md) ou a [CLI do Azure](xplat-cli-install.md). Você poderá usar os scripts baixados com seu modelo quando tiver exportado o modelo original ou poderá escrever seu próprio script para implantar o modelo. As duas opções aparecem neste artigo.

2. Para implantar usando seu próprio script, use um dos procedimentos a seguir.

     Para o PowerShell, execute:

        # login
        Add-AzureRmAccount

        # create a new resource group
        New-AzureRmResourceGroup -Name ExportGroup -Location "West Europe"

        # deploy the template to the resource group
        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExportGroup -TemplateFile {path-to-file}\template.json -TemplateParameterFile {path-to-file}\parameters.json

     Para a CLI do Azure, execute:

        azure login

        azure group create -n ExportGroup -l "West Europe"

        azure group deployment create -f {path-to-file}\azuredeploy.json -e {path-to-file}\parameters.json -g ExportGroup -n ExampleDeployment

3. Se você baixou o modelo exportado e scripts, encontre o arquivo **deploy. ps1** (para o PowerShell) ou o arquivo **deploy.sh** (para a CLI do Azure).

     Para o PowerShell, execute:

        Get-Item deploy.ps1 | Unblock-File

        .\deploy.ps1

     Para a CLI do Azure, execute:

        .\deploy.sh

## Próximas etapas

- O [Passo a Passo do Modelo do Resource Manager](resource-manager-template-walkthrough.md) baseia-se no que você aprendeu neste artigo, criando um modelo para uma solução mais complicada. Ele ajuda você a saber mais sobre os recursos disponíveis e como determinar quais valores fornecer.
- Para ver como exportar um modelo por meio do PowerShell, consulte [Usando o Azure PowerShell com o Azure Resource Manager](powershell-azure-resource-manager.md).
- Para ver como exportar um modelo por meio da CLI do Azure, consulte [Usar a CLI do Azure para Mac, Linux e Windows com o Azure Resource Manager](xplat-cli-azure-resource-manager.md).
- Para saber mais sobre como os modelos são estruturados, consulte [Criando modelos do Azure Resource Manager](resource-group-authoring-templates.md).

<!---HONumber=AcomDC_0803_2016-->