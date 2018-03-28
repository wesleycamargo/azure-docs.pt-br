---
title: Modificar um conjunto de dimensionamento de máquinas virtuais do Azure | Microsoft Docs
description: Modificar um conjunto de dimensionamento de máquinas virtuais do Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: negat
ms.openlocfilehash: fcca912a8120a51d2f0a454ef0a6341cd5882015
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="modify-a-virtual-machine-scale-set"></a>Modificar um conjunto de dimensionamento de máquinas virtuais
Este artigo descreve como modificar um conjunto de dimensionamento de máquina virtual já existente. As tarefas incluem instruções de como alterar a configuração do conjunto de dimensionamento, como alterar a configuração de aplicativos em execução no conjunto de dimensionamento, como gerenciar a disponibilidade e muito mais.

## <a name="fundamental-concepts"></a>Conceitos fundamentais

### <a name="scale-set-model"></a>Modelo do conjunto de dimensionamento

Um conjunto de dimensionamento tem um modelo que captura o estado *desejado* do conjunto de dimensionamento como um todo. Para consultar o modelo de um conjunto de dimensionamento, você pode usar:

* API REST: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` 
   
  Para obter mais informações, consulte a [Documentação da API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/get).

* PowerShell:

  `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}`
   
  Para obter mais informações, consulte a [Documentação do PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss).

* CLI do Azure: 

  `az vmss show -g {resourceGroupName} -n {vmSaleSetName}` 
   
  Para saber mais, confira a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show).

Você também pode usar o [Azure Resource Explorer (Versão prévia)](https://resources.azure.com) ou [SDKs do Azure](https://azure.microsoft.com/downloads/) para consultar o modelo de um conjunto de dimensionamento.

A apresentação exata da saída depende das opções que você fornecer para o comando. Aqui está um exemplo de saída da CLI do Azure:

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



### <a name="scale-set-instance-view"></a>Exibição de instância do conjunto de dimensionamento

Um conjunto de dimensionamento também tem uma exibição de instância que captura o estado atual do *tempo de execução* do conjunto de dimensionamento como um todo. Para consultar a exibição de instância de um conjunto de dimensionamento, você pode usar:

* API REST: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version={apiVersion}` 
   
  Para obter mais informações, consulte a [Documentação da API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview).

* PowerShell: 

  `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceView` 
  
  Para obter mais informações, consulte a [Documentação do PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss).

* CLI do Azure: 

  `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName}` 
   
  Para saber mais, confira a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view).

Você também pode usar o [Azure Resource Explorer (Versão prévia)](https://resources.azure.com) ou [SDKs do Azure](https://azure.microsoft.com/downloads/) para consultar a exibição de instância de um conjunto de dimensionamento.

A apresentação exata da saída depende das opções que você fornecer para o comando. Aqui está um exemplo de saída da CLI do Azure:

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

Como você pode ver, essas propriedades fornecem um resumo do estado atual de tempo de execução das VMs no conjunto de dimensionamento. O resumo inclui o status de extensões aplicadas ao conjunto de dimensionamento (omitida para fins de brevidade).



### <a name="scale-set-vm-model-view"></a>Exibição do modelo de VM do conjunto de dimensionamento

Semelhante a como um conjunto de dimensionamento tem uma exibição de modelo, cada VM no conjunto de dimensionamento tem sua própria exibição de modelo. Para consultar a exibição do modelo de um conjunto de dimensionamento, você pode usar:

* API REST: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}?api-version={apiVersion}` 
  
  Para obter mais informações, consulte a [Documentação da API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/get).

* PowerShell: 

  `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` 
  
  Para obter mais informações, consulte a [Documentação do PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm).

* CLI do Azure: 

  `az vmss show -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` 
  
  Para saber mais, confira a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show).

Você também pode usar o [Azure Resource Explorer (Versão prévia)](https://resources.azure.com) ou [SDKs do Azure](https://azure.microsoft.com/downloads/) para consultar o modelo de uma VM em um conjunto de dimensionamento.

A apresentação exata da saída depende das opções que você fornecer para o comando. Aqui está um exemplo de saída da CLI do Azure:

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

Como você pode ver, essas propriedades descrevem a configuração da VM em si e não a configuração do conjunto de dimensionamento como um todo. Por exemplo, o modelo do conjunto de dimensionamento tem `overprovision` como propriedade, enquanto o modelo de uma VM em um conjunto de dimensionamento não tem. A razão para essa diferença é que o excesso de provisionamento é uma propriedade para o conjunto de dimensionamento como um todo, não para VMs individuais no conjunto de dimensionamento. (Para obter mais informações sobre excesso de provisionamento, consulte [Considerações de design para conjuntos de dimensionamento](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning).)



### <a name="scale-set-vm-instance-view"></a>Exibição de instância da VM do conjunto de dimensionamento

Semelhante a como um conjunto de dimensionamento tem uma exibição de instância, cada VM no conjunto de dimensionamento tem sua própria exibição de instância. Para consultar a exibição de instância de um conjunto de dimensionamento, você pode usar:

* API REST: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}/instanceView?api-version={apiVersion}` 
 
  Para obter mais informações, consulte a [Documentação da API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/getinstanceview).

* PowerShell: 

  `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -InstanceView` 
  
  Para obter mais informações, consulte a [Documentação do PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm).

* CLI do Azure: 

  `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` 
  
  Para saber mais, confira a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view).

Você também pode usar o [Azure Resource Explorer (Versão prévia)](https://resources.azure.com) ou [SDKs do Azure](https://azure.microsoft.com/downloads/) para consultar a exibição de instância de uma VM em um conjunto de dimensionamento.

A apresentação exata da saída depende das opções que você fornecer para o comando. Aqui está um exemplo de saída da CLI do Azure:

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

Como você pode ver, essas propriedades descrevem o estado de tempo de execução atual da própria máquina virtual. O estado inclui quaisquer extensões aplicadas ao conjunto de dimensionamento (omitida para fins de brevidade).




## <a name="techniques-for-updating-global-scale-set-properties"></a>Técnicas para a atualização de propriedades globais de conjunto de dimensionamento

Para atualizar uma propriedade global do conjunto de dimensionamento, você deve atualizar a propriedade no modelo do conjunto de dimensionamento. Você pode fazer essa atualização por meio de:

* API REST: 

  `PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` 
  
  Para obter mais informações, consulte a [Documentação da API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

  Como alternativa, você pode implantar um modelo do Azure Resource Manager usando as propriedades da API REST para atualizar as propriedades globais do conjunto de dimensionamento.

* PowerShell: 

  `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -VirtualMachineScaleSet {scaleSetConfigPowershellObject}` 
  
  Para obter mais informações, consulte a [Documentação do PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss).

* CLI do Azure:

  * Para modificar uma propriedade: `az vmss update --set {propertyPath}={value}` 
  
  * Para adicionar um objeto a uma propriedade de lista em um conjunto de dimensionamento: `az vmss update --add {propertyPath} {JSONObjectToAdd}` 
  
  * Para remover um objeto de uma propriedade de lista em um conjunto de dimensionamento: `az vmss update --remove {propertyPath} {indexToRemove}` 
  
  Para saber mais, confira a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update). 
  
  Como alternativa, se você tiver implantado anteriormente o conjunto de dimensionamento usando o comando `az vmss create`, você pode executar o comando `az vmss create` novamente para atualizar o conjunto de dimensionamento. Para fazer isso, certifique-se de que todas as propriedades do comando `az vmss create` sejam as mesmas de antes, exceto as propriedades que você quer modificar.



Você também pode usar o [Azure Resource Explorer (Versão prévia)](https://resources.azure.com) ou [SDKs do Azure](https://azure.microsoft.com/downloads/) para atualizar o modelo do conjunto de dimensionamento.

Depois que o modelo de conjunto de dimensionamento for atualizado, a nova configuração se aplicará a novas VMs criadas no conjunto de dimensionamento. No entanto, os modelos para VMs existentes no conjunto de dimensionamento ainda devem ser atualizados com o modelo geral mais recente do conjunto de dimensionamento. No modelo para cada VM, há uma propriedade booleana chamada `latestModelApplied` que indica se a VM está atualizada com o modelo geral mais recente do conjunto de dimensionamento ou não. (Um valor de `true` significa que a VM está atualizada com o modelo mais recente.)




## <a name="techniques-for-bringing-vms-up-to-date-with-the-latest-scale-set-model"></a>Técnicas para atualizar as VMs com o modelo mais recente do conjunto de dimensionamento

Os conjuntos de dimensionamento têm uma *política de atualização* que determina como as VMs são atualizadas com o modelo mais recente do conjunto de dimensionamento. Os três modos da política de atualização são:

- **Automático**: neste modo, o conjunto de escala não garante a ordem das VMs que são interrompidas. O conjunto de dimensionamento pode interromper todas as VMs ao mesmo tempo. 
- **Distribuição em lotes**: neste modo, o conjunto de dimensionamento distribui a atualização em lotes com um tempo de pausa opcional entre os lotes.
- **Manual**: neste modo, quando você atualiza o modelo do conjunto de dimensionamento, nada acontecerá nas VMs existentes. Para atualizar VMs existentes, você deve atualizar manualmente cada uma delas. Você pode fazer essa atualização manual por meio de:

  - API REST: 
  
    `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/manualupgrade?api-version={apiVersion}` 
    
    Para obter mais informações, consulte a [Documentação da API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/updateinstances).

  - PowerShell: 
  
    `Update-AzureRmVmssInstance -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` 
    
    Para obter mais informações, consulte a [Documentação do PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance).

  - CLI do Azure: 
  
    `az vmss update-instances -g {resourceGroupName} -n {vmScaleSetName} --instance-ids {instanceIds}` 
    
    Para saber mais, confira a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update_instances).

  Você também pode usar os [SDKs do Azure](https://azure.microsoft.com/downloads/) para fazer uma atualização manual em uma VM em um conjunto de dimensionamento.

>[!NOTE]
> Clusters do Azure Service Fabric só podem usar o modo Automático, mas a atualização é tratada de maneira diferente. Para saber mais sobre atualizações do Service Fabric, consulte a [documentação do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade).

Um tipo de modificação das propriedades globais do conjunto de dimensionamento não segue a política de atualização: alterações para o perfil do SO do conjunto de dimensionamento. (Exemplos são nome de usuário de administrador e senha.) Essas propriedades só podem ser alteradas na versão da API de 01-12-2017 ou posterior. Essas alterações se aplicam somente a VMs criadas depois da alteração no modelo do conjunto de dimensionamento. Para atualizar VMs existentes, você deve refazer a imagem de cada VM existente. Refaça a imagem de uma VM por meio de:

* API REST: 

  `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` 
  
  Para obter mais informações, consulte a [Documentação da API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage).

* PowerShell: 

  `Set-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` 
  
  Para obter mais informações, consulte a [Documentação do PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm).

* CLI do Azure: 

  `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` 
  
  Para saber mais, confira a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_reimage).

Você também pode usar os [SDKs do Azure](https://azure.microsoft.com/downloads/) para refazer a imagem de uma VM em um conjunto de dimensionamento.




## <a name="properties-with-restrictions-on-modification"></a>Propriedades com restrições de modificação

### <a name="create-time-properties"></a>Propriedades do momento da criação

Algumas propriedades só podem ser definidas ao criar inicialmente o conjunto de dimensionamento. Essas propriedades incluem:

- Zonas
- Publicador de referência de imagem
- Oferta de referência de imagem

### <a name="properties-that-can-be-changed-based-on-the-current-value-only"></a>Propriedades que podem ser alteradas com base somente no valor atual

Algumas propriedades podem ser alteradas, com exceções dependendo do valor atual. Essas propriedades incluem:

- `singlePlacementGroup`: Se `singlePlacementGroup` for true, ele poderá ser modificado para false. No entanto, se `singlePlacementGroup` for false, ele *não poderá* ser modificada para true.
- `subnet`: A sub-rede de um conjunto de dimensionamento pode ser modificada desde que a sub-rede original e a nova sub-rede estejam na mesma rede virtual.

### <a name="properties-that-require-deallocation-to-change"></a>Propriedades que exigem desalocação para serem alteradas

Algumas propriedades podem ser alteradas para determinados valores somente se as VMs no conjunto de dimensionamento forem desalocadas. Essas propriedades incluem:

- `sku name`: se não houver suporte para a nova SKU de VM no hardware em que o conjunto de dimensionamento está no momento, você precisará desalocar as VMs no conjunto de dimensionamento antes de você modificar `sku name`. Para obter mais informações sobre o redimensionamento de VMs, consulte [esta postagem no blog do Azure](https://azure.microsoft.com/blog/resize-virtual-machines/).


## <a name="vm-specific-updates"></a>Atualizações específicas da VM

Algumas modificações podem ser aplicadas a VMs específicas em vez das propriedades globais do conjunto de dimensionamento. Atualmente, a única atualização específica de VM compatível é anexação/desanexação de discos de dados para/de VMs no pacote de atualização. Esse recurso está em visualização. Para saber mais, confira a [documentação de versão prévia](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).

## <a name="scenarios"></a>Cenários

### <a name="application-updates"></a>Atualizações de aplicativos

Se um aplicativo for implantado em uma conjunto de dimensionamento por meio de extensões, a atualização da configuração da extensão fará com que o aplicativo seja atualizado de acordo com a política de atualização. Por exemplo, se você tiver uma nova versão de um script para ser executado em uma extensão de script personalizada, você pode atualizar a propriedade `fileUris` para apontar para o novo script. 

Em alguns casos, você talvez queira forçar uma atualização mesmo que a configuração da extensão permaneça inalterada. (Por exemplo, você atualizou o script sem alterar o URI do script.) Nesses casos, você pode modificar o `forceUpdateTag` para forçar uma atualização. A plataforma Azure não interpreta essa propriedade, então a alteração do valor não tem efeito em como a extensão é executada. Modificá-lo simplesmente força a extensão a ser executada novamente. 

Para obter mais informações sobre o `forceUpdateTag`, consulte a [documentação da API REST para extensões](https://docs.microsoft.com/rest/api/compute/virtualmachineextensions/createorupdate).

Também é comum que aplicativos sejam implantados por meio de uma imagem personalizada. Esse cenário é abordado na seção a seguir.

### <a name="os-updates"></a>Atualizações do sistema operacional

Se você estiver usando imagens de plataforma, você poderá atualizar as imagens modificando `imageReference`. Para obter mais informações, consulte a [Documentação da API REST](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate).

>[!NOTE]
> Com imagens de plataforma, é comum especificar "mais recente" para a versão de referência de imagem. Isso significa que quando conjuntos de dimensionamento são criados, expandidos e têm a imagem recriada, as VMs são criadas com a versão mais recente disponível. No entanto, isso *não* significa que a imagem do SO será atualizada automaticamente ao longo do tempo com o lançamento de novas versões da imagem. Esse é um recurso separado, atualmente em versão prévia. Para obter mais informações, veja [Atualizações automáticas do SO](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade).

Se você estiver usando imagens personalizadas, você poderá atualizar as imagens atualizando a ID `imageReference`. Para obter mais informações, consulte a [Documentação da API REST](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="examples"></a>Exemplos

### <a name="update-the-os-image-for-your-scale-set"></a>Atualizar a imagem do SO para seu conjunto de dimensionamento

Digamos que você tenha uma conjunto de dimensionamento executando uma versão antiga do Ubuntu LTS 16.04. Você deseja atualizar para uma versão mais recente do Ubuntu LTS 16.04 (por exemplo, a versão 16.04.201801090). A propriedade da versão de referência da imagem não é parte de uma lista, logo você pode modificar diretamente essas propriedades usando estes comandos:

* PowerShell: 

  `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -ImageReferenceVersion 16.04.201801090`

* CLI do Azure: 

  `az vmss update -g {resourceGroupName} -n {vmScaleSetName} --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090`


### <a name="update-the-load-balancer-for-your-scale-set"></a>Atualizar o balanceador de carga para o conjunto de dimensionamento

Digamos que você tenha um conjunto de dimensionamento com um balanceador de carga do Azure e você queira substituir o balanceador de carga do por um gateway de aplicativo do Azure. As propriedades de balanceador de carga e gateway de aplicativo para um conjunto de dimensionamento são parte de uma lista. Portanto, você pode usar os comandos para remover e adicionar elementos da lista em vez de modificar as propriedades diretamente.

PowerShell:
```
# Get the current model of the scale set and store it in a local PowerShell object named $vmss
> $vmss=Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName}

# Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
> $ipconf = New-AzureRmVmssIPConfig myNic -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name

# Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure load balancer) with the new IP configuration
> $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf

# Update the model of the scale set with the new configuration in the local PowerShell object
> Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName} -virtualMachineScaleSet $vmss

```

CLI do Azure:
```
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0 # Remove the load balancer back-end pool from the scale set model
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0 # Remove the load balancer back-end pool from the scale set model; only necessary if you have NAT pools configured on the scale set
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}' # Add the application gateway back-end pool to the scale set model
```

>[!NOTE]
> Esses comandos pressupõem que há apenas um balanceador de carga e uma configuração de IP no conjunto de dimensionamento. Se houver vários, você precisará usar um índice de lista diferente de 0.