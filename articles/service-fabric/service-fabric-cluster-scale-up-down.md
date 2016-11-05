---
title: Escalar ou reduzir horizontalmente um cluster do Service Fabric | Microsoft Docs
description: Escale ou reduza horizontalmente um cluster do Service Fabric para que ele corresponda à demanda, com a definição de regras de autoescala para cada tipo de nó/conjunto de escala de VM. Adicionar ou remover nós de um cluster do Service Fabric
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2016
ms.author: chackdan

---
# Escalar ou reduzir horizontalmente um cluster do Service Fabric usando regras de autoescala
Os conjuntos de escala de Máquina Virtual são um recurso de Computação do Azure que você pode usar para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Service Fabric é configurado como um conjunto de escala de VM separado. Cada tipo de nó pode ser escalado ou reduzido horizontalmente de forma independente, ter conjuntos diferentes de portas abertas e métricas de capacidade diferentes. Leia mais sobre isso no documento [Tipos de nó do Service Fabric](service-fabric-cluster-nodetypes.md). Uma vez que os tipos de nó do Service Fabric no cluster são compostos por conjuntos de escala de VM no back-end, você precisa configurar as regras de autoescala para cada tipo de nó/conjunto de escala de VM.

> [!NOTE]
> Sua assinatura precisa ter uma quantidade suficiente de núcleos para adicionar as novas VMs que compõe esse cluster. Atualmente, não há nenhuma validação de modelo. Logo, você receberá uma falha de tempo de implantação se qualquer um dos limites de cota for atingido.
> 
> 

## Escolher o tipo de nó/conjunto de escala de VM a ser escalado
Atualmente, não é possível especificar as regras de escala automática para os conjuntos de escala de VM usando o portal; por esse motivo, vamos usar o Azure PowerShell (1.0+) para listar os tipos de nó e adicionar regras de escala automática a eles.

Para obter a lista de conjuntos de escala de VM que compõem o cluster, execute os seguintes cmdlets:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <VM Scale Set name>
```

## Definir regras de escala automática para o tipo de nó/conjunto de escala de VM
Se o cluster tiver vários tipos de nós, repita este procedimento para cada tipo de nó/conjunto de escala de VM que você quiser escalar ou reduzir horizontalmente. Leve em conta o número de nós que você precisa ter antes de configurar o dimensionamento automático. O número mínimo de nós necessários para o tipo de nó primário é controlado pelo nível de confiabilidade escolhido. Leia mais sobre os [níveis de confiabilidade](service-fabric-cluster-capacity.md).

> [!NOTE]
> A redução vertical do tipo de nó primário para um número inferior ao mínimo causará a instabilidade ou indisponibilidade do cluster. Isso poderá resultar em perda de dados de seus aplicativos e dos serviços do sistema.
> 
> 

Atualmente, o recurso de escala automática não é controlado pelas cargas que os aplicativos podem relatar ao Service Fabric. Portanto, no momento, a escala automático obtida é meramente controlada pelos contadores de desempenho que são emitidos por cada uma das instâncias do conjunto de escala de VM.

Siga estas instruções [para configurar a escala automática para cada conjunto de escala de VM](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> Em um cenário de redução vertical, a menos que o tipo de nó tenha um nível de durabilidade Gold ou Silver, você precisará chamar o cmdlet [Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/azure/mt125993.aspx) pelo nome de nó apropriado.
> 
> 

## Adicione as VMs manualmente a um tipo de nó/conjunto de escala de VM
Siga os modelos/instruções da [galeria de modelos de Início Rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para alterar o número de VMs em cada Nodetype.

> [!NOTE]
> O processo de adicionar VMs leva tempo. Portanto, não espere que as adições sejam instantâneas. Sendo assim, planeje adicionar capacidade pontual para permitir mais 10 minutos antes que a capacidade da VM esteja disponível para que as réplicas/instâncias de serviço sejam alocadas.
> 
> 

## Remova as VMs manualmente do tipo de nó/conjunto de escala de VM
> [!NOTE]
> Os serviços do sistema Service Fabric são executados no tipo de nó Primário do cluster. Deste modo, nunca desligue ou reduza verticalmente o número de instâncias neste tipo de nó para uma quantidade menor do que a camada de confiabilidade garante. Confira [os detalhes sobre os níveis de confiabilidade aqui](service-fabric-cluster-capacity.md).
> 
> 

Você precisa executar as etapas a seguir em uma instância VM por vez. Isso permite que os serviços do sistema (e seus serviços com estado) sejam desligados normalmente na instância VM que você estiver removendo e que novas réplicas sejam criadas em outros nós.

1. Execute [Disable-ServiceFabricNode](https://msdn.microsoft.com/library/mt125852.aspx) com a intenção 'RemoveNode' para desabilitar o nó que você removerá (a instância mais alta deste tipo de nó).
2. Execute [Get-ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) para verificar se o nó realmente foi desabilitado. Caso contrário, aguarde até que o nó seja desabilitado. Não tenha pressa nesta etapa.
3. Siga os modelos/instruções da [galeria de modelos de Início Rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para alterar a quantidade de VMs para uma no Nodetype. A instância removida é a instância VM mais alta.
4. Repita as etapas 1 a 3 conforme necessário, mas nunca reduza verticalmente o número de instâncias no tipo de nó primário para uma quantidade menor do que a camada de confiabilidade garante. Confira [os detalhes sobre os níveis de confiabilidade aqui](service-fabric-cluster-capacity.md).

## Remova as VMs manualmente do tipo de nó não primário/conjunto de escala de VM
> [!NOTE]
> Para um serviço com estado, você precisa de um determinado número de nós que devem estar sempre ativos para manter a disponibilidade e preservar o estado do serviço. No mínimo, é necessário que o número de nós seja igual à contagem de conjunto de réplicas de destino do serviço/partição.
> 
> 

Você precisa executar as seguintes etapas em uma instância VM por vez. Isso permite que os serviços do sistema (e seus serviços com estado) sejam desligados normalmente na instância VM que você estiver removendo e que novas réplicas sejam criadas em outro lugar.

1. Execute [Disable-ServiceFabricNode](https://msdn.microsoft.com/library/mt125852.aspx) com a intenção 'RemoveNode' para desabilitar o nó que você removerá (a instância mais alta deste tipo de nó).
2. Execute [Get-ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) para verificar se o nó realmente foi desabilitado. Caso contrário, aguarde até que o nó seja desabilitado. Não tenha pressa nesta etapa.
3. Siga os modelos/instruções da [galeria de modelos de Início Rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para alterar a quantidade de VMs para uma no Nodetype. Agora, isso removerá a instância VM mais alta.
4. Repita as etapas 1 a 3 conforme necessário, mas nunca reduza verticalmente o número de instâncias no tipo de nó primário para uma quantidade menor do que a camada de confiabilidade garante. Confira [os detalhes sobre os níveis de confiabilidade aqui](service-fabric-cluster-capacity.md).

## Comportamentos que podem ser observados no Service Fabric Explorer
Ao escalar verticalmente um cluster, o Service Fabric Explorer refletirá o número de nós (instâncias de conjuntos de escala de VM) que fazem parte do cluster. No entanto, ao reduzir verticalmente um cluster, você verá o nó removido/instância VM exibida em um estado não íntegro, a menos que você chame o cmdlet [Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx) pelo nome de nó apropriado.

Veja a seguir a explicação para esse comportamento.

Os nós listados no Service Fabric Explorer são um reflexo do que os serviços do sistema do Service Fabric (especificamente, FM) conhecem sobre o número de nós que o cluster tinha/tem. Ao reduzir verticalmente o conjunto de escala de VM, isso significa que a VM foi excluída, mas o serviço do sistema do FM ainda acreditará que o nó (mapeado para a VM excluída) voltará. Portanto, o Service Fabric Explorer continua exibindo o nó (embora o estado de integridade possa ser erro ou desconhecido).

Para certificar-se de que um nó será removido quando uma VM for removida, você tem duas opções:

1) Escolher um nível de durabilidade Gold ou Silver (disponível em breve) para os tipos de nó no cluster, que fornecem a integração da infraestrutura. Isso, em seguida, removerá automaticamente os nós do estado (FM) dos serviços do sistema ao reduzir verticalmente. Confira [os detalhes sobre os níveis de durabilidade aqui](service-fabric-cluster-capacity.md)

2) Depois que a instância VM for reduzida verticalmente, você precisará chamar o cmdlet [Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx).

> [!NOTE]
> Os clusters de Service Fabric exigem um determinado número de nós que devem estar ativos o tempo todo para manter a disponibilidade e preservar o estado – conhecido como "manter o quórum". Desta forma, em geral, não é seguro desligar todos os computadores no cluster, a menos que você tenha executado primeiro um [backup completo do estado](service-fabric-reliable-services-backup-restore.md).
> 
> 

## Próximas etapas
Leia os seguintes artigos para saber também sobre como planejar a capacidade do cluster, atualizar um cluster e particionar os serviços:

* [Planejar a capacidade do cluster](service-fabric-cluster-capacity.md)
* [Atualizações do cluster](service-fabric-cluster-upgrade.md)
* [Serviços com estado de partição para escala máxima](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

<!---HONumber=AcomDC_0921_2016-->