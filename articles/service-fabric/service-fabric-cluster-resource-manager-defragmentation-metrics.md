---
title: "Desfragmentação de métricas no Azure Service Fabric | Microsoft Docs"
description: "Uma visão geral do uso de desfragmentação ou como uma estratégia de métricas no Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: e5ebfae5-c8f7-4d6c-9173-3e22a9730552
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 5ef6381f7d182c818171eca3e3d32a00bc30268e


---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Desfragmentação de métricas e carga no Service Fabric
O Gerenciador de Recursos de Cluster do Service Fabric está basicamente relacionado ao balanceamento em termos de distribuição da carga, garantindo que os nós no cluster sejam igualmente utilizados. A distribuição das cargas de trabalho é o layout mais seguro em termos de sobrevivência a falhas, uma vez que isso garante que uma falha não destruirá uma grande porcentagem de uma determinada carga de trabalho. O Resource Manager do Service Fabric Cluster também dá suporte a uma estratégia diferente, que é a desfragmentação. Em geral, a desfragmentação significa que, em vez de tentar distribuir a utilização de uma métrica em todo o cluster, devemos, de fato, tentar consolidá-la. A consolidação é uma inversão favorável da nossa estratégia normal. Em vez de minimizar o desvio médio padrão da carga da métrica, o Gerenciador de Recursos de Cluster procura aumentos no desvio. Mas por que você usaria essa estratégia?

Bem, caso tenha distribuído a carga de maneira uniforme entre os nós no cluster, você já terá consumido alguns dos recursos que os nós têm a oferecer. No entanto, algumas cargas de trabalho criam serviços que são excepcionalmente grandes e consomem a maior parte de um nó. Nesses casos, é possível que 75% a 95% dos recursos de um nó acabem dedicados a um único objeto de serviço. As cargas de trabalho grandes não são um problema. O Gerenciador de Recursos de Cluster determina, no momento da criação do serviço, que precisa reorganizar o cluster para liberar espaço para essa grande carga de trabalho. No entanto, nesse intervalo, essa carga de trabalho precisa esperar para ser agendada no cluster.

Se houver muitos serviços e estados, pode ser que demore um longo tempo para que uma carga de trabalho grande seja colocada no cluster. A probabilidade disso acontecer será maior se outras cargas de trabalho no cluster forem grandes e, portanto, demorarem mais para se mover. A equipe do Service Fabric mediu o tempo de criação em simulações desse cenário. Descobrimos que, se os serviços fossem grandes o suficiente e o cluster fosse altamente utilizado, a criação desses serviços grandes seria mais lenta. Para lidar com essa situação, introduzimos a desfragmentação como uma estratégia de balanceamento. Descobrimos que, para grandes cargas de trabalho, especialmente aquelas em que o tempo de criação era importante, a desfragmentação realmente ajudava no agendamento dessas novas cargas de trabalho no cluster.

Você pode configurar as métricas de desfragmentação para que o Gerenciador de Recursos de Cluster tente, de modo proativo, condensar a carga dos serviços em menos nós. Isso ajuda a garantir que (quase) sempre haja espaço até mesmo para os serviços grandes. Assim, tais serviços podem ser criados rapidamente quando necessário.

A maioria das pessoas não precisa da desfragmentação. Normalmente, os serviços devem ser pequenos e, portanto, não deve ser difícil encontrar espaço para eles no cluster. No entanto, se você tiver grandes serviços e precisar que eles sejam criados rapidamente (e estejam dispostos a aceitar as desvantagens), a estratégia de desfragmentação é ideal para você.

Mas quais são as desvantagens? Basicamente, a desfragmentação pode aumentar o impacto das falhas (já que mais serviços estão em execução no nó que falha). Além disso, a desfragmentação garante que alguns recursos do cluster sejam inutilizados enquanto aguardam que as cargas de trabalho sejam agendadas.

O diagrama a seguir fornece uma representação visual de dois clusters diferentes, um deles desfragmentado e o outro não. No caso do cluster balanceado, considere o número de movimentações que seria necessário para colocar um dos maiores objetos de serviço. Compare com o cluster desfragmentado, onde a carga de trabalho grande pode ser colocada imediatamente nos nós quatro ou cinco.

<center>
![Comparando clusters balanceados e desfragmentados][Image1]
</center>

## <a name="defragmentation-pros-and-cons"></a>Prós e contras da desfragmentação
Quais são as outras compensações conceituais? Recomendamos fazer uma medição completa das cargas de trabalho antes de ativar as métricas de desfragmentação. Aqui está uma tabela rápida de itens a ser considerados:

| Vantagens da desfragmentação | Desvantagens da desfragmentação |
| --- | --- |
| Agiliza a criação de serviços grandes |Concentra a carga em menos nós, aumentando a contenção |
| Permite a redução da movimentação de dados durante a criação |Falhas podem afetar mais serviços e causar mais variação |
| Permite a descrição detalhada dos requisitos e a recuperação de espaço |Configuração geral mais complexa do Gerenciamento de Recursos |

Você pode misturar métricas desfragmentadas e normais no mesmo cluster. O Gerenciador de Recursos de Cluster tenta consolidar o maior número de métricas de desfragmentação enquanto distribui as outras. Se não houver serviços que compartilhem essas métricas, os resultados podem ser bons. Os resultados exatos dependerão do número de métricas de balanceamento em comparação com o número de métricas de desfragmentação, de quanto elas se sobrepõem, de seus pesos, das cargas atuais, entre outros fatores. O experimento é necessário para determinar a configuração exata necessária.

## <a name="configuring-defragmentation-metrics"></a>Configurando métricas de desfragmentação
Configurar métricas de desfragmentação é uma decisão global no cluster, e métricas individuais podem ser selecionadas para desfragmentação:

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Disk" Value="true" />
    <Parameter Name="CPU" Value="false" />
</Section>
```

via ClusterConfig.json para implantações autônomas ou Template.json para clusters hospedados pelo Azure:

```json
"fabricSettings": [
  {
    "name": "DefragmentationMetrics",
    "parameters": [
      {
          "name": "Disk",
          "value": "true"
      },
      {
          "name": "CPU",
          "value": "false"
      }
    ]
  }
]
```


## <a name="next-steps"></a>Próximas etapas
* O Gerenciador de Recursos de Cluster tem muitas opções para descrever o cluster. Para saber mais sobre elas, confira este artigo sobre a [descrição de um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
* As métricas são como o Gerenciador de Recursos de Cluster do Service Fabric gerencia o consumo e a capacidade no cluster. Para saber mais sobre elas e como configurá-las, confira [este artigo](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png



<!--HONumber=Jan17_HO1-->


