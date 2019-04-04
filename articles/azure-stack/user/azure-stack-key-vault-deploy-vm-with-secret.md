---
title: Implantar uma VM com uma senha armazenada com segurança no Azure Stack | Microsoft Docs
description: Saiba como implantar uma VM usando uma senha armazenada no cofre de chaves do Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 3318e52b29723eaa08d8c3a4fba18e278e6cfe9c
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487746"
---
# <a name="create-a-virtual-machine-using-a-secure-password-stored-in-azure-stack-key-vault"></a>Criar uma máquina virtual usando uma senha segura armazenada no cofre de chaves do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Este artigo aborda a implantação de uma máquina virtual do Windows Server usando uma senha armazenada no cofre de chaves do Azure Stack. Usando uma senha do Cofre de chaves é mais seguro do que passando uma senha de texto sem formatação.

## <a name="overview"></a>Visão geral

Você pode armazenar valores como uma senha como um segredo em um cofre de chaves do Azure Stack. Depois de criar um segredo, você pode referenciá-lo em modelos do Azure Resource Manager. Usar os segredos com o Resource Manager oferece os seguintes benefícios:

* Você não precisa inserir manualmente sempre que você implantar um recurso de segredo.
* Você pode especificar quais usuários ou entidades de serviço podem acessar um segredo.

## <a name="prerequisites"></a>Pré-requisitos

* Você deve assinar uma oferta que inclui o serviço Key Vault.
* [Instale o PowerShell para o Azure Stack.](azure-stack-powershell-install.md)
* [Configure o ambiente do PowerShell.](azure-stack-powershell-configure-user.md)

As etapas a seguir descrevem o processo necessário para criar uma máquina virtual, recuperando a senha armazenada em um cofre de chaves:

1. Crie um cofre de chave secreta.
2. Atualize o arquivo azuredeploy.parameters.json.
3. Implante o modelo.

> [!NOTE]  
> Se você estiver conectado por meio de VPN, você pode usar essas etapas do Kit de desenvolvimento do Azure Stack ou de um cliente externo.

## <a name="create-a-key-vault-secret"></a>Criar um cofre de chave secreta

O script a seguir cria um cofre de chaves e armazena uma senha no cofre de chaves como um segredo. Use o `-EnabledForDeployment` parâmetro ao criar o Cofre de chaves. Esse parâmetro torna-se de que o Cofre de chaves pode ser referenciado de modelos do Azure Resource Manager.

```powershell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location
  -EnabledForTemplateDeployment

$secretValue = ConvertTo-SecureString -String '<Password for your virtual machine>' -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
  -SecretValue $secretValue

```

Quando você executa o script anterior, a saída inclui o URI do segredo. Anote esse URI. Você precisa referenciá-lo na [máquina virtual de implantar o Windows com a senha no modelo do Cofre de chaves](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv). Baixe o [101-vm-secure-password](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) pasta no seu computador de desenvolvimento. Esta pasta contém o `azuredeploy.json` e `azuredeploy.parameters.json` arquivos, que será necessário nas próximas etapas.

Modificar o `azuredeploy.parameters.json` arquivo de acordo com seus valores de ambiente. Os parâmetros de interesse especial são o nome do cofre, o grupo de recursos do cofre e o segredo do URI (conforme gerado pelo script anterior). O arquivo a seguir está um exemplo de um arquivo de parâmetro:

## <a name="update-the-azuredeployparametersjson-file"></a>Atualizar o arquivo azuredeploy.parameters.json

Atualize o arquivo de azuredeploy.parameters.json com o URI de KeyVault, secretName, adminUsername dos valores de máquina virtual, de acordo com seu ambiente. Arquivo JSON a seguir mostra um exemplo do arquivo de parâmetros de modelo:

```json
{
    "$schema":  "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":  "1.0.0.0",
    "parameters":  {
       "adminUsername":  {
         "value":  "demouser"
          },
         "adminPassword":  {
           "reference":  {
              "keyVault":  {
                "id":  "/subscriptions/xxxxxx/resourceGroups/RgKvPwd/providers/Microsoft.KeyVault/vaults/KvPwd"
                },
              "secretName":  "MySecret"
           }
         },
       "dnsLabelPrefix":  {
          "value":  "mydns123456"
        },
        "windowsOSVersion":  {
          "value":  "2016-Datacenter"
        }
    }
}

```

## <a name="template-deployment"></a>Implantação de modelo

Agora, implante o modelo usando o seguinte script do PowerShell:

```powershell  
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Quando o modelo é implantado com êxito, ele resulta na seguinte saída:

![Saída de implantação](media/azure-stack-key-vault-deploy-vm-with-secret/deployment-output.png)

## <a name="next-steps"></a>Próximas etapas

* [Implante um aplicativo de exemplo com o Key Vault](azure-stack-key-vault-sample-app.md)
* [Implantar uma VM com um certificado de Cofre de chaves](azure-stack-key-vault-push-secret-into-vm.md)
