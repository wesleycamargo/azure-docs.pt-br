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
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: f85365a36aea39b4179805e728c7ddafa140f08b


---
# <a name="service-movement-cost-for-influencing-cluster-resource-manager-choices"></a>Custo de movimento do serviço para influenciar escolhas do Gerenciador de Recursos de Cluster
Um fator importante que o Gerenciador de Recursos de Cluster do Service Fabric considera ao tentar determinar quais alterações fazer em um cluster é o custo geral de chegar a essa solução. A noção de "custo" é trocada pelo valor do saldo que pode ser alcançado.

Mover réplicas ou instâncias de serviço custa, no mínimo, tempo de CPU e largura de banda de rede. Para serviços com estado, há também o custo da quantidade de espaço em disco e na memória necessária para criar uma cópia do estado antes de desligar réplicas antigas. Obviamente, seria desejável minimizar o custo de qualquer solução que o Gerenciador de Recursos de Cluster do Azure Service Fabric criar. Por outro lado, não seria bom ignorar soluções que melhorariam significativamente a alocação de recursos no cluster.

O Gerenciador de Recursos de Cluster tem duas maneiras de calcular custos e limitá-los, mesmo enquanto tenta gerenciar o cluster de acordo com seus outros objetivos. A primeira é que, ao planejar um novo layout para o cluster, o Gerenciador de Recursos de Cluster conta cada uma das movimentações que faria. Se duas soluções forem geradas com quase o mesmo saldo (pontuação), prefira aquela com o custo mais baixo (número total de movimentações).

Esta estratégia funciona bem. Mas, como ocorre com cargas estáticas ou padrão, é improvável em qualquer sistema complexo que todas as mudanças sejam iguais. É provável que algumas delas sejam muito mais caras.

## <a name="changing-a-replicas-move-cost-and-factors-to-consider"></a>Alterando o custo de movimento de uma réplica e fatores a serem considerados
Assim como na carga de relatório (outro recurso do Gerenciador de Recursos de Cluster), os serviços podem gerar relatórios automáticos dinamicamente para mostrar o quão oneroso é se mover a qualquer momento.

Código:

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

Um custo de movimentação padrão também pode ser especificado quando um serviço é criado.

O MoveCost tem quatro níveis: Zero, Baixo, Médio e Alto. Os Custos de Movimentação têm relação uns com os outros, exceto Zero. O custo de movimentação Zero significa que a movimentação é gratuita e não deve contar na pontuação da solução. Configurar o custo de movimentação para Alto *não* garante que a réplica permaneça em um só lugar.

<center>
![O custo de movimentação como um fator na seleção de réplicas para movimentação][Image1]
</center>

O MoveCost ajuda a encontrar as soluções que causam, em geral, o mínimo de interrupções e que sejam mais fáceis de conseguir enquanto ainda alcançam o equilíbrio equivalente. A noção de custo de um serviço pode ser relativa a muitas coisas. Os fatores mais comuns ao calcular o custo do movimento são:

* a quantidade de estados ou de dados que o serviço deve mover.
* o custo de desconexão de clientes. O custo de mover uma réplica primária normalmente é mais alto do que o custo de mover uma réplica secundária.
* o custo de interromper uma operação em andamento. Algumas operações no nível do armazenamento de dados ou operações realizadas em resposta a uma chamada de cliente são caras. Depois de um certo ponto, você não quer interrompê-las a não ser que seja necessário. Assim, enquanto a operação estiver em andamento, você aumenta o custo de movimentação desse objeto de serviço para reduzir a probabilidade de que ele se mova. Quando a operação estiver concluída, defina o custo de volta para normal.

## <a name="next-steps"></a>Próximas etapas
* O Gerenciador de Recursos de Cluster do Service Fabric usa métricas para gerenciar o consumo e a capacidade no cluster. Para saber mais sobre as métricas e como configurá-las, confira [Gerenciando o consumo e a carga de recursos no Service Fabric com métricas](service-fabric-cluster-resource-manager-metrics.md).
* Para saber como o Gerenciador de Recursos de Cluster gerencia e balanceia carga no cluster, confira [Balanceamento do cluster do Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png



<!--HONumber=Jan17_HO1-->


