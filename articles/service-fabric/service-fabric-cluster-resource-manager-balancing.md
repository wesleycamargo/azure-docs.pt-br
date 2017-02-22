---
title: Equilibrar seu cluster do Azure Service Fabric | Microsoft Docs
description: "Uma introdução ao balanceamento de cluster com o Gerenciador de Recursos de Cluster do Service Fabric."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 030b1465-6616-4c0b-8bc7-24ed47d054c0
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: ee77a01b2bb17ab70099e891e00255d1cec676f6


---
# <a name="balancing-your-service-fabric-cluster"></a>Balanceamento do cluster do Service Fabric
O Gerenciador de Recursos de Cluster do Service Fabric oferece suporte a alterações de carga dinâmico, reagindo a inclusões ou remoções de nós ou serviços, corrigindo violações de restrição e rebalanceando o cluster. Mas com que frequência ele faz tudo isso e que o aciona?

O primeiro conjunto de controles de balanceamento são um conjunto de temporizadores. Esses temporizadores determinam a frequência com que o Gerenciador de Recursos de Cluster examina o estado do cluster para encontrar o que precisa ser resolvido. Há três categorias diferentes de trabalho, cada uma com seu próprio temporizador correspondente. Eles são:

1. Posicionamento – esse estágio lida com o posicionamento de réplicas com estado ou de instâncias sem estado que estejam ausentes. Isso abrange os novos serviços e a manipulação de réplicas com estado ou de instâncias sem estado que falharam. A exclusão e o descarte de réplicas ou de instâncias são tratados aqui.
2. Verificações de Restrição – esse estágio verifica e corrige violações das restrições (regras) de posicionamento no sistema. Os exemplos de regras são a garantia de que os nós não estão acima da capacidade e se as restrições de posicionamento do serviço estão sendo atendidas.
3. Balanceamento – neste estágio verifica para ver se o rebalanceamento proativo é necessário com base no nível desejado de saldo para métricas diferentes configurado. Nesse caso, ele tenta localizar uma organização em cluster que é mais equilibrado.

## <a name="configuring-cluster-resource-manager-steps-and-timers"></a>Configuração de etapas e temporizadores do Resource Manager do cluster
Cada um desses tipos diferentes de correções que o Gerenciador de Recursos de Cluster pode fazer é controlado por um temporizador diferente que rege sua frequência. Quando cada temporizador é acionado, a tarefa é agendada. Por padrão, o Resource Manager:

* verifica o estado e aplica atualizações (como gravação de um nó estiver inativo) cada 1/10 de segundo
* Define o posicionamento e a restrição check sinalizadores por segundo
* Define o sinalizador de balanceamento a cada cinco segundos.

Podemos ver isso refletidas nas informações de configuração a seguir:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

via ClusterConfig.json para implantações Autônomas ou Template.json para clusters hospedados pelo Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PLBRefreshGap",
          "value": "0.10"
      },
      {
          "name": "MinPlacementInterval",
          "value": "1.0"
      },
      {
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

Atualmente o Resource Manager de Cluster executa apenas uma dessas ações ao mesmo tempo, sequencialmente (é por isso que fazemos referência a esses temporizadores como "intervalos mínimos"). Por exemplo, o Resource Manager de Cluster se encarrega de solicitações pendentes para criar serviços antes do cluster de balanceamento. Como você pode ver, os intervalos de tempo padrão especificados, o Resource Manager de Cluster examina e verifica se há qualquer coisa que precisa ser feito com frequência, portanto o conjunto de alterações feitas no final de cada etapa geralmente é pequeno. Fazer pequenas alterações frequentemente faz o Resource Manager de Cluster responde às coisas que acontecem no cluster. Os temporizadores padrão oferecem algum lote, já que muitos dos mesmos tipos de eventos tendem a ocorrer simultaneamente. Por padrão o Resource Manager de Cluster não é uma varredura horas de alterações no cluster e tentando resolver todas as alterações ao mesmo tempo. Isso levaria a picos de variação.

O Resource Manager de Cluster também precisa de algumas informações adicionais para determinar se o cluster desequilibrado. Para isso, temos duas outras configurações: os *Limites de Balanceamento* e os *Limites de Atividade*.

## <a name="balancing-thresholds"></a>Limites de balanceamento
Um Limite de Balanceamento é o controle principal que dispara o rebalanceamento proativo. O temporizador MinLoadBalancingInterval é apenas para a frequência com que o Resource Manager de Cluster deve verificar - não significa que nada acontece. O Limite de Balanceamento define o quão desequilibrado o cluster precisa estar em relação a uma métrica específica para o Gerenciador de Recursos de Cluster considerá-lo desequilibrado e disparar o balanceamento.

Os Limites de Balanceamento são definidos baseados em cada métrica, como parte da definição do cluster. Para saber mais sobre como fazer isso, leia [este artigo](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

via ClusterConfig.json para implantações Autônomas ou Template.json para clusters hospedados pelo Azure:

```json
"fabricSettings": [
  {
    "name": "MetricBalancingThresholds",
    "parameters": [
      {
          "name": "MetricName1",
          "value": "2"
      },
      {
          "name": "MetricName2",
          "value": "3.5"
      }
    ]
  }
]
```

O Limite de Balanceamento para uma métrica é uma razão. Se a quantidade de carga no nó mais carregado dividido pela quantidade de carga no nó menos carregado excede esse número, o cluster é considerado desequilibrado. Como resultado de balanceamento é disparada na próxima vez que o Resource Manager de Cluster verifica.

<center>
![Exemplo de Limite de Balanceamento][Image1]
</center>

Neste exemplo, cada serviço está consumindo uma unidade de alguma métrica. No exemplo superior, a carga máxima em um nó é cinco e o mínimo é dois. Digamos que o limite de balanceamento para esta métrica seja três. Como a proporção do cluster é 5/2 = 2,5 e é menor do que o especificado balanceamento de limite de três, o cluster é equilibrado. Nenhum balanceamento é disparada quando verifica se o Resource Manager de Cluster.

No exemplo inferior, a carga máxima em um nó é dez, enquanto o mínimo é dois, resultando em uma taxa de cinco. Cinco é maior que o limite de balanceamento designado de três para métrica. Como resultado, uma execução de rebalanceamento será agendada na próxima vez em que o temporizador de balanceamento for acionado. Em uma situação como essa alguma carga certamente será distribuída para Node3. Como o Gerenciador de Recursos de Cluster do Service Fabric não usa uma abordagem greedy, alguma carga também pode ser distribuída para Node2. Que fazer isso resulta na minimização das diferenças gerais entre nós, é um dos objetivos do Resource Manager de Cluster.

<center>
![Ações de exemplo de Limite de Balanceamento][Image2]
</center>

Obtendo abaixo do limite de balanceamento não é um objetivo explícito. Limites de balanceamento são apenas um *gatilho* que informa o Resource Manager de Cluster que deve ser o cluster para determinar quais melhorias pode fazer, se houver. Na verdade, só porque uma pesquisa balanceamento é inicializada não significa nada move - às vezes, o cluster está desequilibrado mas a situação não pode ser melhorada.

## <a name="activity-thresholds"></a>Limites de Atividade
Às vezes, embora os nós estejam relativamente desequilibrados, a quantidade *total* de carga no cluster é baixa. A falta de carga pode ser um dip transitório, ou porque o cluster é novo e está apenas começando a ser inicializado. Em ambos os casos, talvez não queira gastar tempo o cluster de balanceamento porque não há muito a ser obtido. Se o cluster sofreu balanceamento, você gastar rede e computação recursos para mover as coisas sem fazer quaisquer *absoluto* diferença. Para evitar isso, há outro controle conhecido como limites de atividade. Limites de atividade permite que você especifique algum limite inferior absoluto para a atividade. Se nenhum nó está acima desse limite, balanceamento não é disparado mesmo se o limite de balanceamento é atingido.

Por exemplo, vamos examinar o diagrama a seguir. Vamos supor também que o Limite de Balanceamento seja mantido em três para essa métrica, mas agora também temos um Limite de Atividade de 1536. No primeiro caso, embora o cluster esteja desequilibrado pelo Limite de Balanceamento, nenhum nó atende ao Limite de Atividade e, portanto, nada acontece. No exemplo inferior, Node1 é maneira acima do limite de atividade. Uma vez que o limite de balanceamento e o limite de atividade para a métrica são excedidos, portanto balanceamento é agendado.

<center>
![Exemplo de limite de atividade][Image3]
</center>

Assim como os Limites de Balanceamento, os Limites de Atividade são definidos por métrica por meio da definição do cluster:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

via ClusterConfig.json para implantações Autônomas ou Template.json para clusters hospedados pelo Azure:

```json
"fabricSettings": [
  {
    "name": "MetricActivityThresholds",
    "parameters": [
      {
          "name": "Memory",
          "value": "1536"
      }
    ]
  }
]
```

Limites de balanceamento e atividade estão ambos vinculados a uma métrica específica - balanceamento é disparado somente se o limite de balanceamento e atividade limite é excedido para a mesma métrica.

## <a name="balancing-services-together"></a>Balanceamento dos serviços em conjunto
Algo que é interessante observar é que, se o cluster está desequilibrado ou não é uma decisão de todo o cluster. No entanto, a maneira como nós vamos corrigi-lo é mover réplicas de serviço individuais e instâncias. Isso faz sentido, certo? Se a memória é empilhada em um nó, várias réplicas ou instâncias podem contribuir para ela. Corrigir o desequilíbrio pode exigir a movimentação de qualquer uma das réplicas com monitoração de estado ou instâncias sem monitoração de estado que usam a métrica desequilibrada.

Ocasionalmente, no entanto, um serviço que não era desequilibrado foi movido. Como um serviço pode ser movido se todas as métricas do serviço estavam balanceadas, mesmo perfeitamente, no momento do outro desequilíbrio? Vejamos!

Vamos usar como exemplo quatro serviços, Service1, Service2, Service3 e Service4. O Service1 reporta em relação às métricas Metric1 e Metric2, o Service2 em relação às métricas Metric2 e Mmetric3, o Service3 em relação às métricas Metric3 e Metric4 e o Service4 em relação à métrica Metric99. Certamente, você consegue ver onde queremos chegar. Temos uma cadeia! Realmente não há quatro serviços independentes. Temos um monte de serviços que estão relacionados (Service1, Service2 e Service3) e um que está por conta própria.

<center>
![Balanceamento dos serviços em conjunto][Image4]
</center>

Assim, é possível que um desequilíbrio em Metric1 possa mover réplicas ou instâncias pertencentes ao Service3 (que não relatam Metric1). Geralmente, esses movimentos são limitados, mas podem ser maiores dependendo exatamente de quão desequilibrado ficou a Metric1 e quais alterações foram necessárias no cluster para corrigi-la. Também podemos dizer com certeza que um desequilíbrio nas métricas de 1, 2 ou 3 não pode causar movimentos no Serviço4. Não haveria nenhum ponto desde movendo as réplicas ou instâncias pertencentes a Serviço4 ao redor podem fazer absolutamente nada para afetar o saldo das métricas de 1, 2 ou 3.

O Resource Manager de Cluster detecta automaticamente quais serviços estão relacionados, como os serviços podem ter sido adicionados, removidos ou tinham as alterações de configuração de métrica. Por exemplo, entre duas execuções de balanceamento Service2 podem ter sido reconfiguradas para remover Metric2. Essa alteração interromperá a cadeia entre Service1 e Service2. Agora, em vez de dois grupos de serviços relacionados, você tem três:

<center>
![Balanceamento dos serviços em conjunto][Image5]
</center>

## <a name="next-steps"></a>Próximas etapas
* As métricas são como o Gerenciador de Recursos de Cluster do Service Fabric gerencia o consumo e a capacidade no cluster. Para saber mais sobre eles e como configurá-los, confira [este artigo](service-fabric-cluster-resource-manager-metrics.md)
* O Custo de Movimento é uma forma de sinalizar para o Gerenciador de Recursos de Cluster que a movimentação de determinados serviços é mais cara do que para outros. Para saber mais sobre o custo de movimento, consulte [este artigo](service-fabric-cluster-resource-manager-movement-cost.md)
* O Resource Manager do Cluster tem várias limitações que você pode configurar para diminuir a variação no cluster. Normalmente, eles não são necessários, mas você poderá aprender mais sobre eles [aqui](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png



<!--HONumber=Jan17_HO4-->


