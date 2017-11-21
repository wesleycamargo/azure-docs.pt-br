---
title: "Upgrades automáticos de sistema operacional com conjuntos de dimensionamento de máquinas virtuais do Azure | Microsoft Docs"
description: "Sabia como atualizar automaticamente o sistema operacional em instâncias de VM em um conjunto de dimensionamento"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: guybo
ms.openlocfilehash: 32358b23bb0a0a878e986150dd992513579d61c4
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2017
---
# <a name="azure-virtual-machine-scale-set-automatic-os-upgrades"></a>Upgrades automáticos de sistema operacional do conjunto de dimensionamento de máquinas virtuais do Azure

A atualização automática da imagem do sistema operacional é um recurso em versão prévia do conjunto de dimensionamento de máquinas virtuais do Azure que atualiza automaticamente todas as VMs para a imagem mais recente do sistema operacional.

A atualização de sistema operacional do Azure tem as seguintes características:

- Depois de configurada, a imagem mais recente do sistema operacional publicada pelos editores de imagem é aplicada automaticamente ao conjunto de dimensionamento sem intervenção do usuário.
- Atualiza lotes de instâncias de maneira ininterrupta sempre que uma nova imagem de plataforma é publicada pelo editor.
- Integra-se à investigação de integridade do aplicativo (opcional, mas recomendado por questões de segurança).
- Funciona para todos os tamanhos de VM.
- Funciona para imagens de plataforma do Windows e do Linux.
- É possível recusar os upgrades automáticos a qualquer momento (os upgrades do sistema operacional podem ser iniciados manualmente também).
- O disco do sistema operacional de uma VM é substituído pelo novo disco do sistema operacional criado com a versão mais recente da imagem. As extensões configuradas e os scripts de dados personalizados são executados, enquanto os discos de dados persistentes são retidos.


## <a name="preview-notes"></a>Notas de versão prévia 
Enquanto estão na versão prévia, as seguintes limitações e restrições se aplicam:

- Os upgrades automáticos do sistema operacional dão suporte a apenas [três SKUs de sistema operacional](#supported-os-images). Não há nenhum SLA ou garantias. Recomendamos que você não use upgrades automáticos em cargas de trabalho críticas de produção durante a versão prévia.
- O suporte para conjuntos de dimensionamento nos clusters do Service Fabric estará disponível em breve.
- No momento, **não** há suporte para a criptografia de disco do Azure (atualmente em versão prévia) com atualização automática do sistema operacional do conjunto de dimensionamento de máquinas virtuais.
- Experiência do portal disponível em breve.


## <a name="register-to-use-automatic-os-upgrade"></a>Registrar-se para usar a atualização automática do sistema operacional
Para usar o recurso automatizado de atualização do sistema operacional, registre o provedor de versão prévia com [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature), da seguinte maneira:

```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName AutoOSUpgradePreview
```

Leva aproximadamente 10 minutos para que o estado de registro seja reportado como *Registrado*. É possível verificar o status do registro atual com [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). Depois de registrado, certifique-se de que o provedor *Microsoft.Compute* está registrado com o [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) da seguinte maneira:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

Recomendamos que seus aplicativos usem investigações de integridade. Para registrar o recurso do provedor para investigações de integridade, use o [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) da seguinte maneira:

```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVmssHealthProbe
```

Novamente, levará aproximadamente 10 minutos para que o estado de registro seja reportado como *Registrado*. É possível verificar o status do registro atual com [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). Depois de registrado, certifique-se de que o provedor *Microsoft.Network* está registrado com o [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) da seguinte maneira:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```


## <a name="supported-os-images"></a>Imagens do sistema operacional com suporte
No momento, há suporte apenas determinadas imagens de plataforma do sistema operacional. No momento, não é possível usar imagens personalizadas que você mesmo criou. A propriedade *versão* da imagem da plataforma deve ser definida como *a mais recente*.

No momento, há suporte para os seguintes SKUs (serão adicionados mais):
    
| Editor               | Oferta         |  Sku               | Versão  |
|-------------------------|---------------|--------------------|----------|
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter | mais recente   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    | mais recente   |
| Canônico               | UbuntuServer  | 16.04-LTS          | mais recente   |


## <a name="application-health"></a>Integridade do aplicativo
Durante a atualização do sistema operacional, as instâncias de VM em um conjunto de dimensionamento são atualizadas em um lote por vez. A atualização deverá continuar apenas se o aplicativo do cliente for íntegro nas instâncias de VM atualizadas. Recomendamos que o aplicativo ofereça sinais de integridade ao mecanismo de atualização do sistema operacional do conjunto de dimensionamento. Por padrão, durante os upgrades do sistema operacional, a plataforma considera o estado de energia da VM e o estado de provisionamento da extensão para determinar se uma instância VM é íntegra após uma atualização. Durante a atualização do sistema operacional de uma instância VM, o disco do sistema operacional em uma instância VM é substituído por um novo com base na versão mais recente da imagem. Após a conclusão de atualização do sistema operacional, as extensões configuradas são executadas nessas VMs. Apenas quando todas as extensões em uma VM forem provisionadas com êxito é que o aplicativo será considerado íntegro. 

Um conjunto de dimensionamento pode opcionalmente ser configurado com Investigações de integridade do aplicativo para oferecer à plataforma informações precisas sobre o estado em andamento do aplicativo. As Investigações de integridade do aplicativo são Investigações personalizadas do Load Balancer usadas como um sinal de integridade. O aplicativo em execução em uma instância VM do conjunto de dimensionamento pode responder a solicitações HTTP ou TCP externas que indicam se ele é íntegro. Para obter mias informações sobre como as Investigações personalizadas do Load Balancer funcionam, consulte [Noções básicas de investigações do balanceador de carga](../load-balancer/load-balancer-custom-probe-overview.md). Não é necessária uma Investigação de integridade do aplicativo para upgrades automáticos do sistema operacional, mas é recomendável.

Se o conjunto de dimensionamento estiver configurado para usar vários grupos de posicionamento, as investigações que usarem o [Load Balancer Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) precisarão ser usadas.


### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Configurando uma Investigação personalizada do Load Balancer como uma investigação de integridade do aplicativo em um conjunto de dimensionamento
Como uma melhor prática, crie uma investigação do balanceador de carga explicitamente para a integridade do conjunto de dimensionamento. Poderá ser usado o mesmo ponto de extremidade para uma investigação HTTP ou TCP existente, mas uma investigação de integridade pode exigir um comportamento diferente de uma investigação tradicional do balanceador de carga. Por exemplo, uma investigação tradicional do balanceador de carga poderá ser retornada não íntegra se a carga na instância for alta demais, enquanto que isso pode não ser adequado para determinar a integridade da instância durante uma atualização automática do sistema operacional. Configure a investigação para que ela tenha uma alta taxa de sondagem de menos de 2 minutos.

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
Para ter upgrades seguros, recomenda-se impor uma política de atualização. Essa política pode exigir investigações de integridade do aplicativo em sua assinatura. A política a seguir do Azure Resource Manager rejeita implantações que não têm definições de atualização automatizada da imagem do sistema operacional configuradas:

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


## <a name="configure-auto-updates"></a>Configurar atualizações automáticas
Para configurar upgrades automáticos, certifique-se de que a propriedade *automaticOSUpgrade* está definida como *true* na definição do modelo do conjunto de dimensionamento. É possível configurar essa propriedade com o Azure PowerShell ou a CLI do Azure 2.0.

O exemplo a seguir usa o Azure PowerShell (4.4.1 ou posterior) para configurar upgrades automáticos para o conjunto de dimensionamento denominado *myVMSS* no grupo de recursos denominado *myResourceGroup*:

```powershell
$rgname = myResourceGroup
$vmssname = myVMSS
$vmss = Get-AzureRmVMss -ResourceGroupName $rgname -VmScaleSetName $vmssname
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true
Update-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname -VirtualMachineScaleSet $vmss
```


O exemplo a seguir usa a CLI do Azure (2.0.20 ou posterior) para configurar atualizações automáticas para o conjunto de dimensionamento denominado *myVMSS* no grupo de recursos denominado *myResourceGroup*:

```azure-cli
rgname="myResourceGroup"
vmssname="myVMSS"
az vmss update --name $vmssname --resource-group $rgname --set upgradePolicy.AutomaticOSUpgrade=true
```


## <a name="check-the-status-of-an-automatic-os-upgrade"></a>Verificar o status de uma atualização automática do sistema operacional
É possível verificar o status da atualização mais recente do sistema operacional realizado em seu conjunto de dimensionamento com o Azure PowerShell, a CLI do Azure 2.0 ou as APIs REST.

### <a name="azure-powershell"></a>Azure PowerShell
O exemplo a seguir usa o Azure PowerShell (4.4.1 ou posterior) para verificar o status do conjunto de dimensionamento denominado *myVMSS* no grupo de recursos denominado *myResourceGroup*:

```powershell
Get-AzureRmVmssRollingUpgrade -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0
O exemplo a seguir usa a CLI do Azure (2.0.20 ou posterior) para verificar o status do conjunto de dimensionamento denominado *myVMSS* no grupo de recursos denominado *myResourceGroup*:

```azure-cli
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

1. Se mais de 20% das instâncias forem Não íntegras, interrompa o atualização; caso contrário, continue.
2. Identifique o próximo lote de instâncias VM a serem atualizadas, com um lote tendo, no máximo, 20% da contagem total de instâncias.
3. Atualize o sistema operacional do próximo lote de instâncias VM.
4. Se mais de 20% das instâncias atualizadas forem Não íntegras, interrompa o atualização; caso contrário, continue.
5. Se o cliente tiver configurado Investigações de integridade do aplicativo, o atualização aguardará até 5 minutos para que as investigações se tornem íntegras e ele passará imediatamente para o próximo lote; caso contrário, aguardará 30 minutos antes de passar para o próximo lote.
6. Se houver instâncias remanescentes a serem atualizadas, passe para a etapa 1) para o próximo lote; caso contrário, o atualização será concluído.

O Mecanismo do atualização do sistema operacional do conjunto de dimensionamento verifica a integridade geral da instância VM antes de atualizar cada lote. Ao atualizar um lote, pode haver outra manutenção planejada ou não planejada simultânea acontecendo nos Data centers do Azure que pode afetar a disponibilidade de suas VMs. Portanto, é possível que, temporariamente, mais de 20% das instâncias possam ficar inoperantes. Nesses casos, no final do lote atual, o atualização do conjunto de dimensionamento é interrompido.


## <a name="deploy-with-a-template"></a>Implantar com um modelo

É possível usar o seguinte modelo para implantar um conjunto de dimensionamento que usa atualizaçãos automáticos <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>Atualizações sem interrupção – Ubuntu 16.04-LTS</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"> <img src="http://azuredeploy.net/deploybutton.png"/>
</a>


## <a name="next-steps"></a>Próximas etapas
Para ver mais exemplos sobre como usar atualizaçãos automáticos do sistema operacional com conjuntos de dimensionamento, consulte se há [Recursos de versão prévia no repositório GitHub](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
