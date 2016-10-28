<properties
   pageTitle="Gerenciador de Recursos de Cluster do Service Fabric – Grupos de Aplicativos | Microsoft Azure"
   description="Visão geral da funcionalidade de Grupo de Aplicativos no Gerenciador de Recursos de Cluster do Service Fabric"
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
   ms.date="08/19/2016"
   ms.author="masnider"/>

# Introdução aos grupos de aplicativos
Gerenciador de Recursos de Cluster da malha de serviço geralmente gerencia os recursos de cluster, distribuindo a carga (representada por meio de Métricas) uniformemente em todo o cluster. O Service Fabric também gerencia a capacidade de nós no cluster e o cluster como um todo por meio da noção da capacidade. Isso funciona muito bem para muitos tipos diferentes de cargas de trabalho, mas os padrões que fazem uso intenso de Instâncias de Aplicativos Service Fabric diferentes às vezes trazem requisitos adicionais. Alguns dos requisitos adicionais típicos são:

- Habilidade de reservar capacidade para os serviços de uma Instância de Aplicativo em algum número de nós
- Habilidade de limitar o número total de nós que um determinado conjunto de serviços dentro de um aplicativo pode executar
- Definindo as capacidades na instância do aplicativo em si para limitar o número ou o consumo de recursos total dos serviços dentro dele

Para atender a esses requisitos, desenvolvemos o suporte para o que chamamos de Grupos de Aplicativos.

## Gerenciando a capacidade do Aplicativo
Capacidade do aplicativo pode ser usada para limitar o número de nós estendidos por um aplicativo, bem como a carga de métrica total daquelas instâncias de aplicativos em nós individuais. Ele também pode ser usado para reservar recursos no cluster para o aplicativo.

A capacidade do aplicativo pode ser configurada para novos aplicativos quando eles são criados; também pode ser atualizada para aplicativos existentes criados sem especificar a capacidade de Aplicativo.

### Limitando o número máximo de nós
O caso de uso mais simples para capacidade do Aplicativo é quando uma instanciação de aplicativo precisa ser limitada a um determinado número máximo de nós. Se nenhuma Capacidade de Aplicativo for especificada, o Gerenciador de Recursos de Cluster do Service Fabric instanciará réplicas de acordo com as regras normais (balanceamento ou desfragmentação), o que normalmente significa que seus serviços serão distribuídos em todos os nós disponíveis no cluster, ou se a desfragmentação for ativada em um número arbitrário, mas menor, de nós.

A imagem a seguir mostra o posicionamento em potencial de uma instância do aplicativo sem o número máximo de nós definido e, em seguida, o mesmo aplicativo com um número máximo de nós definido. Observe que não são feitas garantias sobre quais réplicas ou instâncias de quais serviços serão colocadas juntas.

![Instância do aplicativo definindo o número máximo de nós][Image1]

No exemplo à esquerda, o aplicativo não tem uma Capacidade de Aplicativo definida e ele tem três serviços. O CRM tomou uma decisão lógica de dispersar todas as réplicas em seis nós disponíveis para obter o melhor equilíbrio no cluster. No exemplo à direita, vemos o mesmo aplicativo que está restrito em três nós e em que ponto o CRM do Service Fabric atingiu o melhor equilíbrio para as réplicas dos serviços do aplicativo.

O parâmetro que controla esse comportamento é chamado de MaximumNodes. Esse parâmetro pode ser definido durante a criação de aplicativos ou atualizado para uma instância do aplicativo que já estava em execução, caso em que o CRM do Service Fabric restringirá as réplicas de serviços do aplicativo ao número máximo definido de nós.

Powershell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –Name fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;

adUpdate.Metrics.Add(appMetric);
```

## Métrica, carga e capacidade do aplicativo
Grupos de aplicativos também permitem definir métricas associadas a uma determinada instância do aplicativo, bem como a capacidade do aplicativo em relação a essas métricas. Assim, por exemplo, você pode definir que tantos serviços quanto você queira possam ser criados em

Para cada métrica, podem ser definidos dois valores que para descrever a capacidade para aquela instância do aplicativo:

-	Capacidade Total do Aplicativo – representa a capacidade total do aplicativo de uma determinada métrica. O CRM do Service Fabric tentará limitar a soma das cargas de métrica dos serviços desse aplicativo para o valor especificado; além disso, se os serviços do aplicativo já estiverem consumindo a carga até esse limite, o Gerenciador de Recursos de Cluster do Service Fabric não permitirá a criação de quaisquer novos serviços ou partições que fariam a carga total ir além desse limite.
-	Capacidade máxima do nó – especifica a carga total máximo de réplicas de serviços dos aplicativos em um único nó. Se a carga total no nó ultrapassar essa capacidade, o CRM do Service Fabric tentará mover réplicas para outros nós para que a restrição de capacidade seja respeitada.

## Reserva de capacidade
Outro uso comum de grupos de aplicativos é garantir que recursos dentro do cluster sejam reservados para uma determinada instância do aplicativo, mesmo que a instância do aplicativo ainda não tenha os serviços dentro dela, ou mesmo que eles ainda não estejam consumindo os recursos. Vamos dar uma olhada em como isso funcionaria.

### Especificar um número mínimo de nós e reserva de recursos
Reservar recursos para uma instância de aplicativo requer especificar alguns parâmetros adicionais: *MinimumNodes* e *NodeReservationCapacity*

- MinimumNodes – assim como especificar um número máximo de destino de nós que podem executar os serviços dentro de um aplicativo, você pode também especificar o número mínimo de nós em que um aplicativo deve ser executado. Essa configuração define efetivamente o número de nós em que os recursos devem ser reservados no mínimo, garantindo a capacidade dentro do cluster como parte da criação da instância do aplicativo.
- NodeReservationCapacity – a NodeReservationCapacity pode ser definida para cada métrica dentro do aplicativo. Isso define a quantidade de carga métrica reservada para o aplicativo em qualquer nó dentro do qual é colocada qualquer uma das réplicas ou instâncias dos serviços.

Vamos examinar um exemplo de reserva de capacidade:

![Instâncias do aplicativo definindo a capacidade reservada][Image2]

No exemplo à esquerda, os aplicativos não têm nenhuma Capacidade de Aplicativo definida. O Gerenciador de Recursos de Cluster do Service Fabric equilibrará a réplicas de serviços filho e instâncias do aplicativo juntamente com aquelas de outros serviços (fora do aplicativo) para garantir o equilíbrio do cluster.

No exemplo à direita, digamos que o aplicativo tenha sido criado com um MinimumNodes definido como 2, MaximumNodes definido como 3 e uma Métrica de aplicativo definida com uma reserva de 20, capacidade máxima em um nó de 50 e capacidade total do aplicativo de 100, o Service Fabric reservará capacidade em dois nós para o aplicativo azul e não permitirá que outras réplicas no cluster consumam essa capacidade. Essa capacidade reservada do aplicativo será considerada consumida e contada em relação à capacidade restante do cluster.

Quando um aplicativo for criado com reserva, o Gerenciador de Recursos de Cluster reservará capacidade igual a MinimumNodes * NodeReservationCapacity no cluster, mas não reservará capacidade em nós específicos até que as réplicas de serviços do aplicativo sejam criadas e colocadas. Isso permite flexibilidade, pois nós são escolhidos para novas réplicas somente quando elas são criadas. A capacidade é reservada em um nó específico quando pelo menos uma réplica é colocada sobre ele.

## Obtendo as informações de carga do aplicativo
Para cada aplicativo que tem capacidade de aplicativos definida, você pode obter as informações sobre a carga de agregada relatada por réplicas de seus serviços. O Service Fabric fornece consultas de API Gerenciada e do PowerShell para essa finalidade.

Por exemplo, a carga pode ser recuperada usando o seguinte cmdlet do PowerShell:

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1

```

A saída dessa consulta conterá as informações básicas sobre a capacidade do aplicativo que foi especificada para o aplicativo, como Nós Mínimos e Nós Máximos. Também haverá informações sobre o número de nós que o aplicativo está usando no momento. Portanto, para cada métrica de carga, haverá informações sobre:
- Nome da Métrica: o nome da métrica.
-	Capacidade de Reserva: a Capacidade do Cluster reservada no cluster para esse Aplicativo.
-	Carga do Aplicativo: a Carga Total de réplicas filho deste Aplicativo.
-	Capacidade do Aplicativo: valor máximo permitido de Carga do Aplicativo.

## Removendo a capacidade do aplicativo
Depois que os parâmetros de Capacidade do Aplicativo estão definidos para um aplicativo, eles podem ser removidos usando cmdlets de Atualizar APIs de Aplicativo ou PowerShell. Por exemplo:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Esse comando removerá do aplicativo todos os parâmetros de Capacidade do Aplicativo e o Gerenciador de Recursos de Cluster do Service Fabric começará a tratar esse aplicativo como qualquer outro aplicativo no cluster que não tenha esses parâmetros definidos. O efeito do comando é imediato e o Gerenciador de Recursos de Cluster excluirá todos os parâmetros de Capacidade do Aplicativo para esse aplicativo; especificá-los novamente exigiria chamar Atualizar APIs do Aplicativo com os parâmetros apropriados.

## Restrições à capacidade do aplicativo
Há várias restrições aos parâmetros de Capacidade de Aplicativo que devem ser respeitadas. No caso de erros de validação, a criação ou a atualização do aplicativo será rejeitada com um erro. Todos os parâmetros de inteiro devem ser números não negativos. Além disso, para parâmetros individuais, as restrições são as seguintes:

-	MinimumNodes nunca deve ser maior que MaximumNodes.
-	Se as capacidades de uma métrica de carga forem definidas, elas deverão seguir estas regras:
  - A Capacidade de Reserva do Nó não deve ser maior que a Capacidade Máxima do Nó. Por exemplo, você não pode limitar a capacidade para a "CPU" métrica no nó a 2 unidades e tentar reservar 3 unidades em cada nó.
  - Se MaximumNodes for especificado, o produto de MaximumNodes e a Capacidade Máxima do Nó não deverão ser maiores que a Capacidade Total do Aplicativo. Por exemplo, se você definir a Capacidade Máxima do Nó para métrica de carga "CPU" para 8 e definir o Máximo de Nós para 10, a Capacidade Total do Aplicativo deverá ser maior do que 80 para essa métrica de carga.

As restrições são aplicadas tanto durante a criação do aplicativo (no lado do cliente) quanto durante a atualização do aplicativo (no lado do servidor). Durante a criação, este é um exemplo de uma clara violação dos requisitos, já que MaximumNodes < MinimumNodes, e o comando falhará no cliente antes que a solicitação sequer seja enviada ao cluster do Service Fabric:

``` posh
New-ServiceFabricApplication –Name fabric:/MyApplication1 –MinimumNodes 6 –MaximumNodes 2
```

Um exemplo de atualização inválida é o seguinte. Se pegarmos um aplicativo existente e atualizarmos o máximo de nós para algum valor, a atualização passará:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MaximumNodes 2
```

Depois disso, poderemos tentar atualizar nós mínimos:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MinimumNodes 6
```

O cliente não tem contexto suficiente sobre o aplicativo, assim, ele permitirá que a atualização passe para o cluster do Service Fabric. No entanto, no cluster, o Service Fabric validará o novo parâmetro junto com os parâmetros existentes e a operação de atualização falhará, pois o valor para os nós mínimos é maior que o valor para os nós máximos. Nesse caso, os parâmetros de capacidade do aplicativo permanecerão inalterados.

Essas restrições são colocadas em vigor para o Gerenciador de Recursos de Cluster poder fornecer a melhor disposição dos réplicas dos serviços de aplicativos.

## Como não usar a Capacidade do Aplicativo

-	Não use a Capacidade do Aplicativo para restringir o aplicativo a um subconjunto específico de nós: embora o Service Fabric garanta que o Máximo de Nós seja respeitado para cada aplicativo que tenha a Capacidade de Aplicativo especificada, os usuários não poderão decidir em quais nós ele será instanciado. Isso pode ser feito usando restrições de posicionamento para serviços.
-	Não use a Capacidade do Aplicativo para garantir que dois serviços do mesmo aplicativo sempre sejam colocados lado a lado. Isso pode ser feito por meio de relacionamento de afinidade entre serviços, e a afinidade pode ser limitada somente aos serviços que de fato devem ser colocados juntos.

## Próximas etapas
- Para saber mais sobre outras opções disponíveis para a configuração de serviços, confira o tópico sobre outras configurações disponíveis do Gerenciador de Recursos de Cluster em [Saiba mais sobre a configuração de Serviços](service-fabric-cluster-resource-manager-configure-services.md)
- Para descobrir como o Gerenciador de Recursos de Cluster gerencia e balanceia carga no cluster, confira o artigo sobre [balanceamento de carga](service-fabric-cluster-resource-manager-balancing.md)
- Comece do princípio e [veja uma introdução ao Gerenciador de Recursos de Cluster do Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
- Para obter mais informações sobre como as métricas funcionam em geral, leia sobre [Métricas de Carga do Service Fabric](service-fabric-cluster-resource-manager-metrics.md)
- O Gerenciador de Recursos de Cluster tem muitas opções para descrever o cluster. Para saber mais sobre elas, confira este artigo sobre a [descrição de um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)


[Image1]: ./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]: ./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png

<!---HONumber=AcomDC_0824_2016-->