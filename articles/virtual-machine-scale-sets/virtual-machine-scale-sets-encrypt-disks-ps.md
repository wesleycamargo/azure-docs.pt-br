---
title: Criptografar discos para conjuntos de dimensionamento com o Azure PowerShell | Microsoft Docs
description: Saiba como usar o Azure PowerShell para criptografar as instâncias de VM e discos conectados em conjuntos de escala de máquinas virtuais do Windows
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: cynthn
ms.openlocfilehash: cc1405d2dd972aff6091a9d5b60ff9da18185286
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978095"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell-preview"></a>Criptografar os discos de sistema operacional e de dados anexados em um conjunto de dimensionamento de máquinas virtuais com o Azure PowerShell (versão prévia)

Para proteger os dados em repouso usando tecnologia de criptografia padrão da indústria, os conjuntos de dimensionamento de máquinas oferecem suporte ao Azure Disk Encryption (ADE). A criptografia pode ser habilitada para conjuntos de dimensionamento de máquinas virtuais de Windows e Linux. Para saber mais, confira [Azure Disk Encryption para Windows e Linux](../security/azure-security-disk-encryption.md).

> [!NOTE]
>  O Azure Disk Encryption para conjuntos de dimensionamento de máquinas virtuais está atualmente em versão prévia pública, disponível em todas as regiões públicas do Azure.

Há suporte do Azure Disk Encryption:
- para conjuntos de dimensionamento criados com discos gerenciados, mas não há suporte para conjuntos de dimensionamento com disco nativo (ou não gerenciado).
- para volumes do sistema operacional e de dados em conjuntos de dimensionamento Windows. Há suporte para desabilitar a criptografia em volumes de sistema operacional e de dados de conjuntos de dimensionamento Windows.
- para volumes de dados em conjuntos de dimensionamento Linux. NÃO há suporte para criptografia de disco do sistema operacional na versão prévia atual de conjuntos de dimensionamento Linux.

Não há suporte para operações de recriação de imagem e atualização de VMs de conjunto de dimensionamento na versão prévia atual. O Azure Disk Encryption para a versão prévia dos conjuntos de dimensionamento de máquinas virtuais é recomendado apenas em ambientes de teste. Na versão prévia, não habilite a criptografia de disco em ambientes de produção, onde você talvez precise atualizar uma imagem do sistema operacional em um conjunto de dimensionamento criptografado.

[!INCLUDE [updated-for-az-vm.md](../../includes/updated-for-az-vm.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]


## <a name="register-for-disk-encryption-preview"></a>Registre-se para visualização de criptografia de disco

O Azure Disk Encryption para a versão prévia dos conjuntos de dimensionamento de máquinas virtuais requer que você faça o registro da sua própria assinatura com [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature). Você só precisará executar as seguintes etapas na primeira vez que você usar o recurso de visualização de criptografia de disco:

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```


Pode levar até 10 minutos para que a solicitação registro seja propagada. É possível verificar o status do registro com [Get-AzProviderFeature](/powershell/module/az.resources/Get-AzProviderFeature). Quando o `RegistrationState` relatar *Registrado*, registre novamente o provedor *Microsoft.Compute* com [Register-AzResourceProvider](/powershell/module/az.resources/Register-AzResourceProvider):


```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Criar um Azure Key Vault habilitado para criptografia de disco

O Cofre de Chaves do Azure pode armazenar chaves, segredos ou senhas que permitem implementá-los de forma segura em seus aplicativos e serviços. Chaves e criptográficas são armazenadas no Cofre de chaves do Azure usando a proteção de software ou você pode importar ou gerar as chaves em Módulos de segurança de Hardware (HSMs) certificados para padrões de nível 2 de FIPS 140-2. Essas chaves criptográficas são usadas para criptografar e descriptografar os discos virtuais conectados à sua VM. Você mantém o controle dessas chaves criptográficas e pode auditar seu uso.

Crie um Key Vault com [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Para permitir que o Key Vault seja usado para criptografia de disco, defina o parâmetro *EnabledForDiskEncryption*. O exemplo a seguir também define variáveis para o nome do grupo de recursos, o nome do Key Vault e o local. Forneça seu próprio nome exclusivo do Key Vault:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Usar um Key Vault existente

Essa etapa só é necessária se você tiver um Key Vault existente que você deseje usar com a criptografia de disco. Ignore esta etapa se você tiver criado um Key Vault na seção anterior.

Você pode habilitar um Key Vault existente na mesma assinatura e região do conjunto de dimensionamento para a criptografia de disco com [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy). Defina o nome do seu Key Vault existente na variável *$vaultName* da seguinte maneira:


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Criar um conjunto de escala

Primeiro, defina o nome de usuário e a senha de um administrador para as instâncias de VM com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora crie um conjunto de dimensionamento de máquinas virtuais com [New-AzVmss](/powershell/module/az.compute/new-azvmss). Para distribuir o tráfego para as instâncias de VM individuais, um balanceador de carga também é criado. O balanceador de carga inclui regras para distribuir o tráfego na porta TCP 80, além de permitir o tráfego de área de trabalho remota na porta TCP 3389 e comunicação remota do PowerShell na porta TCP 5985:

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```

## <a name="enable-encryption"></a>Habilitar criptografia

Para criptografar as instâncias de VM em um conjunto de dimensionamento, primeiro obtenha algumas informações sobre a ID de recursos e o URI do Key Vault com [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault). Essas variáveis são usadas para iniciar o processo de criptografia com [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension):


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

Quando solicitado, digite *y* para continuar o processo de criptografia de disco nas instâncias de VM do conjunto de dimensionamento.

## <a name="check-encryption-progress"></a>Verificar o andamento da criptografia

Para verificar o status da criptografia de disco, use [Get-AzVmssDiskEncryption](/powershell/module/az.compute/Get-AzVmssDiskEncryption):


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

Quando instâncias de VM são criptografadas, o código *EncryptionSummary* relata *ProvisioningState/succeeded* conforme mostrado no seguinte exemplo de saída:

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="disable-encryption"></a>Desabilitar criptografia

Se você não quiser mais usar discos de instâncias de VM criptografadas, poderá desabilitar a criptografia com [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) da seguinte maneira:


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você usou o Azure PowerShell para criptografar um conjunto de dimensionamento de máquinas virtuais. Você também pode usar a [CLI do Azure](virtual-machine-scale-sets-encrypt-disks-cli.md) ou modelos para [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) ou [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).
