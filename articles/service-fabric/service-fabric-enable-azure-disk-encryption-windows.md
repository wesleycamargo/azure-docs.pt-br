---
title: Habilitar a criptografia de disco para clusters do Windows do Service Fabric | Microsoft Docs
description: Este artigo descreve como habilitar a criptografia de disco para os nós de cluster do Service Fabric definida no Azure usando o Azure Resource Manager, o Azure Key Vault.
services: service-fabric
documentationcenter: .net
author: v-viban
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/23/2018
ms.author: v-viban
ms.openlocfilehash: 0b84d270cc50888822b8463df2b95aedaa34ee9a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658899"
---
# <a name="enable-disk-encryption-for-service-fabric-windows-cluster-nodes"></a>Habilitar a criptografia de disco para nós de cluster do Windows do Service Fabric 
> [!div class="op_single_selector"]
> * [Criptografia de Disco para Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Criptografia de Disco para Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Siga as etapas abaixo para habilitar a criptografia de disco em Nós de Cluster Windows do Service Fabric. Você precisará fazer o seguinte para cada um dos tipos de nó/conjuntos de dimensionamento de máquinas virtuais. Para criptografar os nós, estenderemos o recurso Azure Disk Encryption em conjuntos de dimensionamento de máquinas virtuais.

O guia aborda os seguintes procedimentos:

* Conceitos principais de que você precisa estar ciente para habilitar a criptografia de disco no conjunto de dimensionamento de máquinas virtuais do Cluster do Windows do Service Fabric.
* Etapas de pré-requisitos a serem seguidas antes de habilitar a criptografia de disco no conjunto de dimensionamento de máquinas virtuais do Cluster do Windows do Service Fabric.
* Etapas a serem seguidas antes para habilitar a criptografia de disco no conjunto de dimensionamento de máquinas virtuais do Cluster do Windows do Service Fabric.


## <a name="prerequisites"></a>pré-requisitos
1. **Autorregistro** - Para ser usado, a visualização de criptografia de disco do conjunto de dimensionamento de máquinas virtuais exige o registro automático
2. Você pode autorregistrar sua assinatura executando as seguintes etapas: 
```Powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
3. Aguarde cerca de 10 minutos até que o estado seja “Registrado”. Você pode verificar o estado executando o seguinte comando: 
```Powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```
4. **Azure Key Vault** - Crie um KeyVault na mesma assinatura e região como o conjunto de dimensionamento e defina a política de acesso “EnabledForDiskEncryption” no KeyVault usando o cmdlet do PS. Você também pode definir a política usando a interface do usuário do KeyVault no portal do Azure: 
```Powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
5. Instale a [CLI do Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) mais recente, que tem os novos comandos de criptografia.
6. Instale a versão mais recente do [SDK do Azure a partir da versão do PowerShell](https://github.com/Azure/azure-powershell/releases). Seguem os cmdlets ADE do conjunto de dimensionamento de máquinas virtuais para habilitar a criptografia ([Set](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension?view=azurermps-4.4.1)), recuperar o status da criptografia ([Get](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption?view=azurermps-4.4.1)) e remover a criptografia ([disable](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption?view=azurermps-4.4.1)) na instância do conjunto de dimensionamento.

| Comando | Versão |  Fonte  |
| ------------- |-------------| ------------|
| Get-AzureRmVmssDiskEncryptionStatus   | 3.4.0 ou superior | AzureRM.Compute |
| Get-AzureRmVmssDiskEncryptionStatus   | 3.4.0 ou superior | AzureRM.Compute |
| Disable-AzureRmVmssDiskEncryption   | 3.4.0 ou superior | AzureRM.Compute |
| Get-AzureRmVmssDiskEncryption   | 3.4.0 ou superior | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryption   | 3.4.0 ou superior | AzureRM.Compute |
| Set-AzureRmVmssDiskEncryptionExtension   | 3.4.0 ou superior | AzureRM.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Cenários com suporte para criptografia de disco
* A criptografia do conjunto de dimensionamento de máquinas virtuais é suportada apenas para conjuntos de dimensionamento com discos gerenciados, e não há suporte para conjuntos de dimensionamento com disco nativo (ou não gerenciado).
* Há suporte para a criptografia de conjunto de dimensionamento de máquinas virtuais em volumes de sistema operacional para Windows VMSS. Há suporte para desabilitar a criptografia em volumes de sistema operacional e de dados para conjunto de dimensionamento Windows.
* Não há suporte para as operações de recriação de imagem e atualização de VMs de conjunto de dimensionamento de máquina virtual na versão prévia atual.


### <a name="create-new-cluster-and-enable-disk-encryption"></a>Criar novo cluster e habilitar a criptografia de disco

Use os seguintes comandos para criar o cluster e habilitar a criptografia de disco usando o modelo do Azure Resource Manager e o certificado autoassinado.

### <a name="log-in-to-azure"></a>Fazer logon no Azure 

```Powershell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Usar um modelo personalizado que você já tem 

Caso precise criar um modelo personalizado para atender às suas necessidades, é altamente recomendável que você inicie com um dos modelos que estão disponíveis no [Exemplos de modelo do Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Siga as orientações e explicações para [personalizar o modelo de cluster][personalizar-seu-modelo-de-cluster] na seção abaixo.

Se você já tem um modelo personalizado, certifique-se de verificar novamente se todos os três parâmetros relacionados ao certificado no modelo e no arquivo de parâmetro são nomeados da seguinte maneira e se os valores são nulos da maneira a seguir.

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```Powershell


$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```


```CLI

declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```

#### <a name="deploy-application-to-windows-service-fabric-cluster"></a>Implantar aplicativo no cluster do Windows do Service Fabric
Siga as etapas e diretrizes para [implantar o aplicativo no cluster](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications)


#### <a name="enable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set-created-above"></a>Habilitar a criptografia de disco no conjunto de dimensionamento de máquinas virtuais do Cluster do Service Fabric criado acima
 
```Powershell

$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```CLI

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```


#### <a name="validate-if-disk-encryption-enabled-for-windows-virtual-machine-scale-set"></a>Valide se a criptografia de disco está habilitada para o conjunto de dimensionamento de máquinas virtuais do Windows.
Obtenha o status de todo um conjunto de dimensionamento de máquinas virtuais ou qualquer instância no conjunto de dimensionamento. Consulte os comandos abaixo.
Além disso, o usuário pode fazer logon na VM no conjunto de dimensionamento e certificar-se de que as unidades são criptografadas

```Powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```CLI

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Desabilitar a criptografia de disco no conjunto de dimensionamento de máquinas virtuais do Cluster do Service Fabric 
A desabilitação da criptografia de disco se aplica a todo o conjunto de dimensionamento de máquinas virtuais e não por instância 

```Powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Próximas etapas
Neste ponto, você tem um cluster seguro com o modo como habilitar/desabilitar a criptografia de disco para o conjunto de dimensionamento de máquinas virtuais do Cluster do Service Fabric. Em seguida, [Criptografia de Disco para Linux](service-fabric-enable-azure-disk-encryption-linux.md) 

