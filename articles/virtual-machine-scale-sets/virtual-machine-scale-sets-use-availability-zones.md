---
title: Criar um conjunto de dimensionamento do Azure que use Zonas de Disponibilidade | Microsoft Docs
description: Saiba como criar conjunto de dimensionamento de máquinas virtuais do Azure que usam Zonas de Disponibilidade para aumentar a redundância contra interrupções
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: iainfou
ms.openlocfilehash: dee06eee045bc24c2864333a66a6d145a771b3ad
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Criar um conjunto de dimensionamento de máquinas virtuais que use Zonas de Disponibilidade
Para proteger seus conjuntos de dimensionamento de máquinas virtuais contra falhas do nível do datacenter, é possível criar um conjunto de dimensionamento entre Zonas de Disponibilidade. Regiões do Azure que oferecem suporte às Zonas de Disponibilidade têm um mínimo de três zonas separadas, cada um com suas próprias e independentes fonte de energia, rede e resfriamento. Para obter mais informações, consulte [Visão geral de zonas de disponibilidade](../availability-zones/az-overview.md).


## <a name="single-zone-and-zone-redundant-scale-sets"></a>Conjuntos de dimensionamento única zona e redundância de zona
Quando você implanta um conjunto de dimensionamento de máquinas virtuais, você pode optar por usar uma única Zona de Disponibilidade em uma região ou várias zonas.

Quando você cria um conjunto de dimensionamento em uma única zona, você controla em qual zona todas as instâncias VM serão executadas, e o conjunto de dimensionamento é gerenciado e escalado automaticamente somente dentro dessa zona. Um conjunto de dimensionamento com redundância de zona permite criar um conjunto de dimensionamento único que abrange várias zonas. Conforme são criadas instâncias VM, por padrão elas são balanceadas igualmente em zonas. Se ocorrer uma interrupção em uma das zonas, um conjunto de dimensionamento não dimensionará automaticamente para aumentar a capacidade. Uma prática recomendada seria configurar regras de dimensionamento automático com base no uso de CPU ou memória. As regras de dimensionamento automático permitem que o conjunto de dimensionamento responda a uma perda das instâncias de VM em uma zona expandindo novas instâncias nas zonas operacionais restantes.

Para usar Zonas de Disponibilidade, seu conjunto de dimensionamento deve ser criado em uma [região do Azure com suporte](../availability-zones/az-overview.md#regions-that-support-availability-zones). Você pode criar um conjunto de dimensionamento que usa Zonas de Disponibilidade com um dos seguintes métodos:

- [Portal do Azure](#use-the-azure-portal)
- [CLI 2.0 do Azure](#use-the-azure-cli-20)
- [PowerShell do Azure](#use-azure-powershell)
- [Modelos do Gerenciador de Recursos do Azure](#use-azure-resource-manager-templates)

## <a name="availability-considerations"></a>Considerações sobre disponibilidade
Começando na API versão 2017-12-01, quando você implanta um conjunto de dimensionamento em uma ou mais zonas, você tem a opção de implantar com "distribuição máx." ou "distribuição estática de 5 domínios de falha". Com a distribuição máxima, o conjunto de dimensionamento distribui suas VMs em no máximo de domínios de falha possíveis dentro de cada zona. Essa distribuição pode ser entre mais ou menos de cinco domínios de falha por zona. Por outro lado, com "distribuição estática de 5 domínios de falha", o conjunto de dimensionamento distribui suas VMs em exatamente 5 domínios de falha por zona. Se o conjunto de dimensionamento não conseguir localizar 5 domínios de falha distintos por zona para atender à solicitação de alocação, a solicitação falhará.

**É recomendável implantar com distribuição máxima para a maioria das cargas de trabalho** porque a distribuição máxima fornece a melhor distribuição na maioria dos casos. Se você precisar que réplicas sejam distribuídas em unidades de isolamento de hardware diferentes, é recomendável distribuir por zonas de disponibilidade e utilizar a distribuição máxima dentro de cada região. Observe que com a distribuição máxima, você verá apenas um domínio de falha na exibição da VM do conjunto de dimensionamento e nos metadados de instância, independentemente de em quantos domínios de falha as VMs estão espalhadas na realidade; a distribuição dentro de cada zona é implícita.

Para usar a distribuição máxima, defina "platformFaultDomainCount" como 1. Para usar a distribuição estática de 5 domínios de falha, defina "platformFaultDomainCount" como 5. Na API versão 2017-12-01, "platformFaultDomainCount" tem como padrão 1 para conjuntos de dimensionamento de única zona e entre zonas. Atualmente, apenas a distribuição estática de 5 domínios de falha tem suporte para conjuntos de dimensionamento regionais.

Além disso, quando você implanta um conjunto de dimensionamento, você tem a opção de implantar com um único [grupo posicionamento](./virtual-machine-scale-sets-placement-groups.md) por zona de disponibilidade ou com vários por zona (para conjuntos de dimensionamento regionais, a opção é ter um único grupo posicionamento na região ou ter vários na região). Para a maioria das cargas de trabalho, é recomendável usar vários grupos de posicionamento, o que permite maior dimensionamento. Na API versão 2017-12-01, os conjuntos de dimensionamento têm como padrão múltiplos grupos de posicionamento para conjuntos de dimensionamento de zona única e entre zonas, mas eles têm como padrão um grupo de posicionamento único para conjuntos de dimensionamento regionais.

>[!NOTE]
> Se você usar a distribuição máxima, deverá usar vários grupos de posicionamento.

Por fim, para conjuntos de dimensionamento implantados em várias regiões, você também tem a opção de escolher "melhor balanceamento de zona possível" ou "balanceamento de zona estrito". Um conjunto de dimensionamento é considerado "balanceado" se o número de VMs em cada zona está dentro de um número de VMs em todas as outras zonas para o conjunto de dimensionamento. Para a instância, um conjunto de dimensionamento com 2 VMs na zona 1, 3 VMs na zona 2 e 3 VMs na zona 3 é considerado balanceado. However, um conjunto de dimensionamento com 1 VM na zona 1, 3 VMs na zona 2 e 3 VMs na zona 3 é considerado não balanceado. É possível que as VMs no conjunto de dimensionamento sejam criadas com êxito, enquanto a extensão nessas VMs falham. Essas VMs com falhas de extensão ainda são contadas ao determinar se um conjunto de dimensionamento está balanceado. Por exemplo, um conjunto de dimensionamento com 3 VMs na zona 1, 3 VMs na zona 2 e 3 VMs na zona 3 é considerado balanceado mesmo se todas as extensões falham na zona 1 e todas as extensões obtêm êxito nas zonas 2 e 3. Com melhor balanceamento de zona possível, o conjunto de dimensionamento tenta reduzir e expandir mantendo o balanceamento. No entanto, se por alguma razão não for possível (por exemplo, uma zona ficar inativa de forma que o conjunto de dimensionamento não possa criar uma nova VM nessa zona), o conjunto de dimensionamento permitirá o desbalanceamento temporário para expandir ou reduzir corretamente. Em tentativas de expansão subsequentes, o conjunto de dimensionamento adiciona VMs a zonas que precisam de mais VMs para que o conjunto de dimensionamento seja balanceado. De forma semelhante, em tentativas de redução subsequentes, o conjunto de dimensionamento remove VMs de zonas que precisam de menos VMs para que o conjunto de dimensionamento seja balanceado. Por outro lado, com "balanceamento de zona estrito", o conjunto de dimensionamento falhará em qualquer tentativa de expandir ou reduzir que cause desbalanceamento.

Para usar o melhor balanceamento de zona possível, defina "zoneBalance" como false (o padrão na API versão 2017-12-01). Para usar o balanceamento de zona estrito, defina "zoneBalance" como true.

## <a name="use-the-azure-portal"></a>Use o Portal do Azure
O processo para criar um conjunto de dimensionamento que use uma Zona de Disponibilidade é o mesmo detalhado no [artigo de introdução](quick-create-portal.md). Certifique-se de que você se [registrou para visualizar as Zonas de Disponibilidade](http://aka.ms/azenroll). Quando você seleciona uma região do Azure com suporte, pode criar um conjunto de dimensionamento em uma das zonas disponíveis, conforme mostrado no exemplo a seguir:

![Criar um conjunto de dimensionamento em uma única Região de Disponibilidade](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

O conjunto de escala e os recursos de suporte, como o balanceador de carga do Azure e o endereço IP público, são criados na zona única especificada por você.


## <a name="use-the-azure-cli-20"></a>Usar a CLI 2.0 do Azure
O processo para criar um conjunto de dimensionamento que use uma Zona de Disponibilidade é o mesmo detalhado no [artigo de introdução](quick-create-cli.md). Para usar Zonas de Disponibilidade, você deve criar seu conjunto de dimensionamento em uma região do Azure com suporte e ter se [registrado para visualizar as Zonas de Disponibilidade](http://aka.ms/azenroll).

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
Para criar um conjunto de dimensionamento com redundância de zona, você usa um endereço IP público de SKU *padrão* e balanceador de carga. Para redundância aprimorada, o SKU *padrão* cria recursos de rede com redundância de zona. Para obter mais informações, veja [Visão geral do Azure Load Balancer Standard](../load-balancer/load-balancer-standard-overview.md). 

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
O processo para criar um conjunto de dimensionamento que use uma Zona de Disponibilidade é o mesmo detalhado no [artigo de introdução](quick-create-powershell.md). Para usar Zonas de Disponibilidade, você deve criar seu conjunto de dimensionamento em uma região do Azure com suporte e ter se [registrado para visualizar as Zonas de Disponibilidade](http://aka.ms/azenroll). Adicione o parâmetro `-Zone` ao comando [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) e especifique qual zona usar (como zona *1*, *2* ou *3*). 

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
Para criar um conjunto de dimensionamento com redundância de zona, você usa um endereço IP público de SKU *padrão* e balanceador de carga. Para redundância aprimorada, o SKU *padrão* cria recursos de rede com redundância de zona. Para obter mais informações, veja [Visão geral do Azure Load Balancer Standard](../load-balancer/load-balancer-standard-overview.md).

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
O processo para criar um conjunto de dimensionamento que use uma Zona de Disponibilidade é o mesmo detalhado no artigo de introdução para [Linux](quick-create-template-linux.md) ou [Windows](quick-create-template-windows.md). Para usar Zonas de Disponibilidade, você deve criar seu conjunto de dimensionamento em uma região do Azure com suporte e ter se [registrado para visualizar as Zonas de Disponibilidade](http://aka.ms/azenroll). Adicione a `zones` propriedade para o tipo de recurso do *Microsoft.Compute/virtualMachineScaleSets* em seu modelo e especifique qual zona usar (como a zona *1*, *2* ou *3*).

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

Para obter um exemplo completo de um conjunto de dimensionamento de zona única e recursos de rede, consulte [este Resource Manager de exemplo](https://github.com/Azure/vm-scale-sets/blob/master/zones/singlezone.json)

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

Para obter um exemplo completo de um conjunto de dimensionamento com redundância de zona e recursos de rede, consulte [este Resource Manager de exemplo](https://github.com/Azure/vm-scale-sets/blob/master/zones/multizone.json)


## <a name="next-steps"></a>Próximas etapas
Agora que você criou conjunto de dimensionamento em uma Zona de Disponibilidade, você pode aprender como [Implantar aplicativos em conjuntos de dimensionamento de máquina virtual](virtual-machine-scale-sets-deploy-app.md) ou [Usar o dimensionamento automático com conjuntos de dimensionamento de máquina virtual](virtual-machine-scale-sets-autoscale-overview.md).
