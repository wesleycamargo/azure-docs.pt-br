---
title: "Implantar uma máquina virtual com um certificado armazenado com segurança na pilha do Azure | Microsoft Docs"
description: "Saiba como implantar uma máquina virtual e enviar por push a um certificado para ele por meio de um cofre de chaves na pilha do Azure"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/03/2017
ms.author: sngun
ms.openlocfilehash: 29ccdc9eca9911b2f550f9e09da83d0b1d30f9db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-and-include-certificate-retrieved-from-a-key-vault"></a>Criar uma máquina virtual e são recuperado de um cofre de chaves de certificado

Este artigo ajuda a criar uma máquina virtual na pilha do Azure e certificados por push para ele. 

## <a name="prerequisites"></a>Pré-requisitos

* Você deve deve assinar uma oferta que inclui o serviço de Cofre de chaves. 
* [Instale o PowerShell para a pilha do Azure.](azure-stack-powershell-install.md)  
* [Configurar o ambiente do PowerShell do usuário a pilha do Azure](azure-stack-powershell-configure-user.md)

Um cofre de chaves na pilha do Azure é usado para armazenar certificados. Os certificados são úteis em vários cenários diferentes. Por exemplo, considere um cenário em que uma máquina virtual na pilha do Azure que esteja executando um aplicativo que precisa de um certificado. Esse certificado pode ser usado para criptografar para autenticação no Active Directory ou para o SSL em um site. Com o certificado no cofre de chaves ajuda a garantir que ele é seguro.

Neste artigo, vamos orientá-lo pelas etapas necessárias para enviar por push a um certificado para uma máquina virtual do Windows na pilha do Azure. Se você estiver conectado por meio de VPN, você pode usar essas etapas do Kit de desenvolvimento de pilha do Azure ou de um cliente externo baseado no Windows.

As etapas a seguir descrevem o processo necessário para enviar por push um certificado na máquina virtual:

1. Crie um cofre de chave secreta.
2. Atualize o arquivo azuredeploy.parameters.json.
3. Implantar o modelo

## <a name="create-a-key-vault-secret"></a>Crie um cofre de chave secreta

O script a seguir cria um certificado no formato. pfx, cria um cofre de chaves e armazena o certificado no cofre de chave como um segredo. Você deve usar o `-EnabledForDeployment` parâmetro ao criar a chave do cofre. Esse parâmetro torna-se de que o Cofre de chaves pode ser referenciado de modelos do Gerenciador de recursos do Azure.

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

Agora, implante o modelo usando o seguinte script do PowerShell:

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Quando o modelo é implantado com êxito, ele resulta na seguinte saída:

![Saída de implantação](media/azure-stack-kv-push-secret-into-vm/deployment-output.png)

Quando essa máquina virtual é implantada, o Azure pilha envia o certificado na máquina virtual. No Windows, o certificado é adicionado ao local de certificados LocalMachine, com o repositório de certificados fornecidos pelo usuário. No Linux, o certificado é colocado no diretório /var/lib/waagent, com o nome do arquivo &lt;UppercaseThumbprint&gt;. crt X509 para o arquivo de certificado e &lt;UppercaseThumbprint&gt;.prv para a chave privada .

## <a name="retire-certificates"></a>Desativar certificados

Na seção anterior, mostramos como enviar por push a um novo certificado em uma máquina virtual. O certificado antigo é ainda na máquina virtual, e ele não pode ser removido. No entanto, você pode desabilitar a versão mais antiga do segredo usando o `Set-AzureKeyVaultSecretAttribute` cmdlet. A seguir está um exemplo de uso deste cmdlet. Certifique-se de substituir o nome do cofre, nome do segredo e valores de versão de acordo com seu ambiente:

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>Próximas etapas

* [Implantar uma VM com uma senha do Cofre de Chaves](azure-stack-kv-deploy-vm-with-secret.md)
* [Permitir que um aplicativo acessar o Cofre de chaves](azure-stack-kv-sample-app.md)


