<properties
   pageTitle="Descrições de serviço do Balanceador de Recursos"
   description="Uma visão geral de como configurar as descrições de serviço para uso do Balanceador de Recursos"
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
   ms.date="09/15/2015"
   ms.author="masnider"/>

# Visão geral da descrição do serviço

## Restrições de Posicionamento
As [Restrições de Posicionamento](../service-fabric-placement-constraint) em um serviço são o mecanismo pelo qual uma determinada instância de serviço seleciona as propriedades de nó necessárias. Assim como as propriedades de nó, elas são pares de chave/valor que descrevem o nome da propriedade e os requisitos do serviço para o valor. Instruções individuais podem ser agrupadas com lógica booliana simples para criar a restrição necessária. Observe que o Balanceador de Recursos da Malha de Serviço interpreta as restrições.

As restrições de posicionamento de serviço podem ser definidas por meio do manifesto do aplicativo ou serviço, ou diretamente no código.

O manifesto do serviço fornece as definições ServiceTypes.

``` xml
<ServiceTypes>
    <StatefulServiceType ServiceTypeName="QueueReplica" HasPersistedState="false" >
      <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
    </StatefulServiceType>
</ServiceTypes>

```

O manifesto do aplicativo fornece as definições ServiceTemplates ou DefaultServices.

``` xml
<ServiceTemplates>
    <StatefulService ServiceTypeName="QueueReplica">
      <SingletonPartition></SingletonPartition>
      <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
    </StatefulService>
  </ServiceTemplates>

  <DefaultServices>
    <Service Name="QR">
      <StatefulService MinReplicaSetSize="3" ServiceTypeName="QueueReplica" TargetReplicaSetSize="3">
        <SingletonPartition/>
        <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
      </StatefulService>
    </Service>
  </DefaultServices>
```

``` cpp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasDisk == true  && SpindleCount >= 4)";
Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

O diagrama a seguir mostra que no cluster com nós, o único nó válido para posicionamento desse serviço é N5.

![Restrições de Posicionamento][Image1]

Se o serviço especifica uma restrição para uma propriedade que um nó específico não definiu, esse nó não é considerado uma correspondência, independentemente do valor especificado.

## Afinidade de serviço
Uma forma especial de restrição de posicionamento, a afinidade de serviço permite a descrição de diferentes serviços que por motivos variados dependem um do outro, o que significa que ambos os serviços devem estar em execução no mesmo nó para que funcionem. Embora esse tipo de arquitetura não seja comum entre aplicativos da Malha de Serviço, a Malha de Serviço reconhece que é possível a arquitetura transicional para determinados tipos de aplicativos herdados e, portanto, disponibiliza esse recurso.

Estabelecer afinidade de serviço entre dois serviços garante que as réplicas Primárias desses serviços sejam sempre colocadas no mesmo nó.

As relações de afinidade são representadas por uma hierarquia pai-filho, isto é, há “pais” e “filhos”. Na relação, o serviço estabelecido primeiro é o pai e o serviço estabelecido depois é o filho. A relação é modelada como uma restrição "rígida".

Atualmente, a afinidade de serviço tem as seguintes limitações:

- Não pode ser usada entre serviços com e sem estado.
- Não pode ser usada entre serviços sem estado com diferentes contagens de instância; por exemplo, ambos os serviços sem estado devem ter a mesma propriedade InstanceCount quando são criados.
- Não pode ser usada entre serviços com estado voláteis ou persistentes com diferentes números de réplicas; por exemplo, ambos os serviços devem ter os mesmos valores ReplicaSetSizes de Destino e Mínimo especificados.
- Não pode ser usada com serviços particionados. Cada serviço deve ter um esquema de partição FABRIC\_PARTITION\_SCHEME\_SINGLETON.
- As relações de afinidade, assim como outras propriedades da descrição do serviço, são definidas quando o serviço é criado e não podem ser modificadas.
- Cadeias de serviços não são permitidas. Se vários serviços devem ser colocados em uma relação de afinidade, eles devem usar um modelo de "estrela".

``` xml
<ServiceTemplates>
  <StatelessService ServiceTypeName="StatelessCalculatorService" InstanceCount="5">
    <SingletonPartition></SingletonPartition>
      <ServiceCorrelations>
        <ServiceCorrelation Scheme="Affinity" ServiceName="fabric:/otherApplication/parentService"/>
      </ServiceCorrelations>
  </StatelessService>
</ServiceTemplates>
```

O exemplo de código mostra o uso alternativo das definições DefaultServices.

``` xml
<DefaultServices>
  <Service Name="childService">
    <StatelessService InstanceCount="3" ServiceTypeName="calculatorService">
      <SingletonPartition/>
     <ServiceCorrelations>
        <ServiceCorrelation Scheme="Affinity" ServiceName="fabric:/otherApplication/parentService"/>
      </ServiceCorrelations>
    </StatelessService>
  </Service>
</DefaultServices>
```

O exemplo de código mostra como criar uma relação de afinidade depois que o aplicativo de contenção é criado.

``` cpp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## Métricas de Balanceamento de Recursos
Enquanto as restrições de posicionamento de serviço e as propriedades de nó representam regras e políticas rígidas que descrevem quais disposições de serviços são válidas, as Métricas de Balanceamento de Recursos geralmente ajudam a descrever a disposição ideal dos serviços dentro do cluster. As métricas que são definidas pelo serviço têm várias propriedades diferentes que descrevem como elas são usadas no cluster.

### Usando métricas padrão
O primeiro ponto a ser entendido é que lidar com Métricas de Recursos é uma questão completamente opcional para serviços e que, por padrão, o Balanceador de Recursos da Malha de Serviço usa algumas métricas “internas” do sistema para realizar o balanceamento básico de recursos sem exigir que os administradores de cluster ou serviço tomem alguma medida. As métricas padrão que o Balanceador de Recursos da Malha de Serviço rastreia e equilibra dentro do cluster são PrimaryCount, ReplicaCount e Count. PrimaryCount é a contagem de réplicas Primárias do serviço com estado em um nó. ReplicaCount é a contagem de todas as réplicas com estado em um nó. Count é a contagem total de todos os objetos de serviço no nó, incluindo serviços com e sem estado. Geralmente, a métrica PrimaryCount é considerada a mais importante e, portanto, tem o peso mais alto, conforme descrito na seção a seguir. A métrica PrimaryCount é seguida pela métrica ReplicaCount e, por fim, pela métrica Count.

O diagrama a seguir mostra um exemplo de balanceamento padrão de três serviços com estado, com três réplicas cada um.

![Métricas padrão][Image2]

Geralmente, as métricas padrão são suficientes para muitos serviços e devem ser usadas, a menos que haja um requisito específico por métricas ou recursos adicionais.

### Definindo métricas personalizadas
Se as métricas padrão não são suficientes para um determinado serviço ou esse serviço tem requisitos conhecidos em torno de um recurso específico, como espaço em disco ou memória, métricas personalizadas devem ser usadas. As métricas personalizadas são úteis quando um serviço tem um ou mais recursos que devem ser bem equilibrados para evitar a contenção. O uso dessas métricas pode variar consideravelmente entre as réplicas no cluster; por exemplo, uma Primária pode estar usando 100% da métrica, enquanto outra pode estar usando apenas 20%. Também pode ser útil usar métricas personalizadas para capturar qualquer recurso que esteja rigidamente limitado em um computador, como memória, espaço em disco ou conexões. Às vezes, métricas personalizadas adicionais podem ser criadas, que capturam ou representam métricas que não têm limites superiores nos nós, mas representam o consumo de trabalho e recurso da perspectiva de serviço; por exemplo, uma métrica de “transações pendentes atuais”. Embora não haja um limite para essa métrica de “capacidade” em um determinado nó, ainda há o benefício de desempenho ao garantir que essa métrica seja distribuída igualmente em todo o cluster.

Observe que se um serviço define alguma métrica personalizada, ele não usa as métricas padrão fornecidas pelo sistema. As métricas padrão ainda podem ser usadas, mas devem ser explicitamente reincluídas na lista de métricas do serviço no momento da criação.

### Nomes de métrica
As métricas personalizadas podem ser criadas simplesmente com a definição de um Nome de Métrica quando você cria um serviço. Observe que o Balanceador de Recursos da Malha de Serviço associa métricas usando o próprio nome da métrica, de modo que se a métrica for usada como uma capacidade nas definições do nó ou entre serviços, os nomes de métrica deverão ser exatamente iguais para que o Balanceador de Recursos da Malha de Serviço possa relacioná-los.

### Pesos de métrica
Quando um serviço define várias métricas, também é útil definir os pesos para essas métricas. Diferentes pesos de métrica instruem o Balanceador de Recursos da Malha de Serviço sobre qual métrica é mais importante para a função do serviço. Por exemplo, uma fila na memória pode ser afetada pela largura de banda de rede, mas provavelmente é mais limitada pelo uso real da memória no nó. Desse modo, embora a fila possa ter várias métricas, a métrica que representa o uso de memória tem o peso mais alto. Da mesma forma, um banco de dados persistente provavelmente depende do uso do disco e da memória, mas embora a memória limitada reduza a capacidade de processar consultas complexas, esgotar o espaço em disco impede outras operações de armazenamento, o que, provavelmente, é uma situação mais crítica. Portanto, o armazenamento persistente provavelmente escolhe o espaço em disco como a métrica com o peso mais alto.

Observe que o peso é realmente usado apenas no Balanceador de Recursos da Malha de Serviço quando este não pode equilibrar completamente um conjunto de métricas e, dessa forma, deve encontrar soluções em que uma métrica acabe menos equilibrada no geral do que outra métrica. Nesse caso, o equilíbrio da métrica com o peso mais baixo é determinado para ser de prioridade mais baixa do que da métrica com o peso mais alto, assim, o Balanceador de Recursos da Malha de Serviço apresenta propostas que favoreçam o balanceador da métrica ponderada mais alta.

Além disso, quando o Balanceador de Recursos da Malha de Serviço reconhece que vários serviços definiram a mesma métrica por nome, ele considera essas métricas como sendo as mesmas. Em casos em que a métrica é a mesma, mas os pesos são diferentes, o Balanceador de Recursos calcula a média dos pesos especificados para determinar os pesos reais que devem ser usados durante o tempo de execução. Portanto, se um serviço define um peso de Zero, que indica que ele não exige balanceamento, mas outro serviço define a mesma métrica com um peso Alto, o peso real acaba em algum ponto entre os dois.

![Pesos de métrica][Image3]

Esse exemplo mostra como dois cenários de peso de métrica diferente geram dois resultados de balanceamento diferentes, onde a métrica mais altamente priorizada é mais bem distribuída. No exemplo à esquerda, B, é mais bem equilibrada, enquanto no exemplo à direita, A, é mais bem equilibrada. Como nesse exemplo não há solução de balanceamento que resulte em equilíbrio de ambas as métricas, o Balanceador de Recursos da Malha de Serviço usa o Peso das métricas para determinar qual é mais importante e, portanto, qual métrica escolher durante o balanceamento.

Observe que o peso da métrica Zero é fornecido para casos em que um serviço rastreia uma métrica, mas não exige balanceamento que seja baseado nessa métrica. Por exemplo, pense em uma métrica que tenha um limite explícito em vários nós, mas para a qual a “utilização regular entre nós”, que é o que o balanceamento de recursos normalmente garante, não é importante. Para esses tipos de métrica que não exigem balanceamento, mas exigem imposição de limites definidos nos nós, o peso de balanceamento pode ser definido como Zero. Durante o tempo de execução, o Balanceador de Recursos da Malha de Serviço impõe capacidade, mas não tenta equilibrar a métrica de modo proativo, mesmo que o uso dessa métrica seja muito desequilibrado entre os nós.

### Carga de métrica padrão para uma função primária ou secundária
Ao definir uma métrica para um serviço, você fornece o consumo esperado para essa métrica quando o serviço está em uma função Primária ou Secundária. Essa informação não só ajuda o Balanceador de Recursos a posicionar inicialmente o serviço de maneira eficiente, mas pode ser uma boa aproximação do uso real das métricas ao qual é relacionado durante todo o seu tempo de vida. O Balanceador de Recursos da Malha de Serviço leva o consumo dessa métrica em consideração automaticamente não somente no posicionamento do seu serviço, mas sempre que precisa mover réplicas devido ao balanceamento ou a outras mudanças no cluster. Se a carga de um serviço for claramente previsível e estável, isso significa que ao definir esse valores, o serviço pode se recusar a relatar a carga durante o tempo de execução.

O exemplo de código a seguir mostra um serviço que define completamente duas métricas personalizadas, uma para uso da memória e a outra para espaço em disco.

``` cpp
ServiceLoadMetricDescription memoryMetric = new ServiceLoadMetricDescription();
memoryMetric.Name = "MemoryInMb";
memoryMetric.Weight = ServiceLoadMetricWeight.High;
memoryMetric.PrimaryDefaultLoad = 100;
memoryMetric.SecondaryDefaultLoad = 50;

ServiceLoadMetricDescription diskMetric = new ServiceLoadMetricDescription();
diskMetric.Name = "DiskInMb";
diskMetric.Weight = ServiceLoadMetricWeight.Medium;
diskMetric.PrimaryDefaultLoad = 1024;
diskMetric.SecondaryDefaultLoad = 750;

serviceDescription.Metrics.Add(memoryMetric);
serviceDescription.Metrics.Add(diskMetric);

Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

O exemplo de código mostra as definições ServiceTypes por meio do manifesto do serviço.

``` xml
<ServiceTypes>
  <StatefulServiceType ServiceTypeName="QueueReplica" HasPersistedState="true">
    <LoadMetrics>
      <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
      <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
    </LoadMetrics>
  </StatefulServiceType>
</ServiceTypes>
```

O exemplo de código mostra as definições ServiceTemplates por meio do manifesto do aplicativo.

``` xml
<ServiceTemplates>
   <StatefulService ServiceTypeName="QueueReplica">
     <SingletonPartition></SingletonPartition>
     <LoadMetrics>
       <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
       <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
     </LoadMetrics>
   </StatefulService>
 </ServiceTemplates>
```
O exemplo de código mostra as definições DefaultServices por meio do manifesto do aplicativo.

``` xml
<DefaultServices>
  <Service Name="QueueServiceInstance">
    <StatefulService MinReplicaSetSize="3" ServiceTypeName="QueueService" TargetReplicaSetSize="3">
      <SingletonPartition/>
      <LoadMetrics>
        <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
        <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
      </LoadMetrics>
    </StatefulService>
  </Service>
</DefaultServices>
```

Como os valores de carga padrão não são atualizados, a menos que os serviços aceitem especificamente relatar a carga no tempo de execução por meio de código, os valores também podem ser usados para instituir mais de um modelo de balanceamento de recurso para “reserva de capacidade” Por exemplo, se as cargas de trabalho geralmente se encaixam em vários blocos de tamanho e um número conhecido de unidades de trabalho pode ser colocado em um nó a qualquer momento, uma métrica personalizada de “unidades” pode ser criada e, em seguida, a capacidade do nó e a carga de serviço padrão são definidas em termos de unidade.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

Para obter mais informações: [Arquitetura do Balanceador de Recursos](service-fabric-resource-balancer-architecture.md), [Restrições de Posicionamento](service-fabric-placement-constraint.md)

[Image1]: media/service-fabric-resource-balancer-service-description/PC.png
[Image2]: media/service-fabric-resource-balancer-service-description/DM.png
[Image3]: media/service-fabric-resource-balancer-service-description/MW.png
 

<!---HONumber=Sept15_HO3-->