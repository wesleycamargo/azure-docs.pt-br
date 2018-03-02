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
ms.openlocfilehash: 3263ed4d6325207f38656d741fa4a1f091d4fdea
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

Um exemplo de arquivo em lotes de ponta a ponta para criptografia de disco de dados do conjunto de dimensionamento Linux pode ser encontrado [aqui](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat).  Este exemplo cria um grupo de recursos e o conjunto de dimensionamento Linux, monta um disco de dados de 5 GB e criptografa o conjunto de dimensionamento de máquinas virtuais.

## <a name="prerequisites"></a>pré-requisitos
Instalar as versões mais recentes do [Azure Powershell](https://github.com/Azure/azure-powershell/releases) ou da [CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), que contém os comandos de criptografia.

A Azure Disk Encryption para a versão prévia de conjuntos de dimensionamento de máquinas virtuais requer que você faça o registro da sua própria assinatura usando os seguintes comandos do PowerShell: 

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

```azurecli
rgname="linuxdatadiskencryptiontest"
VaultName="encryptionvault321"

az keyvault create --name $VaultName --resource-group $rgname --enabled-for-disk-encryption
```

Ou habilite um cofre de chaves existente na mesma assinatura e região do conjunto de dimensionamento para a criptografia de disco.

```azurecli
VaultName="encryptionvault321"
az keyvault update --name $VaultName --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Habilitar criptografia

Os comandos a seguir criptografam um disco de dados em um conjunto de dimensionamento em execução usando um cofre de chaves no mesmo grupo de recursos. Você também pode usar modelos para criptografar discos em um [conjunto de dimensionamento Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) ou em um [conjunto de dimensionamento Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"
EncryptionKeyVaultUrl="https://encryptionvaultlinuxsf.vault.azure.net"
VaultResourceId="/subscriptions/0754ecc2-d80d-426a-902c-b83f4cfbdc95/resourceGroups/linuxdatadiskencryptiontest/providers/Microsoft.KeyVault/vaults/encryptionvaultlinuxsf"

az vmss encryption enable -g $ResourceGroup -n $VmssName --disk-encryption-keyvault $VaultResourceId --volume-type DATA
az vmss update-instances -g $ResourceGroup -n $VmssName --instance-ids *
```

## <a name="check-encryption-progress"></a>Verificar o andamento da criptografia

Use os comandos a seguir para mostrar o status de criptografia do conjunto de dimensionamento.

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"

az vmss encryption show -g $ResourceGroup -n $VmssName
```

## <a name="disable-encryption"></a>Desabilitar criptografia
Desabilite a criptografia em um conjunto de dimensionamento de máquinas virtuais em execução usando os comandos a seguir. Você também pode usar modelos para desabilitar a criptografia em um [conjunto de dimensionamento de VMs Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows) ou em um [conjunto de dimensionamento de VMs Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux) em execução.

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"

az vmss encryption disable -g $ResourceGroup -n $VmssName
```

