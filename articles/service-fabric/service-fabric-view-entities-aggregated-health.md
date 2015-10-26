<properties
   pageTitle="Como exibir a integridade agregada das entidades da Malha do Serviço"
   description="Descreve como consultar, exibir e avaliar a integridade agregada das entidades da Malha do Serviço do Azure, por meio de consultas de integridade e consultas gerais."
   services="service-fabric"
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
   ms.date="09/03/2015"
   ms.author="oanapl"/>

# Como exibir relatórios de integridade da Malha do Serviço
A Malha do Serviço apresenta um [Modelo de Integridade](service-fabric-health-introduction.md) composto por entidades de integridade nas quais componentes do sistema e watchdogs podem relatar condições locais que estão monitorando. O [Repositório de Integridade](service-fabric-health-introduction.md#health-store) agrega todos os dados de integridade para determinar se as entidades estão íntegras.

Pronto para uso, o cluster é populado com relatórios de integridade enviados pelos componentes do sistema. Leia mais em [Usando relatórios de integridade do sistema para solução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

A Malha do Serviço fornece várias maneiras de obter a integridade agregada de entidades:

- [Gerenciador de Malha do Serviço](service-fabric-visualizing-your-cluster.md) ou outras ferramentas de visualização

- Consultas de integridade (por meio de API/Powerhsell/REST)

- Consultas gerais que retornam uma lista de entidades que têm a integridade como uma das propriedades (por meio de API/PowerShell/REST)

Para demonstrar essas opções, vamos usar um cluster local com **5 nós**. Ao lado do aplicativo **fabric:/System** (pronto para uso), existem alguns outros aplicativos implantados, um dos quais é **fabric:/WordCount**. Este aplicativo contém um serviço com estado configurado com 7 réplicas. Como existem apenas 5 nós, os componentes do sistema sinalizarão que a partição está abaixo da contagem de destino com um Aviso.

```xml
<Service Name="WordCount.Service">
  <StatefulService ServiceTypeName="WordCount.Service" MinReplicaSetSize="2" TargetReplicaSetSize="7">
    <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## Integridade no Gerenciador da Malha do Serviço
O Gerenciador da Malha do Serviço fornece uma exibição visual do cluster. Na imagem abaixo, você pode ver que:

- O aplicativo **fabric:/WordCount** está “vermelho” (em Erro) porque ele tem um evento de erro relatado por MyWatchdog para a propriedade Disponibilidade.

- Um dos seus serviços, **fabric:/WordCount/WordCount.Service** está “amarelo” (em Aviso). Conforme descrito acima, o serviço está configurado com 7 réplicas, que não podem ser todas colocadas (uma vez que temos apenas 5 nós). Embora não seja mostrado aqui, a partição de serviço está “amarela” devido ao relatório do sistema. A partição “amarela” dispara o serviço “amarelo”.

- O **cluster** está “vermelho” devido ao aplicativo “vermelho”.

A avaliação usa políticas padrão do manifesto do cluster e do manifesto do aplicativo, que são as políticas rígidas (não toleram nenhuma falha).

![Exibição do cluster com ServiceFabricExplorer.][1]

Exibição do cluster com ServiceFabricExplorer.

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE]Leia mais sobre o [Gerenciador da Malha do Serviço](service-fabric-visualizing-your-cluster.md).

## Consultas de integridade
A Malha do Serviço expõe consultas de integridade para cada um dos [tipos de entidade](service-fabric-health-introduction.md#health-entities-and-hierarchy) com suporte. Elas podem ser acessadas por API (métodos em FabricClient.HealthManager), cmdlets do Powershell e REST. Essas consultas retornam informações de integridade completas sobre a entidade, incluindo estado de integridade agregada, eventos de integridade relatados na entidade, estados de integridade dos filhos (quando aplicável) e avaliações não íntegras quando a entidade não está íntegra.

> [AZURE.NOTE]Uma entidade de integridade é retornada ao usuário quando é totalmente populada no Repositório de Integridade: a entidade tem um relatório do sistema, está ativa (não excluída) e as entidades pai na cadeia de hierarquia têm relatórios do sistema. Se alguma dessas condições não for atendida, as consultas de integridade retornarão uma exceção mostrando por que a entidade não foi retornada.

As consultas de integridade exigem passar pelo identificador da entidade, o que depende do tipo de entidade. Elas aceitam parâmetros opcionais da política de integridade. Se não forem especificadas, as [políticas de integridade](service-fabric-health-introduction.md#health-policies) do manifesto do cluster ou do aplicativo serão usadas para avaliação. Elas também aceitam filtros para retornar apenas eventos ou filhos parciais; aqueles que respeitarem os filtros especificados.

> [AZURE.NOTE]Os filtros de saída são aplicados no lado do servidor, de modo que o tamanho da resposta da mensagem é reduzido. É recomendável usar os filtros para limitar os dados retornados, em vez de aplicar filtros no lado do cliente.

Uma integridade de uma entidade contém as seguintes informações:

- O estado da integridade agregada da entidade. Isso é calculado pelo Repositório de Integridade com base nos relatórios de integridade da entidade, estados da integridade dos filhos (quando aplicável) e políticas de integridade. Leia mais sobre [Avaliação de integridade da entidade](service-fabric-health-introduction.md#entity-health-evaluation).  

- Os eventos de integridade da entidade.

- Para as entidades que podem ter filhos, coleção de estados de integridade de todos os filhos. Os estados da integridade contêm o identificador da entidade e o estado da integridade agregada. Para obter toda a integridade de um filho, chame a integridade de consulta para o tipo de entidade filho, passando pelo identificador filho.

- Se a entidade não estiver íntegra, as avaliações não íntegras que apontam para o relatório que disparou o estado da entidade.

## Obter integridade do cluster
Retorna a integridade da entidade do cluster. Contém os estados da integridade de aplicativos e nós (filhos do cluster). Entrada:

- [opcional] Mapa da política de integridade do aplicativo com políticas de integridade usadas para substituir as políticas do manifesto do aplicativo.

- [opcional] Filtro para retornar apenas eventos, nós, aplicativos com determinado estado de integridade (por exemplo, retornar apenas erros ou avisos, etc).

### API
Para obter a integridade do cluster, crie um FabricClient e chame o método GetClusterHealthAsync em seu HealthManager.

A seguir, você obtém a integridade do cluster:

```csharp
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync().Result;
```

A seguir, você obtém a integridade do cluster usando política de integridade de cluster personalizada para nós e aplicativos. Observe que ele cria System.Fabric.Description.ClusterHealthQueryDescription, que contém todos os dados de entrada.

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

### Powershell
O cmdlet para obter a integridade do cluster é Get-ServiceFabricClusterHealth. Primeiro, conecte-se ao cluster com o cmdlet Connect-ServiceFabricCluster. Estado do cluster: 5 nós, aplicativo de sistema e fabric:/WordCount configurados como acima.

O cmdlet a seguir obtém a integridade do cluster com políticas de integridade padrão. O estado da integridade agregada é Aviso porque o aplicativo fabric:/WordCount está em Aviso. Observe como as avaliações não íntegras mostram com detalhes a condição que disparou a integridade agregada.

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

O cmdlet do Powershell a seguir obtém a integridade do cluster com política de aplicativo personalizada. Ele filtra os resultados para obter apenas os aplicativos e nós com Erro ou Aviso. Consequentemente, nenhum nó será retornado, pois todos eles estão íntegros. Somente o aplicativo fabric:/WordCount respeita o filtro de aplicativos. Como a política personalizada especifica considerar aviso como erro para o aplicativo fabric:/WordCount, o aplicativo é avaliado em Erro, assim como o cluster.

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
Retorna a integridade de uma entidade de nó. Contém os eventos de integridade relatados no nó. Entrada:

- [obrigatório] O nome do nó que identifica o nó.

- [opcional] Configurações da política de integridade do cluster usadas para avaliar a integridade.

- [opcional] Filtro para retornar apenas eventos com determinado estado de integridade (por exemplo, retornar apenas erros ou avisos, etc).

### API
Para obter a integridade do nó por meio de API, crie um FabricClient e chame o método GetNodeHealthAsync em seu HealthManager.

O exemplo a seguir obtém a integridade do nó para o nome do nó especificado.

```csharp
NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(nodeName).Result;
```

O exemplo a seguir obtém a integridade do nó para o nome do nó especificado, passando pelo filtro de eventos e política personalizada por meio de System.Fabric.Description.NodeHealthQueryDescription.

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.Warning },
};

NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(queryDescription).Result;
```

### Powershell
O cmdlet para obter a integridade do nó é Get-ServiceFabricNodeHealth. Primeiro, conecte-se ao cluster com o cmdlet Connect-ServiceFabricCluster. O cmdlet a seguir obtém a integridade do nó com políticas de integridade padrão.

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
Retorna a integridade de uma entidade de aplicativo. Contém os estados de integridade do aplicativo implantado e filhos de serviço. Entrada:

- [obrigatório] O nome do aplicativo (URI) que identifica o aplicativo

- [opcional] Política de integridade do aplicativo usada para substituir as políticas do manifesto do aplicativo.

- [opcional] Filtro para retornar apenas eventos, serviços, aplicativos implantados com determinado estado de integridade (por exemplo, retornar apenas erros ou avisos, etc).

### API
Para obter a integridade do aplicativo, crie um FabricClient e chame o método GetApplicationHealthAsync em seu HealthManager.

O exemplo a seguir obtém a integridade do aplicativo para o URI do nome do aplicativo especificado.

```csharp
ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(applicationName).Result;
```

O exemplo a seguir obtém a integridade do aplicativo para o URI do nome do aplicativo especificado, especificando filtros e política personalizada por meio de System.Fabric.Description.ApplicationHealthQueryDescription.

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

### Powershell
O cmdlet para obter a integridade do aplicativos é Get-ServiceFabricApplicationHealth. Primeiro, conecte-se ao cluster com o cmdlet Connect-ServiceFabricCluster.

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

O cmdlet do Powershell a seguir passa a política personalizada e filtra filhos e eventos.

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
Retorna a integridade de uma entidade de serviço. Contém os estados de integridade da partição. Entrada:

- [obrigatório] Nome do serviço (URI) que identifica o serviço
- [opcional] Política de integridade do aplicativo usada para substituir a política do manifesto do aplicativo.
- [opcional] Filtro para retornar apenas eventos e partições com determinado estado de integridade (por exemplo, retornar apenas erros ou avisos, etc).

### API
Para obter a integridade do serviço por meio de API, crie um FabricClient e chame o método GetServiceHealthAsync em seu HealthManager.

O exemplo a seguir obtém a integridade de um serviço com o nome do serviço especificado (URI):

```charp
ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(serviceName).Result;
```

O exemplo a seguir obtém a integridade do serviço para o URI do nome do serviço especificado, especificando filtros e política personalizada por meio de System.Fabric.Description.ServiceHealthQueryDescription.

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilter = (long)HealthStateFilter.Error },
};

ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(queryDescription).Result;
```

### Powershell
O cmdlet para obter a integridade do serviço é Get-ServiceFabricServiceHealth. Primeiro, conecte-se ao cluster com o cmdlet Connect-ServiceFabricCluster.

O cmdlet a seguir obtém a integridade do serviço com políticas de integridade padrão.

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
Retorna a integridade de uma entidade de partição. Contém os estados de integridade da réplica. Entrada:

- [obrigatório] ID de partição (Guid) que identifica a partição

- [opcional] Política de integridade do aplicativo usada para substituir a política do manifesto do aplicativo.

- [opcional] Filtro para retornar apenas os eventos, réplicas com determinado estado de integridade (por exemplo, retornar apenas erros ou avisos, etc.).

### API
Para obter a integridade da partição por meio de API, crie um FabricClient e chame o método GetPartitionHealthAsync em seu HealthManager. Para especificar parâmetros opcionais, crie System.Fabric.Description.PartitionHealthQueryDescription.

```csharp
PartitionHealth partitionHealth = fabricClient.HealthManager.GetPartitionHealthAsync(partitionId).Result;
```

### Powershell
O cmdlet para obter a integridade da partição é Get-ServiceFabricPartitionHealth. Primeiro, conecte-se ao cluster com o cmdlet Connect-ServiceFabricCluster.

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
Retorna a integridade de uma réplica. Entrada:

- [obrigatório] Identificação da partição (Guid) e a identificação da réplica que identificam a réplica

- [opcional] Parâmetros da política de integridade do aplicativo usados para substituir as políticas do manifesto do aplicativo.

- [opcional] Filtro para retornar apenas eventos com determinado estado de integridade (por exemplo, retornar apenas erros ou avisos, etc).

### API
Para obter a integridade da réplica por meio de API, crie um FabricClient e chame o método GetReplicaHealthAsync em seu HealthManager. Especifique parâmetros avançados com System.Fabric.Description.ReplicaHealthQueryDescription.

```csharp
ReplicaHealth replicaHealth = fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId).Result;
```

### Powershell
O cmdlet para obter a integridade da réplica é Get-ServiceFabricReplicaHealth. Primeiro, conecte-se ao cluster com o cmdlet Connect-ServiceFabricCluster.

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
Retorna a integridade de um aplicativo implantado em uma entidade de nó. Contém os estados de integridade do pacote de serviço implantado. Entrada:

- [obrigatório] Nome do aplicativo (URI) e nome do nó (cadeia de caracteres) que identificam o aplicativo implantado

- [opcional] Política de integridade do aplicativo usada para substituir as políticas do manifesto do aplicativo.

- [opcional] Filtro para retornar apenas eventos, pacotes de serviço implantados com determinado estado de integridade (por exemplo, retornar apenas erros ou avisos, etc).

### API
Para obter a integridade de um aplicativo implantado em um nó por meio de API, crie um FabricClient e chame o método GetDeployedApplicationHealthAsync em seu HealthManager. Para especificar parâmetros opcionais, use System.Fabric.Description.DeployedApplicationHealthQueryDescription.

```csharp
DeployedApplicationHealth health = fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName)).Result;
```

### Powershell
O cmdlet para obter a integridade do aplicativo implantado é Get-ServiceFabricDeployedApplicationHealth. Primeiro, conecte-se ao cluster com o cmdlet Connect-ServiceFabricCluster. Para descobrir onde um aplicativo está implantado, execute Get-ServiceFabricApplicationHealth e observe os filhos do aplicativo implantados.

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
Retorna a integridade de uma entidade de pacote de serviço implantada. Entrada:

- [obrigatório] Nome do aplicativo (URI), nome do nó (cadeia de caracteres) e nome do manifesto do serviço (cadeia de caracteres) que identificam o pacote de serviço implantado

- [opcional] Política de integridade do aplicativo usada para substituir a política do manifesto do aplicativo.

- [opcional] Filtro para retornar apenas eventos com determinado estado de integridade (por exemplo, retornar apenas erros ou avisos, etc).

### API
Para obter a integridade de um pacote de serviço implantado por meio de API, crie um FabricClient e chame o método GetDeployedServicePackageHealthAsync em seu HealthManager.

```csharp
DeployedServicePackageHealth health = fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName)).Result;
```

### Powershell
O cmdlet para obter a integridade do pacote de serviço implantado é Get-ServiceFabricDeployedServicePackageHealth. Primeiro, conecte-se ao cluster com o cmdlet Connect-ServiceFabricCluster. Para ver onde um aplicativo está implantado, execute Get-ServiceFabricApplicationHealth e observe aplicativos implantados. Para ver quais pacotes de serviço estão em um aplicativo, observe os filhos do pacote de serviço implantado na saída Get-ServiceFabricDeployedApplicationHealth.

O cmdlet a seguir obtém a integridade do pacote de serviço WordCount.Service do aplicativo fabric:/WordCount implantado no nó Node.1. A entidade tem relatórios System.Hosting para ativação bem-sucedida do pacote de serviço e do ponto de entrada e registro bem-sucedido do tipo de serviço.

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
As consultas gerais retornam a lista de entidades da Malha do Serviço do tipo especificado. Elas são expostas por meio de API (métodos em FabricClient.QueryManager), cmdlets do Powershell e REST. Essas consultas agregam subconsultas de vários componentes. Um deles é o [Repositório de Integridade](service-fabric-health-introduction.md#health-store), que preenche o estado de integridade agregada para cada resultado da consulta.

> [AZURE.NOTE]As consultas gerais retornam o estado da integridade da entidade agregada e não contêm os dados de integridade avançados. Se uma entidade não estiver íntegra, você poderá acompanhar as consultas de integridade para obter todas as informações de integridade, como eventos, estados da integridade dos filhos e avaliações não íntegras.

Se as consultas gerais retornarem o estado de integridade Desconhecido para uma entidade, é possível que o Repositório de Integridade não tenha completado os dados sobre a entidade ou a subconsulta para o Repositório de Integridade não foi bem-sucedida (por exemplo, erro de comunicação, repositório de integridade estava limitado etc.). Acompanhe uma consulta de integridade da entidade. Isso pode obter êxito, se a subconsulta tiver encontrado erros transitórios (por exemplo, problemas de rede) ou fornecerá mais detalhes sobre o motivo pelo qual a entidade não é exposta no Repositório de Integridade.

As consultas que contêm HealthState para entidades são:

- Lista de nós. Retorna os nós da lista no cluster.
  - Api: FabricClient.QueryManager.GetNodeListAsync.
  - Powershell: Get-ServiceFabricNode.
- Lista de aplicativos. Retorna a lista de aplicativos no cluster.
  - Api: FabricClient.QueryManager.GetApplicationListAsync.
  - Powershell: Get-ServiceFabricApplication.
- Lista de serviços. Retorna a lista de serviços em um aplicativo.
  - Api: FabricClient.QueryManager.GetServiceListAsync.
  - Powershell: Get-ServiceFabricService.
- Lista de partições. Retorna a lista de partições em um serviço.
  - Api: FabricClient.QueryManager.GetPartitionListAsync.
  - Powershell: Get-ServiceFabricPartition.
- Lista de réplicas. Retorna a lista de réplicas em uma partição.
  - Api: FabricClient.QueryManager.GetReplicaListAsync.
  - Powershell: Get-ServiceFabricReplica.
- Lista de aplicativos implantados. Retorna a lista de aplicativos implantados em um nó.
  - Api: FabricClient.QueryManager.GetDeployedApplicationListAsync.
  - Powershell: Get-ServiceFabricDeployedApplication.
- Lista de pacotes de serviço implantados. Retorna a lista de pacotes de serviço em um aplicativo implantado.
  - Api: FabricClient.QueryManager.GetDeployedServicePackageListAsync.
  - Powershell: Get-ServiceFabricDeployedApplication.

### Exemplos

O exemplo a seguir obtém os aplicativos não íntegros no cluster:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

O cmdlet a seguir obtém detalhes de aplicativo para o aplicativo fabric:/WordCount. Observe que o estado de integridade é Aviso.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "_WFDebugParams_" = "[{"ServiceManifestName":"WordCount.WebService","CodePackageName":"Code","EntryPointType":"Main"}]" }
```

O cmdlet a seguir obtém os serviços com o estado de integridade Aviso.

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
Durante a atualização monitorada do cluster e do aplicativo, a Malha do Serviço verifica a integridade para garantir que tudo esteja e permaneça íntegro. Se algo não estiver íntegro de acordo com a política configurada, a atualização será pausada para permitir interação do usuário ou será automaticamente revertida.

Durante a atualização do **cluster**, você pode obter o status de atualização do cluster, que incluirá avaliações não íntegras que apontam para o que não está íntegro no cluster. Se a atualização for revertida devido a problemas de integridade, o status da atualização manterá os últimos motivos da não integridade para que os administradores possam investigar o que deu errado. Da mesma forma, durante a atualização do **aplicativo**, o status da atualização do aplicativo contém as avaliações não íntegras.

O exemplo a seguir mostra o status da atualização do aplicativo para um aplicativo fabric:/WordCount modificado. Um watchdog relatou um Erro em uma de suas réplicas. A atualização está sendo revertida porque as verificações de integridade não são respeitadas.

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

Leia mais sobre a [Atualização de aplicativo da Malha do Serviço](service-fabric-application-upgrade.md).

## Solucionar problemas com integridade
Sempre que houver um problema no cluster ou aplicativo, observe a integridade do cluster ou do aplicativo para identificar o que está errado. As avaliações não íntegras mostrarão com detalhes o que disparou o estado não íntegro atual. Se necessário, analise detalhadamente as entidades filho não íntegras para entender os problemas.

## Próximas etapas
[Usando relatórios de integridade do sistema para solução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Adicionando relatórios de integridade personalizados da Malha de Serviço](service-fabric-report-health.md)

[Como monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização de aplicativo da Malha de Serviço](service-fabric-application-upgrade.md)
 

<!---HONumber=Oct15_HO3-->