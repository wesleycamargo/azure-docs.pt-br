---
title: "Limitação no Resource Manager de Cluster do Service Fabric | Microsoft Docs"
description: Aprenda a configurar os limitadores fornecidos pelo Resource Manager de Cluster do Service Fabric.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 4a44678b-a5aa-4d30-958f-dc4332ebfb63
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 8a8419497bda3f1df523d6aff28028548abc155a


---

# <a name="throttling-the-behavior-of-the-service-fabric-cluster-resource-manager"></a>Limitando o comportamento do Gerenciador de Recursos do cluster do Service Fabric
Mesmo se você tiver configurado o Gerenciador de Recursos do Cluster corretamente, o cluster poderá ser interrompido. Por exemplo, poderia haver nós simultâneos ou falhas do tipo domínio de falha – o que aconteceria se isso ocorresse durante uma atualização? O Gerenciador de Recursos de Cluster tenta corrigir tudo, mas isso pode introduzir variação no cluster. As restrições ajudam a construir uma barreira para que o cluster possa usar recursos para se estabilizar — os nós voltam, as partições de rede são corrigidas, bits corrigidos são implantados.

Para ajudar com esses tipos de situação, o Gerenciador de Recursos de Cluster do Service Fabric inclui várias restrições. Essas restrições são exigências razoavelmente grandes. O padrão dessas configurações não deve ser alterado, a menos que seja feito um cálculo em torno do volume de trabalho que o cluster pode fazer paralelamente.

As restrições têm valores padrão que a equipe do Service Fabric descobriu por experiência que são padrões aceitáveis. Se você precisar alterá-los, será preciso ajustá-los para sua carga real esperada. Você pode descobrir que precisa ter algumas restrições definidas, mesmo que isso signifique que o cluster levará mais tempo para ser estabilizado em situações importantes.

## <a name="configuring-the-throttles"></a>Configurando os limitadores
As restrições incluídas por padrão são:

* GlobalMovementThrottleThreshold – essa configuração controla o número total de movimentações no cluster ao longo de um período (definido como GlobalMovementThrottleCountingInterval, com o valor em segundos)
* MovementPerPartitionThrottleThreshold – essa configuração controla o número total de movimentações de qualquer partição de serviço ao longo de um período (o MovementPerPartitionThrottleCountingInterval, com o valor em segundos)

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

via ClusterConfig.json para implantações autônomas ou Template.json para clusters hospedados pelo Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThreshold",
          "value": "1000"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      },
      {
          "name": "MovementPerPartitionThrottleThreshold",
          "value": "50"
      },
      {
          "name": "MovementPerPartitionThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

Na maioria das vezes em que vimos os clientes usarem essas restrições, eles já estavam em um ambiente restrito por recurso. Alguns exemplos desse ambiente seriam a largura de banda de rede limitada em nós individuais ou discos que não podem criar muitas réplicas paralelamente devido às limitações da taxa de transferência. Esses tipos de restrição significavam que as operações disparadas em resposta às falhas não seriam bem-sucedidas ou seriam lentas, mesmo sem as restrições. Nessas situações, os clientes sabiam que estavam estendendo o tempo que levaria para o cluster chegar a um estado estável. Os clientes também entendiam que poderiam acabar executando com uma confiabilidade geral menor enquanto estivessem restritos.

## <a name="next-steps"></a>Próximas etapas
* Para descobrir como o Gerenciador de Recursos de Cluster gerencia e balanceia carga no cluster, confira o artigo sobre [como balancear carga](service-fabric-cluster-resource-manager-balancing.md)
* O Gerenciador de Recursos de Cluster tem muitas opções para descrever o cluster. Para saber mais sobre elas, confira este artigo sobre a [descrição de um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)



<!--HONumber=Jan17_HO1-->


