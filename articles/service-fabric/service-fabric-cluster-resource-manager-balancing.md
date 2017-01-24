---
title: Balanceando o cluster com o Resource Manager de Cluster do Azure Service Fabric | Microsoft Docs
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
ms.date: 08/19/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: d278e0125d698306366713a6e11957dad2de10c5


---
# <a name="balancing-your-service-fabric-cluster"></a>Balanceamento do cluster do Service Fabric
O Gerenciador de Recursos de Cluster do Service Fabric permite relatar a carga dinâmica, reagir a alterações no cluster, corrigir violações de restrição e fazer o rebalanceamento do cluster, se necessário. Mas com que frequência ele faz tudo isso e que o aciona? Há vários controles relacionados a isso.

O primeiro conjunto de controles de balanceamento são um conjunto de temporizadores. Esses temporizadores determinam a frequência com que o Gerenciador de Recursos de Cluster examina o estado do cluster para encontrar o que precisa ser resolvido. Há três categorias diferentes de trabalho, cada uma com seu próprio temporizador correspondente. Eles são:

1. Posicionamento – esse estágio lida com o posicionamento de réplicas com estado ou de instâncias sem estado que estejam ausentes. Isso abrange os novos serviços e a manipulação de réplicas com estado ou de instâncias sem estado que falharam e precisam ser recriadas. A exclusão e o descarte de réplicas ou de instâncias também são tratados aqui.
2. Verificações de Restrição – esse estágio verifica e corrige violações das restrições (regras) de posicionamento no sistema. Os exemplos de regras são a garantia de que os nós não estão acima da capacidade e se as restrições de posicionamento do serviço (mais sobre isso posteriormente) estão sendo atendidas.
3. Balanceamento – esse estágio verifica se o rebalanceamento proativo é necessário com base no nível de equilíbrio configurado desejado entre métricas diferentes e, nesse caso, tenta encontrar uma organização no cluster que seja mais balanceada.

## <a name="configuring-cluster-resource-manager-steps-and-timers"></a>Configuração de etapas e temporizadores do Resource Manager do cluster
Cada um desses tipos diferentes de correções que o Gerenciador de Recursos de Cluster pode fazer é controlado por um temporizador diferente que rege sua frequência. Por exemplo, se você deseja lidar com o posicionamento de novas cargas de trabalho de serviço no cluster a cada hora (para colocá-las em lotes), mas quer uma verificação de balanceamento regular de poucos em poucos segundos, você pode configurar esse comportamento. Quando cada temporizador é acionado, a tarefa é agendada. Por padrão, o Gerenciador de Recursos examina seu estado e aplica atualizações (enviando em lote todas as alterações que ocorreram desde a última verificação como, por exemplo, perceber que um nó está inativo) a cada décimo de segundo, define os sinalizadores de posicionamento e verificação de restrição a cada segundo e o balanceamento de sinalizador a cada cinco segundos.

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

Hoje executamos somente uma destas ações de cada vez, sequencialmente (é por isso que nos referimos a essas configurações como "intervalos mínimos")). Isso serve para que já tenhamos respondido a solicitações pendentes de criação de novas réplicas, por exemplo, antes de passar para o balanceamento do cluster. Como você pode ver pelos intervalos de tempo padrão especificados, podemos examinar e verificar se há coisas que precisamos fazer com muita frequência, o que significa que o conjunto de alterações que fazemos no final de cada etapa é geralmente menor; não varremos horas de alterações no cluster tentando corrigi-los ao mesmo tempo; podemos tratar das coisas mais ou menos conforme vão aparecendo, mas em alguns lotes muitas coisas ocorrem ao mesmo tempo. Isso torna o gerenciador de recursos do Service Fabric muito ágil para lidar com o que acontece no cluster.

Enquanto a maioria dessas tarefas são simples (se houver violações de restrição, corrija as mesmas, se houver serviços a serem criados, crie os mesmos), o Gerenciador de Recursos de Cluster também precisa de algumas informações adicionais para determinar se o cluster está desequilibrado. Para isso, temos duas outras configurações: os *Limites de Balanceamento* e os *Limites de Atividade*.

## <a name="balancing-thresholds"></a>Limites de balanceamento
Um Limite de Balanceamento é o controle principal para disparar o rebalanceamento proativo (lembre-se de que o temporizador é apenas para a frequência com a qual o Gerenciador de Recursos de Cluster deve fazer a verificação, não significa que nada vai acontecer). O Limite de Balanceamento define o quão desequilibrado o cluster precisa estar em relação a uma métrica específica para o Gerenciador de Recursos de Cluster considerá-lo desequilibrado e disparar o balanceamento.

Os Limites de Balanceamento são definidos baseados em cada métrica, como parte da definição do cluster. Para obter informações sobre como fazer isso, leia [este artigo](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

``` xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

O Limite de Balanceamento para uma métrica é uma razão. Se a quantidade de carga no nó mais carregado dividido pela quantidade de carga no nó menos carregado exceder esse número, o cluster será considerado desequilibrado e o balanceamento será disparado durante a próxima verificação do Gerenciador de Recursos de Cluster (por padrão, a cada cinco segundos, conforme regido pelo MinLoadBalancingInterval, mostrado acima).

![Exemplo de Limite de Balanceamento][Image1]

Neste exemplo simples, cada serviço está consumindo uma unidade de alguma métrica. No exemplo superior, a carga máxima em um nó é 5 e o mínimo é 2. Digamos que o Limite de Balanceamento para esta métrica seja 3. Portanto, no exemplo superior, o cluster é considerado balanceado e nenhum balanceamento será disparado durante a verificação do Gerenciador de Recursos do Cluster (visto que a proporção do cluster é 5/2 = 2,5 e é menor do que o limite especificado de balanceamento de 3).

No exemplo inferior, a carga máxima em um nó é 10, enquanto a mínima é 2, resultando em uma proporção de 5. Isso coloca o cluster acima do limite de balanceamento projetado de 3 para aquela métrica. Como resultado, uma execução de rebalanceamento global será agendada na próxima vez em que o temporizador de balanceamento for acionado. Observe que só porque uma pesquisa de balanceamento é inicializada não significa que algo irá acontecer, às vezes, o cluster está desequilibrado, mas a situação não pode ser melhorada, porém em uma situação como essa (pelo menos por padrão) alguma quantidade da carga certamente será distribuída para o Node3. Observe que como não estamos usando uma abordagem agressiva, alguma carga também poderia ser distribuída para o Node2, o que resultaria na minimização das diferenças gerais entre os nós, mas seria esperado que a maior parte da carga fluiria para o Node3.

![Ações de exemplo de Limite de Balanceamento][Image2]

Observe que ficar abaixo do limite de equilíbrio não é um objetivo explícito, Balanceamento de Limites é apenas um *gatilho* que informa o Gerenciador de Recursos do Cluster do Service Fabric que ele deve ser o cluster para determinar quais melhorias ele pode realizar, se necessário.

## <a name="activity-thresholds"></a>Limites de Atividade
Às vezes, embora os nós estejam relativamente desequilibrados, a quantidade *total* de carga no cluster é baixa. Isso pode ser devido à hora do dia ou porque o cluster é novo e está apenas começando a ser inicializado. Em ambos os casos, não é ideal gastar tempo com o balanceamento do cluster porque há realmente muito pouca vantagem: você vai gastar recursos de computação e rede para mover as coisas, sem que haja nenhuma diferença absoluta. Como queremos evitar fazer isso, há outro controle dentro do Gerenciador de Recursos, conhecido como Limites de Atividade, que permite que você especifique um limite inferior absoluto para a atividade. Se nenhum nó tiver pelo menos essa quantidade de carga, o balanceamento não será disparado mesmo se o Limite de Balanceamento for atingido.

Como exemplo, digamos que temos relatórios com os totais para consumo a seguir nos nós. Vamos supor também que o Limite de Balanceamento seja mantido em 3 para essa métrica, mas agora também temos um Limite de Atividade de 1536. No primeiro caso, embora o cluster esteja desequilibrado pelo Limite de Balanceamento, nenhum nó atende ao Limite de Atividade mínimo e, portanto, tudo fica como está. No exemplo abaixo, o Node1 está bem acima do Limite de Atividade, portanto o balanceamento será executado (pois tanto o Limite de Balanceamento como o Limite de Atividade para a métrica foram excedidos)

![Exemplo de limite de atividade][Image3]

Assim como os Limites de Balanceamento, os Limites de Atividade são definidos por métrica por meio da definição do cluster:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

Observe que os limites de balanceamento e atividade estão ambos vinculados à métrica. O balanceamento só será acionado se os limites de balanceamento e atividade forem excedidos para a mesma métrica. Assim, se excedermos o Limite de Balanceamento de Memória e o Limite de Atividade de CPU, o balanceamento não disparará desde que os limites restantes (Limite de Balanceamento de CPU e Limite de Atividade de Memória) não sejam excedidos.

## <a name="balancing-services-together"></a>Balanceamento dos serviços em conjunto
Algo interessante de se observar é que a decisão sobre o cluster estar desequilibrado ou não é uma decisão em todo o cluster, mas a maneira que usamos para corrigi-lo é movendo instâncias e réplicas de serviço individuais. Isso faz sentido, certo? Se a memória é empilhada em um nó, várias réplicas ou instâncias podem contribuir para isso, portanto, isso poderia exigir mover qualquer uma das réplicas com estado ou instâncias sem estado que usam a métrica afetada e desequilibrada.

No entanto, de vez em quando um cliente ligará ou apresentará um tíquete dizendo que um serviço que não estava desequilibrado foi movido. Como um serviço pode ser movido se todas as métricas do serviço estavam balanceadas, mesmo perfeitamente, no momento do outro desequilíbrio? Vejamos!

Vamos usar como exemplo quatro serviços, Service1, Service2, Service3 e Service4. O Service1 reporta em relação às métricas Metric1 e Metric2, o Service2 em relação às métricas Metric2 e Mmetric3, o Service3 em relação às métricas Metric3 e Metric4 e o Service4 em relação à métrica Metric99. Certamente, você consegue ver onde queremos chegar. Temos uma cadeia! Da perspectiva do Gerenciador de Recursos do Cluster, realmente não há quatro serviços independentes. Temos um monte de serviços que estão relacionados (Service1, Service2 e Service3) e um que está por conta própria.

![Balanceamento dos serviços em conjunto][Image4]

Assim, é possível que um desequilíbrio em Metric1 possa mover réplicas ou instâncias pertencentes ao Service3. Geralmente, esses movimentos são muito limitados, mas podem ser maiores dependendo exatamente de quão desequilibrado ficou a Metric1 e quais alterações foram necessárias para que o cluster pudesse corrigi-la. Também podemos dizer com certeza que um desequilíbrio nas Métricas 1, 2 ou 3 nunca criará  movimentação no Service4; não faria sentido, já que a movimentação de réplicas ou instâncias do Service4 não faria nada para afetar o equilíbrio das Métricas 1, 2 ou 3.

O Gerenciador de Recursos de Cluster detecta automaticamente os serviços relacionados, já que os serviços podem ter sido adicionados, removidos, ou tido suas configurações de métrica alteradas, por exemplo, entre duas execuções de balanceamento Service2, elas podem ter sido reconfiguradas para remover a Metric2. Isso interromperá a cadeia entre Service1 e Service2. Agora, em vez de dois grupos de serviços, você tem três:

![Balanceamento dos serviços em conjunto][Image5]

## <a name="next-steps"></a>Próximas etapas
* As métricas são como o Gerenciador de Recursos de Cluster do Service Fabric gerencia o consumo e a capacidade no cluster. Para saber mais sobre eles e como configurá-los, confira [este artigo](service-fabric-cluster-resource-manager-metrics.md)
* O Custo de Movimento é uma forma de sinalizar para o Gerenciador de Recursos de Cluster que a movimentação de determinados serviços é mais cara do que para outros. Para saber mais sobre o custo de movimento, consulte [este artigo](service-fabric-cluster-resource-manager-movement-cost.md)
* O Resource Manager do Cluster tem várias limitações que você pode configurar para diminuir a variação no cluster. Normalmente, eles não são necessários, mas você poderá aprender mais sobre eles [aqui](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png



<!--HONumber=Dec16_HO2-->


