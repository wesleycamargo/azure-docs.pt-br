---
title: Implantar uma máquina virtual com um certificado armazenado com segurança no Azure Stack | Microsoft Docs
description: Saiba como implantar uma máquina virtual e enviar por push a um certificado para ele por meio de um cofre de chaves no Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/27/2018
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 3e2ef77e0932c81746e24efecad020bd567a1fd5
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250394"
---
# <a name="create-a-virtual-machine-and-install-a-certificate-retrieved-from-an-azure-stack-key-vault"></a>Criar uma máquina virtual e instalar um certificado recuperado de um cofre de chaves do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Saiba como criar uma máquina virtual do Azure Stack (VM) com um certificado de Cofre de chaves instalado.

## <a name="overview"></a>Visão geral

Certificados são usados em muitos cenários, como autenticar no Active Directory, ou para criptografar o tráfego da web. Você pode armazenar com segurança certificados como segredos em um cofre de chaves do Azure Stack. Os benefícios de usar o Cofre de chaves do Azure Stack são:

* Certificados não são expostos em um script, o histórico de linha de comando ou o modelo.
* O processo de gerenciamento de certificado é dinâmico.
* Você tem controle das chaves que acessam os certificados.

### <a name="process-description"></a>Descrição do processo

As etapas a seguir descrevem o processo necessário para enviar por push a um certificado para a máquina virtual:

1. Crie um cofre de chave secreta.
2. Atualize o arquivo azuredeploy.parameters.json.
3. Implante o modelo.

> [!NOTE]
> Se você estiver conectado por meio de VPN, você pode usar essas etapas do Kit de desenvolvimento do Azure Stack ou de um cliente externo.

## <a name="prerequisites"></a>Pré-requisitos

* Você deve assinar uma oferta que inclui o serviço Key Vault.
* [Instale o PowerShell para o Azure Stack](azure-stack-powershell-install.md).
* [Configurar o ambiente do PowerShell do usuário do Azure Stack](azure-stack-powershell-configure-user.md).

## <a name="create-a-key-vault-secret"></a>Criar um cofre de chave secreta

O script a seguir cria um certificado no formato. pfx, cria um cofre de chaves e armazena o certificado no cofre de chaves como um segredo.

> [!IMPORTANT]
> Você deve usar o `-EnabledForDeployment` parâmetro ao criar o Cofre de chaves. Esse parâmetro garante que o Cofre de chaves pode ser referenciado de modelos do Azure Resource Manager.

```powershell
# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<Certificate Thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret
```

Quando você executa o script anterior, a saída inclui o URI do segredo. Anote esse URI. Você precisa referenciá-lo na [certificado de Push para o modelo do Gerenciador de recursos do Windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate). Baixe o [vm-push-certificado-windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) pasta de modelo para seu computador de desenvolvimento. Esta pasta contém o `azuredeploy.json` e `azuredeploy.parameters.json` arquivos, que será necessário nas próximas etapas.

Modificar o `azuredeploy.parameters.json` arquivo de acordo com seus valores de ambiente. Os parâmetros de interesse especial são o nome do cofre, o grupo de recursos do cofre e o segredo do URI (conforme gerado pelo script anterior). A seção a seguir mostra um exemplo de um arquivo de parâmetro.

## <a name="update-the-azuredeployparametersjson-file"></a>Atualizar o arquivo azuredeploy.parameters.json

Atualização do `azuredeploy.parameters.json` do arquivo com o `vaultName`, URI do segredo, `VmName`e outros valores de acordo com seu ambiente. Arquivo JSON a seguir mostra um exemplo do arquivo de parâmetros de modelo:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newStorageAccountName": {
      "value": "kvstorage01"
    },
    "vmName": {
      "value": "VM1"
    },
    "vmSize": {
      "value": "Standard_D1_v2"
    },
    "adminUserName": {
      "value": "demouser"
    },
    "adminPassword": {
      "value": "demouser@123"
    },
    "vaultName": {
      "value": "contosovault"
    },
    "vaultResourceGroup": {
      "value": "contosovaultrg"
    },
    "secretUrlWithVersion": {
      "value": "https://testkv001.vault.local.azurestack.external/secrets/testcert002/82afeeb84f4442329ce06593502e7840"
    }
  }
}
```

## <a name="deploy-the-template"></a>Implantar o modelo

Implante o modelo usando o seguinte script do PowerShell:

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Quando o modelo é implantado com êxito, ele resulta na seguinte saída:

![Resultados de implantação de modelo](media/azure-stack-key-vault-push-secret-into-vm/deployment-output.png)

O Azure Stack envia por push o certificado para a máquina virtual durante a implantação. O local do certificado depende do sistema operacional da VM:

* No Windows, o certificado é adicionado para o **LocalMachine** local com o repositório de certificados que o usuário forneceu do certificado.
* No Linux, o certificado está sob o `/var/lib/waagent directory`, com o nome do arquivo &lt;UppercaseThumbprint&gt;. CRT para X509 arquivo de certificado e &lt;UppercaseThumbprint&gt;.prv para a chave privada.

## <a name="retire-certificates"></a>Retirar certificados

Desativar certificados é parte do processo de gerenciamento de certificado. Você não pode excluir a versão mais antiga de um certificado, mas você pode desabilitá-lo usando o `Set-AzureKeyVaultSecretAttribute` cmdlet.

O exemplo a seguir mostra como desabilitar um certificado. Use seus próprios valores para o **VaultName**, **nome**, e **versão** parâmetros.

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>Próximas etapas

* [Implantar uma VM com uma senha do Cofre de Chaves](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Permitir que um aplicativo acessar o Cofre de chaves](azure-stack-key-vault-sample-app.md)
