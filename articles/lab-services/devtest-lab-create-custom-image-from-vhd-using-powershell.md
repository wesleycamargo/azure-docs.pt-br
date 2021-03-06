---
title: Criar uma imagem personalizada do Azure DevTest Labs de um arquivo VHD usando o PowerShell | Microsoft Docs
description: Automatizar a criação de uma imagem personalizada no Azure DevTest Labs de um arquivo VHD usando o PowerShell
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: dc6e218fe048e1781f53c53935308eb193fcd094
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487151"
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>Criar uma imagem personalizada de um arquivo VHD usando o PowerShell

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="step-by-step-instructions"></a>Instruções passo a passo

As seguintes etapas orientam você durante a criação de uma imagem personalizada de um arquivo VHD usando o PowerShell:

1. Em um prompt do PowerShell, faça logon sua conta do Azure com a seguinte chamada para o **AzAccount Connect** cmdlet.  
    
    ```powershell
    Connect-AzAccount
    ```

1.  Selecione a assinatura do Azure desejada chamando o **AzSubscription selecione** cmdlet. Substitua o espaço reservado a seguir para a variável **$subscriptionId** por uma ID de assinatura válida do Azure. 

    ```powershell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzSubscription -SubscriptionId $subscriptionId
    ```

1.  Obtém o objeto de laboratório chamando o **Get-AzResource** cmdlet. Substitua os espaços reservados a seguir para as variáveis **$labRg** e **$labName** pelos valores apropriados para seu ambiente. 

    ```powershell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```
 
1.  Obtenha os valores da conta de armazenamento e da chave da conta de armazenamento do laboratório do objeto de laboratório. 

    ```powershell
    $labStorageAccount = Get-AzResource -ResourceId $lab.Properties.defaultStorageAccount 
    $labStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $labStorageAccount.ResourceGroupName -Name $labStorageAccount.ResourceName)[0].Value
    ```

1.  Substitua o espaço reservado a seguir da variável **$vhdUri** pelo URI para seu arquivo VHD carregado. Você pode obter o URI do arquivo VHD na folha de blob da conta de armazenamento no Portal do Azure.

    ```powershell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  Criar a imagem personalizada usando o **New-AzResourceGroupDeployment** cmdlet. Substitua os espaços reservados a seguir para as variáveis **$customImageName** e **$customImageDescription** por nomes significativos para seu ambiente.

    ```powershell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/Samples/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>Script do PowerShell para criar uma imagem personalizada de um arquivo VHD

O script do PowerShell a seguir pode ser usado para criar uma imagem personalizada de um arquivo VHD. Substitua os espaços reservados (que começam e terminam com colchetes angulares) pelos valores apropriados às suas necessidades. 

```powershell
# Log in to your Azure account.  
Connect-AzAccount

# Select the desired Azure subscription. 
$subscriptionId = '<Specify your subscription ID here>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the lab storage account and lab storage account key values.
$labStorageAccount = Get-AzResource -ResourceId $lab.Properties.defaultStorageAccount 
$labStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $labStorageAccount.ResourceGroupName -Name $labStorageAccount.ResourceName)[0].Value

# Set the URI of the VHD file.  
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image. 
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/Samples/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>Postagens de blogs relacionadas

- [Imagens personalizadas ou fórmulas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copiar imagens personalizadas entre Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Próximas etapas

- [Adicionar uma VM ao laboratório](devtest-lab-add-vm.md)
