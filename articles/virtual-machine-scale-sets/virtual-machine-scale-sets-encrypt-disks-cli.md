---
title: Criptografar discos para conjuntos de dimensionamento do Azure com a CLI do Azure | Microsoft Docs
description: Saiba como usar a CLI do Azure para criptografar as instâncias de VM e discos conectados em um conjunto de dimensionamento de máquinas virtuais do Linux
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
ms.openlocfilehash: 1ae352a0292e75eb9a5bf07e3ddca79ca687dea2
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51687377"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli-preview"></a>Criptografar os discos de sistema operacional e de dados anexados em um conjunto de dimensionamento de máquinas virtuais com a CLI do Azure (versão prévia)

Para proteger os dados em repouso usando tecnologia de criptografia padrão da indústria, os conjuntos de dimensionamento de máquinas oferecem suporte ao Azure Disk Encryption (ADE). A criptografia pode ser habilitada para conjuntos de dimensionamento de máquinas virtuais de Windows e Linux. Para obter mais informações, confira [Azure Disk Encryption para Windows e Linux](../security/azure-security-disk-encryption.md).

> [!NOTE]
>  O Azure Disk Encryption para conjuntos de dimensionamento de máquinas virtuais está atualmente em versão prévia pública, disponível em todas as regiões públicas do Azure.

Há suporte do Azure Disk Encryption:
- para conjuntos de dimensionamento criados com discos gerenciados, mas não há suporte para conjuntos de dimensionamento com disco nativo (ou não gerenciado).
- para volumes do sistema operacional e de dados em conjuntos de dimensionamento Windows. Há suporte para desabilitar a criptografia em volumes de sistema operacional e de dados de conjuntos de dimensionamento Windows.
- para volumes de dados em conjuntos de dimensionamento Linux. NÃO há suporte para criptografia de disco do sistema operacional na versão prévia atual de conjuntos de dimensionamento Linux.

Não há suporte para operações de recriação de imagem e atualização de VMs de conjunto de dimensionamento na versão prévia atual. O Azure Disk Encryption para a versão prévia dos conjuntos de dimensionamento de máquinas virtuais é recomendado apenas em ambientes de teste. Na versão prévia, não habilite a criptografia de disco em ambientes de produção, onde você talvez precise atualizar uma imagem do sistema operacional em um conjunto de dimensionamento criptografado.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tutorial exigirá que você esteja executando a CLI do Azure versão 2.0.31 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="register-for-disk-encryption-preview"></a>Registre-se para visualização de criptografia de disco

O Azure Disk Encryption para a versão prévia de conjuntos de dimensionamento de máquinas virtuais requer que você faça o registro da sua própria assinatura com [az feature register](/cli/azure/feature#az_feature_register). Você só precisará executar as seguintes etapas na primeira vez que você usar o recurso de visualização de criptografia de disco:

```azurecli-interactive
az feature register --name UnifiedDiskEncryption --namespace Microsoft.Compute
```

Pode levar até 10 minutos para que a solicitação registro seja propagada. É possível verificar o status do registro com [az feature show](/cli/azure/feature#az_feature_show). Quando `State` reportar *Registrado*, registre novamente o *provedor Microsoft.Compute* com [registro do provedor de sincronização az](/cli/azure/provider#az_provider_register):

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="create-a-scale-set"></a>Criar um conjunto de escala

Antes de criar uma máquina virtual, crie um grupo de recursos com o [az group create](/cli/azure/group#az_group_create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Crie um conjunto de dimensionamento de máquinas virtuais com [az vmss create](/cli/azure/vmss#az_vmss_create). O exemplo a seguir cria um conjunto de dimensionamento chamado *myScaleSet* que está definido para atualizar automaticamente à medida que alterações são aplicadas e gera chaves SSH caso elas não existam em *~/.ssh/id_rsa*. Um disco de dados de 32Gb é anexado a cada instância VM e a [extensão de Script personalizado](../virtual-machines/linux/extensions-customscript.md) do Azure é usada para preparar os discos de dados com [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set):

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

Leva alguns minutos para criar e configurar todos os recursos e as VMs do conjunto de dimensionamento.

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Criar um cofre de chaves do Azure habilitado para criptografia de disco

O Cofre de Chaves do Azure pode armazenar chaves, segredos ou senhas que permitem implementá-los de forma segura em seus aplicativos e serviços. Chaves e criptográficas são armazenadas no Cofre de chaves do Azure usando a proteção de software ou você pode importar ou gerar as chaves em Módulos de segurança de Hardware (HSMs) certificados para padrões de nível 2 de FIPS 140-2. Essas chaves criptográficas são usadas para criptografar e descriptografar os discos virtuais conectados à sua VM. Você mantém o controle dessas chaves criptográficas e pode auditar seu uso.

Defina seu próprio *keyvault_name* exclusivo. Em seguida, crie um cofre de chaves com [az keyvault create](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) na mesma assinatura e região do conjunto de dimensionamento e defina a política de acesso *--enabled-for-disk-encryption*.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Usar um Key Vault existente

Essa etapa só é necessária se você tiver um Key Vault existente que você deseje usar com a criptografia de disco. Ignore esta etapa se você tiver criado um Key Vault na seção anterior.

Defina seu próprio *keyvault_name* exclusivo. Em seguida, atualize o KeyVault com [az keyvault update](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) e defina a política de acesso *--enabled-for-disk-encryption*.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Habilitar criptografia

Para criptografar as instâncias de VM em um conjunto de dimensionamento, primeiro obtenha algumas informações sobre a ID de recursos do Key Vault com [az keyvault show](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show). Essas variáveis são usadas para iniciar o processo de criptografia com [az vmss encryption enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable):

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

Pode levar um minuto ou dois para iniciar o processo de criptografia.

Como o conjunto de dimensionamento atualiza a política no conjunto de dimensionamento criado em uma etapa anterior definida como *automática*, as instâncias de VM iniciam automaticamente o processo de criptografia. Em conjuntos de dimensionamento em que a política de atualização é manual, inicie a política de criptografia nas instâncias de VM com [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances).

## <a name="check-encryption-progress"></a>Verificar o andamento da criptografia

Para verificar o status da criptografia de disco, use [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show):

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Quando instâncias de VM são criptografadas, o código relata *EncryptionState/encrypted*, conforme mostrado no seguinte exemplo de saída:

```bash
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>Desabilitar criptografia

Se você não quiser mais usar discos de instâncias de VM criptografadas, você pode desabilitar a criptografia com [az vmss encryption disable](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable) da seguinte maneira:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você usou a CLI do Azure para criptografar um conjunto de dimensionamento de máquinas virtuais. Você também pode usar o [Azure PowerShell](virtual-machine-scale-sets-encrypt-disks-ps.md) ou modelos para [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) ou [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).

Um exemplo de arquivo em lotes de ponta a ponta para criptografia de disco de dados do conjunto de dimensionamento Linux pode ser encontrado [aqui](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat). Este exemplo cria um grupo de recursos e o conjunto de dimensionamento Linux, monta um disco de dados de 5 GB e criptografa o conjunto de dimensionamento de máquinas virtuais.
