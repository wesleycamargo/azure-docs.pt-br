---
title: Proteger um cluster do Azure Service Fabric | Microsoft Docs
description: Saiba como dimensionar rapidamente um cluster do Service Fabric.
services: service-fabric
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/24/2017
ms.author: adegeo
ms.custom: mvc
ms.openlocfilehash: 63b4747164959b0e95f6d3f1908d1fd265589a98
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2017
---
# <a name="scale-a-service-fabric-cluster"></a>Colocar em escala um cluster do Service Fabric

Este tutorial é parte dois de uma série e mostra como expandir e reduzir o seu cluster existente. Quando tiver terminado, saberá como colocar o cluster em escala e limpar quaisquer recursos restantes.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Ler a contagem de nó de cluster
> * Adicionar nós de cluster (aumentar)
> * Remover nós de cluster (reduzir horizontalmente)

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * Criar um [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou [cluster do Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) seguro no Azure usando um modelo
> * Reduzir ou escalar um cluster horizontalmente
> * [Atualizar o tempo de execução de um cluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Implantar o Gerenciamento de API com o Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial:
- Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Instale o [módulo do Azure PowerShell versão 4.1 ou superior](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) ou [CLI 2.0 do Azure](/cli/azure/install-azure-cli).
- Criar um [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou [cluster do Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) seguro no Azure
- Se você implantar um cluster do Windows, configure um ambiente de desenvolvimento do Windows. Instale as cargas de trabalho do [Visual Studio 2017](http://www.visualstudio.com) e o **Desenvolvimento do Azure**, de **desenvolvimento ASP.NET e Web** e de **desenvolvimento multiplataforma do .NET Core**.  Em seguida, configure um [ambiente de desenvolvimento .NET](service-fabric-get-started.md).
- Se você implantar um cluster do Linux, configure um ambiente de desenvolvimento Java em [Linux](service-fabric-get-started-linux.md) ou [MacOS](service-fabric-get-started-mac.md).  Instale a [CLI do Service Fabric](service-fabric-cli.md). 

## <a name="sign-in-to-azure"></a>Entrar no Azure
Entre na sua conta do Azure e selecione sua assinatura antes de executar comandos do Azure.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="connect-to-the-cluster"></a>Conectar-se ao cluster

Para concluir esta parte do tutorial com sucesso, você precisa conectar-se ao cluster do Service Fabric e ao conjunto de dimensionamento de máquinas virtuais (que hospeda o cluster). O conjunto de dimensionamento de máquinas virtuais é o recurso do Azure que hospeda o Service Fabric no Azure.

Quando você se conectar a um cluster, poderá consultá-lo para obter informações. Você pode usar o cluster para saber mais sobre de quais nós o cluster está ciente. Conectar-se ao cluster no código a seguir usa o mesmo certificado criado na primeira parte desta série. Verifique se você configurou as variáveis `$endpoint` e `$thumbprint` para seus valores.

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint $thumbprint `
          -FindType FindByThumbprint -FindValue $thumbprint `
          -StoreLocation CurrentUser -StoreName My

Get-ServiceFabricClusterHealth
```

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Agora que você está conectado, pode usar um comando para obter o status de cada nó no cluster. Para o PowerShell, use o comando `Get-ServiceFabricClusterHealth` e, para **sfctl**, use o comando ``.

## <a name="scale-out"></a>Expansão

Ao aumentar, você adicionar mais instâncias de máquina virtual ao conjunto de dimensionamento. Essas instâncias tornam-se os nós que o Service Fabric usa. O Service Fabric sabe quando o conjunto de dimensionamento tem mais instâncias adicionadas (aumentando) e reage automaticamente. O código a seguir obtém um conjunto de dimensionamento por nome e aumenta a **capacidade** do conjunto de dimensionamento em 1.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Esse código define a capacidade para 6.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="scale-in"></a>Reduzir horizontalmente

Reduzir é igual a aumentando, exceto pelo uso de um valor de **capacidade** menor. Ao reduzir horizontalmente o conjunto de dimensionamento, você remove instâncias de máquina virtual do conjunto de dimensionamento. Normalmente, o Service Fabric desconhece o que aconteceu e acredita que um nó foi perdido. Service Fabric então relata um estado não íntegro para o cluster. Para evitar que esse estado inválido, você deve informar ao service fabric que você espera que o nó desapareça.

### <a name="remove-the-service-fabric-node"></a>Remover o nó do service fabric

> [!NOTE]
> Essa parte aplica-se somente à camada de durabilidade *Bronze*. Para obter mais informações sobre durabilidade, consulte [Planejamento de capacidade do cluster do Service Fabric][durability].

Quando você reduz um conjunto de dimensionamento de máquinas virtuais, a o conjunto de dimensionamento (na maioria dos casos) remove a última instância de máquina virtual criada. Portanto, você precisa localizar o último nó do service fabric criado correspondente. Você pode encontrar este último nó verificando o maior valor da propriedade `NodeInstanceId` em nós do service fabric. Os exemplos de código abaixo são classificados pela instância do nó e retornam os detalhes sobre a instância com o maior valor de ID. 

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
```

```azurecli
`sfctl node list --query "sort_by(items[*], &instanceId)[-1]"`
```

O cluster do service fabric precisa saber que este nó será removido. Você precisa executar três etapas:

1. Desabilite o nó para que ele não seja mais uma replicação de dados.  
PowerShell: `Disable-ServiceFabricNode`  
sfcli: `sfctl node disable`

2. Pare o nó de modo que o tempo de execução do service fabric seja desligado corretamente e o aplicativo obtenha uma solicitação de encerramento.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfcli: `sfctl node transition --node-transition-type Stop`

2. Remova o nó do cluster.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfcli: `sfctl node remove-state`

Depois que essas três etapas tiverem sido aplicadas ao nó, ele poderá ser removido do conjunto de dimensionamento. Se você estiver usando qualquer camada de durabilidade além [bronze][durability], estas etapas serão executadas para você quando a instância do conjunto de dimensionamento for removida.

O bloco de código a seguir obtém o último nó criado, desabilita, para e remove o nó do cluster.

```powershell
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300
    
    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }
    
    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

No código **sfctl** abaixo, o comando a seguir é usado para obter o os valores **node-name** e **node-instance-id** do último nó criado: `sfctl node list --query "sort_by(items[*], &instanceId)[-1].[instanceId,name]"`

```azurecli
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> Use as seguintes consultas **sfctl** para verificar o status de cada etapa
>
> **Verificar o status de desativação**  
> `sfctl node list --query "sort_by(items[*], &instanceId)[-1].nodeDeactivationInfo"`
>
> **Verificar o status de parada**  
> `sfctl node list --query "sort_by(items[*], &instanceId)[-1].isStopped"`
>


### <a name="scale-in-the-scale-set"></a>Reduzir horizontalmente o conjunto de dimensionamento

Agora que o nó do service fabric foi removido do cluster, o conjunto de dimensionamento de máquinas virtuais pode ser reduzido horizontalmente. No exemplo a seguir, a capacidade do conjunto de dimensionamento é reduzida em 1.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Esse código define a capacidade para 5.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Ler a contagem de nó de cluster
> * Adicionar nós de cluster (aumentar)
> * Remover nós de cluster (reduzir horizontalmente)


Em seguida, avance para o próximo tutorial para saber como atualizar o tempo de execução de um cluster.
> [!div class="nextstepaction"]
> [Atualizar o tempo de execução de um cluster](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
