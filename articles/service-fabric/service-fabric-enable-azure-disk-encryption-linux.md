---
title: Habilitar a criptografia de disco para clusters do Linux do Azure Service Fabric | Microsoft Docs
description: Este artigo descreve como habilitar a criptografia de disco para a escala de cluster do Service Fabric definida no Azure usando o Azure Resource Manager, o Azure Key Vault.
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
ms.openlocfilehash: 3de26efb74b9349282d36beb94e8a2a269227fbf
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488165"
---
# <a name="enable-disk-encryption-for-service-fabric-linux-cluster-nodes"></a>Habilitar a criptografia de disco para nós de cluster do Linux do Service Fabric 
> [!div class="op_single_selector"]
> * [Criptografia de Disco para Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Criptografia de Disco para Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

Siga as etapas abaixo para habilitar a criptografia de disco em Nós de Cluster Linux do Service Fabric. Você precisará fazer o seguinte para cada um dos tipos de nó/conjuntos de dimensionamento de máquinas virtuais. Para criptografar os nós, estenderemos o recurso Azure Disk Encryption em conjuntos de dimensionamento de máquinas virtuais.

O guia aborda os seguintes procedimentos:

* Conceitos principais de que você precisa estar ciente para habilitar a criptografia de disco no conjunto de dimensionamento de máquinas virtuais do Cluster do Linux do Service Fabric.
* Etapas de pré-requisitos a serem seguidas antes de habilitar a criptografia de disco no conjunto de dimensionamento de máquinas virtuais do Cluster do Linux do Service Fabric.
* Etapas a serem seguidas antes para habilitar a criptografia de disco no conjunto de dimensionamento de máquinas virtuais do Cluster do Linux do Service Fabric.


## <a name="prerequisites"></a>Pré-requisitos

* **Autorregistro** - Para ser usado, a visualização de criptografia de disco do conjunto de dimensionamento de máquinas virtuais exige o registro automático
* Você pode autorregistrar sua assinatura executando as seguintes etapas: 
```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
* Aguarde cerca de 10 minutos até que o estado seja “Registrado”. Você pode verificar o estado executando o seguinte comando: 
```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```
* **Azure Key Vault** - Crie um KeyVault na mesma assinatura e região como o conjunto de dimensionamento de máquinas virtuais e defina a política de acesso “EnabledForDiskEncryption” no KeyVault usando o cmdlet do PS. Você também pode definir a política usando a interface do usuário do KeyVault no Portal do Azure: 
```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
* Instale a [CLI do Azure 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) mais recente, que tem os novos comandos de criptografia.
* Instale a versão mais recente do [SDK do Azure a partir da versão do PowerShell](https://github.com/Azure/azure-powershell/releases). Estes são os cmdlets ADE para habilitar de conjunto de dimensionamento de máquinas virtuais ([definir](/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension?view=azurermps-4.4.1)) a criptografia, recuperar ([obter](/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption?view=azurermps-4.4.1)) status de criptografia e remover ([desabilitar](/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption?view=azurermps-4.4.1)) criptografia em escala instância do conjunto. 

| Comando | Versão |  Fonte  |
| ------------- |-------------| ------------|
| Get-AzureRmVmssDiskEncryptionStatus   | 3.4.0 ou superior | AzureRM.Compute |
| Get-AzureRmVmssDiskEncryptionStatus   | 3.4.0 ou superior | AzureRM.Compute |
| Disable-AzureRmVmssDiskEncryption   | 3.4.0 ou superior | AzureRM.Compute |
| Get-AzureRmVmssDiskEncryption   | 3.4.0 ou superior | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryption   | 3.4.0 ou superior | AzureRM.Compute |
| Set-AzureRmVmssDiskEncryptionExtension   | 3.4.0 ou superior | AzureRM.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Cenários com suporte para criptografia de disco
* A criptografia do conjunto de dimensionamento de máquinas virtuais é suportada apenas para conjuntos de dimensionamento com discos gerenciados, mas não há suporte para conjuntos de dimensionamento com disco nativo (ou não gerenciado).
* A criptografia do conjunto de dimensionamento de máquinas virtuais tem suporte para o volume de dados do conjunto de dimensionamento de máquinas virtuais do Linux. NÃO há suporte para criptografia de disco do sistema operacional na versão prévia atual para Linux.
* Refazer a imagem VM de conjunto de dimensionamento de máquina virtual e não há suporte para operações de atualização no modo de visualização atual.


### <a name="create-new-linux-cluster-and-enable-disk-encryption"></a>Criar novo cluster do Linux e habilitar a criptografia de disco

Use os seguintes comandos para criar o cluster e habilitar a criptografia de disco usando o modelo do Resource Manager e o certificado autoassinado.

### <a name="sign-in-to-azure"></a>Entrar no Azure  

```powershell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Usar um modelo personalizado que você já tem 

Caso precise criar um modelo personalizado para atender às suas necessidades, é altamente recomendável que você inicie com um dos modelos que estão disponíveis no [Exemplos de modelo do Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) para Cluster do Linux. 

Se você já tiver um modelo personalizado, certifique-se de verificar novamente se todos os três parâmetros relacionados ao certificado no modelo e no arquivo de parâmetro são nomeados da seguinte maneira e se os valores são nulos da maneira a seguir.

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

Para o conjunto de dimensionamento de máquinas virtuais do Linux, apenas a criptografia de disco de dados é suportada, portanto, precisamos adicionar o disco de dados usando o modelo do Azure Resource Manager. Atualize o modelo para provisionamento de disco de dados como a seguir:

```Json
   
   "storageProfile": { 
            "imageReference": { 
              "publisher": "[parameters('vmImagePublisher')]", 
              "offer": "[parameters('vmImageOffer')]", 
              "sku": "[parameters('vmImageSku')]", 
              "version": "[parameters('vmImageVersion')]" 
            }, 
            "osDisk": { 
              "caching": "ReadOnly", 
              "createOption": "FromImage", 
              "managedDisk": { 
                "storageAccountType": "[parameters('storageAccountType')]" 
              } 
           }, 
                "dataDisks": [ 
                { 
                    "diskSizeGB": 1023, 
                    "lun": 0, 
                    "createOption": "Empty" 
   
```
 

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Este é o comando de CLI equivalente para fazer o mesmo. Altere os valores nas instruções declaradas para os valores apropriados. A CLI é compatível com todos os outros parâmetros compatíveis com o comando do Powershell acima.

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

#### <a name="linux-data-disk-mounting"></a>Montagem de disco de dados do Linux
Antes de continuarmos com a criptografia no conjunto de dimensionamento de máquinas virtuais do Linux, é preciso se certificar de que o disco de dados esteja montado corretamente ou não. Entrar para a VM de Cluster do Linux e execute o comando LSBLK. A saída deve mostrar esse disco de dados adicionado na coluna de ponto de montagem.


#### <a name="deploy-application-to-linux-service-fabric-cluster"></a>Implantar aplicativo no cluster do Service Fabric
Siga as etapas e diretrizes para [implantar o aplicativo no cluster](service-fabric-quickstart-containers-linux.md)


#### <a name="enable-disk-encryption-for-service-fabric-linux-cluster-virtual-machine-scale-set-created-above"></a>Habilitar a criptografia de disco no conjunto de dimensionamento de máquinas virtuais do Cluster do Linux do Service Fabric criado acima
 
```powershell
$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```

#### <a name="validate-if-disk-encryption-enabled-for-linux-virtual-machine-scale-set"></a>Valide se a criptografia de disco está habilitada para o conjunto de dimensionamento de máquinas virtuais do Linux.
Obtenha o status de todo um conjunto de dimensionamento de máquinas virtuais ou qualquer VM de instância no conjunto de dimensionamento. Consulte os comandos abaixo.
Além do usuário pode entrar VM de Cluster do Linux e execute o comando LSBLK. A saída deve mostrar esse disco de dados adicionado na coluna de ponto de montagem e na coluna Tipo como Criptografia de disco de dados adicionado.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Desabilitar a criptografia de disco no conjunto de dimensionamento de máquinas virtuais do Cluster do Service Fabric 
A desabilitação da criptografia de disco se aplica a todo o conjunto de dimensionamento de máquinas virtuais e não por instância 

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Próximas etapas
Neste ponto, você tem um cluster seguro com o modo como habilitar/desabilitar a criptografia de disco para o conjunto de dimensionamento de máquinas virtuais do Cluster do Service Fabric do Linux. Em seguida, [Criptografia de Disco para Windows](service-fabric-enable-azure-disk-encryption-windows.md) 
