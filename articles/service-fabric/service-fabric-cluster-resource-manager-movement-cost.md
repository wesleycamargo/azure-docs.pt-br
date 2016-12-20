---
title: 'Gerenciador de Recursos de Cluster do Service Fabric: custo de movimento | Microsoft Docs'
description: "Visão geral do custo dos movimentos de serviços do Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: f022f258-7bc0-4db4-aa85-8c6c8344da32
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 54cdfee64360543e0d1011f04c9b79400e912582


---
# <a name="service-movement-cost-for-influencing-cluster-resource-manager-choices"></a>Custo de movimento do serviço para influenciar escolhas do Gerenciador de Recursos de Cluster
Um fator importante a ser levado em consideração ao tentar determinar quais alterações devem ser feitas em um cluster e a pontuação de uma solução é o custo geral de alcançar essa solução.

Mover réplicas ou instâncias de serviço custa, no mínimo, tempo de CPU e largura de banda de rede. Para serviços com estado, há também o custo da quantidade de espaço em disco necessária para criar uma cópia do estado antes de desligar réplicas antigas. Obviamente, seria desejável minimizar o custo de qualquer solução que o Gerenciador de Recursos de Cluster do Azure Service Fabric criar. Por outro lado, não seria bom ignorar soluções que melhorariam significativamente a alocação de recursos no cluster.

O Gerenciador de Recursos de Cluster tem duas maneiras de computar custos e limitá-los, mesmo ao tentar gerenciar o cluster de acordo com seus outros objetivos. A primeira é que, ao planejar um novo layout para o cluster, o Gerenciador de Recursos de Cluster conta cada uma das movimentações que faria. Em um caso simples, se você tiver duas soluções com praticamente o mesmo saldo (pontuação) geral no final, escolha aquela com o menor custo (número total de movimentações).

Isso funciona muito bem. Mas, como ocorre com cargas estáticas ou padrão, é improvável em qualquer sistema complexo que todas as mudanças sejam iguais. É provável que algumas delas sejam muito mais caras.

## <a name="changing-a-replicas-move-cost-and-factors-to-consider"></a>Alterando o custo de movimento de uma réplica e fatores a serem considerados
Assim como ao relatar a carga (outro recurso do Gerenciador de Recursos de Cluster), você fornece ao serviço uma forma de relatar automaticamente quão caro é o serviço de movimento em um dado momento.

Código:

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

O MoveCost tem quatro níveis: Zero, Baixo, Médio e Alto. Eles são relativos uns aos outros, exceto pelo Zero. Zero significa que a mudança de uma réplica é gratuita e não deve contar para a pontuação da solução. Definir o custo de movimentação para Alto *não* é uma garantia de que a réplica não será movida, apenas que ela não será movida a menos que haja um bom motivo para isso.

![Custo de movimentos como um fator na seleção de réplicas para movimento][Image1]

O MoveCost ajuda a encontrar as soluções que causam, em geral, o mínimo de interrupções e que sejam mais fáceis de conseguir enquanto ainda alcançam o equilíbrio equivalente. A noção de custo de um serviço pode ser relativa a muitas coisas. Os fatores mais comuns ao calcular o custo do movimento são:

* a quantidade de estados ou de dados que o serviço deve mover.
* o custo de desconexão de clientes. O custo de mover uma réplica primária normalmente é mais alto do que o custo de mover uma réplica secundária.
* o custo de interromper uma operação em andamento. Algumas operações no nível do armazenamento de dados ou operações realizadas em resposta a uma chamada de cliente são caras. Depois de um certo ponto, você não quer interrompê-las a não ser que seja necessário. Portanto, durante a operação, você aumenta o custo para reduzir a probabilidade de que a réplica ou a instância de serviço seja movida. Quando a operação for concluída, você a coloca novamente no estado normal.

## <a name="next-steps"></a>Próximas etapas
* O Gerenciador de Recursos de Cluster do Service Fabric usa métricas para gerenciar o consumo e a capacidade no cluster. Para saber mais sobre as métricas e como configurá-las, confira [Gerenciando o consumo e a carga de recursos no Service Fabric com métricas](service-fabric-cluster-resource-manager-metrics.md).
* Para saber como o Gerenciador de Recursos de Cluster gerencia e balanceia carga no cluster, confira [Balanceamento do cluster do Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png



<!--HONumber=Nov16_HO3-->


