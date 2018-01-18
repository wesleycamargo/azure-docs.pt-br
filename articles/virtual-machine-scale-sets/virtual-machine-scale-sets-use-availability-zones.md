---
title: "Criar um conjunto de dimensionamento do Azure que use Zonas de Disponibilidade (versão prévia) | Microsoft Docs"
description: "Saiba como criar conjunto de dimensionamento de máquinas virtuais do Azure que usam Zonas de Disponibilidade para aumentar a redundância contra interrupções"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: iainfou
ms.openlocfilehash: 310fdc68d3eb662906053d2bc0c45e6cfa18d4da
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones-preview"></a>Criar um conjunto de dimensionamento de máquinas virtuais que use Zonas de Disponibilidade (versão prévia)
Para proteger seus conjuntos de dimensionamento de máquinas virtuais contra falhas do nível do datacenter, é possível criar um conjunto de dimensionamento em uma Zona de Disponibilidade. Regiões do Azure que oferecem suporte às Zonas de Disponibilidade têm um mínimo de três zonas separadas, cada um com suas próprias e independentes fonte de energia, rede e resfriamento. Para obter mais informações, consulte [Visão geral de zonas de disponibilidade](../availability-zones/az-overview.md).

Para usar Zonas de Disponibilidade, seu conjunto de dimensionamento deve ser criado em uma [região do Azure com suporte](../availability-zones/az-overview.md#regions-that-support-availability-zones). Você pode criar um conjunto de dimensionamento que usa Zonas de Disponibilidade com um dos seguintes métodos:

- [portal do Azure](#use-the-azure-portal)
- [CLI 2.0 do Azure](#use-the-azure-cli-20)
- [PowerShell do Azure](#use-azure-powershell)
- [Modelos do Gerenciador de Recursos do Azure](#use-azure-resource-manager-templates)


## <a name="use-the-azure-portal"></a>Use o Portal do Azure
O processo para criar um conjunto de dimensionamento que use uma Zona de Disponibilidade é o mesmo detalhado no [artigo de introdução](virtual-machine-scale-sets-create-portal.md). Quando você seleciona uma região do Azure com suporte, pode criar um conjunto de dimensionamento em uma das zonas disponíveis, conforme mostrado no exemplo a seguir:

![Criar um conjunto de dimensionamento em uma única Região de Disponibilidade](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

O conjunto de escala e os recursos de suporte, como o balanceador de carga do Azure e o endereço IP público, são criados na zona única especificada por você.


## <a name="use-the-azure-cli-20"></a>Usar a CLI 2.0 do Azure
O processo para criar um conjunto de dimensionamento que use uma Zona de Disponibilidade é o mesmo detalhado no [artigo de introdução](virtual-machine-scale-sets-create-cli.md). Para usar Zonas de Disponibilidade, você deve criar seu conjunto de dimensionamento em uma região do Azure com suporte. Adicione o parâmetro `--zones` ao comando [az vmss create](/cli/azure/vmss#az_vmss_create) e especifique qual zona usar (como zona *1*, *2* ou *3*). O exemplo a seguir cria um conjunto de dimensionamento chamado *myScaleSet* na zona *1*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```

Leva alguns minutos para criar e configurar todos os recursos e as VMs do conjunto de dimensionamento na zona especificada por você.


## <a name="use-azure-powershell"></a>Usar PowerShell do Azure
O processo para criar um conjunto de dimensionamento que use uma Zona de Disponibilidade é o mesmo detalhado no [artigo de introdução](virtual-machine-scale-sets-create-powershell.md). Para usar Zonas de Disponibilidade, você deve criar seu conjunto de dimensionamento em uma região do Azure com suporte. Adicione o parâmetro `-Zone` ao comando [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) e especifique qual zona usar (como zona *1*, *2* ou *3*). O exemplo a seguir cria uma configuração de conjunto de escala chamada *vmssConfig* em *East US 2*, zona *1*:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

Para criar o conjunto de dimensionamento real, siga as etapas adicionais conforme detalhado no [artigo de introdução](virtual-machine-scale-sets-create-powershell.md).


## <a name="use-azure-resource-manager-templates"></a>Usar modelos do Gerenciador de Recursos do Azure
O processo para criar um conjunto de dimensionamento que use uma Zona de Disponibilidade é o mesmo detalhado no artigo de introdução para [Linux](virtual-machine-scale-sets-create-template-linux.md) ou [Windows](virtual-machine-scale-sets-create-template-windows.md). Para usar Zonas de Disponibilidade, você deve criar seu conjunto de dimensionamento em uma região do Azure com suporte. Adicione a `zones` propriedade para o tipo de recurso do *Microsoft.Compute/virtualMachineScaleSets* em seu modelo e especifique qual zona usar (como a zona *1*, *2* ou *3*). O exemplo a seguir cria um conjunto de dimensionamento do Linux chamado *myScaleSet* em *East US 2*, *zona 1*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

Para criar o conjunto de dimensionamento real, siga as etapas adicionais conforme detalhado no artigo de introdução para [Linux](virtual-machine-scale-sets-create-template-linux.md) ou [Windows](virtual-machine-scale-sets-create-template-windows.md)


## <a name="next-steps"></a>Próximas etapas
Agora que você criou conjunto de dimensionamento em uma Zona de Disponibilidade, você pode aprender como [Implantar aplicativos em conjuntos de dimensionamento de máquina virtual](virtual-machine-scale-sets-deploy-app.md) ou [Usar o dimensionamento automático com conjuntos de dimensionamento de máquina virtual](virtual-machine-scale-sets-autoscale-overview.md).
