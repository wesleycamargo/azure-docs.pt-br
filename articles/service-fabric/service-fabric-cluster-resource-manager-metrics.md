<properties
   pageTitle="Gerenciando métricas com o Gerenciador de Recursos de Cluster do Azure Service Fabric | Microsoft Azure"
   description="Saiba mais sobre como configurar e usar métricas no Service Fabric."
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

# Gerenciando o consumo e a carga de recursos no Service Fabric com métricas
As métricas são o termo genérico do Service Fabric para os recursos que são importantes para seus serviços. Em geral, uma métrica é tudo o que você deseja gerenciar em termos de recurso para lidar com o desempenho de seus serviços.

Em todos os exemplos acima, temos mencionado as métricas implicitamente. Termos como memória, disco, uso da CPU são exemplos de métricas. Elas são métricas físicas, recursos que correspondem aos recursos físicos no nó que precisam ser gerenciados. As métricas também podem ser lógicas, coisas como "MyWorkQueueDepth", que são definidas pelo aplicativo e que correspondem a algum nível de consumo de recursos (mas que o aplicativo realmente não conhece ou sabe como medi-los).

## Métricas padrão
Vamos supor que você queira apenas começar e não saiba quais recursos pretende consumir ou até mesmo quais seriam importantes. Portanto, você vai implementar e criar seus serviços sem especificar as métricas. Sem problemas! Vamos escolher algumas métricas para você. As métricas padrão que usaremos para você no momento se você não especificar os seus próprios são chamadas PrimaryCount, ReplicaCount e (um pouco vagamente, sabemos) Count. A tabela abaixo mostra o quanto de carga para cada uma dessas métricas controlamos por padrão:

| Métrica | Carga de Instância Sem Estado |	Carga Secundária com Estado |	Carga Primária com Estado |
|--------|--------------------------|-------------------------|-----------------------|
| PrimaryCount | 0 |	0 |	1 |
| ReplicaCount | 0 | 1 | 1 |
| Contagem |	1 |	1 |	1 |

OK. Com essas métricas padrão, o que você obtém? Bem, para cargas de trabalho básicas, você obtém uma boa distribuição de trabalho. No exemplo a seguir, vamos ver o que acontece quando criamos um serviço com estado com três partições e um tamanho de conjunto de réplicas de destino de três, além de um único serviço sem estado com uma contagem de três instâncias. Você obterá algo assim.

![Layout de cluster com métricas padrão][Image1]

Neste exemplo, vemos que
-	As réplicas primárias para o serviço com estado não ficam empilhadas em um único nó
-	As réplicas da mesma partição não estão no mesmo nó
-	O número total de primários e de secundários está bem distribuído no cluster
-	O número total de objetos de serviço (com e sem estado) é alocado uniformemente em cada nó

Muito bom!

Isso funciona muito bem até você começar a pensar sobre isso: qual é realmente a probabilidade de todos os primários dos meus serviços diferentes apresentarem a mesma carga agora? Além disso, qual é a chance da carga de um determinado serviço ficar constante ao longo do tempo? Na verdade, para qualquer carga de trabalho séria, ela é bem pequena.

Pensando de maneira realista, você pode executar com as métricas padrão ou pelo menos com métricas estáticas personalizadas, mas isso normalmente significa que o uso do seu cluster é mais baixo do que você gostaria (já que relatar não é adaptativo). Na pior das hipóteses, isso pode resultar em nós com muito agendamento, o que levaria a problemas de desempenho. Podemos melhorar com métricas personalizadas e relatórios de carga dinâmica. Isso nos leva às nossas próximas seções: métricas personalizadas e cargas dinâmicas.

## Métricas personalizadas
Já discutimos que pode haver métricas físicas e lógica e que as pessoas podem definir suas próprias métricas. Como elas fazem isso? É fácil! Basta configurar a métrica e o carregamento inicial padrão ao criar o serviço e pronto! Os conjunto de métricas e os valores padrão podem ser configurados com base em cada instância de serviço quando você estiver criando o serviço.

Observe que quando você começar a definir as métricas personalizadas, precisará adicioná-las explicitamente às métricas padrão se quiser usá-las para balancear seu serviço também. Isso ocorre porque queremos que você seja claro sobre a relação entre as métricas padrão e suas métricas personalizadas. Talvez nos preocupemos mais com o consumo de memória do que você com a distribuição de primários ou talvez você tenha várias métricas que deseja "misturar" com os padrões.

Digamos que você queira configurar um serviço que poderia informar uma métrica chamada "Memory" (além das métricas padrão). Para a memória, vamos dizer que você tomou algumas medidas básicas e sabe que normalmente uma réplica primária desse serviço usará 20 MB de memória e secundários do mesmo serviço ocuparão 5 MB. Você sabe que Memory é a métrica mais importante em termos de gerenciamento de desempenho desse serviço específico, mas ainda deseja que as réplicas principais fiquem balanceadas para que a perda de alguns nós ou domínios de falha não levem um número excessivo de réplicas primárias junto com eles. Fora isso, você usará os padrões.

Eis o que você deveria fazer:

Código:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription memoryMetric = new ServiceLoadMetricDescription();
memoryMetric.Name = "MemoryInMb";
memoryMetric.PrimaryDefaultLoad = 20;
memoryMetric.SecondaryDefaultLoad = 5;
memoryMetric.Weight = ServiceLoadMetricWeight.High;

ServiceLoadMetricDescription primaryCountMetric = new ServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

ServiceLoadMetricDescription replicaCountMetric = new ServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

ServiceLoadMetricDescription totalCountMetric = new ServiceLoadMetricDescription();
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

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

(Lembre-se: se você quiser usar as métricas padrão, não precisará sequer encostar na coleção de métricas).

Agora que mostramos a você como definir suas próprias métricas, vamos falar sobre as diferentes propriedades que as métricas podem ter. Já as mostramos a você, mas está na hora de falar sobre o que elas realmente significam! Há quatro propriedades diferentes que uma métrica pode ter hoje:

-	MetricName: esse é o nome da métrica. É um identificador exclusivo para a métrica do cluster da perspectiva do Gerenciador de Recursos.
-	PrimaryDefaultLoad: o valor padrão de carga que esse serviço irá exercer para a métrica como primário
-	SecondaryDefaultLoad: o valor padrão de carga que o serviço irá exercer para a métrica como uma réplica secundária
-	Weight: é quão importante a métrica é em relação a outras métricas configuradas para esse serviço.

## Carga
A carga é a noção geral de quanto uma determinada métrica é consumida por uma instância de serviço ou por uma réplica em um determinado nó.

## Carga padrão
A carga padrão é quanto de carga o Gerenciador de Recursos deve assumir que cada instância de serviço ou réplica do serviço consumirá até receber as atualizações das instâncias reais ou réplicas do serviço. Para os serviços mais simples, isso acaba sendo uma definição estática que nunca é atualizada dinamicamente e, portanto, será usada pelo tempo de vida do serviço. Isso funciona muito bem com um planejamento de capacidade simples porque é exatamente o que estamos acostumados a fazer, dedicar determinados recursos para determinadas cargas de trabalho, mas a vantagem é que pelo menos agora estamos operando com a mentalidade de microsserviços, em que os recursos não são realmente atribuídos estaticamente a cargas de trabalho específicas e em que não há pessoas no loop de tomada de decisão.

Permitimos que serviços com estado para especifiquem cargas padrão para seus Primários e Secundários. Pensando de forma realista, para vários serviços, esses números são diferentes por conta das cargas de trabalho diferentes executadas por réplicas primárias e secundárias, e já que as primárias servem tanto para leitura quanto para gravação (como a maioria da carga computacional), a carga padrão para uma réplica primária é mais alta do que para réplicas secundárias.

Mas agora vamos supor que você esteja executando o serviço por algum tempo e tenha notado que algumas instâncias ou réplicas do seu serviço consomem mais recursos que outras ou que seu consumo varia ao longo do tempo; talvez elas estejam associadas a um determinado cliente, talvez apenas correspondam às cargas de trabalho que variam ao longo do dia, como mensagens de tráfego ou transações. De qualquer forma, você nota que há um "número específico" que você pode usar para a carga sem representar um desvio muito grande. Você também observará um "desvio muito grande" na estimativa resulta na falta ou no excesso de alocação de recursos pelo Service Fabric para o seu serviço e, consequentemente, haverá nós sobrecarregados ou sem muito uso. O que fazer? Bem, o serviço podia estar relatando cargas dinamicamente!

## Carga dinâmica
Os relatórios de carga dinâmica permitem às réplicas ou instâncias ajustar suas alocações/uso relatado de métricas no cluster durante seu ciclo de vida. Uma réplica de serviço ou uma instância fria e sem trabalho normalmente informaria estar usando valores baixos de recursos, e as réplicas ou instâncias ocupadas indicariam estar usando mais recursos. Esse nível geral de variação do cluster permite reorganizar as réplicas de serviço e instâncias do cluster em tempo real para garantir que os serviços e as instâncias estão obtendo os recursos que exigem, no sentido de que os serviços ocupados são capazes de recuperar recursos de outras réplicas ou instâncias que estão atualmente frios ou com menos trabalho. O relatório em tempo real pode ser feito por meio do método ReportLoad, disponível em ServicePartition como uma propriedade no StatefulService base. Em seu serviço, o código ficaria assim:

Código:

```csharp
this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("Memory", 1234), new LoadMetric("Foo", 42) });
```

As réplicas ou instâncias de serviço somente podem relatar carga para as métricas que estão configuradas para uso. A lista de métricas é definida quando cada serviço é criado. Se uma réplica ou instância de serviço tenta relatar carga de uma métrica que não está atualmente configurada para uso, o Service Fabric registra o relatório em log, mas o ignora, o que significa que não vamos usá-lo durante o cálculo ou o relatório do estado do cluster. Isso é legal porque permite maior experimentação: o código pode medir e relatar tudo o que ele sabe, e o operador pode configurar, ajustar e atualizar as regras de balanceamento de recurso para o serviço em tempo real sem ter de alterar o código. Isso pode incluir, por exemplo, desabilitar uma métrica com um relatório com bug, reconfigurar os pesos de métricas com base no comportamento ou habilitar uma nova métrica somente depois que o código já foi implantado e validado.

## Combinando valores de carga padrão e relatórios de carga dinâmica
Faz sentido ter uma carga padrão especificada para um serviço que irá relatar carga dinamicamente? Claro que não! Nesse caso, a carga padrão serve como uma estimativa até que os relatórios reais comecem a aparecer na réplica ou instância de serviço real. Isso é ótimo, pois ele oferece ao Gerenciador de Recursos algo para trabalhar ao posicionar a réplica ou instância no momento da criação. A carga padrão acaba sendo uma estimativa inicial que permite que o Gerenciador de Recursos coloque as instâncias ou réplicas de serviço em bons locais desde o início. Se nenhuma informação foi fornecida, o posicionamento seria aleatório e muito provavelmente teríamos que mover as coisas assim que os relatórios de carga começassem a entrar.

Então, vamos pegar nosso exemplo anterior e ver o que acontece quando adicionamos uma carga personalizada e o serviço é atualizado dinamicamente depois de ser criado. Neste exemplo, vamos usar "Memory" como um exemplo e vamos assumir que criamos o serviço com estado usando o seguinte comando:

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Falamos sobre essa sintaxe anteriormente (MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad), mas falaremos mais sobre o que significa o valor específico de Weight mais tarde.

Vamos ver qual poderia ser a aparência de um layout de cluster:

![Cluster equilibrado com métricas padrão e personalizadas][Image2]

Algumas coisas que vale a pena observar:

-	Como as réplicas ou instâncias usam a carga padrão do serviço até relatar suas próprias cargas, sabemos que as réplicas dentro da partição 1 do serviço com estado ainda não relatou carga por conta própria
-	As réplicas secundárias dentro de uma partição podem ter sua própria carga
-	Em geral, as métricas parecem muito boas, com a diferença entre as cargas mínima e máxima em um nó (para memória, a métrica personalizada que seria a mais importante para nós) de 1,75 (o nó com o máximo de carga para a memória é N3, mínimo é N2 e 28/16 = 1,75): bem equilibrada!

Há algumas coisas que precisamos explicar

-	O que determinou se uma taxa de 1,75 é razoável ou não? Como sabemos que é bom o suficiente ou se há mais a melhorar?
-	Quando o balanceamento acontece?
-	O que significa que a memória foi ponderada como "Alta"?

## Pesos de métrica
Pesos de métrica são o que permite que dois serviços diferentes relatem a mesma métrica, mas exibam a importância diferente de balancear tal métrica. Por exemplo, considere um mecanismo analítico na memória e um banco de dados persistente: ambos provavelmente se importam com a métrica "Memory", mas o serviço na memória interna provavelmente não se preocupa muito com a métrica "Disk"; ela pode consumir um pouco dessa, mas não é essencial para o desempenho do serviço. A capacidade de controlar as mesmas métricas entre diferentes serviços é ótima, pois isso é o que permite que o Gerenciador de Recursos controle o consumo real no cluster, garanta que os nós não fiquem sobrecarregados, etc.

Os pesos de métrica permitem que o Gerenciador de Recursos tome decisões reais sobre como balancear cluster quando não há uma resposta perfeita (o que ocorre muito). As métricas podem ter quatro níveis de peso diferente: zero, baixa, média e alta. Uma métrica com um peso Zero não contribui em nada ao considerar se as coisas estão balanceadas ou não, mas sua carga ainda contribui para coisas como medida de capacidade.

O impacto real de pesos de métrica diferentes no cluster é o que vemos com disposições materialmente diferentes dos serviços, já que o Gerenciador de Recursos ouviu, ao todo, que algumas métricas são mais importantes que outras e que devem ter preferência no balanceamento ao entrar em conflito com outras métricas de menor prioridade.

Vamos dar uma olhada em um exemplo simples de alguns relatórios de carga para ver como os pesos de métrica diferentes podem resultar em alocações diferentes no cluster. Neste exemplo, podemos ver que alternar o peso relativo das métricas resulta na preferência do Gerenciador de Recursos por determinadas soluções criando diferentes disposições dos serviços.

![Exemplo de ponderação da métrica e seu impacto sobre as soluções de balanceamento][Image3]

Neste exemplo existem quatro serviços diferentes, todos relatando diferentes valores para métricas A e B diferentes. Em uma hipótese definida por todos os serviços, a Métrica A é a mais importante (Peso = Alto) e a Métrica B é relativamente sem importância (Peso = Baixo). Vamos que o Gerenciador de Recursos do Service Fabric posiciona os serviços de forma que a Métrica A fique mais equilibrada (com menos desvios) que a Métrica B. No segundo caso, revertemos os pesos e vemos que o Gerenciador de Recursos provavelmente trocaria os serviços A e B para apresentar uma alocação onde a Métrica B é mais equilibrada que a Métrica A.

### Pesos de métrica global
Se o Serviço A define a Métrica A como mais importantes e o Serviço B não se importa, qual é o peso real que o Gerenciador de Recursos acaba usando?

Há realmente dois pesos que podemos controlar para cada métrica: o peso do próprio serviço definido e o peso médio global entre todos os serviços que se importam com essa métrica. Nós usamos ambos ao calcularmos as pontuações das soluções que geramos, já que é importante garantir que um serviço esteja equilibrado com relação às suas próprias prioridades, mas também que o cluster como um todo esteja alocado corretamente.

O que aconteceria se não nos importássemos com equilíbrio global e local? Bem, é comum construir soluções globalmente equilibradas, mas que resultam em equilíbrio e alocação de recursos ruins no caso de serviços individuais. No exemplo a seguir vamos considerar as métricas padrão configuradas para um serviço com estado, PrimaryCount, ReplicaCount e TotalCount, e ver o que acontece quando consideramos apenas o equilíbrio global:

![O impacto de uma única solução global][Image4]

No exemplo superior, em que só vimos o equilíbrio global, o cluster como um todo, de fato, está balanceado: todos os nós têm a mesma contagem de primárias e réplicas totais, que ótimo! As coisas estão equilibradas. Entretanto, se você examinar o impacto real dessa alocação, isso não é tão bom: a perda de qualquer nó afeta uma determinada carga de trabalho desproporcionalmente, já que leva todos os seus primários. Suponhamos, por exemplo, que fôssemos perder o primeiro nó. Se isso acontecesse, os três primários das três partições diferentes do serviço Círculo seriam perdidos ao mesmo tempo. Por outro lado, os outros dois serviços (Triângulo e Hexágono) perdem uma réplica em suas partições, o que não causa interrupção (além de precisar recuperar a réplica inoperante).

No exemplo inferior, distribuímos as réplicas com base no balanceamento global e por serviço. Ao calcular a pontuação da solução, colocamos a maioria do peso na solução global, mas uma parte (configurável) serve para garantir que os serviços estejam equilibrados neles mesmos tanto quanto possível. Como resultado, se perdermos o primeiro nó, poderemos ver que a perda da primários (e secundários) é distribuída por todas as partições de todos os serviços e o impacto de cada um é o mesmo.

Considerando os pesos de métrica, o balanceamento global é calculado com base na média dos pesos de métrica. Podemos balancear um serviço em relação a seus próprios pesos de métrica definidos.

## Próximas etapas
- Para saber mais sobre outras opções disponíveis para a configuração de serviços, confira o tópico sobre outras configurações disponíveis do Gerenciador de Recursos de Cluster em [Saiba mais sobre a configuração de Serviços](service-fabric-cluster-resource-manager-configure-services.md)
- Definir as Métricas de Desfragmentação é uma forma de consolidar a carga em nós, em vez de distribuí-la. Para saber como configurar a desfragmentação, leia [este artigo](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- Para descobrir como o Gerenciador de Recursos de Cluster gerencia e balanceia carga no cluster, confira o artigo sobre [como balancear carga](service-fabric-cluster-resource-manager-balancing.md).
- Comece do princípio e [veja uma introdução ao Gerenciador de Recursos de Cluster do Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
- O Custo de Movimento é uma forma de sinalizar para o Gerenciador de Recursos de Cluster que a movimentação de determinados serviços é mais cara do que para outros. Para saber mais sobre o custo de movimento, veja [este artigo](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]: ./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png

<!---HONumber=AcomDC_0316_2016-->