---
title: Criar um cluster do Azure Service Fabric usando o nome comum do certificado | Microsoft Docs
description: Saiba como criar um cluster do Service Fabric usando o nome comum do certificado a partir de um modelo.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi
ms.openlocfilehash: 78812f7bcce82090802672e3e232e713f0d047d1
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214106"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Implantar um cluster do Service Fabric que usa o nome comum do certificado em vez de impressão digital
Dois certificados não podem ter a mesma impressão digital, o que dificulta a substituição ou gerenciamento de certificados de cluster. Vários certificados, no entanto, podem ter o mesmo nome comum ou assunto.  Um cluster usando nomes comuns do certificado simplifica muito o gerenciamento de certificados. Este artigo descreve como implantar um cluster do Service Fabric para usar o nome comum do certificado em vez da impressão digital do certificado.
 
## <a name="get-a-certificate"></a>Obter um certificado
Primeiro, obtenha um [certificado de uma autoridade de certificação (CA)](https://wikipedia.org/wiki/Certificate_authority).  O nome comum do certificado deve ser para o domínio personalizado de sua propriedade e comprado de um registrador de domínios. Por exemplo, "azureservicefabricbestpractices.com"; aqueles que não são funcionários da Microsoft não podem provisionar certificados para domínios da MS, portanto, você não poderá usar os nomes DNS do seu LB ou Gerenciador de Tráfego como nomes comuns para o seu certificado e precisará provisionar uma [Zona DNS do Azure](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns) se o domínio personalizado precisar ser resolvido no Azure. Declare também o domínio personalizado de sua propriedade como o "managementEndpoint" do cluster se você quiser que o portal reflita o alias do domínio personalizado para seu cluster.

Para fins de teste, você pode obter um certificado assinado CA de uma autoridade de certificação livre ou aberta.

> [!NOTE]
> Não há suporte para certificados autoassinados, inclusive os gerados durante a implantação de um cluster do Service Fabric no Portal do Azure. 

## <a name="upload-the-certificate-to-a-key-vault"></a>Carregar o certificado em um cofre de chaves
No Azure, um cluster do Service Fabric é um conjunto de dimensionamento de máquinas virtuais.  Carregue o certificado em um cofre de chaves.  Quando o cluster é implantado, o certificado é instalado no conjunto de dimensionamento de máquina virtual em que o cluster está em execução.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    
```

## <a name="download-and-update-a-sample-template"></a>Fazer o download e atualizar um modelo de exemplo
Este artigo usa o modelo de exemplo e parâmetros de modelo do [cluster seguro de cinco nós](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure). Faça o download dos arquivos *azuredeploy.json* e *azuredeploy.parameters.json* no seu computador.

### <a name="update-parameters-file"></a>Atualizar arquivo de parâmetros
Primeiro, abra o arquivo *azuredeploy.parameters.json* em um editor de texto e adicione o seguinte valor de parâmetro:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Em seguida, defina os valores de parâmetro *certificateCommonName*, *sourceVaultValue* e *certificateUrlValue* para aqueles retornados pelo script anterior:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>Atualizar o arquivo de modelo
Em seguida, abra o arquivo *azuredeploy.json* em um editor de texto e faça três atualizações para dar suporte ao nome comum do certificado.

1. Na seção **Parâmetros**, adicione um parâmetro *certificateCommonName*:
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    ```

    Também Considere remover o *certificateThumbprint*, ele pode no ser mais necessário.

2. Defina o valor da variável *sfrpApiVersion* para "2018-02-01":
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. No recurso **Microsoft.Compute/virtualMachineScaleSets**, atualize a extensão de máquina virtual para usar o nome comum em configurações de certificado em vez da impressão digital.  Em **virtualMachineProfile**->**extensionProfile**->**extensões**->**propriedades**->**configurações**->**certificado**, adicionar 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    e remover `"thumbprint": "[parameters('certificateThumbprint')]",`.

    ```json
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              "type": "ServiceFabricNode",
              "autoUpgradeMinorVersion": true,
              "protectedSettings": {
                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
              },
              "publisher": "Microsoft.Azure.ServiceFabric",
              "settings": {
                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                "nodeTypeRef": "[variables('vmNodeType0Name')]",
                "dataPath": "D:\\SvcFab",
                "durabilityLevel": "Bronze",
                "enableParallelJobs": true,
                "nicPrefixOverride": "[variables('subnet0Prefix')]",
                "certificate": {
                  "commonNames": [
                     "[parameters('certificateCommonName')]"
                  ],
                  "x509StoreName": "[parameters('certificateStoreValue')]"
                }
              },
              "typeHandlerVersion": "1.0"
            }
          },
    ```

4.  No recurso **Microsoft.ServiceFabric/clusters**, atualize a versão da API para "2018-02-01".  Adicione também uma configuração **certificateCommonNames** com uma propriedade **commonNames** e remova a configuração **certificado** (com a propriedade de impressão digital), como no exemplo a seguir:
    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
        ],
        "properties": {
        "addonFeatures": [
            "DnsService",
            "RepairManager"
        ],        
        "certificateCommonNames": {
            "commonNames": [
            {
                "certificateCommonName": "[parameters('certificateCommonName')]",
                "certificateIssuerThumbprint": ""
            }
            ],
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
        ...
    ```

## <a name="deploy-the-updated-template"></a>Implantar o modelo atualizado
Reimplante o modelo atualizado após fazer as alterações.

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [segurança de cluster](service-fabric-cluster-security.md).
* Saiba como [substituir um certificado de cluster](service-fabric-cluster-rollover-cert-cn.md)
* [Atualizar e gerenciar certificados do cluster](service-fabric-cluster-security-update-certs-azure.md)
* Simplifique o gerenciamento de certificados [Alterando o cluster da impressão digital do certificado para o nome comum](service-fabric-cluster-change-cert-thumbprint-to-cn.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
