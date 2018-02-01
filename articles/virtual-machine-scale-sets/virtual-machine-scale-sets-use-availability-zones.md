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
ms.date: 01/11/2018
ms.author: iainfou
ms.openlocfilehash: 397afc28b5f4c4f7f84afde13b6d031d83aaced4
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones-preview"></a>Criar um conjunto de dimensionamento de máquinas virtuais que use Zonas de Disponibilidade (versão prévia)
Para proteger seus conjuntos de dimensionamento de máquinas virtuais contra falhas do nível do datacenter, é possível criar um conjunto de dimensionamento entre Zonas de Disponibilidade. Regiões do Azure que oferecem suporte às Zonas de Disponibilidade têm um mínimo de três zonas separadas, cada um com suas próprias e independentes fonte de energia, rede e resfriamento. Para obter mais informações, consulte [Visão geral de zonas de disponibilidade](../availability-zones/az-overview.md).

[!INCLUDE [availability-zones-preview-statement.md](../../includes/availability-zones-preview-statement.md)]


## <a name="single-zone-and-zone-redundant-scale-sets"></a>Conjuntos de dimensionamento única zona e redundância de zona
Quando você implanta um conjunto de dimensionamento de máquinas virtuais, você pode optar por usar uma única Zona de Disponibilidade em uma região ou várias zonas.

Quando você cria um conjunto de dimensionamento em uma única zona, você controla em qual zona todas as instâncias VM serão executadas, e o conjunto de dimensionamento é gerenciado e escalado automaticamente somente dentro dessa zona. O diagrama a seguir mostra um exemplo de como você pode criar vários conjunto de dimensionamento de zona única com um balanceador de carga com redundância de zona que distribui o tráfego:

![Implantação de conjunto de dimensionamento de zona única com o balanceador de carga com redundância de zona](media/virtual-machine-scale-sets-use-availability-zones/zonal-vmss.png)

Um conjunto de dimensionamento com redundância de zona permite criar um conjunto de dimensionamento único que abrange várias zonas. Conforme são criadas instâncias VM, por padrão elas são balanceadas igualmente em zonas. Se ocorrer uma interrupção em uma das zonas, um conjunto de dimensionamento não dimensionará automaticamente para aumentar a capacidade. Uma prática recomendada seria configurar regras de dimensionamento automático com base no uso de CPU ou memória. As regras de dimensionamento automático permitem que o conjunto de dimensionamento responda a uma perda das instâncias de VM em uma zona expandindo novas instâncias nas zonas operacionais restantes. O diagrama a seguir mostra um exemplo de um conjunto de dimensionamento único que é implantado em várias zonas:

![Implantação de conjunto de dimensionamento com redundância de zona e balanceador de carga](media/virtual-machine-scale-sets-use-availability-zones/zone-redundant-vmss.png)

Para usar Zonas de Disponibilidade, seu conjunto de dimensionamento deve ser criado em uma [região do Azure com suporte](../availability-zones/az-overview.md#regions-that-support-availability-zones). Você também precisa [registrar-se para visualizar as Zonas de Disponibilidade](http://aka.ms/azenroll). Você pode criar um conjunto de dimensionamento que usa Zonas de Disponibilidade com um dos seguintes métodos:

- [portal do Azure](#use-the-azure-portal)
- [CLI 2.0 do Azure](#use-the-azure-cli-20)
- [PowerShell do Azure](#use-azure-powershell)
- [Modelos do Gerenciador de Recursos do Azure](#use-azure-resource-manager-templates)


## <a name="use-the-azure-portal"></a>Use o Portal do Azure
O processo para criar um conjunto de dimensionamento que use uma Zona de Disponibilidade é o mesmo detalhado no [artigo de introdução](virtual-machine-scale-sets-create-portal.md). Certifique-se de que você se [registrou para visualizar as Zonas de Disponibilidade](http://aka.ms/azenroll). Quando você seleciona uma região do Azure com suporte, pode criar um conjunto de dimensionamento em uma das zonas disponíveis, conforme mostrado no exemplo a seguir:

![Criar um conjunto de dimensionamento em uma única Região de Disponibilidade](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

O conjunto de escala e os recursos de suporte, como o balanceador de carga do Azure e o endereço IP público, são criados na zona única especificada por você.


## <a name="use-the-azure-cli-20"></a>Usar a CLI 2.0 do Azure
O processo para criar um conjunto de dimensionamento que use uma Zona de Disponibilidade é o mesmo detalhado no [artigo de introdução](virtual-machine-scale-sets-create-cli.md). Para usar Zonas de Disponibilidade, você deve criar seu conjunto de dimensionamento em uma região do Azure com suporte e ter se [registrado para visualizar as Zonas de Disponibilidade](http://aka.ms/azenroll).

Adicione o parâmetro `--zones` ao comando [az vmss create](/cli/azure/vmss#az_vmss_create) e especifique qual zona usar (como zona *1*, *2* ou *3*). O exemplo a seguir cria um conjunto de dimensionamento de zona única chamado *myScaleSet* na zona *1*:

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
Para obter um exemplo completo de um conjunto de dimensionamento de zona única e recursos de rede, consulte [este script CLI de exemplo](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh.)

### <a name="zone-redundant-scale-set"></a>Conjunto de dimensionamento com redundância de zona
Para criar um conjunto de dimensionamento com redundância de zona, você usa um endereço IP público de SKU *padrão* e balanceador de carga. Para redundância aprimorada, o SKU *padrão* cria recursos de rede com redundância de zona. Para obter mais informações, veja [Visão geral do Azure Load Balancer Standard](../load-balancer/load-balancer-standard-overview.md). Na primeira vez que você criar um conjunto de dimensionamento com redundância de zona ou balanceador de carga, você deve concluir as seguintes etapas para registrar sua conta para estes recursos de visualização.

1. Registre sua conta para o conjunto de dimensionamento com redundância de zona e a rede recursos com [registro de recurso az](/cli/azure/feature#az_feature_register) da seguinte maneira:

    ```azurecli
    az feature register --name MultipleAvailabilityZones --namespace Microsoft.Compute
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    ```
    
2. Isso pode levar alguns minutos para registrar para os recursos. É possível verificar o status da operação com [Mostrar recursos az](/cli/azure/feature#az_feature_show):

    ```azurecli
    az feature show --name MultipleAvailabilityZones --namespace Microsoft.Compute
    az feature show --name AllowLBPreview --namespace Microsoft.Network
    ```

    O exemplo a seguir mostra o status desejado do recurso como *Registrado*:
    
    ```json
    "properties": {
          "state": "Registered"
       },
    ```

3. Depois que o conjunto de dimensionamento com redundância de zona e a rede de recursos relatam como *Registrado*, registre novamente os provedores *de Computação* e *Rede* com [registro de provedor az](/cli/azure/provider#az_provider_register) da seguinte maneira:

    ```azurecli
    az provider register --namespace Microsoft.Compute
    az provider register --namespace Microsoft.Network
    ```

Para criar um conjunto de dimensionamento com redundância de zona, especifique várias zonas com o parâmetro `--zones`. O exemplo a seguir cria um conjunto de dimensionamento com redundância de zona chamado *myScaleSet* nas zonas *1,2,3*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones {1,2,3}
```

Leva alguns minutos para criar e configurar todos os recursos e as VMs do conjunto de dimensionamento na zona especificada por você. Para obter um exemplo completo de um conjunto de dimensionamento com redundância de zona e recursos de rede, consulte [este script CLI de exemplo](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)


## <a name="use-azure-powershell"></a>Usar PowerShell do Azure
O processo para criar um conjunto de dimensionamento que use uma Zona de Disponibilidade é o mesmo detalhado no [artigo de introdução](virtual-machine-scale-sets-create-powershell.md). Para usar Zonas de Disponibilidade, você deve criar seu conjunto de dimensionamento em uma região do Azure com suporte e ter se [registrado para visualizar as Zonas de Disponibilidade](http://aka.ms/azenroll). Adicione o parâmetro `-Zone` ao comando [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) e especifique qual zona usar (como zona *1*, *2* ou *3*). 

O exemplo a seguir cria uma configuração de conjunto de dimensionamento de zona única chamada *vmssConfig* em *East US 2*, zona *1*:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

Para obter um exemplo completo de um conjunto de dimensionamento de zona única e recursos de rede, consulte [este script PowerShell de exemplo](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1)

### <a name="zone-redundant-scale-set"></a>Conjunto de dimensionamento com redundância de zona
Para criar um conjunto de dimensionamento com redundância de zona, você usa um endereço IP público de SKU *padrão* e balanceador de carga. Para redundância aprimorada, o SKU *padrão* cria recursos de rede com redundância de zona. Para obter mais informações, veja [Visão geral do Azure Load Balancer Standard](../load-balancer/load-balancer-standard-overview.md). Na primeira vez que você criar um conjunto de dimensionamento com redundância de zona ou balanceador de carga, você deve concluir as seguintes etapas para registrar sua conta para estes recursos de visualização.

1. Registre sua conta para o conjunto de dimensionamento com redundância de zona e a rede recursos com [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) da seguinte maneira:

    ```powershell
    Register-AzureRmProviderFeature -FeatureName MultipleAvailabilityZones -ProviderNamespace Microsoft.Compute
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```
    
2. Isso pode levar alguns minutos para registrar para os recursos. É possível verificar o status da operação com [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature):

    ```powershell
    Get-AzureRmProviderFeature -FeatureName MultipleAvailabilityZones -ProviderNamespace Microsoft.Compute 
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

    O exemplo a seguir mostra o status desejado do recurso como *Registrado*:
    
    ```powershell
    RegistrationState
    -----------------
    Registered
    ```

3. Depois que o conjunto de dimensionamento com redundância de zona e a rede de recursos relatam como *Registrado*, registre novamente os provedores *de Computação* e *Rede* com [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) da seguinte maneira:

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```

Para criar um conjunto de dimensionamento com redundância de zona, especifique várias zonas com o parâmetro `-Zone`. O exemplo a seguir cria uma configuração de conjunto de dimensionamento com redundância de zona chamado *myScaleSet* em *East US 2*, zonas *1, 2, 3*:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1", "2", "3"
```

Se você criar um endereço IP público com [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) ou um balanceador de carga com [New-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/New-AzureRmLoadBalancer), especifique o *- SKU "Standard"* para criar recursos de rede com redundância de zona. Você também precisa criar um grupo de segurança de rede e regras para permitir que qualquer tráfego. Para obter mais informações, veja [Visão geral do Azure Load Balancer Standard](../load-balancer/load-balancer-standard-overview.md).

Para obter um exemplo completo de um conjunto de dimensionamento com redundância de zona e recursos de rede, consulte [este script PowerShell de exemplo](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.ps1)


## <a name="use-azure-resource-manager-templates"></a>Usar modelos do Gerenciador de Recursos do Azure
O processo para criar um conjunto de dimensionamento que use uma Zona de Disponibilidade é o mesmo detalhado no artigo de introdução para [Linux](virtual-machine-scale-sets-create-template-linux.md) ou [Windows](virtual-machine-scale-sets-create-template-windows.md). Para usar Zonas de Disponibilidade, você deve criar seu conjunto de dimensionamento em uma região do Azure com suporte e ter se [registrado para visualizar as Zonas de Disponibilidade](http://aka.ms/azenroll). Adicione a `zones` propriedade para o tipo de recurso do *Microsoft.Compute/virtualMachineScaleSets* em seu modelo e especifique qual zona usar (como a zona *1*, *2* ou *3*).

O exemplo a seguir cria um conjunto de dimensionamento de zona única do Linux chamado *myScaleSet* em *East US 2*, *zona 1*:

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

Para obter um exemplo completo de um conjunto de dimensionamento de zona única e recursos de rede, consulte [este Resource Manager de exemplo](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>Conjunto de dimensionamento com redundância de zona
Para criar um conjunto de dimensionamento com redundância de zona, especifique vários valores na propriedade `zones` para o tipo de recurso *Microsoft.Compute/virtualMachineScaleSets*. O exemplo a seguir cria um conjunto de dimensionamento com redundância de zona chamado *myScaleSet* em *East US 2*, zonas *1,2,3*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

Se você criar um endereço IP público ou um balanceador de carga, especifique a propriedade *"sku": {"name": "Padrão"} "* para criar recursos de rede com redundância de zona. Você também precisa criar um grupo de segurança de rede e regras para permitir que qualquer tráfego. Para obter mais informações, veja [Visão geral do Azure Load Balancer Standard](../load-balancer/load-balancer-standard-overview.md).

Para obter um exemplo completo de um conjunto de dimensionamento com redundância de zona e recursos de rede, consulte [este Resource Manager de exemplo](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)


## <a name="next-steps"></a>Próximas etapas
Agora que você criou conjunto de dimensionamento em uma Zona de Disponibilidade, você pode aprender como [Implantar aplicativos em conjuntos de dimensionamento de máquina virtual](virtual-machine-scale-sets-deploy-app.md) ou [Usar o dimensionamento automático com conjuntos de dimensionamento de máquina virtual](virtual-machine-scale-sets-autoscale-overview.md).
