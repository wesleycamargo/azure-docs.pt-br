---
title: Upgrades automáticos de sistema operacional com conjuntos de dimensionamento de máquinas virtuais do Azure | Microsoft Docs
description: Sabia como atualizar automaticamente o sistema operacional em instâncias de VM em um conjunto de dimensionamento
services: virtual-machine-scale-sets
documentationcenter: ''
author: yeki
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/03/2018
ms.author: yeki
ms.openlocfilehash: 6b20ef98e008d9c5d984ba29eed894b1c5ec8c09
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263241"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-upgrades"></a>Upgrades automáticos de sistema operacional do conjunto de dimensionamento de máquinas virtuais do Azure

A atualização automática da imagem do sistema operacional é um recurso em versão prévia do conjunto de dimensionamento de máquinas virtuais do Azure que atualiza automaticamente todas as VMs para a imagem mais recente do sistema operacional.

A atualização de sistema operacional do Azure tem as seguintes características:

- Depois de configurada, a imagem mais recente do sistema operacional publicada pelos editores de imagem é aplicada automaticamente ao conjunto de dimensionamento sem intervenção do usuário.
- Atualiza lotes de instâncias de maneira ininterrupta sempre que uma nova imagem de plataforma é publicada pelo editor.
- Integra-se à investigação de integridade do aplicativo (opcional, mas recomendado por questões de segurança).
- Funciona para todos os tamanhos de VM.
- Funciona para imagens de plataforma do Windows e do Linux.
- Você pode desativar as atualizações automáticas a qualquer momento (os upgrades do sistema operacional também podem ser iniciados manualmente).
- O disco do sistema operacional de uma VM é substituído pelo novo disco do sistema operacional criado com a versão mais recente da imagem. As extensões configuradas e os scripts de dados personalizados são executados, enquanto os discos de dados persistentes são retidos.


## <a name="preview-notes"></a>Notas de versão prévia 
Enquanto estão na versão prévia, as seguintes limitações e restrições se aplicam:

- Os upgrades automáticos do sistema operacional dão suporte a apenas [quatro SKUs de sistema operacional](#supported-os-images). Não há nenhum SLA ou garantias. Recomendamos que você não use upgrades automáticos em cargas de trabalho críticas de produção durante a visualização.
- A criptografia de disco do Azure **não é** atualmente suportada com a atualização automática do sistema operacional do conjunto de dimensionamento da máquina virtual.


## <a name="register-to-use-automatic-os-upgrade"></a>Registrar-se para usar a atualização automática do sistema operacional
Para usar o recurso de atualização do sistema operacional automatizado, registre o provedor de visualização com o Azure Powershell ou o Azure CLI 2.0.

### <a name="powershell"></a>PowerShell

1. Registre-se com [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature):

     ```powershell
     Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName AutoOSUpgradePreview
     ```

2. Leva aproximadamente 10 minutos para que o estado de registro seja reportado como *Registrado*. É possível verificar o status do registro atual com [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). 

3. Uma vez registrado, confirme se o provedor *Microsoft.Compute* está registrado. O exemplo a seguir usa o Azure Powershell com [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider):

     ```powershell
     Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
     ```


### <a name="cli-20"></a>CLI 2.0

1. Registre-se com o [registro de recurso z](/cli/azure/feature#az-feature-register):

     ```azurecli
     az feature register --name AutoOSUpgradePreview --namespace Microsoft.Compute
     ```

2. Leva aproximadamente 10 minutos para que o estado de registro seja reportado como *Registrado*. Você pode verificar o status atual do registro com o [az feature show](/cli/azure/feature#az-feature-show). 
 
3. Depois de registrado, verifique se o provedor *Microsoft.Compute* está registrado. O exemplo a seguir usa a CLI do Azure (2.0.20 ou posterior) com [az provider register](/cli/azure/provider#az-provider-register):

     ```azurecli
     az provider register --namespace Microsoft.Compute
     ```

> [!NOTE]
> Clusters de Service Fabric têm sua próprias noção da integridade do aplicativo, mas conjuntos de dimensionamento sem o Service Fabric usam a investigação de integridade do balanceador de carga para monitorar a integridade do aplicativo. 
>
> ### <a name="azure-powershell"></a>Azure PowerShell
>
> 1. Registre o recurso do provedor para probes de integridade com [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature):
>
>      ```powershell
>      Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVmssHealthProbe
>      ```
>
> 2. Novamente, levará aproximadamente 10 minutos para que o estado de registro seja reportado como *Registrado*. É possível verificar o status do registro atual com [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature).
>
> 3. Uma vez registrado, certifique-se de que o provedor *Microsoft.Network* esteja registrado usando [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider):
>
>      ```powershell
>      Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
>      ```
>
>
> ### <a name="cli-20"></a>CLI 2.0
>
> 1. Registre o recurso do provedor para probes de integridade com o [registro de recurso z](/cli/azure/feature#az-feature-register):
>
>      ```azurecli
>      az feature register --name AllowVmssHealthProbe --namespace Microsoft.Network
>      ```
>
> 2. Novamente, levará aproximadamente 10 minutos para que o estado de registro seja reportado como *Registrado*. Você pode verificar o status atual do registro com o [az feature show](/cli/azure/feature#az-feature-show). 
>
> 3. Uma vez registrado, verifique se o provedor *Microsoft.Network* está registrado usando [az provider register](/cli/azure/provider#az-provider-register) da seguinte forma:
>
>      ```azurecli
>      az provider register --namespace Microsoft.Network
>      ```

## <a name="portal-experience"></a>Experiência do Portal
Depois de seguir as etapas de registro acima, você pode ir ao [portal do Azure](https://aka.ms/managed-compute) para habilitar atualizações automáticas do sistema operacional em seus conjuntos de dimensionamento e ver o progresso das atualizações:

![](./media/virtual-machine-scale-sets-automatic-upgrade/automatic-upgrade-portal.png)


## <a name="supported-os-images"></a>Imagens do sistema operacional com suporte
No momento, há suporte apenas determinadas imagens de plataforma do sistema operacional. No momento, você não pode usar imagens personalizadas que você mesmo criou. A propriedade *versão* da imagem da plataforma deve ser definida como *a mais recente*.

No momento, há suporte para os seguintes SKUs (serão adicionados mais):
    
| Publicador               | Oferta         |  Sku               | Versão  |
|-------------------------|---------------|--------------------|----------|
| Canônico               | UbuntuServer  | 16.04-LTS          | mais recente   |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter | mais recente   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    | mais recente   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Smalldisk | mais recente   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-with-Containers | mais recente   |



## <a name="application-health-without-service-fabric"></a>Integridade do aplicativo sem o Service Fabric
> [!NOTE]
> Esta seção aplica-se somente para conjuntos de dimensionamento sem o Service Fabric. O Service Fabric tem sua própria noção da integridade do aplicativo. Ao usar as atualizações automáticas do sistema operacional com o Service Fabric, a nova imagem do sistema operacional é implementada ao domínio de atualização pelo domínio de atualização para manter a alta disponibilidade dos serviços em execução no Service Fabric. Para obter mais informações sobre as características de durabilidade de clusters do Service Fabric, consulte [esta documentação](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

Durante a atualização do sistema operacional, as instâncias de VM em um conjunto de dimensionamento são atualizadas em um lote por vez. A atualização deverá continuar apenas se o aplicativo do cliente for íntegro nas instâncias de VM atualizadas. Por esse motivo, o aplicativo deve fornecer sinais de integridade ao mecanismo de atualização do sistema operacional de conjunto de escala. Durante os upgrades do SO, a plataforma considera o estado de energia da VM e o estado de provisionamento de extensão para determinar se uma instância de VM está íntegra após um upgrade. Durante a atualização do sistema operacional de uma instância VM, o disco do sistema operacional em uma instância VM é substituído por um novo com base na versão mais recente da imagem. Após a conclusão de atualização do sistema operacional, as extensões configuradas são executadas nessas VMs. Apenas quando todas as extensões em uma VM forem provisionadas com êxito é que o aplicativo será considerado íntegro. 

Além disso, o conjunto de dimensionamento *deve* ser configurado com investigações de integridade do aplicativo para fornecer a plataforma com as informações corretas sobre o estado atual do aplicativo. As Investigações de integridade do aplicativo são Investigações personalizadas do Load Balancer usadas como um sinal de integridade. O aplicativo em execução em uma instância VM do conjunto de dimensionamento pode responder a solicitações HTTP ou TCP externas que indica se ele está íntegro. Para obter mias informações sobre como as Investigações personalizadas do Load Balancer funcionam, consulte [Noções básicas de investigações do balanceador de carga](../load-balancer/load-balancer-custom-probe-overview.md).

Se o conjunto de dimensionamento estiver configurado para usar vários grupos de posicionamento, as investigações que usarem o [Load Balancer Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) precisarão ser usadas.

### <a name="important-keep-credentials-up-to-date"></a>Importante: Mantenha as credenciais atualizadas
Se seu conjunto de dimensionamento usa todas as credenciais para acessar recursos externos, você precisará certificar-se de que as credenciais são mantidas atualizadas. Por exemplo, uma extensão de VM pode ser configurada para usar um token SAS de conta de armazenamento. Se quaisquer credenciais, incluindo certificados e tokens, tiveram expirado, a atualização falhará e o primeiro lote de VMs será deixado em estado de falha.

As etapas recomendadas para recuperar VMs e reativar a atualização automática do sistema operacional se houver uma falha de autenticação de recurso são:

* Gerar novamente o token (ou qualquer outra credencial) passado para suas extensões.
* Certifique-se de que qualquer credencial usada de dentro da VM para se comunicar com entidades externas esteja atualizada.
* Atualizar extensões no modelo do conjunto de escala com quaisquer tokens novos.
* Implantar o conjunto de escala atualizado, que atualizará todas as instâncias de VM, incluindo aquelas com falha. 

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Configurando uma Investigação personalizada do Load Balancer como uma investigação de integridade do aplicativo em um conjunto de dimensionamento
Você *deve* criar uma investigação do balanceador de carga explicitamente para a integridade do conjunto de dimensionamento. O mesmo ponto de extremidade para um probe de HTTP ou TCP existente pode ser usado, mas um probe de integridade pode exigir um comportamento diferente de um probe de balanceador de carga tradicional. Por exemplo, uma investigação do balanceador de carga tradicional poderia retornar não íntegra se a carga na instância é muito alta. Por outro lado que pode não ser adequado para determinar a integridade da instância durante uma atualização automática do sistema operacional. Configure a investigação para que ela tenha uma alta taxa de sondagem de menos de 2 minutos.

A investigação do balanceador de carga pode ser referenciada no *networkProfile* do conjunto de dimensionamento e associada a um balanceador de carga interno ou público da seguinte maneira:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```


## <a name="enforce-an-os-image-upgrade-policy-across-your-subscription"></a>Impor uma política de atualização da imagem do sistema operacional em sua assinatura
Para ter upgrades seguros, recomenda-se impor uma política de atualização. Essa política pode exigir investigações de integridade do aplicativo em sua assinatura. A seguinte diretiva do Azure Resource Manager rejeita implantações que não possuem configurações de atualização de imagem do sistema operacional automatizadas configuradas:

### <a name="powershell"></a>Powershell
1. Obtenha a definição de política interna do Azure Resource Manager com [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) da seguinte maneira:

    ```powershell
    $policyDefinition = Get-AzureRmPolicyDefinition -Id "/providers/Microsoft.Authorization/policyDefinitions/465f0161-0087-490a-9ad9-ad6217f4f43a"
    ```

2. Atribua a política a uma assinatura com [New-AzureRmPolicyAssignment](/powershell/module/AzureRM.Resources/New-AzureRmPolicyAssignment) da seguinte maneira:

    ```powershell
    New-AzureRmPolicyAssignment `
        -Name "Enforce automatic OS upgrades with app health checks" `
        -Scope "/subscriptions/<SubscriptionId>" `
        -PolicyDefinition $policyDefinition
    ```

### <a name="cli-20"></a>CLI 2.0
Atribua política a uma assinatura com a política interna do Azure Resource Manager:

```azurecli
az policy assignment create --display-name "Enforce automatic OS upgrades with app health checks" --name "Enforce automatic OS upgrades" --policy 465f0161-0087-490a-9ad9-ad6217f4f43a --scope "/subscriptions/<SubscriptionId>"
```

## <a name="configure-auto-updates"></a>Configurar atualizações automáticas
Para configurar upgrades automáticos, certifique-se de que a propriedade *automaticOSUpgrade* está definida como *true* na definição do modelo do conjunto de dimensionamento. É possível configurar essa propriedade com o Azure PowerShell ou a CLI do Azure 2.0.

### <a name="powershell"></a>PowerShell
O exemplo a seguir usa o Azure PowerShell (4.4.1 ou posterior) para configurar upgrades automáticos para o conjunto de dimensionamento denominado *myVMSS* no grupo de recursos denominado *myResourceGroup*:

```powershell
$rgname = myResourceGroup
$vmssname = myVMSS
$vmss = Get-AzureRmVMss -ResourceGroupName $rgname -VmScaleSetName $vmssname
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true
Update-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname -VirtualMachineScaleSet $vmss
```

### <a name="cli-20"></a>CLI 2.0
O exemplo a seguir usa a CLI do Azure (2.0.20 ou posterior) para configurar atualizações automáticas para o conjunto de dimensionamento denominado *myVMSS* no grupo de recursos denominado *myResourceGroup*:

```azurecli
rgname="myResourceGroup"
vmssname="myVMSS"
az vmss update --name $vmssname --resource-group $rgname --set upgradePolicy.AutomaticOSUpgrade=true
```


## <a name="check-the-status-of-an-automatic-os-upgrade"></a>Verificar o status de uma atualização automática do sistema operacional
É possível verificar o status da atualização mais recente do sistema operacional realizado em seu conjunto de dimensionamento com o Azure PowerShell, a CLI do Azure 2.0 ou as APIs REST.

### <a name="powershell"></a>PowerShell
O exemplo a seguir usa o Azure PowerShell (4.4.1 ou posterior) para verificar o status do conjunto de dimensionamento denominado *myVMSS* no grupo de recursos denominado *myResourceGroup*:

```powershell
Get-AzureRmVmssRollingUpgrade -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS
```

### <a name="cli-20"></a>CLI 2.0
O exemplo a seguir usa a CLI do Azure (2.0.20 ou posterior) para verificar o status do conjunto de dimensionamento denominado *myVMSS* no grupo de recursos denominado *myResourceGroup*:

```azurecli
az vmss rolling-upgrade get-latest --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>API REST
O exemplo a seguir usa a API REST para verificar o status do conjunto de dimensionamento denominado *myVMSS* no grupo de recursos denominado *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/rollingUpgrades/latest?api-version=2017-03-30`
```

A chamada GET retorna propriedades semelhantes à saída de exemplo a seguir:

```json
{
  "properties": {
    "policy": {
      "maxBatchInstancePercent": 20,
      "maxUnhealthyInstancePercent": 5,
      "maxUnhealthyUpgradedInstancePercent": 5,
      "pauseTimeBetweenBatches": "PT0S"
    },
    "runningStatus": {
      "code": "Completed",
      "startTime": "2017-06-16T03:40:14.0924763+00:00",
      "lastAction": "Start",
      "lastActionTime": "2017-06-22T08:45:43.1838042+00:00"
    },
    "progress": {
      "successfulInstanceCount": 3,
      "failedInstanceCount": 0,
      "inprogressInstanceCount": 0,
      "pendingInstanceCount": 0
    }
  },
  "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
  "location": "southcentralus"
}
```


## <a name="automatic-os-upgrade-execution"></a>Execução da atualização automático do sistema operacional
Para expandir o uso de investigações de integridade do aplicativo, os atualizações do sistema operacional do conjunto de dimensionamento executam as seguintes etapas:

1. Se mais de 20% das instâncias forem não íntegras, interrompa o atualização; Caso contrário, continue.
2. Identifique o próximo lote de instâncias VM a serem atualizadas, com um lote tendo, no máximo, 20% da contagem total de instâncias.
3. Atualize o sistema operacional do próximo lote de instâncias VM.
4. Se mais de 20% das instâncias atualizadas forem insalubres, pare a atualização; caso contrário, continue.
5. Para conjuntos de dimensionamento que não fazem parte de um cluster do Service Fabric, o upgrade aguarda até 5 minutos para que as investigações se torne íntegro, em seguida, continua imediatamente no próximo lote. Para conjuntos de dimensionamento que fazem parte de um cluster do Service Fabric, o conjunto de dimensionamento aguarda 30 minutos antes de passar para o próximo lote.
6. Se houver instâncias remanescentes a serem atualizadas, passe para a etapa 1) para o próximo lote; caso contrário, o atualização será concluído.

O Mecanismo do atualização do sistema operacional do conjunto de dimensionamento verifica a integridade geral da instância VM antes de atualizar cada lote. Ao atualizar um lote, pode haver outra manutenção Planejada ou Não Planejada simultânea acontecendo nos Datacenters do Azure que podem afetar a disponibilidade de suas VMs. Portanto, é possível que temporariamente mais de 20% das instâncias pode estar inativo. Nesses casos, no final do lote atual, o atualização do conjunto de dimensionamento é interrompido.


## <a name="deploy-with-a-template"></a>Implantar com um modelo

É possível usar o seguinte modelo para implantar um conjunto de dimensionamento que usa atualizaçãos automáticos <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>Atualizações sem interrupção – Ubuntu 16.04-LTS</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>


## <a name="next-steps"></a>Próximas etapas
Para ver mais exemplos sobre como usar atualizaçãos automáticos do sistema operacional com conjuntos de dimensionamento, consulte se há [Recursos de versão prévia no repositório GitHub](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
