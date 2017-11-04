---
title: "Implantar uma VM com senha armazenada com segurança na pilha do Azure | Microsoft Docs"
description: "Saiba como implantar uma máquina virtual usando uma senha armazenada no cofre de chaves de pilha do Azure"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 23322a49-fb7e-4dc2-8d0e-43de8cd41f80
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/08/2017
ms.author: sngun
ms.openlocfilehash: 3292a2dfefc17e5034c66122a3eab24d6c03e694
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-by-retrieving-the-password-stored-in-a-key-vault"></a>Criar uma máquina virtual, recuperando a senha armazenada em um cofre de chaves

Quando você precisa passar um valor de seguro, como uma senha durante a implantação, você pode armazenar esse valor como um segredo em um cofre de chave de pilha do Azure e referenciá-lo nos modelos do Azure Resource Manager. Você não não precisa inserir manualmente o segredo de cada vez que você implantar os recursos, você também pode especificar quais usuários ou entidades de serviço podem acessar o segredo. 

Neste artigo, vamos orientá-lo pelas etapas necessárias para implantar uma máquina virtual do Windows Azure pilha Recuperando a senha que é armazenada em um cofre de chaves. Portanto, a senha nunca é colocada em texto sem formatação no arquivo de parâmetro de modelo. Se você estiver conectado por meio de VPN, você pode usar essas etapas do Kit de desenvolvimento de pilha do Azure ou de um cliente externo.

## <a name="prerequisites"></a>Pré-requisitos
 
* Você deve deve assinar uma oferta que inclui o serviço de Cofre de chaves.  
* [Instale o PowerShell para a pilha do Azure.](azure-stack-powershell-install.md)  
* [Configure o ambiente do PowerShell do usuário a pilha do Azure.](azure-stack-powershell-configure-user.md)

As etapas a seguir descrevem o processo necessário para criar uma máquina virtual, recuperando a senha armazenada em um cofre de chaves:

1. Crie um cofre de chave secreta.
2. Atualize o arquivo azuredeploy.parameters.json.
3. Implante o modelo.

## <a name="create-a-key-vault-secret"></a>Crie um cofre de chave secreta

O script a seguir cria um cofre de chaves e armazena uma senha no cofre de chave como um segredo. Use o `-EnabledForDeployment` parâmetro ao criar a chave do cofre. Esse parâmetro torna-se de que o Cofre de chaves pode ser referenciado de modelos do Gerenciador de recursos do Azure.

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

Quando você executa o script anterior, a saída inclui o URI de segredo. Anote esse URI. Você precisa referenciá-lo no [máquina virtual de implantar o Windows com senha no modelo de Cofre de chaves](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv). Baixe o [101-vm--senha segura](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) pasta no seu computador de desenvolvimento. Esta pasta contém o `azuredeploy.json` e `azuredeploy.parameters.json` arquivos, que será necessário nas próximas etapas.

Modificar o `azuredeploy.parameters.json` arquivo de acordo com seus valores de ambiente. Os parâmetros de especial interesse são o nome do cofre, o grupo de recursos do cofre e o segredo do URI (conforme gerado por script anterior). O arquivo a seguir está um exemplo de um arquivo de parâmetro:

## <a name="update-the-azuredeployparametersjson-file"></a>Atualizar o arquivo azuredeploy.parameters.json

Atualize o arquivo de azuredeploy.parameters.json com URI KeyVault, secretName, adminUsername dos valores de acordo com seu ambiente de máquina virtual. O arquivo JSON a seguir mostra um exemplo do arquivo de parâmetros do modelo: 

```json
{
    "$schema":  "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

![Saída de implantação](media/azure-stack-kv-deploy-vm-with-secret/deployment-output.png)


## <a name="next-steps"></a>Próximas etapas
[Implantar um aplicativo de exemplo com o Cofre de chaves](azure-stack-kv-sample-app.md)

[Implantar uma VM com um certificado de chave de cofre](azure-stack-kv-push-secret-into-vm.md)

