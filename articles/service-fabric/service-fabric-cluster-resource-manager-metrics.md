---
title: "Gerenciar a carga de microsserviço do Azure usando métricas | Microsoft Docs"
description: "Saiba mais sobre como configurar e usar métricas no Service Fabric para gerenciar o consumo de recursos de serviço."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 0d622ea6-a7c7-4bef-886b-06e6b85a97fb
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: c0e5418574920495bf277f4127b97ff5b07b56ef


---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Gerenciando o consumo e a carga de recursos no Service Fabric com métricas
As métricas são o termo genérico do Service Fabric para os recursos que são importantes para seus serviços e que são fornecidas pelo nós no cluster. Em geral, uma métrica é qualquer item que você deseja gerenciar para lidar com o desempenho dos serviços.

Itens como Memória, Disco e Uso de CPU são exemplos de métricas. Elas são métricas físicas, recursos que correspondem aos recursos físicos no nó que precisam ser gerenciados. As métricas também podem ser (e normalmente são) métricas lógicas. Métricas lógicas são itens como "MyWorkQueueDepth", "MessagesToProcess" ou "TotalRecords". As métricas lógicas são definidos pelo aplicativo e correspondem a um consumo de recursos físicos, mas o aplicativo realmente não sabe como medi-lo. Isso é comum. A maioria das métricas usadas são métricas lógicas. Em geral, isso ocorre porque, atualmente, muitos serviços são escritos em código gerenciado. O código gerenciado significa que, de dentro de um processo de host, pode ser difícil medir e relatar o consumo de recursos físicos de um único objeto de serviço. A complexidade de medir e relatar suas próprias métricas também é o motivo pelo qual fornecermos algumas métricas padrão prontas.

## <a name="default-metrics"></a>Métricas padrão
Vamos supor que você queira começar e não saiba quais recursos pretende consumir ou até mesmo quais seriam importantes. Portanto, você vai implementar e criar seus serviços sem especificar as métricas. Sem problemas! O Gerenciador de Recursos de Cluster do Service Fabric seleciona algumas medidas simples para você. As métricas padrão que usamos hoje quando você não especifica nada são chamadas PrimaryCount, ReplicaCount e Count. A tabela a seguir mostra o quanto de carga para cada uma dessas métricas associadas com cada objeto de serviço:

| Métrica | Carga de Instância Sem Estado | Carga Secundária com Estado | Carga Primária com Estado |
| --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |
| ReplicaCount |0 |1 |1 |
| Contagem |1 |1 |1 |

OK. Com essas métricas padrão, o que você obtém? Bem, para cargas de trabalho básicas, você obtém uma boa distribuição de trabalho. No exemplo a seguir, vamos ver o que acontece quando criamos dois serviços. O primeiro é um serviço com estado com três partições e um tamanho de conjunto de réplicas de destino de três. O segundo é um serviço sem estado com uma partição e uma contagem de instâncias de três:

<center>
![Layout de cluster com métricas padrão][Image1]
</center>

Veja o acontece:

* As réplicas primárias para o serviço com estado não ficam empilhadas em um único nó
* As réplicas da mesma partição não estão no mesmo nó
* O número total de primários e de secundários é distribuído no cluster
* O número total de objetos de serviço é alocado uniformemente em cada nó

Ótimo!

Isso funciona muito bem até que você comece a executar grandes números de cargas de trabalho reais: qual é a probabilidade de que o esquema de particionamento que você escolheu resulte na utilização perfeitamente uniforme por todas as partições? Qual é a chance de que a carga de determinado serviço seja constante ao longo do tempo ou até mesmo agora?

Na verdade, você pode usar apenas as métricas padrão, mas isso normalmente significa que a utilização do cluster é menor do que o desejado. Isso ocorre porque os relatórios de métricas padrão não são adaptáveis e presumem que tudo seja equivalente. Na pior das hipóteses, usar apenas os padrões também pode resultar em nós com agendamento excessivo, causando problemas de desempenho. Podemos melhorar com métricas personalizadas e relatórios de carga dinâmica, dos quais falaremos a seguir. Para qualquer carga de trabalho séria, as chances de que todos os serviços sejam equivalentes para sempre é baixa. Se você está interessado em obter o máximo de seu cluster e evitar problemas de desempenho, convém começar a analisar as métricas personalizadas.

## <a name="custom-metrics"></a>Métricas personalizadas
As métricas são configuradas por instância de serviço nomeado ao criar o serviço.

Qualquer métrica tem algumas propriedades que a descrevem: um nome, uma carga padrão e um peso.

* Nome da Métrica: o nome da métrica. O nome da métrica é um identificador exclusivo para a métrica do cluster da perspectiva do Gerenciador de Recursos.
* Default Load: A carga padrão é representada de maneira diferente dependendo se o serviço está com ou sem estado.
  * Para serviços sem estado cada métrica tem uma propriedade única chamada DeafultLoad
  * Para serviços com estado, você define:
    * PrimaryDefaultLoad: o valor padrão dessa métrica que esse serviço consome quando é primário
    * SecondaryDefaultLoad: o valor padrão dessa métrica que esse serviço consome quando é secundário
* Peso: o peso da métrica define o quanto ela é importante em relação as outras métricas para esse serviço.

Se definir métricas personalizadas e também desejar usar as métricas padrão, você precisará adicioná-las explicitamente. Isso ocorre porque você deseja definir a relação entre as métricas padrão e as métricas personalizadas. Por exemplo, talvez você se preocupe com o consumo de memória ou WorkQueueDepth muito mais do que a distribuição do primário.

### <a name="defining-metrics-for-your-service---an-example"></a>Definindo métricas para seu serviço - um exemplo
Digamos que você queira configurar um serviço que relata uma métrica chamada "MemoryInMb" e também queira usar as métricas padrão. Digamos também que você tenha feito algumas medições e saiba que uma réplica primária desse serviço requer 20 unidades de "MemoryInMb", enquanto as secundárias exigem até 5. Você sabe que a memória é a métrica mais importante em termos de gerenciamento de desempenho desse serviço específico, mas ainda deseja que as réplicas principais sejam balanceadas. O balanceamento de primárias é uma boa ideia, para que a perda de um nó ou uma falha no domínio não afete também a maioria das réplicas primárias. Além desses ajustes, você deseja usar as métricas padrão.

Aqui está o código que você escreveria para criar um serviço com essa configuração de métrica:

Código:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
StatefulServiceLoadMetricDescription memoryMetric = new StatefulServiceLoadMetricDescription();
memoryMetric.Name = "MemoryInMb";
memoryMetric.PrimaryDefaultLoad = 20;
memoryMetric.SecondaryDefaultLoad = 5;
memoryMetric.Weight = ServiceLoadMetricWeight.High;

StatefulServiceLoadMetricDescription primaryCountMetric = new StatefulServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

StatefulServiceLoadMetricDescription replicaCountMetric = new StatefulServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

StatefulServiceLoadMetricDescription totalCountMetric = new StatefulServiceLoadMetricDescription();
totalCountMetric.Name = "Count";
totalCountMetric.PrimaryDefaultLoad = 1;
totalCountMetric.SecondaryDefaultLoad = 1;
totalCountMetric.Weight = ServiceLoadMetricWeight.Low;

serviceDescription.Metrics.Add(memoryMetric);
serviceDescription.Metrics.Add(primaryCountMetric);
serviceDescription.Metrics.Add(replicaCountMetric);
serviceDescription.Metrics.Add(totalCountMetric);

await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

(Lembre-se: se você quiser usar as métricas padrão, não precisará sequer encostar na coleção de métricas ou fazer algo especial ao criar o seu serviço)

Agora que vimos uma introdução e um exemplo, vamos analisar cada uma dessas configurações mais detalhadamente e falar sobre o comportamento que você obterá.

## <a name="load"></a>Carregar
O objetivo da definição de métricas é representar alguma carga. "Carga" é a quantidade de determinada métrica consumida por alguma instância de serviço ou réplica em determinado nó. A carga esperada pode ser configurada quando um serviço é criado, atualizada depois que o serviço é criado, relatada por objeto de serviço ou todos os itens acima.

## <a name="default-load"></a>Carga padrão
A carga padrão é a carga que cada objeto de serviço (instância ou réplica) desse serviço consome por padrão. Para os serviços mais simples, a carga padrão é uma definição estática que nunca é atualizada e que é usada pelo tempo de vida do serviço. A carga padrão funciona muito bem para cenários de planejamento de capacidade simples em que certos valores de recursos são dedicados para diferentes cargas de trabalho.

O Gerenciador de Recursos de Cluster permite que os serviços com monitoração de estado especifiquem uma carga padrão diferente para primários e secundários, enquanto os serviços sem monitoração de estado só podem especificar um valor. Para serviços com monitoração de estado, a carga padrão para réplicas primárias e secundárias normalmente é diferente, pois as réplicas realizam diferentes tipos de trabalho em cada função. Por exemplo, as primárias normalmente lidam com leituras e gravações (e a maior parte da carga computacional), enquanto as secundárias não o fazem. É esperado que a carga padrão de uma réplica primária seja maior do que para réplicas secundárias, mas os números reais dependem de suas próprias medições.

## <a name="dynamic-load"></a>Carga dinâmica
Digamos que você esteja executando o serviço há algum tempo. Com o monitoramento, você observou que:

1. Algumas partições ou instâncias de determinado serviço consomem mais recursos do que outras
2. Alguns serviços têm carga que varia ao longo do tempo.

Há muitos itens que podem causar esses tipos de flutuações de carga. O serviço ou a partição pode ser associado a um cliente específico ou talvez corresponda a cargas de trabalho que variam ao longo do dia. Independentemente do motivo, há um número único que você pode usar para a carga padrão. Qualquer valor que você escolher para a carga padrão estará errado durante parte do tempo. Isso é um problema, pois cargas padrão incorretas fazem com que o Gerenciador de Recursos de Cluster aloque recursos a mais ou a menos para o serviço. Como resultado, você tem nós com utilização acima ou abaixo do esperado, mesmo que o Gerenciador de Recursos de Cluster pense que o cluster está equilibrado. As cargas padrão ainda são válidas, pois fornecem algumas informações, mas não dão a visão completa das cargas de trabalho reais, na maioria das vezes. Isso ocorre porque o Gerenciador de Recursos de Cluster permite que cada objeto de serviço atualize sua própria carga durante o tempo de execução. Isso é chamado de relatório dinâmico de carga.

Relatórios de carga dinâmicos permitem que réplicas ou instâncias ajustem sua carga de métricas de alocação/relatadas durante o ciclo de vida. Uma réplica de serviço ou uma instância inoperante e que não realizasse trabalho normalmente informaria que estava usando valores baixos de determinada métrica. Uma réplica ou instância ocupada relataria que está usando mais.

Os relatórios por réplica ou instância permitem que o Gerenciador de Recursos de Cluster reorganize os objetos de serviço individuais no cluster para garantir que os serviços obtenham os recursos de que precisam. Serviços ocupados efetivamente podem "recuperar" recursos de outras réplicas ou instâncias que estão atualmente inoperantes ou executando menos trabalho.

No Serviço Confiável, o código para relatar a carga dinamicamente teria esta aparência:

Código:

```csharp
this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
```

Réplicas de serviço ou instâncias só podem relatar cargas para as métricas que foram configuradas para usar ao serem criadas. A lista de métricas que um serviço pode relatar é o mesmo conjunto especificado quando o serviço é criado. A lista de métricas associadas ao serviço também pode ser atualizada dinamicamente. Se uma réplica de serviço ou uma instância tenta relatar a carga para uma métrica que ela não está configurada atualmente para usar, o Service Fabric registra esse relatório, mas o ignora. Se houver outras métricas relatadas na mesma chamada de API que sejam válidas, esses relatórios serão aceitos e usados. Isso é ótimo, pois permite maior experimentação. O código pode medir e relatar todas as métricas que conhece, e o operador pode especificar e atualizar a configuração de métrica para o serviço sem precisar alterar o código. Por exemplo, o administrador ou a equipe de operações pode desabilitar uma métrica com um relatório com bugs para determinado serviço, reconfigurar os pesos de métricas com base no comportamento ou habilitar uma nova métrica somente depois que o código já tiver sido implantado e validado por meio de outros mecanismos.

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Combinando valores de carga padrão e relatórios de carga dinâmica
Se a carga padrão não é suficiente e é recomendável relatar carga dinâmica, ambos podem ser usados? Sim! De fato, essa é a configuração recomendada. Quando a carga padrão é definida e os relatórios de carga dinâmicos são utilizados, a carga padrão serve como uma estimativa até os relatórios dinâmicos serem gerados. Isso é bom porque fornece ao Gerenciador de Recursos de Cluster algo com o qual trabalhar. A carga padrão permite que o Gerenciador de Recursos de Cluster coloque os objetos de serviço em locais adequados quando eles são criados. Se nenhuma informação de carga padrão for fornecida, o posicionamento dos serviços será aleatório. Quando relatórios de carga são fornecidos posteriormente, o Gerenciador de Recursos de Cluster quase sempre precisa mover serviços.

Vamos conferir nosso exemplo anterior e ver o que acontece quando adicionamos algumas métricas personalizadas e relatórios dinâmicos de carga. Neste exemplo, usamos "Memória" como uma métrica de exemplo. Vamos pressupor que tenhamos criado inicialmente o serviço com estado com o seguinte comando:

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Como lembrete, essa sintaxe é ("MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

Vamos ver qual poderia ser a aparência de um layout de cluster:

<center>
![Cluster equilibrado com métricas padrão e personalizadas][Image2]
</center>

Algumas coisas que vale a pena observar:

* Como as réplicas ou instâncias usam a carga padrão do serviço até relatar suas próprias cargas, sabemos que as réplicas dentro da partição 1 do serviço com estado ainda não relatou carga de forma dinâmica
* As réplicas secundárias dentro de uma partição podem ter sua própria carga
* Em geral, as métricas parecem equilibradas. Para a memória, a proporção entre a carga máxima e a mínima é de 1,75 (o nó com o máximo de carga é N3, o que tem o mínimo é N2 e 28/16 = 1,75).

Há alguns itens que precisamos explicar:

* O que determinou se uma taxa de 1,75 é razoável ou não? Como o Gerenciador de Recursos de Cluster sabe se isso é bom o suficiente ou se há mais trabalho a fazer?
* Quando o balanceamento acontece?
* O que significa que a memória foi ponderada como "Alta"?

## <a name="metric-weights"></a>Pesos de métrica
É importante ser capaz de controlar as mesmas métricas em serviços diferentes. Essa exibição é o que permite que o Gerenciador de Recursos de Cluster rastreie o consumo do cluster, equilibre o consumo entre os nós e garanta que os nós não ultrapassem a capacidade. No entanto, os serviços podem ter diferentes opiniões sobre a importância da mesma métrica. Além disso, em um cluster com várias métricas e muitos serviços, talvez não existam soluções perfeitamente equilibradas para todas as métricas. Como o Gerenciador de Recursos de Cluster deve lidar com essas situações?

Os pesos das métricas permitem que o Gerenciador de Recursos de Cluster decida como equilibrar o cluster quando não houver uma resposta perfeita. Os pesos das métricas também permitem que o Gerenciador de Recursos de Cluster equilibre serviços específicos de maneira diferente. As métricas podem ter quatro níveis de peso diferente: zero, baixa, média e alta. Uma métrica com um peso Zero não contribui em nada ao considerar se as coisas estão balanceadas ou não, mas sua carga ainda contribui para itens como capacidade.

O impacto real de pesos de métricas diferentes no cluster é que o Gerenciador de Recursos de Cluster gera diferentes soluções. Os pesos das métricas informam ao Gerenciador de Recursos de Cluster que determinadas métricas são mais importantes do que outras. Quando não há uma solução perfeita, o Gerenciador de Recursos de Cluster pode preferir soluções que equilibrem melhor as métricas ponderadas mais elevadas. Se um serviço achar que uma métrica não é importante, ele poderá considerar seu uso dessa métrica desequilibrado. Isso permite que outro serviço obtenha uma distribuição uniforme que é importante para ele.

Vamos conferir um exemplo de alguns relatórios de carga para ver como os pesos de métrica diferentes podem resultar em alocações diferentes no cluster. Neste exemplo, podemos ver que alternar o peso relativo das métricas resulta na preferência do Gerenciador de Recursos de Clustert por diferentes soluções e na criação de diferentes disposições dos serviços.

<center>
![Exemplo de ponderação da métrica e seu impacto sobre as soluções de balanceamento][Image3]
</center>

Neste exemplo, há quatro serviços diferentes e todos relatam valores diferentes para duas métricas diferentes, A e B. Em um caso, todos os serviços definem a Métrica A como a mais importante (Peso = Alto) e a Métrica B como sem importância (Peso = Baixo). Nesse caso, vemos que o Gerenciador de Recursos de Cluster dispõe os serviços para que a Métrica A seja mais equilibrada (com um desvio padrão inferior) do que a Métrica B. No segundo caso, invertemos os pesos de métrica. Como resultado, o Gerenciador de Recursos de Cluster provavelmente trocaria os serviços A e B para propor uma alocação em que a Métrica B seja mais equilibrada do que a Métrica A.

### <a name="global-metric-weights"></a>Pesos de métrica global
Se o Serviço A define a Métrica A como mais importante e o Serviço B não se importa com isso, qual é o peso real que acaba sendo usado?

Na verdade, há vários pesos que são rastreados para cada métrica. O primeiro conjunto são os pesos que cada serviço definiu para a métrica. O outro peso é um peso global, que é a média de todos os serviços que relatam essa métrica. O Gerenciador de Recursos de Cluster usa os dois desses pesos ao calcular as pontuações das soluções. Isso ocorre porque é importante que um serviço seja balanceado de acordo com suas próprias prioridades, mas também que o cluster como um todo seja alocado corretamente.

O que aconteceria se o Gerenciador de Recursos de Cluster não se importasse com o balanceamento global e local? Bem, é trivial criar soluções que são balanceadas globalmente, mas que resultam em alocações de recursos inadequadas para serviços individuais. No exemplo a seguir, vamos examinar as métricas padrão que um serviço com estado configurou e ver o que acontece quando apenas o balanceamento global é considerado:

<center>
![O impacto de uma única solução global][Image4]
</center>

No exemplo superior, em que só vimos o balanceamento global, o cluster como um todo é realmente balanceado. Todos os nós têm a mesma contagem de primárias e o mesmo número total de réplicas. Entretanto, se você examinar o impacto real dessa alocação, isso não é tão bom: a perda de qualquer nó afeta uma determinada carga de trabalho desproporcionalmente, já que leva todos seus primários. Por exemplo, se o primeiro nó falhasse, as três primárias para as três partições diferentes do serviço Círculo seriam todas perdidas. Por outro lado, os outros dois serviços (Triângulo e Hexágono) perdem uma réplica em suas partições, o que não causa interrupção (além de precisar recuperar a réplica inoperante).

No exemplo inferior, o Gerenciador de Recursos de Cluster distribuiu as réplicas com base no balanceamento global e por serviço. Ao calcular a pontuação da solução, ele fornece a maioria do peso para a solução global e uma parte (configurável) para serviços individuais. O balanceamento global é calculado com base na média dos pesos das métricas definidos para cada um dos serviços. Cada serviço é balanceado de acordo com seus próprios pesos de métricas definidos. Isso garante que os serviços sejam balanceados em si mesmos de acordo com suas próprias necessidades, tanto quanto possível. Como resultado, se o mesmo primeiro nó falhar, a perda das primárias (e secundárias) será distribuída entre todas as partições de todos os serviços. O impacto para cada um é igual.

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre outras opções disponíveis para a configuração de serviços, confira o tópico sobre outras configurações disponíveis do Gerenciador de Recursos de Cluster em [Saiba mais sobre a configuração de serviços](service-fabric-cluster-resource-manager-configure-services.md)
* Definir as Métricas de Desfragmentação é uma forma de consolidar a carga em nós, em vez de distribuí-la. Para saber como configurar a desfragmentação, leia [este artigo](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* Para descobrir como o Gerenciador de Recursos de Cluster gerencia e balanceia carga no cluster, confira o artigo sobre [como balancear carga](service-fabric-cluster-resource-manager-balancing.md)
* Comece do princípio e [veja uma introdução ao Resource Manager de Cluster do Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
* O Custo de Movimento é uma forma de sinalizar para o Gerenciador de Recursos de Cluster que a movimentação de determinados serviços é mais cara do que para outros. Para saber mais sobre o custo de movimento, consulte [este artigo](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png



<!--HONumber=Jan17_HO4-->


