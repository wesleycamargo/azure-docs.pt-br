<properties
   pageTitle="Escalar ou reduzir verticalmente um cluster do Service Fabric | Microsoft Azure"
   description="Escale ou reduza verticalmente um cluster do Service Fabric para que ele corresponda à demanda, com a definição de regras de escala automática para cada tipo de nó/conjunto de escala de VM."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/04/2016"
   ms.author="chackdan"/>


# Escalar ou reduzir verticalmente um cluster do Service Fabric usando regras de escala automática

Os conjuntos de escala de máquinas virtuais são um recurso de computação do Azure que podem ser usados para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Service Fabric é configurado como um conjunto separado de escala de VM. Cada tipo de nó pode ser escalado verticalmente para cima ou para baixo de forma independente, tem conjuntos diferentes de portas abertas e pode ter métricas de capacidade diferente. Leia mais sobre isso no documento [Tipos de nó do Service Fabric](service-fabric-cluster-nodetypes.md). Como os tipos de nó do Service Fabric no cluster são compostos por conjuntos de escala de VM no back-end, você precisará configurar regras de escala automática para cada tipo de nó/conjunto de escala de VM.

>[AZURE.NOTE] Sua assinatura precisa ter uma quantidade suficiente de núcleos para adicionar as novas VMs que comporão esse cluster. Atualmente, não há nenhuma validação de modelo e, portanto, você receberá uma falha de tempo de implantação caso qualquer um dos limites de cota sejam atingidos.

## Escolher o tipo de nó/conjunto de escala de VM a ser escalado

Atualmente, não é possível especificar as regras de escala automática para os conjuntos de escala de VM usando o portal; por esse motivo, vamos usar o Azure PowerShell (1.0+) para listar os tipos de nó e adicionar regras de escala automática a eles.

Para obter a lista de conjuntos de escala de VM que compõem o cluster, execute os seguintes cmdlets:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <VM Scale Set name>
```

## Definir regras de escala automática para o tipo de nó/conjunto de escala de VM

Se o cluster tiver vários tipos de nó, você precisará fazer isso para cada tipo de nó/conjuntos de escala de VM que você deseja escalar ou reduzir verticalmente. Leve em conta o número de nós que você precisa ter antes de configurar o dimensionamento automático. O número mínimo de nós necessários para o tipo de nó primário é controlado pelo nível de confiabilidade escolhido. Leia mais sobre os [níveis de confiabilidade](service-fabric-cluster-capacity.md).

>[AZURE.NOTE]  Reduzir verticalmente o tipo de nó primário para um número inferior ao número mínimo causará a instabilidade ou indisponibilidade do cluster. Isso poderá resultar em perda de dados de seus aplicativos e dos serviços do sistema.

Atualmente, o recurso de escala automática não é controlado pelas cargas que os aplicativos podem relatar ao Service Fabric. Portanto, no momento, a escala automático obtida é meramente controlada pelos contadores de desempenho que são emitidos por cada uma das instâncias do conjunto de escala de VM.

Siga estas instruções [para configurar a escala automática para cada conjunto de escala de VM](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

>[AZURE.NOTE] Em um cenário de redução vertical, a menos que o tipo de nó tenha um nível de durabilidade de Gold ou Silver, você precisará chamar o cmdlet [Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/azure/mt125993.aspx) com o nome de nó apropriado.

## Comportamentos que podem ser observados no Service Fabric Explorer

Ao escalar verticalmente um cluster, o Service Fabric Explorer refletirá o número de nós (instâncias de conjuntos de escala de VM) que fazem parte do cluster. No entanto, ao reduzir verticalmente um cluster, você ainda verá a instância do nó e/ou da VM removida e exibida em um estado não íntegro, a menos que você chame o cmdlet [Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx) com o nome de nó apropriado.

Veja a seguir a explicação para esse comportamento.

Os nós listados no Service Fabric Explorer são um reflexo do que os serviços do sistema do Service Fabric (especificamente, FM) conhecem sobre o número de nós que o cluster tinha/tem. Ao reduzir verticalmente o conjunto de escala de VM, isso significa que a VM foi excluída, mas o serviço do sistema do FM ainda acreditará que o nó (mapeado para a VM excluída) voltará. Portanto, o Service Fabric Explorer continua exibindo o nó (embora o estado de integridade possa ser erro ou desconhecido).

Para certificar-se de que um nó é removido quando uma VM é removida, você tem duas opções:

1) Escolher um nível de durabilidade de Gold ou Silver (disponível em breve) para os tipos de nó no cluster, que fornecerão a integração da infraestrutura. Isso, em seguida, removerá automaticamente os nós do estado (FM) dos serviços do sistema ao reduzir verticalmente. Consulte [os detalhes sobre os níveis de durabilidade](service-fabric-cluster-capacity.md)

2) Depois que a instância VM for reduzida verticalmente, você precisará chamar o cmdlet [Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx).

>[AZURE.NOTE] Os clusters de Service Fabric exigem um determinado número de nós que devem estar ativos em todos os momentos para manter a disponibilidade e preservar o estado - conhecido como "manter o quórum". Consequentemente, em geral, não é seguro desligar todos os computadores no cluster, a menos que você tenha executado primeiro um [backup completo do estado](service-fabric-reliable-services-backup-restore.md).

## Próximas etapas
Leia os seguintes artigos para saber também sobre como planejar a capacidade do cluster, atualizar um cluster e particionar os serviços:

- [Planejar a capacidade do cluster](service-fabric-cluster-capacity.md)
- [Atualizações do cluster](service-fabric-cluster-upgrade.md)
- [Serviços com estado de partição para escala máxima](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

<!---HONumber=AcomDC_0608_2016-->