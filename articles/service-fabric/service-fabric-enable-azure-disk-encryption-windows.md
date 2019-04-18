---
title: Habilitar a criptografia de disco para clusters do Windows do Azure Service Fabric | Microsoft Docs
description: Este artigo descreve como habilitar a criptografia de disco para os nós de cluster do Service Fabric definida no Azure usando o Azure Resource Manager, o Azure Key Vault.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2019
ms.author: aljo
ms.openlocfilehash: 2e9c41409c1f528947e3bef281e9a3c34da39e9b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59050435"
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

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
* **Autorregistro** - Para ser usado, a visualização de criptografia de disco do conjunto de dimensionamento de máquinas virtuais exige o registro automático
* Você pode autorregistrar sua assinatura executando as seguintes etapas: 
```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
* Aguarde cerca de 10 minutos até que o estado seja “Registrado”. Você pode verificar o estado executando o seguinte comando: 
```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```
* **Azure Key Vault** - Crie um KeyVault na mesma assinatura e região como o conjunto de dimensionamento e defina a política de acesso “EnabledForDiskEncryption” no KeyVault usando o cmdlet do PS. Você também pode definir a política usando a interface do usuário do KeyVault no portal do Azure: 
```powershell
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
* Instalar a versão mais recente [CLI do Azure](/cli/azure/install-azure-cli) , que tem os novos comandos de criptografia.
* Instale a versão mais recente do [SDK do Azure a partir da versão do PowerShell](https://github.com/Azure/azure-powershell/releases). Seguem os cmdlets ADE do conjunto de dimensionamento de máquinas virtuais para habilitar a criptografia ([Set](/powershell/module/az.compute/set-azvmssdiskencryptionextension)), recuperar o status da criptografia ([Get](/powershell/module/az.compute/get-azvmssvmdiskencryption)) e remover a criptografia ([disable](/powershell/module/az.compute/disable-azvmssdiskencryption)) na instância do conjunto de dimensionamento.

| Comando | Versão |  Fonte  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 ou superior | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 ou superior | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 ou superior | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 ou superior | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 ou superior | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 ou superior | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Cenários com suporte para criptografia de disco
* A criptografia do conjunto de dimensionamento de máquinas virtuais é suportada apenas para conjuntos de dimensionamento com discos gerenciados, e não há suporte para conjuntos de dimensionamento com disco nativo (ou não gerenciado).
* Criptografia de conjunto de dimensionamento de máquina virtual tem suporte para volumes de dados e sistema operacional para conjunto de dimensionamento de máquina virtual do Windows. Há suporte para desabilitar a criptografia em volumes de sistema operacional e de dados para conjunto de dimensionamento Windows.
* Refazer a imagem VM de conjunto de dimensionamento de máquina virtual e não há suporte para operações de atualização no modo de visualização atual.


### <a name="create-new-cluster-and-enable-disk-encryption"></a>Criar novo cluster e habilitar a criptografia de disco

Use os seguintes comandos para criar o cluster e habilitar a criptografia de disco usando o modelo do Resource Manager e o certificado autoassinado.

### <a name="sign-in-to-azure"></a>Entrar no Azure 

```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Usar um modelo personalizado que você já tem 

Caso precise criar um modelo personalizado para atender às suas necessidades, é altamente recomendável que você inicie com um dos modelos que estão disponíveis no [Exemplos de modelo do Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Siga as orientações e explicações para [personalizar o modelo de cluster][customize-your-cluster-template] na seção abaixo.

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


```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```


```azurecli

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
Siga as etapas e diretrizes para [implantar o aplicativo no cluster](service-fabric-deploy-remove-applications.md)


#### <a name="enable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set-created-above"></a>Habilitar a criptografia de disco no conjunto de dimensionamento de máquinas virtuais do Cluster do Service Fabric criado acima
 
```powershell

$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```


#### <a name="validate-if-disk-encryption-enabled-for-windows-virtual-machine-scale-set"></a>Valide se a criptografia de disco está habilitada para o conjunto de dimensionamento de máquinas virtuais do Windows.
Obtenha o status de todo um conjunto de dimensionamento de máquinas virtuais ou qualquer instância no conjunto de dimensionamento. Consulte os comandos abaixo.
Além do usuário pode entrar para a VM no conjunto de dimensionamento e verifique se as unidades são criptografadas

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Desabilitar a criptografia de disco no conjunto de dimensionamento de máquinas virtuais do Cluster do Service Fabric 
A desabilitação da criptografia de disco se aplica a todo o conjunto de dimensionamento de máquinas virtuais e não por instância 

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Próximas etapas
Neste ponto, você tem um cluster seguro com o modo como habilitar/desabilitar a criptografia de disco para o conjunto de dimensionamento de máquinas virtuais do Cluster do Service Fabric. Em seguida, [Criptografia de Disco para Linux](service-fabric-enable-azure-disk-encryption-linux.md) 

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
