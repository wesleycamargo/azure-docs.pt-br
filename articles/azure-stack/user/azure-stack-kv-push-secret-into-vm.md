---
title: Implantar uma máquina virtual com um certificado armazenado com segurança na pilha do Azure | Microsoft Docs
description: Saiba como implantar uma máquina virtual e enviar por push a um certificado para ele por meio de um cofre de chaves na pilha do Azure
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/28/2018
ms.author: mabrigg
ms.openlocfilehash: 05278ee4b0dc1f2c22f40bfcff4f9d7342017c0f
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37108749"
---
# <a name="create-a-virtual-machine-and-install-a-certificate-retrieved-from-an-azure-stack-key-vault"></a>Criar uma máquina virtual e instale um certificado recuperado de um cofre de chave de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Saiba como criar uma máquina virtual de pilha do Azure (VM) com um certificado de chave de cofre instalado.

## <a name="overview"></a>Visão geral

Certificados são usados em muitos cenários, como autenticação do Active Directory, ou para criptografar o tráfego da web. Você pode armazenar com segurança certificados como segredos em um cofre de chave de pilha do Azure. Os benefícios de usar o Cofre de chaves de pilha do Azure são:

* Certificados não são expostos em um modelo, histórico de linha de comando ou script.
* O processo de gerenciamento de certificado é dinâmico.
* Você tem controle sobre as chaves de acesso a certificados.

### <a name="process-description"></a>Descrição do processo

As etapas a seguir descrevem o processo necessário para enviar por push um certificado na máquina virtual:

1. Crie um cofre de chave secreta.
2. Atualize o arquivo azuredeploy.parameters.json.
3. Implantar o modelo

> [!NOTE]
> Se você estiver conectado por meio de VPN, você pode usar essas etapas do Kit de desenvolvimento de pilha do Azure ou de um cliente externo.

## <a name="prerequisites"></a>Pré-requisitos

* Você deve assinar uma oferta que inclui o serviço de Cofre de chaves.
* [Instale o PowerShell para a pilha do Azure.](azure-stack-powershell-install.md)
* [Configurar o ambiente do PowerShell do usuário a pilha do Azure](azure-stack-powershell-configure-user.md)

## <a name="create-a-key-vault-secret"></a>Crie um cofre de chave secreta

O script a seguir cria um certificado no formato. pfx, cria um cofre de chaves e armazena o certificado no cofre de chave como um segredo.

> [!IMPORTANT]
> Você deve usar o `-EnabledForDeployment` parâmetro ao criar a chave do cofre. Esse parâmetro garante que o Cofre de chaves pode ser referenciado de modelos do Gerenciador de recursos do Azure.

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

Quando você executa o script anterior, a saída inclui o URI de segredo. Anote esse URI. Você precisa referenciá-lo no [certificados por Push para o modelo do Gerenciador de recursos do Windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate). Baixe o [modelo de vm push-certificado windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) pasta no seu computador de desenvolvimento. Esta pasta contém o `azuredeploy.json` e `azuredeploy.parameters.json` arquivos, que será necessário nas próximas etapas.

Modificar o `azuredeploy.parameters.json` arquivo de acordo com seus valores de ambiente. Os parâmetros de especial interesse são o nome do cofre, o grupo de recursos do cofre e o segredo do URI (conforme gerado por script anterior). O arquivo a seguir está um exemplo de um arquivo de parâmetro:

## <a name="update-the-azuredeployparametersjson-file"></a>Atualizar o arquivo azuredeploy.parameters.json

Atualize o arquivo de azuredeploy.parameters.json com o vaultName, URI secreta, VmName e outros valores de acordo com seu ambiente. O arquivo JSON a seguir mostra um exemplo do arquivo de parâmetros do modelo:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

![Resultados de implantação de modelo](media/azure-stack-kv-push-secret-into-vm/deployment-output.png)

Pilha do Azure emite o certificado na máquina virtual durante a implantação. Local do certificado depende do sistema operacional da VM:

* No Windows, o certificado é adicionado ao local de certificados LocalMachine, com o repositório de certificados fornecidos pelo usuário.
* No Linux, o certificado é colocado no diretório /var/lib/waagent, com o nome do arquivo &lt;UppercaseThumbprint&gt;. crt X509 para o arquivo de certificado e &lt;UppercaseThumbprint&gt;.prv para a chave privada .

## <a name="retire-certificates"></a>Desativar certificados

Desativar certificados faz parte do processo de gerenciamento de certificado. Não é possível excluir a versão mais antiga de um certificado, mas você pode desabilitá-lo usando o `Set-AzureKeyVaultSecretAttribute` cmdlet.

O exemplo a seguir mostra como desabilitar um certificado. Usar seus próprios valores para o **VaultName**, **nome**, e **versão** parâmetros.

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>Próximas etapas

* [Implantar uma VM com uma senha do Cofre de Chaves](azure-stack-kv-deploy-vm-with-secret.md)
* [Permitir que um aplicativo acessar o Cofre de chaves](azure-stack-kv-sample-app.md)
