---
title: "Modificar um conjunto de dimensionamento de máquinas virtuais do Azure | Microsoft Docs"
description: "Modificar um conjunto de dimensionamento de máquinas virtuais do Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: negat
ms.openlocfilehash: cdd1015f63e80b7ec51565c18f3440ce1828fb03
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2018
---
# <a name="modify-a-virtual-machine-scale-set"></a>Modificar um conjunto de dimensionamento de máquinas virtuais
Este artigo descreve como modificar um conjunto de dimensionamento existente. Ele contém instruções de como alterar a configuração do conjunto de dimensionamento, como alterar a configuração de aplicativos em execução no conjunto de dimensionamento, como gerenciar a disponibilidade e muito mais.

## <a name="fundamental-concepts"></a>Conceitos fundamentais

### <a name="the-scale-set-model"></a>O modelo do conjunto de dimensionamento

Um conjunto de dimensionamento tem um "modelo de conjunto de dimensionamento" que captura o estado *desejado* do conjunto de dimensionamento como um todo. Para consultar o modelo de um conjunto de dimensionamento, você pode usar:

API REST: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` (para obter mais informações, consulte a [Documentação da API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/get))

Powershell: `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (para obter mais informações, consulte a [Documentação do Powershell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss))

CLI: `az vmss show -g {resourceGroupName} -n {vmSaleSetName}` (para obter mais informações, consulte a [Documentação da CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show))

Você também pode usar [resources.azure.com](https://resources.azure.com) ou [SDKs do Azure](https://azure.microsoft.com/downloads/) para consultar o modelo de um conjunto de dimensionamento.

A apresentação exata da saída depende das opções que você fornecer para o comando, mas alguns exemplos de saídas da CLI são apresentados a seguir:

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
{
  "location": "westus",
  "overprovision": true,
  "plan": null,
  "singlePlacementGroup": true,
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
  .
  .
  .
}
```

Como você pode ver, essas propriedades se aplicam ao conjunto de dimensionamento como um todo.



### <a name="the-scale-set-instance-view"></a>A exibição de instância do conjunto de dimensionamento

Um conjunto de dimensionamento também tem uma "exibição de instância do conjunto de dimensionamento" que captura o estado atual do *tempo de execução* do conjunto de dimensionamento como um todo. Para consultar a exibição de instância de um conjunto de dimensionamento, você pode usar:

API REST: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version={apiVersion}` (para obter mais informações, consulte a [Documentação da API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview))

Powershell: `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceView` (para obter mais informações, consulte a [Documentação do Powershell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss))

CLI: `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName}` (para obter mais informações, consulte a [Documentação da CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view))

Você também pode usar [resources.azure.com](https://resources.azure.com) ou [SDKs do Azure](https://azure.microsoft.com/downloads/) para consultar a exibição de instância de um conjunto de dimensionamento.

A apresentação exata da saída depende das opções que você fornecer para o comando, mas um exemplo de saídas da CLI é apresentado a seguir:

```
$ az vmss get-instance-view -g {resourceGroupName} -n {virtualMachineScaleSetName}
{
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": null,
      "time": "{time}"
    }
  ],
  "virtualMachine": {
    "additionalProperties": {},
    "statusesSummary": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "count": 1
      }
    ]
  }
  .
  .
  .
}
```

Como você pode ver, essas propriedades fornecem um resumo do estado atual de tempo de execução das VMs no conjunto de dimensionamento, incluindo o status das extensões aplicadas ao conjunto de dimensionamento (omitido para fins de brevidade).



### <a name="the-scale-set-vm-model-view"></a>A exibição do modelo de VM do conjunto de dimensionamento

Semelhante a como um conjunto de dimensionamento tem uma exibição de modelo, cada VM no conjunto de dimensionamento tem sua própria exibição de modelo. Para consultar a exibição do modelo de um conjunto de dimensionamento, você pode usar:

API REST: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}?api-version={apiVersion}` (para obter mais informações, consulte a [Documentação da API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/get))

Powershell: `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` (para obter mais informações, consulte a [Documentação do Powershell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm))

CLI: `az vmss show -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` (para obter mais informações, consulte a [Documentação da CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show))

Você também pode usar [resources.azure.com](https://resources.azure.com) ou [SDKs do Azure](https://azure.microsoft.com/downloads/) para consultar o modelo de uma VM em um conjunto de dimensionamento.

A apresentação exata da saída depende das opções que você fornecer para o comando, mas alguns exemplos de saídas da CLI são apresentados a seguir:

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
  .
  .
  .
}
```

Como você pode ver, essas propriedades descrevem a configuração da VM em si e não a configuração do conjunto de dimensionamento como um todo. Por exemplo, o modelo do conjunto de dimensionamento tem `overprovision` como propriedade, enquanto o modelo de uma VM em um conjunto de dimensionamento não tem. Essa diferença se dá porque o provisionamento em excesso é uma propriedade para o conjunto de dimensionamento como um todo e não para VMs individuais no conjunto de dimensionamento (para obter mais informações sobre provisionamento em excesso, consulte [esta documentação](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning)).



### <a name="the-scale-set-vm-instance-view"></a>A exibição de instância da VM do conjunto de dimensionamento

Semelhante a como um conjunto de dimensionamento tem uma exibição de instância, cada VM no conjunto de dimensionamento tem sua própria exibição de instância. Para consultar a exibição de instância de um conjunto de dimensionamento, você pode usar:

API REST: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}/instanceView?api-version={apiVersion}` (para obter mais informações, consulte a [Documentação da API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/getinstanceview))

Powershell: `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -InstanceView` (para obter mais informações, consulte a [Documentação do Powershell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm))

CLI: `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` (para obter mais informações, consulte a [Documentação da CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view))

Você também pode usar [resources.azure.com](https://resources.azure.com) ou [SDKs do Azure](https://azure.microsoft.com/downloads/) para consultar a exibição de instância de uma VM em um conjunto de dimensionamento.

A apresentação exata da saída depende das opções que você fornecer para o comando, mas alguns exemplos de saídas da CLI são apresentados a seguir:

```
$ az vmss get-instance-view -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}
{
  "additionalProperties": {
    "osName": "ubuntu",
    "osVersion": "16.04"
  },
  "disks": [
    {
      "name": "{name}",
      "statuses": [
        {
          "additionalProperties": {},
          "code": "ProvisioningState/succeeded",
          "displayStatus": "Provisioning succeeded",
          "time": "{time}"
        }
      ]
    }
  ],
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "time": "{time}"
    },
    {
      "additionalProperties": {},
      "code": "PowerState/running",
      "displayStatus": "VM running"
    }
  ],
  "vmAgent": {
    "statuses": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Ready",
        "level": "Info",
        "message": "Guest Agent is running",
        "time": "{time}"
      }
    ],
    "vmAgentVersion": "{version}"
  },
  .
  .
  .
}
```

Como você pode ver, essas propriedades descrevem o estado de tempo de execução atual da máquina virtual em si, incluindo quaisquer extensões aplicadas ao conjunto de dimensionamento (omitido para fins de brevidade).




## <a name="how-to-update-global-scale-set-properties"></a>Como atualizar propriedades globais do conjunto de dimensionamento

Para atualizar uma propriedade global do conjunto de dimensionamento, você deve atualizar a propriedade no modelo do conjunto de dimensionamento. Você pode fazer essa atualização por meio de:

API REST: `PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` (para obter mais informações, consulte a [Documentação da API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate))

Modelos do Gerenciador de Recursos: como alternativa, você pode implantar um modelo do Gerenciador de Recursos usando as propriedades da API REST para atualizar as propriedades globais do conjunto de dimensionamento.

Powershell: `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -VirtualMachineScaleSet {scaleSetConfigPowershellObject}` (para obter mais informações, consulte a [Documentação do Powershell](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss))

CLI. Para modificar uma propriedade: `az vmss update --set {propertyPath}={value}`. Para adicionar um objeto a uma propriedade de lista em um conjunto de dimensionamento: `az vmss update --add {propertyPath} {JSONObjectToAdd}`. Para remover um objeto de uma propriedade de lista em um conjunto de dimensionamento: `az vmss update --remove {propertyPath} {indexToRemove}`. (para obter mais informações, consulte a [Documentação da CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update)). Como alternativa, se você tiver implantado anteriormente o conjunto de dimensionamento usando o comando `az vmss create`, você pode executar o comando `az vmss create` novamente para atualizar o conjunto de dimensionamento. Para fazer isso, você precisa garantir que todas as propriedades do comando `az vmss create` sejam as mesmas de antes, exceto as propriedades que você deseja modificar.



Você também pode usar [resources.azure.com](https://resources.azure.com) ou [SDKs do Azure](https://azure.microsoft.com/downloads/) para atualizar o modelo do conjunto de dimensionamento.

Depois que o modelo de conjunto de dimensionamento for atualizado, a nova configuração se aplicará a novas VMs criadas no conjunto de dimensionamento. No entanto, os modelos para VMs existentes no conjunto de dimensionamento ainda devem ser atualizados com o modelo geral mais recente do conjunto de dimensionamento. No modelo para cada VM, há uma propriedade booleana chamada `latestModelApplied` que indica se a VM está atualizada com o modelo geral mais recente do conjunto de dimensionamento ou não (`true` significa que a VM está atualizada com o modelo mais recente).




## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Como atualizar as VMs com o modelo mais recente do conjunto de dimensionamento

Os conjuntos de dimensionamento têm uma "política de atualização" que determinam como as VMs são atualizadas com o modelo mais recente do conjunto de dimensionamento. Os três modos da política de atualização são:

- Automático: neste modo, o conjunto de escala não garante a ordem das VMs sendo interrompidas. O conjunto de dimensionamento pode interromper todas as VMs ao mesmo tempo. 
- Distribuição em lotes: neste modo, o conjunto de dimensionamento distribui a atualização em lotes com um tempo de pausa opcional entre os lotes.
- Manual: neste modo, quando você atualiza o modelo do conjunto de dimensionamento, nada acontecerá nas VMs existentes. Para atualizar VMs existentes, você deve fazer uma "atualização manual" de cada VM existente. Você pode fazer essa atualização manual por meio de:

API REST: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/manualupgrade?api-version={apiVersion}` (para obter mais informações, consulte a [Documentação da API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/updateinstances))

Powershell: `Update-AzureRmVmssInstance -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` (para obter mais informações, consulte a [Documentação do Powershell](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance))

CLI: `az vmss update-instances -g {resourceGroupName} -n {vmScaleSetName} --instance-ids {instanceIds}` (para obter mais informações, consulte a [Documentação da CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update_instances)).

Você também pode usar os [SDKs do Azure](https://azure.microsoft.com/downloads/) para fazer uma atualização manual em uma VM em um conjunto de dimensionamento.

>[!NOTE]
> Clusters do Service Fabric só podem usar o modo Automático, mas a atualização é tratada de maneira diferente. Para saber mais sobre atualizações da malha de serviço, consulte a [documentação do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade).

>[!NOTE]
> Há um tipo de modificação das propriedades globais do conjunto de dimensionamento que não segue a política de atualização. Estas são as alterações para o Perfil do SO do conjunto de dimensionamento (por exemplo, nome de usuário e senha do administrador). Essas propriedades só podem ser alteradas na versão da API de 01-12-2017 ou posterior. Essas alterações se aplicam somente a VMs criadas depois da alteração no modelo do conjunto de dimensionamento. Para atualizar VMs existentes, você deve "refazer a imagem" de cada VM existente. Você pode refazer a imagem por meio de:

API REST: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (para obter mais informações, consulte a [Documentação da API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage))

Powershell: `Set-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (para obter mais informações, consulte a [Documentação do Powershell](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm))

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (para obter mais informações, consulte a [Documentação da CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_reimage)).

Você também pode usar os [SDKs do Azure](https://azure.microsoft.com/downloads/) para refazer a imagem de uma VM em um conjunto de dimensionamento.




## <a name="properties-with-restrictions-on-modification"></a>Propriedades com restrições de modificação

### <a name="create-time-properties"></a>Propriedades do momento da criação

Algumas propriedades só podem ser definidas ao criar inicialmente o conjunto de dimensionamento. Essas propriedades incluem:

- zonas
- publicador de referência de imagem
- oferta de referência de imagem

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Propriedades que só podem ser alteradas com base no valor atual

Algumas propriedades podem ser alteradas, com exceções dependendo do valor atual. Essas propriedades incluem:

- singlePlacementGroup: se singlePlacementGroup for true, ele pode ser modificado para false. No entanto, se singlePlacementGroup for false, ele **não poderá** ser modificado para true.
- sub-rede: a sub-rede de um conjunto de dimensionamento pode ser modificada desde que a sub-rede original e a nova sub-rede estejam na mesma rede virtual.

### <a name="properties-that-require-deallocation-to-change"></a>Propriedades que exigem desalocação para serem alteradas

Algumas propriedades só podem ser alteradas para determinados valores se as VMs no conjunto de dimensionamento forem desalocadas. Essas propriedades incluem:

- nome da sku: se não houver suporte para a nova SKU de VM no hardware em que o conjunto de dimensionamento está no momento, você precisará desalocar as VMs no conjunto de dimensionamento antes de modificar o nome da sku. Para obter mais informações sobre o redimensionamento de VMs, consulte [esta postagem no blog do Azure](https://azure.microsoft.com/blog/resize-virtual-machines/).


## <a name="vm-specific-updates"></a>Atualizações específicas da VM

Algumas modificações podem ser aplicadas a VMs específicas em vez das propriedades globais do conjunto de dimensionamento. Atualmente, a única atualização específica de VM compatível é anexação/desanexação de discos de dados para/de VMs no pacote de atualização. Esse recurso está em visualização. Para saber mais, confira a [documentação de versão prévia](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).

## <a name="scenarios-application-updates-os-updates-etc"></a>Cenários: atualizações de aplicativos, atualizações do SO, etc.

### <a name="application-updates"></a>Atualizações de aplicativos

Se um aplicativo for implantado em uma conjunto de dimensionamento por meio de extensões, a atualização da configuração da extensão fará com que o aplicativo atualize de acordo com a política de atualização. Por exemplo, se você tiver uma nova versão de um script para ser executado em uma extensão de script personalizada, você pode atualizar a propriedade fileUris para apontar para o novo script. Em alguns casos, no entanto, talvez você queira forçar uma atualização mesmo que a configuração da extensão permaneça inalterada (por exemplo, você atualizou o script sem alterar o URI do script). Nesses casos, você pode modificar o forceUpdateTag para forçar uma atualização. A plataforma Azure não interpreta essa propriedade, então a alteração do valor não tem efeito em como a extensão é executada. Modificá-lo simplesmente força a extensão a ser executada novamente. Para obter mais informações sobre o forceUpdateTag, consulte a [documentação da API REST para extensões](https://docs.microsoft.com/rest/api/compute/virtualmachineextensions/createorupdate).

Também é comum que aplicativos sejam implantados por meio de uma imagem personalizada. Esse cenário é abordado na seção "Atualizações de SO" a seguir

### <a name="os-updates"></a>Atualizações de SO

Se você estiver usando imagens de plataforma, poderá atualizar a imagem modificando o imageReference (mais informações na [documentação da API REST](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate)).

>[!NOTE]
> Com imagens de plataforma, é comum especificar "mais recente" para a versão de referência de imagem. Isso significa que, durante a criação, a expansão e a recriação de imagem do conjunto de dimensionamento, as VMs são criadas com a versão mais recente disponível. No entanto, isso **não** significa que a imagem do SO será atualizada automaticamente ao longo do tempo com o lançamento de novas versões da imagem. Esse é um recurso separado, atualmente em versão prévia. Para obter mais informações, confira a [Documentação de Atualização Automática do SO](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade).

Se você estiver usando imagens personalizadas, poderá atualizar a imagem atualizando a ID do imageReference (mais informações na [documentação da API REST](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate)).

## <a name="examples"></a>Exemplos

### <a name="updating-the-os-image-for-your-scale-set"></a>Atualizando a imagem do SO para seu conjunto de dimensionamento

Digamos que você tenha um conjunto de dimensionamento executando uma versão antiga do Ubuntu LTS 16.04 e você queira atualizar para uma versão mais recente do Ubuntu LTS 16.04 (por exemplo, a versão 16.04.201801090). A propriedade da versão de referência da imagem não é parte de uma lista, logo você pode modificar diretamente essas propriedades com estes comandos:

Powershell: `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -ImageReferenceVersion 16.04.201801090`

CLI: `az vmss update -g {resourceGroupName} -n {vmScaleSetName} --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090`


### <a name="updating-the-load-balancer-for-your-scale-set"></a>Atualizando o balanceador de carga para o conjunto de dimensionamento

Digamos que você tenha um conjunto de dimensionamento com um Azure Load Balancer e você queira substituir o Azure Load Balancer por um Gateway de Aplicativo do Azure. As propriedades do balanceador de carga e do gateway do aplicativo referentes a um conjunto de dimensionamento são parte de uma lista, logo você pode usar os comandos para remover e adicionar elementos da lista em vez de modificar as propriedades diretamente:

Powershell:
```
# get the current model of the scale set and store it in a local powershell object named $vmss
> $vmss=Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName}

# create a local powershell object for the new desired IP configuration, which includes the referencerence to the application gateway
> $ipconf = New-AzureRmVmssIPConfig myNic -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name

# replace the existing IP configuration in the local powershell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
> $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf

# Update the model of the scale set with the new configuration in the local powershell object
> Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName} -virtualMachineScaleSet $vmss

```

CLI:
```
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0 # remove the load balancer backend pool from the scale set model
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0 # remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}' # add the application gateway backend pool to the scale set model
```

>[!NOTE]
> Esses comandos pressupõem que há apenas um balanceador de carga e uma configuração de IP no conjunto de dimensionamento. Se houver vários, você precisará usar um índice de lista diferente de 0.