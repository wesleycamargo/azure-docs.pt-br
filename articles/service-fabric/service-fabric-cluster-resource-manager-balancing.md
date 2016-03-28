<properties
   pageTitle="Balanceando o cluster com o Resource Manager de Cluster do Azure Service Fabric | Microsoft Azure"
   description="Uma introdução ao balanceamento de cluster com o Gerenciador de Recursos de Cluster do Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/10/2016"
   ms.author="masnider"/>

# Balanceamento do cluster do Service Fabric
O Gerenciador de Recursos de Cluster do Service Fabric permite relatar a carga dinâmica, reagir a alterações no cluster e gerar planos para balanceamento, mas quando ele faz essas coisas? O que realmente dispara o rebalanceamento no cluster quando os serviços são solicitados de acordo com os valores de carga padrão durante a criação deles? Há vários controles relacionados a isso.

O primeiro conjunto de controles que regulam o balanceamento de um conjunto de temporizadores que determinam a frequência com que o Gerenciador de Recursos de Cluster examina o estado do cluster para encontrar o que precisa ser resolvido. Esses temporizadores estão relacionados aos diferentes estágios do trabalho que sempre é executado. Estes são:

1.	Posicionamento – esse estágio lida com o posicionamento de réplicas com estado ou de instâncias sem estado que estejam ausentes. Isso abrange os novos serviços e a manipulação de réplicas ou de instâncias que falharam e precisam ser recriadas. A exclusão e o descarte de réplicas ou de instâncias também são tratados aqui.
2.	Verificações de Restrição – esse estágio verifica e corrige violações das restrições (regras) de posicionamento no sistema. Os exemplos de regras são a garantia de que os nós não estão acima da capacidade e se as restrições de posicionamento do serviço (mais sobre isso posteriormente) estão sendo atendidas.
3.	Balanceamento – esse estágio verifica se o rebalanceamento proativo é necessário com base no nível de equilíbrio configurado desejado entre métricas diferentes e, nesse caso, tenta encontrar uma organização no cluster que seja mais balanceada.

## Configuração de etapas e temporizadores do Resource Manager do cluster
Cada um desses estágios é controlado por um temporizador diferente que regula sua frequência. Por exemplo, se você deseja lidar com o posicionamento de novas cargas de trabalho de serviço no cluster a cada hora (para colocá-las em lotes), mas quer uma verificação de balanceamento regular de poucos em poucos segundos, poderá impor essa regra. Quando cada temporizador é acionado, um sinalizador é definido dizendo que precisamos lidar com essa parte das funções do Gerenciador de Recursos, e é captado na próxima varredura geral feita pela máquina de estado (é por isso que essas configurações são definidas como "intervalos mínimos"). Por padrão, o Gerenciador de Recursos examina seu estado e aplica atualizações a cada décimo de segundo, define os sinalizadores de posicionamento e verificação de restrição a cada segundo e o balanceamento de sinalizador a cada cinco segundos.

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

Hoje, só executaremos sequencialmente uma dessas ações de cada vez. Isso serve para que já tenhamos respondido a solicitações de criação de novas réplicas, por exemplo, antes de passar para o balanceamento do cluster. Como você pode ver pelos intervalos de tempo padrão especificados, podemos examinar e verificar se há coisas que precisamos fazer com muita frequência, o que significa que cada conjunto de alterações é geralmente menor (não varremos horas de alterações no cluster tentando corrigi-los ao mesmo tempo; podemos tratar das coisas conforme vão aparecendo). Isso torna o gerenciador de recursos do Service Fabric muito ágil para lidar com o que acontece no cluster.

Fundamentalmente, o Gerenciador de Recursos de Cluster também precisa saber quando considerar o cluster desequilibrado e quais réplicas devem ser movidas para resolver o problema. Para isso, temos duas outras partes principais da configuração: os Limites de Balanceamento e os Limites de Atividade.

## Limites de balanceamento
Um Limite de Balanceamento é o controle principal que dispara o rebalanceamento proativo. O Limite de Balanceamento define quão desequilibrado o cluster precisa estar em relação a uma métrica específica para o Gerenciador de Recursos considerá-lo desequilibrado e disparar o balanceamento na execução seguinte. Os Limites de Balanceamento são definidos baseados em cada métrica, como parte do manifesto do cluster:

``` xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

O Limite de Balanceamento para uma métrica é uma razão. Se a quantidade de carga no nó mais carregado dividido pela quantidade de carga no nó menos carregado exceder essa razão, o cluster será considerado desequilibrado e balanceamento será disparado durante a próxima execução do nó de estado do Gerenciador de Recursos.

![Exemplo de Limite de Balanceamento][Image1]

Neste exemplo simples, cada serviço está consumindo apenas uma unidade de alguma métrica. No exemplo superior, a carga máxima em um nó é 5 e o mínimo é 2. Digamos que o Limite de Balanceamento para esta métrica seja 3. Portanto, no exemplo superior, o cluster é considerado balanceado e nenhum balanceamento será disparado. No exemplo inferior, a carga máxima em um nó é 10, enquanto a mínima é 2, colocando-nos acima do Limite de Balanceamento projetado de 3. Como resultado, a carga certamente será distribuída para o Node3 quando o Gerenciador de Recursos puder ser executado. Observe que como não estamos usando uma abordagem agressiva, alguma coisa também poderia ir para o Node2, o que resultaria na minimização das diferenças gerais entre os nós.

![Ações de exemplo de Limite de Balanceamento][Image2]

Observe que ficar abaixo do Limite de Balanceamento não é um objetivo explícito; os Limites de Balanceamento são apenas o gatilho.

## Limites de atividade
Às vezes, os nós estão relativamente desequilibrados, e a quantidade total de carga no cluster é baixa. Isso pode ser devido à hora do dia ou porque o cluster é novo e está apenas começando a ser inicializado. Em ambos os casos, não é ideal gastar tempo com balanceamento porque há realmente muito pouca vantagem: você vai gastar recursos de computação e rede para mover as coisas. Há outro controle dentro do Gerenciador de Recursos, conhecido como limite de atividade, que permite que você especifique um limite inferior absoluto para a atividade. Se nenhum nó tiver pelo menos essa quantidade de carga, o balanceamento não será disparado mesmo se o Limite de Balanceamento for atingido. Como exemplo, digamos que temos relatórios com os totais para consumo a seguir nos nós. Vamos supor também que o Limite de Balanceamento seja mantido em 3, mas agora também temos um limite de atividade de 1536. No primeiro caso, embora o cluster esteja desequilibrado pelo limite de balanceamento, nenhum nó atende ao limite mínimo de atividade e, portanto, tudo fica como está. No exemplo inferior, o Node1 está bem acima do Limite de Atividade e, portanto, o balanceamento será executado.

![Exemplo de limite de atividade][Image3]

Assim como os Limites de Balanceamento, os limites de atividade são definidos por métrica por meio do manifesto do cluster:

``` xml
      <Section Name="MetricActivityThresholds">
        <Parameter Name="Memory" Value="1536"/>
      </Section>
```

## Balanceamento dos serviços em conjunto
Algo interessante de se observar é que a decisão sobre o cluster estar desequilibrado ou não é uma decisão em todo o cluster, mas a maneira que usamos para corrigi-lo é movendo instâncias e réplicas de serviço individuais. Isso faz sentido, certo? Se a memória é empilhada em um nó, várias réplicas ou instâncias podem contribuir para ele. Portanto, isso poderia mover todas as réplicas ou instâncias que usam a métrica afetada e desequilibrada.

No entanto, de vez em quando um cliente ligará ou apresentará um tíquete dizendo que um serviço que não estava desequilibrado foi movido. Como isso poderia acontecer, se todas as métricas do serviço estavam balanceadas, mesmo perfeitamente, na hora do outro desequilíbrio? Vejamos!

Veja, por exemplo, quatro serviços, S1, S2, S3 e S4. S1 reporta as métricas M1 e M2, S2 reporta M2 e M3, S3 reporta M3 e M4 e S4 reporta algumas métricas M99. Certamente, você consegue ver onde queremos chegar. Temos uma cadeia! Da perspectiva do Gerenciador de Recursos, realmente não há quatro serviços independentes. Temos um monte de serviços que estão relacionados (S1, S2 e S3) e um que está por conta própria.

![Balanceamento dos Serviços em Conjunto][Image4]

Assim, é possível que um desequilíbrio em Metric1 possa mover réplicas ou instâncias pertencentes ao Service3. Geralmente, esses movimentos são muito limitados, mas podem ser maiores dependendo exatamente de quão desequilibrado ficou a Metric1 e quais alterações foram necessárias para que o cluster pudesse corrigi-la. Também podemos dizer com certeza que um desequilíbrio nas Métricas 1, 2 ou 3 nunca criará movimentação no Service4; não faria sentido, já que a movimentação de réplicas ou instâncias do Service4 não faria nada para afetar o equilíbrio das Métricas 1, 2 ou 3.

O Gerenciador de Recursos detecta automaticamente os serviços relacionados sempre que é executado, já que os serviços podem ter sido adicionados, removidos, ou tido suas configurações de métrica alteradas, por exemplo, entre duas execuções de balanceamento Service2, elas podem ter sido reconfiguradas para remover a Metric2. Isso interromperá a cadeia entre Service1 e Service2. Agora, em vez de dois grupos de serviços, você tem três:

![Balanceamento dos Serviços em Conjunto][Image5]

## Próximas etapas
- As métricas são como o Gerenciador de Recursos de Cluster do Service Fabric gerencia o consumo e a capacidade no cluster. Para saber mais sobre elas e como configurá-las, confira [este artigo](service-fabric-cluster-resource-manager-metrics.md)
- O Custo de Movimento é uma forma de sinalizar para o Gerenciador de Recursos de Cluster que a movimentação de determinados serviços é mais cara do que para outros. Para saber mais sobre o custo de movimento, veja [este artigo](service-fabric-cluster-resource-manager-movement-cost.md)
- O Resource Manager do Cluster tem várias limitações que você pode configurar para diminuir a variação no cluster. Normalmente, eles não são necessários, mas você poderá aprender mais sobre eles [aqui](service-fabric-cluster-resource-manager-advanced-throttling.md) caso eles sejam necessários


[Image1]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png

<!---HONumber=AcomDC_0316_2016-->