---
title: Exemplo do PowerShell - criar uma instância gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Script de exemplo do Azure PowerShell para criar uma instância gerenciada do Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop-msft
ms.reviewer: ''
manager: craigg
ms.date: 01/17/2019
ms.openlocfilehash: bbb22f8d5eab3e60eba13bb642edb8f8d190cf67
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54387955"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-an-azure-sql-database-managed-instance"></a>Use o PowerShell com o modelo do Azure Resource Manager para criar uma instância gerenciada do Banco de Dados SQL do Azure

A instância gerenciada do Banco de Dados SQL do Azure pode ser criada usando a biblioteca do PowerShell do Azure e os modelos do Azure Resource Manager. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 5.7.0 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzureRmAccount` para criar uma conexão com o Azure.

Os comandos do PowerShell do Azure podem começar a implantação usando o modelo predefinido do Azure Resource Manager. As propriedades a seguir podem ser especificadas no modelo:
- Nome da instância
- Nome de usuário do administrador do SQL e senha. 
- Tamanho da instância (número de núcleos e o tamanho máximo de armazenamento).
- Rede virtual e sub-rede onde a instância será colocada.
- Agrupamento de nível de servidor da instância (versão prévia).

O nome da instância, o nome de usuário do administrador do SQL, a rede virtual/sub-rede e o agrupamento não podem ser alterados posteriormente. As outras propriedades da instância podem ser alteradas.

## <a name="prerequisites"></a>Pré-requisitos

Este exemplo pressupõe que você tenha [criado um ambiente de rede válido](../sql-database-managed-instance-create-vnet-subnet.md) ou [modificado a VNet existente](../sql-database-managed-instance-configure-vnet-subnet.md) para sua Instância Gerenciada. O exemplo usa os commandlets [New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) e [Get-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetwork), portanto verifique se você instalou os seguintes módulos do PowerShell:

```
Install-Module AzureRM.Network
Install-Module AzureRM.Resources
```

## <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

O conteúdo a seguir deve ser colocado em um arquivo que representa um modelo que será usado para criar a instância:
```
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "string"
        },
        "user": {
            "type": "string"
        },
        "pwd": {
            "type": "securestring"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('instance')]",
            "location": "West Central US",
            "tags": {
                "Description":"GP Instance with custom instance collation - Serbian_Cyrillic_100_CS_AS"
            },          
            "sku": {
                "name": "GP_Gen4",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen4", 
                "collation": "Serbian_Cyrillic_100_CS_AS"
            },
            "type": "Microsoft.Sql/managedInstances",
            "identity": {
                "type": "SystemAssigned"
            },
            "apiVersion": "2015-05-01-preview"
        }
    ]
}
```
Pressupõe-se que já existe uma rede virtual do Azure com a sub-rede configurada corretamente. Caso contrário, prepare o ambiente de rede usando o [modelo gerenciado de recursos do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) separado, que pode ser executado de forma independente ou incluído nesse modelo.

Salve o conteúdo desse arquivo como arquivo .json, coloque o caminho para o arquivo no script do PowerShell a seguir e altere os nomes dos objetos no script: 

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzureRmSubscription -SubscriptionId $subscriptionId

# Managed Instance properties
$resourceGroup = "rg_mi"
$location = "West Central US"
$name = "managed-instance-name"
$user = "miSqlAdmin"
$secpasswd = ConvertTo-SecureString "<Put some strong password here>" -AsPlainText -Force

# Network configuration
$vNetName = "my_vnet"
$vNetResourceGroup = "rg_mi_vnet"
$subnetName = "ManagedInstances"
$vNet = Get-AzureRmVirtualNetwork -Name $vNetName -ResourceGroupName $vNetResourceGroup
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$subnetId = $subnet.Id

# Deploy Instance using Azure Resource Manager template:
New-AzureRmResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```
Depois que o script tiver sido executado com êxito, o Banco de Dados SQL poderá ser acessado de todos os serviços do Azure e o endereço IP configurado. 

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Outros exemplos de script do PowerShell para Banco de Dados SQL podem ser encontrados nos [scripts do PowerShell para Banco de Dados SQL do Azure](../sql-database-powershell-samples.md).



