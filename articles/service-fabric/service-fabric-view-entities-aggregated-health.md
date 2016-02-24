<properties
   pageTitle="Como exibir a integridade agregada das entidades do Azure Service Fabric | Microsoft Azure"
   description="Descreve como consultar, exibir e avaliar a integridade agregada das entidades do Azure Service Fabric, por meio de consultas de integridade e consultas gerais."
   services="service-fabric"HealthManager
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/26/2016"
   ms.author="oanapl"/>

# Como exibir relatórios de integridade do Service Fabric
O Azure Service Fabric apresenta um [modelo de integridade](service-fabric-health-introduction.md) composto por entidades de integridade nas quais os componentes do sistema e watchdogs podem relatar condições locais que estão monitorando. O [repositório de integridade](service-fabric-health-introduction.md#health-store) agrega todos os dados de integridade para determinar se as entidades estão íntegras.

Pronto para uso, o cluster é populado com relatórios de integridade enviados pelos componentes do sistema. Leia mais em [Usar relatórios de integridade do sistema para solução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

O Service Fabric fornece várias maneiras de obter a integridade agregada de entidades:

- [Gerenciador de Malha do Serviço](service-fabric-visualizing-your-cluster.md) ou outras ferramentas de visualização

- Consultas de integridade (por meio do PowerShell, API ou REST)

- Consultas gerais que retornam uma lista de entidades que têm a integridade como uma das propriedades (por meio do PowerShell, API ou REST)

Para demonstrar essas opções, vamos usar um cluster local com cinco nós. Ao lado do aplicativo **fabric:/System** (que existe originalmente), alguns outros aplicativos são implantados. Um deles é **fabric:/WordCount**. Esse aplicativo contém um serviço com estado configurado com sete réplicas. Como há apenas cinco nós, os componentes do sistema mostrarão um aviso de que a partição está abaixo da contagem de destino.

```xml
<Service Name="WordCount.Service">
  <StatefulService ServiceTypeName="WordCount.Service" MinReplicaSetSize="2" TargetReplicaSetSize="7">
    <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## Integridade no Gerenciador da Malha do Serviço
O Gerenciador da Malha do Serviço fornece uma exibição visual do cluster. Na imagem abaixo, você pode ver que:

- O aplicativo **fabric:/WordCount** está vermelho (em erro) porque ele tem um evento de erro relatado por **MyWatchdog** para a propriedade **Disponibilidade**.

- Um dos seus serviços, **fabric:/WordCount/WordCount.Service** está amarelo (em aviso). Conforme descrito acima, o serviço está configurado com sete réplicas, que não podem ser todas colocadas (uma vez que há apenas 5 nós). Embora não seja mostrado aqui, a partição de serviço está amarela devido ao relatório do sistema. A partição amarela dispara o serviço amarelo.

- O cluster está vermelho devido ao aplicativo vermelho.

A avaliação usa políticas padrão do manifesto do cluster e do manifesto do aplicativo. Elas são políticas rígidas e não toleram falhas.

Visualização do cluster com o Gerenciador do Service Fabric:

![Visualização do cluster com o Gerenciador do Service Fabric.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE] Leia mais sobre o [Gerenciador da Malha do Serviço](service-fabric-visualizing-your-cluster.md).

## Consultas de integridade
A Malha do Serviço expõe consultas de integridade para cada um dos [tipos de entidade](service-fabric-health-introduction.md#health-entities-and-hierarchy) com suporte. Elas podem ser acessados por API (os métodos podem ser encontrados em **FabricClient.HealthManager**), por cmdlets do PowerShell e por REST. Essas consultas retornam informações de integridade completas sobre a entidade, incluindo estado de integridade agregada, eventos de integridade relatados na entidade, estados de integridade dos filhos (quando aplicável) e avaliações não íntegras quando a entidade não está íntegra.

> [AZURE.NOTE] Uma entidade de integridade retorna para o usuário quando é totalmente preenchida no repositório de integridade. A entidade deve estar ativa (não excluída) e ter um relatório do sistema. Suas entidades pai na cadeia hierárquica também devem ter relatórios do sistema. Se alguma dessas condições não for atendida, as consultas de integridade retornarão uma exceção que mostra por que a entidade não retornou.

As consultas de integridade devem passar pelo identificador da entidade, que depende do tipo de entidade. As consultas aceitam parâmetros opcionais da política de integridade. Se eles não forem especificados, as [políticas de integridade](service-fabric-health-introduction.md#health-policies) do manifesto do cluster ou do aplicativo serão usadas para a avaliação. As consultas também aceitam filtros para retornar apenas eventos ou filhos parciais; aqueles que respeitarem os filtros especificados.

> [AZURE.NOTE] Os filtros de saída são aplicados no lado do servidor, de modo que o tamanho da resposta da mensagem é reduzido. Recomendamos o uso dos filtros de saída para limitar os dados retornados, em vez de aplicar filtros no lado do cliente.

A integridade de uma entidade contém as seguintes informações:

- O estado da integridade agregada da entidade. Isso é calculado pelo repositório de integridade com base nos relatórios de integridade da entidade, pelos estados da integridade dos filhos (quando aplicável) e por políticas de integridade. Leia mais sobre [Avaliação de integridade da entidade](service-fabric-health-introduction.md#entity-health-evaluation).  

- Os eventos de integridade da entidade.

- A coleção de estados de integridade de todos os filhos das entidades que podem ter filhos. Os estados da integridade contêm identificadores de entidade e o estado da integridade agregada. Para obter toda a integridade de um filho, chame a integridade de consulta do tipo de entidade filho e passe o identificador do filho.

- As avaliações não íntegras que apontam para o relatório que disparou o estado da entidade, se a entidade não estiver íntegra.

## Obter integridade do cluster
Isso retorna a integridade da entidade do cluster e contém os estados da integridade de aplicativos e nós (filhos do cluster). Entrada:

- [Opcional] Mapa da política de integridade do aplicativo, com políticas de integridade usadas para substituir as políticas do manifesto do aplicativo.

- [Opcional] Filtros de eventos, nós e aplicativos que especificam quais entradas são interessantes e devem retornar nos resultados (por exemplo, apenas erros ou avisos e erros). Observe que todos os eventos, nós e aplicativos são usados para avaliar a integridade agregada da entidade, independentemente do filtro.

### API
Para obter a integridade do cluster, crie um **FabricClient** e chame o método [**GetClusterHealthAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthasync.aspx) em seu **HealthManager**.

A seguir, você obtém a integridade do cluster:

```csharp
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync().Result;
```

O código a seguir obtém a integridade do cluster usando política de integridade de cluster personalizada para nós e aplicativos. Observe que ele cria **System.Fabric.Description.[ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthquerydescription.aspx)**, que contém todos os dados de entrada.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilter = (long)(HealthStateFilter.Error | HealthStateFilter.Warning)
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilter = (long)HealthStateFilter.Error
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
};
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync(queryDescription).Result;
```

### PowerShell
O cmdlet para obter a integridade do cluster é **[Get-ServiceFabricClusterHealth](https://msdn.microsoft.com/library/mt125850.aspx)**. Primeiro, conecte-se ao cluster usando o cmdlet **Connect-ServiceFabricCluster**.

Estado do cluster é de cinco nós, o aplicativo do sistema e fabric:/WordCount configurados conforme mostra acima.

O cmdlet a seguir obtém a integridade do cluster usando políticas de integridade padrão. O estado de integridade agregada é de aviso, pois o aplicativo fabric:/WordCount está em aviso. Observe como as avaliações não íntegras mostram com detalhes a condição que disparou a integridade agregada.

```xml
PS C:\> Get-ServiceFabricClusterHealth

AggregatedHealthState   : Warning
UnhealthyEvaluations    :
                          Unhealthy applications: 50% (1/2), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                          Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                          Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Warning'.

                          Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                          Unhealthy partition: PartitionId='889909a3-04d6-4a01-97c1-3e9851d77d6c', AggregatedHealthState='Warning'.

                          Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

NodeHealthStates        :
                          NodeName              : Node.4
                          AggregatedHealthState : Ok

                          NodeName              : Node.2
                          AggregatedHealthState : Ok

                          NodeName              : Node.1
                          AggregatedHealthState : Ok

                          NodeName              : Node.5
                          AggregatedHealthState : Ok

                          NodeName              : Node.3
                          AggregatedHealthState : Ok

ApplicationHealthStates :
                          ApplicationName       : fabric:/CalculatorActor
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
```

O cmdlet do PowerShell a seguir obtém a integridade do cluster usando uma política de aplicativo personalizada. Ele filtra os resultados para obter apenas os aplicativos e nós com erro ou aviso. Consequentemente, nenhum nó retornará, pois todos estão íntegros. Somente o aplicativo fabric:/WordCount respeita o filtro de aplicativos. Como a política personalizada especifica a consideração de avisos como erros para o aplicativo fabric:/WordCount, o aplicativo é avaliado como em estado de erro, assim como o cluster.

```powershell
PS c:> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
$warningAndErrorFilter = [System.Fabric.Health.HealthStateFilter]::Warning.value__  + [System.Fabric.Health.HealthStateFilter]::Error.value__
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsHealthStateFilter $warningAndErrorFilter -NodesHealthStateFilter $warningAndErrorFilter

AggregatedHealthState   : Error
UnhealthyEvaluations    :
                          Unhealthy applications: 50% (1/2), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                          Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                          Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                          Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                          Unhealthy partition: PartitionId='889909a3-04d6-4a01-97c1-3e9851d77d6c', AggregatedHealthState='Error'.

                          Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates :
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None

```

## Obter integridade do nó
Isso retorna a integridade de uma entidade de nó e contém os eventos de integridade reportados no nó. Entrada:

- [Obrigatório] O nome do nó que identifica o nó.

- [Opcional] As configurações da política de integridade do cluster usadas para avaliar a integridade.

- [Opcional] Filtros de eventos que especificam quais entradas são interessantes e devem retornar nos resultados (por exemplo, apenas erros ou avisos e erros). Observe que todos os eventos são usados para avaliar a integridade agregada da entidade, independentemente do filtro.

### API
Para obter a integridade do nó por meio da API, crie um FabricClient e chame o método **GetNodeHealthAsync** em seu HealthManager.

O exemplo a seguir obtém a integridade de nó para o nome de nó especificado:

```csharp
NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(nodeName).Result;
```

O exemplo a seguir obtém a integridade de nó para o nome do nó especificado e passa um filtro de eventos e política personalizada por meio de **System.Fabric.Description.[NodeHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.nodehealthquerydescription.aspx)**:

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.Warning },
};

NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(queryDescription).Result;
```

### PowerShell
O cmdlet para obter a integridade do nó é **Get-ServiceFabricNodeHealth**. Primeiro, conecte-se ao cluster usando o cmdlet **Connect-ServiceFabricCluster**. O cmdlet a seguir obtém a integridade do nó usando políticas de integridade padrão:

```powershell
PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1

NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM
```

O cmdlet a seguir obtém a integridade de todos os nós no cluster.

```powershell
PS C:\> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
Node.4                      Ok
Node.2                      Ok
Node.1                      Ok
Node.5                      Ok
Node.3                      Ok
```

## Obter integridade do aplicativo
Isso retorna a integridade de uma entidade de aplicativo. Contém os estados de integridade do aplicativo implantado e filhos de serviço. Entrada:

- [Obrigatório] O nome do aplicativo (URI) que identifica o aplicativo.

- [Opcional] A política de integridade do aplicativo usada para substituir as políticas do manifesto do aplicativo.

- [Opcional] Filtros de eventos, serviços e aplicativos implantados que especificam quais entradas são interessantes e devem retornar nos resultados (por exemplo, apenas erros ou avisos e erros). Observe que todos os eventos, serviços e aplicativos implantados são usados para avaliar a integridade agregada da entidade, independentemente do filtro.

### API
Para obter a integridade do aplicativo, crie um FabricClient e chame o método **GetApplicationHealthAsync** em seu HealthManager.

O código a seguir obtém a integridade do aplicativo para o nome do aplicativo especificado (URI):

```csharp
ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(applicationName).Result;
```

O exemplo a seguir obtém a integridade do aplicativo para o nome do aplicativo especificado (URI), com filtros e políticas personalizadas especificadas por meio de **System.Fabric.Description.ApplicationHealthQueryDescription**.

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilter = (long)warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilter = (long)warningAndErrors },
};

ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription).Result;
```

### PowerShell
O cmdlet para obter a integridade do aplicativos é **Get-ServiceFabricApplicationHealth**. Primeiro, conecte-se ao cluster usando o cmdlet **Connect-ServiceFabricCluster**.

O cmdlet a seguir retorna a integridade do aplicativo fabric:/WordCount.

```powershell
PS c:> Get-ServiceFabricApplicationHealth fabric:/WordCount

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCount.Service',
                                  MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service',
                                  AggregatedHealthState='Warning'.

                                  Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                  Unhealthy partition: PartitionId='325da69f-16d4-4418-9c30-1feaa40a072c',
                                  AggregatedHealthState='Warning'.

                                  Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning',
                                  ConsiderWarningAsError=false.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.WebService
                                  AggregatedHealthState : Ok

                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Warning

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.5
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.1
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.3
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 2456
                                  SentAt                : 4/20/2015 9:57:06 PM
                                  ReceivedAt            : 4/20/2015 9:57:06 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/20/2015 9:57:06 PM
```

O cmdlet do PowerShell a seguir passa políticas personalizadas. Ele também filtra filhos e eventos.

```powershell
PS C:\> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error.value__
Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesHealthStateFilter $errorFilter -EventsHealthStateFilter $errorFilter -DeployedApplicationsHealthStateFilter $errorFilter

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                                  Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                  Unhealthy partition: PartitionId='8f82daff-eb68-4fd9-b631-7a37629e08c0', AggregatedHealthState='Error'.

                                  Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

## Obter integridade do serviço
Isso retorna a integridade de uma entidade de serviço. Além disso, contém os estados de integridade da partição. Entrada:

- [Obrigatório] O nome de serviço (URI) que identifica o serviço.
- [Opcional] A política de integridade do aplicativo usada para substituir a política do manifesto do aplicativo.
- [Opcional] Filtros de eventos e partições que especificam quais entradas são interessantes e devem retornar nos resultados (por exemplo, apenas erros ou avisos e erros). Observe que todos os eventos e partições são usados para avaliar a integridade agregada da entidade, independentemente do filtro.

### API
Para obter a integridade do serviço por meio de API, crie um FabricClient e chame o método **GetServiceHealthAsync** em seu HealthManager.

O exemplo a seguir obtém a integridade de um serviço com o nome do serviço especificado (URI):

```charp
ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(serviceName).Result;
```

O exemplo a seguir obtém a integridade do serviço para o nome do serviço especificado (URI), especificando filtros e política personalizada por meio de System.Fabric.Description.ServiceHealthQueryDescription.

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilter = (long)HealthStateFilter.Error },
};

ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(queryDescription).Result;
```

### PowerShell
O cmdlet para obter a integridade do serviço é **Get-ServiceFabricServiceHealth**. Primeiro, conecte-se ao cluster usando o cmdlet **Connect-ServiceFabricCluster**.

O cmdlet a seguir obtém a integridade do serviço usando políticas de integridade padrão:

```powershell
PS C:\> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCount.Service


ServiceName           : fabric:/WordCount/WordCount.Service
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                        Unhealthy partition: PartitionId='8f82daff-eb68-4fd9-b631-7a37629e08c0', AggregatedHealthState='Warning'.

                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/20/2015 10:12:29 PM
                        ReceivedAt            : 4/20/2015 10:12:33 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:33 PM
```

## Obter integridade da partição
Isso retorna a integridade de uma entidade de partição. Além disso, contém os estados de integridade da réplica. Entrada:

- [Obrigatório] A ID de partição (GUID) que identifica a partição.

- [Opcional] A política de integridade do aplicativo usada para substituir a política do manifesto do aplicativo.

- [Opcional] Filtros para eventos e réplicas que especificam quais entradas são interessantes e devem retornar nos resultados (por exemplo, apenas erros ou avisos e erros). Observe que todos os eventos e réplicas são usados para avaliar a integridade agregada da entidade, independentemente do filtro.

### API
Para obter a integridade da partição por meio de API, crie um FabricClient e chame o método **GetPartitionHealthAsync** em seu HealthManager. Para especificar parâmetros opcionais, crie **System.Fabric.Description.PartitionHealthQueryDescription**.

```csharp
PartitionHealth partitionHealth = fabricClient.HealthManager.GetPartitionHealthAsync(partitionId).Result;
```

### PowerShell
O cmdlet para obter a integridade da partição é **Get-ServiceFabricPartitionHealth**. Primeiro, conecte-se ao cluster usando o cmdlet **Connect-ServiceFabricCluster**.

O cmdlet a seguir obtém a integridade de todas as partições do serviço de contagem de palavras.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCount.Service | Get-ServiceFabricPartitionHealth

PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130740415594605870
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415502123433
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605867
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605869
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605868
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 39
                        SentAt                : 4/20/2015 10:12:59 PM
                        ReceivedAt            : 4/20/2015 10:13:03 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/20/2015 10:13:03 PM
```

## Obter integridade da réplica
Isso retorna a integridade de uma réplica. Entrada:

- [Obrigatório] A ID da partição (GUID) e a ID da réplica que identificam a réplica

- [Opcional] Os parâmetros da política de integridade do aplicativo usados para substituir as políticas de manifesto do aplicativo.

- [Opcional] Filtros de eventos que especificam quais entradas são interessantes e devem retornar nos resultados (por exemplo, apenas erros ou avisos e erros). Observe que todos os eventos são usados para avaliar a integridade agregada da entidade, independentemente do filtro.

### API
Para obter a integridade da réplica por meio de API, crie um FabricClient e chame o método **GetReplicaHealthAsync** em seu HealthManager. Para especificar parâmetros avançados, use **System.Fabric.Description.ReplicaHealthQueryDescription**.

```csharp
ReplicaHealth replicaHealth = fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId).Result;
```

### PowerShell
O cmdlet para obter a integridade da réplica é **Get-ServiceFabricReplicaHealth**. Primeiro, conecte-se ao cluster usando o cmdlet **Connect-ServiceFabricCluster**.

O cmdlet a seguir obtém a integridade da réplica primária para todas as partições do serviço.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCount.Service | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415502123433
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740415502802942
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## Obter integridade do aplicativo implantado
Isso retorna a integridade de um aplicativo implantado em uma entidade de nó. Além disso, contém os estados de integridade do pacote de serviço implantado. Entrada:

- [Obrigatório] O nome do aplicativo (URI) e o nome do nó (cadeia de caracteres) que identificam o aplicativo implantado

- [Opcional] A política de integridade do aplicativo usada para substituir as políticas do manifesto do aplicativo.

- [Opcional] Filtros de eventos e pacotes de serviço implantados que especificam quais entradas são interessantes e devem retornar nos resultados (por exemplo, apenas erros ou avisos e erros). Observe que todos os eventos e pacotes de serviço implantados são usados para avaliar a integridade agregada da entidade, independentemente do filtro.

### API
Para obter a integridade de um aplicativo implantado em um nó por meio de API, crie um FabricClient e chame o método **GetDeployedApplicationHealthAsync** em seu HealthManager. Para especificar parâmetros opcionais, use **System.Fabric.Description.DeployedApplicationHealthQueryDescription**.

```csharp
DeployedApplicationHealth health = fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName)).Result;
```

### PowerShell
O cmdlet para obter a integridade do aplicativo implantado é **Get-ServiceFabricDeployedApplicationHealth**. Primeiro, conecte-se ao cluster usando o cmdlet **Connect-ServiceFabricCluster**. Para descobrir onde um aplicativo está implantado, execute **Get-ServiceFabricApplicationHealth** e observe os filhos do aplicativo implantado.

O cmdlet a seguir obtém a integridade do aplicativo fabric:/WordCount implantado no nó Node.1.

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName Node.1
ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCount.WebService
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCount.Service
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130740415502842941
                                     SentAt                : 4/20/2015 10:12:30 PM
                                     ReceivedAt            : 4/20/2015 10:12:34 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## Obter integridade do pacote de serviço implantado
Isso retorna a integridade de uma entidade de pacote do serviço implantado. Entrada:

- [Obrigatório] O nome do aplicativo (URI), nome do nó (cadeia de caracteres) e nome do manifesto do serviço (cadeia de caracteres) que identificam o pacote de serviço implantado

- [Opcional] A política de integridade do aplicativo usada para substituir a política do manifesto do aplicativo.

- [Opcional] Filtros de eventos que especificam quais entradas são interessantes e devem retornar nos resultados (por exemplo, apenas erros ou avisos e erros). Observe que todos os eventos são usados para avaliar a integridade agregada da entidade, independentemente do filtro.

### API
Para obter a integridade de um pacote de serviço implantado por meio de API, crie um FabricClient e chame o método **GetDeployedServicePackageHealthAsync** em seu HealthManager.

```csharp
DeployedServicePackageHealth health = fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName)).Result;
```

### PowerShell
O cmdlet para obter a integridade do pacote de serviço implantado é **Get-ServiceFabricDeployedServicePackageHealth**. Primeiro, conecte-se ao cluster usando o cmdlet **Connect-ServiceFabricCluster**. Para descobrir onde um aplicativo está implantado, execute **Get-ServiceFabricApplicationHealth** e observe os aplicativos implantados. Para ver quais pacotes de serviço estão em um aplicativo, observe os filhos do pacote de serviço implantado na saída de **Get-ServiceFabricDeployedApplicationHealth**.

O cmdlet a seguir obtém a integridade do pacote de serviço **WordCount.Service** do aplicativo fabric:/WordCount implantado no nó Node.1. A entidade tem relatórios **System.Hosting** para ativação bem-sucedida do pacote de serviço e do ponto de entrada, além do registro bem-sucedido do tipo de serviço.

```powershell
PS C:\> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName Node.1 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCount.Service

ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCount.Service
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130740415506383060
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130740415506543054
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCount.Service
                        HealthState           : Ok
                        SequenceNumber        : 130740415520193499
                        SentAt                : 4/20/2015 10:12:32 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## Consultas gerais
As consultas gerais retornam a lista de entidades do Service Fabric de um tipo especificado. Elas são expostas por meio da API (métodos em **FabricClient.QueryManager**), dos cmdlets do PowerShell e da REST. Essas consultas agregam subconsultas de vários componentes. Um deles é o [Repositório de Integridade](service-fabric-health-introduction.md#health-store), que preenche o estado de integridade agregada para cada resultado da consulta.

> [AZURE.NOTE] As consultas gerais retornam o estado da integridade da entidade agregada e não contêm dados de integridade avançados. Se uma entidade não estiver íntegra, você poderá acompanhá-la com consultas de integridade a fim de obter todas as informações de integridade, incluindo eventos, estados da integridade dos filhos e avaliações não íntegras.

Se as consultas gerais retornarem um estado de integridade desconhecido para uma entidade, talvez o repositório de integridade não tenha dados completos sobre a entidade. Também é possível que uma subconsulta ao repositório de integridade não tenha êxito (por exemplo, ocorreu um erro de comunicação ou repositório de integridade apresentava alguma restrição). Acompanhe uma consulta de integridade da entidade. Se a subconsulta tiver encontrado erros transitórios, como problemas de rede, essa consulta de acompanhamento poderá ser bem-sucedida. Ela também pode fornecer mais detalhes, com base no repositório de integridade, sobre o motivo de a entidade não ser exposta.

As consultas que contêm **HealthState** para entidades são:

- Lista de nós: retorna os nós da lista no cluster.
  - API: FabricClient.QueryManager.GetNodeListAsync
  - PowerShell: Get-ServiceFabricNode
- Lista de aplicativos: retorna a lista de aplicativos no cluster.
  - API: FabricClient.QueryManager.GetApplicationListAsync
  - PowerShell: Get-ServiceFabricApplication
- Lista de serviços: retorna a lista de serviços em um aplicativo.
  - API: FabricClient.QueryManager.GetServiceListAsync
  - PowerShell: Get-ServiceFabricService
- Lista de partições: retorna a lista de partições em um serviço.
  - API: FabricClient.QueryManager.GetPartitionListAsync
  - PowerShell: Get-ServiceFabricPartition
- Lista de réplicas: retorna a lista de réplicas em uma partição.
  - API: FabricClient.QueryManager.GetReplicaListAsync
  - PowerShell: Get-ServiceFabricReplica
- Lista de aplicativos implantados: retorna a lista de aplicativos implantados em um nó.
  - API: FabricClient.QueryManager.GetDeployedApplicationListAsync
  - PowerShell: Get-ServiceFabricDeployedApplication
- Lista de pacotes de serviço implantados: retorna a lista de pacotes de serviço em um aplicativo implantado.
  - API: FabricClient.QueryManager.GetDeployedServicePackageListAsync
  - PowerShell: Get-ServiceFabricDeployedApplication

### Exemplos

O exemplo a seguir obtém os aplicativos não íntegros no cluster:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

O cmdlet a seguir obtém detalhes de aplicativo para o aplicativo fabric:/WordCount. Observe que o estado de integridade é de aviso.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "_WFDebugParams_" = "[{"ServiceManifestName":"WordCount.WebService","CodePackageName":"Code","EntryPointType":"Main"}]" }
```

O cmdlet a seguir obtém os serviços com o estado de integridade de aviso.

```powershell
PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Warning"}

ServiceName            : fabric:/WordCount/WordCount.Service
ServiceKind            : Stateful
ServiceTypeName        : WordCount.Service
IsServiceGroup         : False
ServiceManifestVersion : 1.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Warning
```

## Atualização de cluster e aplicativo
Durante a atualização monitorada do cluster e do aplicativo, o Service Fabric verifica a integridade para garantir que tudo esteja íntegro e permaneça assim. Se uma entidade não estiver íntegra, conforme avaliação por meio de políticas de integridade, a atualização aplica políticas específicas à atualização para determinar a próxima ação. A atualização pode ser pausada para permitir a interação do usuário (por exemplo, corrigir condições de erro ou alterar políticas), ou pode ser revertida automaticamente para a versão anterior em boa qualidade.

Durante uma atualização de *cluster*, você pode obter o status de atualização do cluster. Isso inclui quaisquer avaliações não íntegras, que apontam para o que não está íntegro no cluster. Se a atualização for revertida devido a problemas de integridade, o status da atualização manterá os últimos motivos não íntegros. Isso mantém as informações que podem ajudar os administradores a investigar o que deu errado.

Da mesma forma, durante uma atualização do *aplicativo*, quaisquer avaliações não íntegras ficam contidas no status de atualização do aplicativo.

O exemplo a seguir mostra o status da atualização do aplicativo para um aplicativo fabric:/WordCount modificado. Um watchdog relatou um erro em uma de suas réplicas. A atualização está sendo revertida porque as verificações de integridade não são respeitadas.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2015 5:23:26 PM
FailureTimestampUtc           : 4/21/2015 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : Node1
                                UpgradePhase        : Upgrading

                                NodeName            : Node2
                                UpgradePhase        : Upgrading

                                NodeName            : Node3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                                Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='30db5be6-4e20-4698-8185-4bd7ca744020', AggregatedHealthState='Error'.

                                Unhealthy replicas: 16% (1/6), MaxPercentUnhealthyReplicasPerPartition=0%.

                                Unhealthy replica: PartitionId='30db5be6-4e20-4698-8185-4bd7ca744020', ReplicaOrInstanceId='130741105362491906', AggregatedHealthState='Error'.

                                Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Leia mais sobre a [Atualização de aplicativo do Service Fabric](service-fabric-application-upgrade.md).

## Usar avaliações de integridade para solucionar problemas
Sempre que houver um problema no cluster ou aplicativo, observe a integridade do cluster ou do aplicativo para identificar o que está errado. As avaliações não íntegras mostrarão detalhes sobre o que disparou o estado não íntegro atual. Se for necessário, você poderá analisar entidades filho não íntegras para identificar a causa raiz.

## Próximas etapas
[Usar relatórios de integridade do sistema para solução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Adicionar relatórios de integridade personalizados do Service Fabric](service-fabric-report-health.md)

[Monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização de aplicativos do Service Fabric](service-fabric-application-upgrade.md)

<!---HONumber=AcomDC_0128_2016-->