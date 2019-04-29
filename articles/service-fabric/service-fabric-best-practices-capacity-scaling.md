---
title: Melhores práticas de dimensionamento e planejamento da capacidade do Azure Service Fabric | Microsoft Docs
description: Melhores práticas para planejamento e dimensionamento de clusters do Service Fabric e aplicativos.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 425154958e4c60902b56f320f714a011b9095830
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61471529"
---
# <a name="capacity-planning-and-scaling"></a>Planejamento de capacidade e dimensionamento

Antes de criar qualquer cluster do Service Fabric do Azure ou dimensionar recursos de computação hospedando seu cluster, é importante planejar a capacidade. Para obter mais informações sobre o planejamento de capacidade, confira [Planejamento de capacidade do cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Além de considerar as características de Nodetype e Cluster, planeje para operações de dimensionamento levarem mais de uma hora para serem concluídas para um ambiente de produção, independentemente do número de VMs que você está adicionando.

## <a name="auto-scaling"></a>Dimensionamento automático
Operações de dimensionamento devem ser executadas por meio de implantação de modelo de Recurso do Azure, porque é uma melhor prática tratar [configurações de recursos como código]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code), e usar o dimensionamento automático do Conjunto de Dimensionamento de Máquinas Virtuais resultará em seu modelo do Resource Manager com controle de versão definir de modo impreciso suas contagens de instância do conjunto de dimensionamento de máquinas virtuais, aumentando o risco de implantações futuras e gerando operações de dimensionamento não intencionais. Em geral, você deve usar o dimensionamento automático se:

* Implantar modelos do Resource Manager com capacidade apropriada declarada não dá suporte a seu caso de uso.
  * Além do dimensionamento manual, você pode configurar um [Pipeline de Entrega e uma Integração Contínua no Azure DevOps Services usando projetos de implantação do Grupo de Recursos do Azure]( https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts), o que é normalmente disparado por um aplicativo lógico que utiliza métricas de desempenho da máquina virtual consultadas da [API REST do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough), efetivamente realizando o dimensionamento automático com base em qualquer métricas que você queira ao mesmo tempo em que otimiza para agregação de valor do Azure Resource Manager.
* Você só precisará escalar horizontalmente um nó de Conjunto de Dimensionamento de Máquinas Virtuais por vez.
  * Para dimensionar por três ou mais nós por vez, você deve [dimensionar um cluster do Service Fabric adicionando um Conjunto de Dimensionamento de Máquinas Virtuais](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out), e é mais seguro reduzir horizontalmente e expandir Conjuntos de Dimensionamento de Máquinas Virtuais um nó por vez.
* Você tem Confiabilidade Prata ou superior para seu Cluster do Service Fabric e Durabilidade Prata ou superior em qualquer Conjunto de Dimensionamento em que configure regras de Dimensionamento Automático.
  * A capacidade das regras de dimensionamento automático [mínima] deve ser igual ou maior que cinco instâncias de máquina virtual e deve ser igual ou maior que o mínimo da Camada de Confiabilidade para seu tipo de Nó Primário.

> [!NOTE]
> Malha de serviço com estado do Azure Service Fabric:/System/InfastructureService/<NODE_TYPE_NAME>, é executado em todos os Tipos de Nó que tenham Durabilidade Prata ou Superior, que é o único Serviço do Sistema com suporte para execução no Azure em qualquer um de seus Tipos de Nó de cluster. 

## <a name="vertical-scaling-considerations"></a>Considerações de dimensionamento vertical

[Dimensionamento vertical](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) um tipo de nó no Azure Service Fabric requer várias etapas e considerações. Por exemplo: 
* O cluster deve estar íntegro antes do dimensionamento. Caso contrário, você só desestabilizará o cluster ainda mais.
* **Nível de durabilidade Prata ou superior** é necessário para todos os NodeTypes do Cluster do Service Fabric que hospedam serviços com estado.

> [!NOTE]
> Seu NodeType primário que hospeda os Serviços de Sistema do Service Fabric com Estado deve ter um nível de durabilidade Prata ou superior. Depois de habilitar a durabilidade prata, as operações de cluster, como atualizar, adicionar ou remover nós e assim por diante, serão mais lentas porque o sistema otimiza para segurança de dados em vez de velocidade de operações.

O dimensionamento vertical de um Conjunto de Dimensionamento de Máquinas Virtuais é uma operação destrutiva. Em vez disso, dimensione horizontalmente seu cluster adicionando um novo Conjunto de Dimensionamento com a SKU desejada e migre os serviços para sua SKU desejada para concluir uma operação de dimensionamento vertical segura. Alterar um SKU de recurso do Conjunto de Dimensionamento de Máquinas Virtuais é uma operação destrutiva porque refaz a imagem de seus hosts, o que remove todos os estados localmente persistentes.

As [restrições de posicionamento e propriedades de nó](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) do Service Fabric são usadas pelo seu cluster para decidir em que local hospedar seus Serviços de Aplicativos. Ao dimensionar verticalmente seu tipo de nó primário, declare valores de propriedade idênticos para `"nodeTypeRef"`, encontrados na Extensão de Service Fabric do Conjunto de Dimensionamento de Máquinas Virtuais. O seguinte snippet do modelo do Resource Manager mostra as propriedades que você vai declarar, com o mesmo valor para seus conjuntos de dimensionamento provisionados para os quais você está dimensionando e tem suporte apenas como uma monitoração com estado temporária para seu cluster:

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Não deixe o cluster em execução com vários conjuntos de dimensionamento que usam o mesmo valor da propriedade `nodeTypeRef` por mais tempo que o necessário para concluir uma operação bem-sucedida de escala vertical.
> Sempre valide as operações em ambientes de teste antes de tentar as alterações de ambiente de produção. Por padrão, os Serviços do Sistema de Cluster do Service Fabric têm uma restrição de posicionamento para direcionar apenas ao nodetype primário.

Com as propriedades de nó e as restrições de posicionamento declaradas, siga estas etapas uma instância de VM por vez. Isso permite que os serviços do sistema (e seus serviços com estado) sejam desligados normalmente na instância VM que você estiver removendo conforme novas réplicas são criadas em outro lugar.
1. No PowerShell, execute `Disable-ServiceFabricNode` com a intenção ‘RemoveNode’ para desabilitar o nó que você vai remover. Remova o tipo de nó que tem o número mais alto. Por exemplo, se você tiver um cluster de seis nós, remova a instância de máquina virtual "MyNodeType_5".
2. Execute `Get-ServiceFabricNode` para garantir que o nó tenha feito a transição para desabilitado. Caso contrário, aguarde até que o nó seja desabilitado. Isso pode levar algumas horas para cada nó. Não continue até que o nó tenha feito a transição para desabilitado.
3. Diminua o número de VMs em um naquele tipo de Nó. A instância da VM mais alta agora será removida.
4. Repita as etapas 1 a 3 conforme necessário, mas nunca reduza verticalmente o número de instâncias no tipo de nó primário para uma quantidade menor do que a camada de confiabilidade garante. Confira [Planejamento de capacidade do cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) para obter uma lista de instâncias recomendadas.

## <a name="horizontal-scaling"></a>Dimensionamento em escala horizontal

A Escala Horizontal no Service Fabric horizontal pode ser feita tanto de modo [manual](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) quanto [programático](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling).

> [!NOTE]
> Se você estiver dimensionando de um tipo de nó que tenha durabilidade prata ou ouro, o dimensionamento será lento.

### <a name="scaling-out"></a>Expansão

Expanda um cluster do Service Fabric aumentando a contagem de instâncias para um determinado Conjunto de Dimensionamento de Máquinas Virtuais. Você pode expandir de modo programático usando o AzureClient e a ID para o conjunto de dimensionamento desejado para aumentar a capacidade.

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Para expandir manualmente, atualize a capacidade na propriedade SKU do recurso [Conjunto de Dimensionamento de Máquinas Virtuais](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) desejado.
```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Redução horizontal

Reduzir exige mais consideração do que expandir. Por exemplo: 
* Os serviços do sistema do Service Fabric são executados no tipo de nó Primário em seu cluster. Nunca desligue nem reduza verticalmente o número de instâncias desse tipo de nó para que você tenha menos instâncias do que o que a camada de confiabilidade requer. 
* Para um serviço com estado, você precisa de um determinado número de nós estar sempre ativos para manter a disponibilidade e preservar o estado do serviço. No mínimo, você precisa de um número de nós seja igual à contagem de conjunto de réplicas de destino do serviço/partição.

Para reduzir manualmente, siga estas etapas:

1. No PowerShell, execute `Disable-ServiceFabricNode` com a intenção ‘RemoveNode’ para desabilitar o nó que você vai remover. Remova o tipo de nó que tem o número mais alto. Por exemplo, se você tiver um cluster de seis nós, remova a instância de máquina virtual "MyNodeType_5".
2. Execute `Get-ServiceFabricNode` para garantir que o nó tenha feito a transição para desabilitado. Caso contrário, aguarde até que o nó seja desabilitado. Isso pode levar algumas horas para cada nó. Não continue até que o nó tenha feito a transição para desabilitado.
3. Diminua o número de VMs em um naquele tipo de Nó. A instância da VM mais alta agora será removida.
4. Repita as etapas 1 a 3 conforme necessário, mas nunca reduza verticalmente o número de instâncias no tipo de nó primário para uma quantidade menor do que a camada de confiabilidade garante. Confira [Planejamento de capacidade do cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) para obter uma lista de instâncias recomendadas.

Para reduzir manualmente, atualize a capacidade na propriedade SKU do recurso [Conjunto de Dimensionamento de Máquinas Virtuais](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) desejado.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

1. Repita as etapas 1 a 3 até provisionar a capacidade desejada. Não reduza o número de instâncias nos tipos de nó primários para menos do que aquilo que a camada de confiabilidade requer. Para obter detalhes sobre níveis de confiabilidade e o número de instâncias exigidas por eles, veja [Planejando a capacidade do cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Você deve preparar o nó para desligamento para reduzir de modo programático. Isso envolve localizar o nó a ser removido, que é o nó mais alto da instância, e desativá-lo. Por exemplo: 

```c#
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

Depois de identificar o nó a ser removido, desative e remova-o usando a mesma instância `FabricClient` (`client`, neste caso) e nome da instância de nó (`instanceIdString`, neste caso) usados no código acima:

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> Quando você escala um cluster para baixo, você verá a instância de nó/VM removida exibida em um estado não íntegro no Service Fabric Explorer. Para obter uma explicação desse comportamento, consulte [comportamentos que você pode observar no Service Fabric Explorer](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer).
> 
> Você pode:
> * Chame [Remove-ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) com o nome de nó apropriado.
> * Implante [aplicativo do service fabric AutoEscala auxiliar](https://github.com/Azure/service-fabric-autoscale-helper/) no seu cluster, que garante a escala nós inativos serão apagados do Service Fabric Explorer.

## <a name="reliability-levels"></a>Níveis de confiabilidade

O [nível de confiabilidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) é uma propriedade de seu recurso de Cluster do Service Fabric e não pode ser configurado de modo diferente para nodeTypes individuais. Ele controla o fator de replicação dos serviços do sistema para o cluster e é uma configuração no nível do recurso de cluster. O nível de confiabilidade determinará o número mínimo de nós que o tipo de nó primário deve ter. A camada de confiabilidade pode ter os valores a seguir:
* Platina – executa serviços do Sistema com uma contagem de conjunto de réplica de destino de sete e nove nós de semente.
* Ouro – executa serviços do Sistema com uma contagem de conjunto de réplica de destino de sete e sete nós de semente.
* Prata – executa serviços do Sistema com uma contagem de conjunto de réplica de destino de cinco e cinco nós de semente.
* Bronze – executa serviços do Sistema com uma contagem de conjunto de réplica de destino de três e três nós de semente.

O nível mínimo recomendado de confiabilidade é Prata.

O nível de confiabilidade é definido na seção de propriedades do [recurso Microsoft.ServiceFabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters), semelhante a isto:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Níveis de durabilidade

> [!WARNING]
> Tipos de nó executados com durabilidade Bronze não têm _nenhum privilégio_. Isso significa que trabalhos de infraestrutura que afetam sus cargas de trabalho sem estado não serão interrompidas ou atrasadas, que podem afetar suas cargas de trabalho. Use a durabilidade Bronze somente para tipos de nós que executam cargas de trabalho sem estado. Para cargas de trabalho de produção, execute Prata ou superior para garantir a consistência de estado. Escolha a confiabilidade correta com base nas diretrizes na [documentação de planejamento de capacidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

O nível de durabilidade deve ser definido em dois recursos. O perfil da extensão do [recurso do Conjunto de Dimensionamento de Máquinas Virtuais](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

```json
"extensionProfile": {
    "extensions":          {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
        "properties": {
            "settings": {
                "durabilityLevel": "Bronze"
            }
        }
    }
}
```

E, em `nodeTypes`, no [recurso Microsoft.ServiceFabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters) 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Próximas etapas

* Criar um cluster em VMs ou em computadores executando o Windows Server: [Criação de cluster do Service Fabric para o Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Criar um cluster nas VMS ou computadores executando Linux: [Criar um cluster do Linux](service-fabric-cluster-creation-via-portal.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
