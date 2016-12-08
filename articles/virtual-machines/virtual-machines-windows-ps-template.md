---
title: Criar uma VM com o modelo do Resource Manager | Microsoft Docs
description: "Use um modelo do Resource Manager e o PowerShell para criar facilmente uma nova máquina virtual do Windows."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/06/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: f0112aafb4930e475b2cb8bf399110e480381b95


---
# <a name="create-a-windows-virtual-machine-with-a-resource-manager-template"></a>Criar uma máquina virtual do Windows com um modelo do Gerenciador de Recursos
Este artigo apresenta um modelo do Azure Resource Manager e mostra como usar o PowerShell para implantá-lo. O modelo implanta uma única máquina virtual que executa o Windows Server em uma nova rede virtual com uma única sub-rede.

Deve levar cerca de 20 minutos para executar as etapas neste artigo.

> [!IMPORTANT]
> Se você quiser que sua VM seja parte de um conjunto de disponibilidade, adicione-a ao conjunto ao criá-la. Atualmente, não há uma maneira de adicionar uma VM a um conjunto de disponibilidade após ela ter sido criada.
> 
> 

## <a name="step-1-create-the-template-file"></a>Etapa 1: Criar o arquivo do modelo
Você pode criar seu próprio modelo usando as informações encontradas em [Criando modelos do Azure Resource Manager](../resource-group-authoring-templates.md). Você também pode implantar modelos que foram criados para você de [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).

1. Abra o editor de texto da sua preferência e adicione o devido elemento do esquema e o elemento contentVersion:

  ```json  
  {
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  }
  ```

2. [Parâmetros](../resource-group-authoring-templates.md#parameters) nem sempre são necessários, mas eles fornecem uma maneira de entrar valores quando o modelo é implantado. Adicione o elemento parameters e seus elementos filho após o elemento contentVersion:

  ```json   
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "adminUserName": { "type": "string" },
      "adminPassword": { "type": "securestring" }
    },
  }
  ```

3. [Variáveis](../resource-group-authoring-templates.md#variables) podem ser usadas em um modelo para especificar valores que podem ser alterados com frequência ou que precisam ser criados com base em uma combinação de valores de parâmetros. Adicione o elemento variables após a seção de parâmetros:

  ```json   
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "adminUsername": { "type": "string" },
      "adminPassword": { "type": "securestring" }
    },
    "variables": {
      "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
      "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
    },
  }
  ```

4. [Recursos](../resource-group-authoring-templates.md#resources) como a máquina virtual, a rede virtual e a conta de armazenamento são definidos no modelo em seguida. Adicione a seção de recursos após a seção de variáveis:

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "adminUsername": { "type": "string" },
      "adminPassword": { "type": "securestring" }
    },
    "variables": {
      "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
      "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
    },
    "resources": [
      {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "mystorage1",
        "apiVersion": "2015-06-15",
        "location": "[resourceGroup().location]",
        "properties": { "accountType": "Standard_LRS" }
      },
      {
        "apiVersion": "2016-03-30",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "myip1",
        "location": "[resourceGroup().location]",
        "properties": {
          "publicIPAllocationMethod": "Dynamic",
          "dnsSettings": { "domainNameLabel": "mydns1" }
        }
      },
      {
        "apiVersion": "2016-03-30",
        "type": "Microsoft.Network/virtualNetworks",
        "name": "myvn1",
        "location": "[resourceGroup().location]",
        "properties": {
          "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
          "subnets": [ {
            "name": "mysn1",
            "properties": { "addressPrefix": "10.0.0.0/24" }
          } ]
        }
      },
      {
        "apiVersion": "2016-03-30",
        "type": "Microsoft.Network/networkInterfaces",
        "name": "mync1",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "Microsoft.Network/publicIPAddresses/myip1",
          "Microsoft.Network/virtualNetworks/myvn1"
        ],
        "properties": {
          "ipConfigurations": [ {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
              },
              "subnet": { "id": "[variables('subnetRef')]" }
            }
          } ]
        }
      },
      {
        "apiVersion": "2016-03-30",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "myvm1",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "Microsoft.Network/networkInterfaces/mync1",
          "Microsoft.Storage/storageAccounts/mystorage1"
        ],
        "properties": {
          "hardwareProfile": { "vmSize": "Standard_A1" },
          "osProfile": {
            "computerName": "myvm1",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
          },
          "storageProfile": {
            "imageReference": {
              "publisher": "MicrosoftWindowsServer",
              "offer": "WindowsServer",
              "sku": "2012-R2-Datacenter",
              "version" : "latest"
            },
            "osDisk": {
              "name": "myosdisk1",
              "vhd": {
                "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
              },
              "caching": "ReadWrite",
              "createOption": "FromImage"
            }
          },
          "networkProfile": {
            "networkInterfaces" : [ {
              "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
            } ]
          }
        }
      } ]
    }
  ```
    
  > [!NOTE]
  > Este artigo cria uma máquina virtual executando uma versão do sistema operacional do Windows Server. Para saber mais sobre a seleção de outras imagens, veja [Navegar e selecionar imagens de máquina virtual do Azure com o Windows PowerShell e a CLI do Azure](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

5. Salve o arquivo de modelo como *VirtualMachineTemplate.json*.

## <a name="step-2-create-the-parameters-file"></a>Etapa 2: Criar o arquivo de parâmetros
Para especificar valores para os parâmetros dos recursos que foram definidos no modelo, crie um arquivo de parâmetros contendo os valores usados na implantação do modelo.

1. No editor de texto, copie esse conteúdo do JSON em um novo arquivo chamado *Parameters.json*:

  ```json 
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }
  ```
   
   > [!NOTE]
   > Veja mais sobre os [requisitos de nome de usuário e senha](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm).
   > 
   > 
2. Salve o arquivo de parâmetros.

## <a name="step-3-install-azure-powershell"></a>Etapa 3: Instalar o Azure PowerShell
Confira [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar a versão mais recente do Azure PowerShell, selecionar a assinatura e entrar em sua conta.

## <a name="step-4-create-a-resource-group"></a>Etapa 4: Criar um grupo de recursos
Todos os recursos devem estar implantados em um [grupo de recursos](../azure-resource-manager/resource-group-overview.md).

1. Obtenha uma lista dos locais disponíveis nos quais os recursos podem ser criados.
   
  ```powershell   
  Get-AzureRmLocation | sort DisplayName | Select DisplayName
  ```
2. Substitua o valor **$locName** por uma localização na lista, por exemplo, **EUA Central**. Crie a variável.

  ```powershell   
  $locName = "location name"
  ```

3. Substitua o valor **$rgName** pelo nome do novo grupo de recursos. Crie a variável e o grupo de recursos.

  ```powershell   
  $rgName = "resource group name"
  New-AzureRmResourceGroup -Name $rgName -Location $locName
  ```
   
  Você deverá ver algo como este exemplo:

  ```powershell
  ResourceGroupName : myrg1
  Location          : centralus
  ProvisioningState : Succeeded
  Tags              :
  ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1
  ```

## <a name="step-5-create-the-resources-with-the-template-and-parameters"></a>Etapa 5: Criar os recursos com o modelo e os parâmetros
Substitua o valor **$templateFile** pelo caminho e nome do arquivo de modelo. Substitua o valor **$parameterFile** pelo caminho e nome do arquivo de parâmetros. Crie as variáveis e, em seguida, implante o modelo. 

```powershell
$templateFile = "template file"
$parameterFile = "parameter file"
New-AzureRmResourceGroupDeployment -ResourceGroupName $rgName -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

Você verá algo semelhante a:

```powershell
DeploymentName    : VirtualMachineTemplate
ResourceGroupName : myrg1
ProvisioningState : Succeeded
Timestamp         : 4/14/2016 8:11:37 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     mytestacct1
                    adminPassword    SecureString

Outputs           :
```

> [!NOTE]
> Você também pode implantar os modelos e os parâmetros de uma conta de armazenamento do Azure. Para saber mais, consulte [Usando o Azure PowerShell com o Armazenamento do Azure](../storage/storage-powershell-guide-full.md).
> 
> 

## <a name="next-steps"></a>Próximas etapas
* Se houver problemas com a implantação, uma próxima etapa será examinar [Solucionando problemas de implantações do grupo de recursos com o portal do Azure](../resource-manager-troubleshoot-deployments-portal.md)
* Saiba como gerenciar a máquina virtual que você criou examinando [Gerenciar Máquinas Virtuais usando o Azure Resource Manager e o PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Nov16_HO3-->


