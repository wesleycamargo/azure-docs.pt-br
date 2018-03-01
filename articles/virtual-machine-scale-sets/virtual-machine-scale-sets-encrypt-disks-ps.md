---
title: "Discos de criptografia de Conjuntos de Dimensionamento de Máquinas Virtuais do Azure | Microsoft Docs"
description: "Saiba como criptografar discos anexados em conjuntos de dimensionamento de máquinas virtuais."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: iainfou
ms.openlocfilehash: dddcece9f7566961b256369330661e5dbd5d4665
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set"></a>Criptografar os discos de sistema operacional e de dados anexados em um conjunto de dimensionamento de máquinas virtuais
Os [conjuntos de dimensionamento de máquinas virtuais](/azure/virtual-machine-scale-sets/) do Azure dão suporte ao ADE (Azure Disk Encryption).  O Azure Disk Encryption pode ser habilitado para conjuntos de dimensionamento de máquinas virtuais Windows e Linux a fim de proteger os dados dos conjuntos de dimensionamento em repouso usando tecnologia de criptografia padrão da indústria. For more information, read Azure Disk Encryption para máquinas virtuais Windows e Linux.

> [!NOTE]
>  O Azure Disk Encryption para conjuntos de dimensionamento de máquinas virtuais está atualmente em versão prévia pública, disponível em todas as regiões públicas do Azure.

Há suporte do Azure Disk Encryption:
- para conjuntos de dimensionamento criados com discos gerenciados, mas não há suporte para conjuntos de dimensionamento com disco nativo (ou não gerenciado).
- para volumes do sistema operacional e de dados em conjuntos de dimensionamento Windows. Há suporte para desabilitar a criptografia em volumes de sistema operacional e de dados de conjuntos de dimensionamento Windows.
- para volumes de dados em conjuntos de dimensionamento Linux. NÃO há suporte para criptografia de disco do sistema operacional na versão prévia atual de conjuntos de dimensionamento Linux.

Não há suporte para operações de recriação de imagem e atualização de VMs de conjunto de dimensionamento na versão prévia atual. O Azure Disk Encryption para a versão prévia dos conjuntos de dimensionamento de máquinas virtuais é recomendado apenas em ambientes de teste. Na versão prévia, não habilite a criptografia de disco em ambientes de produção, onde você talvez precise atualizar uma imagem do sistema operacional em um conjunto de dimensionamento criptografado.

## <a name="prerequisites"></a>pré-requisitos
Instalar as versões mais recentes do [Azure Powershell](https://github.com/Azure/azure-powershell/releases), que contém os comandos de criptografia.

O Azure Disk Encryption para a versão prévia de conjuntos de dimensionamento de máquinas virtuais requer que você faça o registro da sua própria assinatura usando os seguintes comandos do PowerShell: 

```powershell
Login-AzureRmAccount
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Espere cerca de 10 minutos até que o estado 'Registrado' seja retornado pelo seguinte comando: 

```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Criar um cofre de chaves do Azure habilitado para criptografia de disco
Crie um novo cofre de chaves na mesma assinatura e região do conjunto de dimensionamento e defina a política de acesso 'EnabledForDiskEncryption'.

```powershell
$rgname="windatadiskencryptiontest"
$VaultName="encryptionvault321"

New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $rgName -Location southcentralus -EnabledForDiskEncryption
``` 

Ou habilite um cofre de chaves existente na mesma assinatura e região do conjunto de dimensionamento para a criptografia de disco.

```powershell
$VaultName="encryptionvault321"
Set-AzureRmKeyVaultAccessPolicy -VaultName $VaultName -EnabledForDiskEncryption
```

## <a name="enable-encryption"></a>Habilitar criptografia
Os comandos a seguir criptografam um disco de dados em um conjunto de dimensionamento em execução usando um cofre de chaves no mesmo grupo de recursos. Você também pode usar modelos para criptografar discos em um [conjunto de dimensionamento Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) ou em um [conjunto de dimensionamento Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).

```powershell
$rgname="windatadiskencryptiontest"
$VmssName="nt1vm"
$DiskEncryptionKeyVaultUrl="https://encryptionvault321.vault.azure.net"
$KeyVaultResourceId="/subscriptions/0754ecc2-d80d-426a-902c-b83f4cfbdc95/resourceGroups/windatadiskencryptiontest/providers/Microsoft.KeyVault/vaults/encryptionvault321"

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName `
    -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId –VolumeType Data
```

## <a name="check-encryption-progress"></a>Verificar o andamento da criptografia
Use os comandos a seguir para mostrar o status de criptografia do conjunto de dimensionamento.

```powershell
$rgname="windatadiskencryptiontest"
$VmssName="nt1vm"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName -InstanceId "4"
```

## <a name="disable-encryption"></a>Desabilitar criptografia
Desabilite a criptografia em um conjunto de dimensionamento de máquinas virtuais em execução usando os comandos a seguir. Você também pode usar modelos para desabilitar a criptografia em um [conjunto de dimensionamento Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows) ou em um [conjunto de dimensionamento Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux) em execução.

```powershell
$rgname="windatadiskencryptiontest"
$VmssName="nt1vm"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName
```
