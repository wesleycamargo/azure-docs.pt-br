<properties
   pageTitle="Solucionar problemas com relatórios de integridade do sistema | Microsoft Azure"
   description="Descreve os relatórios de integridade enviados por componentes do Service Fabric do Azure e seu uso para solucionar problemas de cluster ou de aplicativos."
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
   ms.date="01/20/2015"
   ms.author="oanapl"/>

# Usar relatórios de integridade do sistema para solução de problemas

Os componentes do Service Fabric apresentam relatórios de integridade prontos para uso sobre todas as entidades. O [repositório de integridade](service-fabric-health-introduction.md#health-store) cria e exclui entidades baseado nos relatórios do sistema. Ele também os organiza em uma hierarquia que captura interações de entidade.

> [AZURE.NOTE]Leia mais sobre o [Modelo de Integridade do Service Fabric](service-fabric-health-introduction.md) para entender os conceitos relacionados à integridade.

Os relatórios de integridade do sistema fornecem visibilidade da funcionalidade do cluster e de aplicativos e sinalizam problemas por meio da integridade. Para aplicativos e serviços, os relatórios de integridade do sistema verificam se as entidades são implementadas e estão se comportando corretamente da perspectiva do Service Fabric. Os relatórios não fornecem monitoramento de integridade da lógica de negócios do serviço nem detecção de processos travados. Os serviços de usuário podem enriquecer os dados de integridade com informações específicas à respectiva lógica.

> [AZURE.NOTE]Os relatórios de integridade dos watchdogs ficam visíveis somente *depois* que os componentes do sistema criam uma entidade. Quando uma entidade é excluída, o repositório de integridade exclui automaticamente todos os relatórios de integridade associados a ela. O mesmo acontece quando uma nova instância da entidade é criada (por exemplo, uma nova instância de réplica do serviço é criada). Todos os relatórios associados à instância antiga são excluídos e removidos do repositório.

Os relatórios de componentes do sistema são identificados por origem, que começa com o prefixo **"System"**. Os watchdogs não podem usar o mesmo prefixo para suas origens, pois os relatórios com parâmetros inválidos serão rejeitados. Vamos examinar alguns relatórios do sistema para entender o que os dispara e como corrigir possíveis problemas que eles representam.

> [AZURE.NOTE]O Service Fabric continua adicionando relatórios sobre condições de interesse que melhoram a visibilidade do que está acontecendo no cluster e no aplicativo.

## Relatórios de integridade do sistema de cluster
A entidade de integridade do cluster é criada automaticamente no repositório de integridade, de modo que se tudo estiver funcionando corretamente, ela não terá um relatório do sistema.

### Perda de ambiente
**System.Federation ** relata um erro quando detecta uma perda de ambiente. O relatório tem origem em nós individuais e a ID do nó é incluída no nome da propriedade. Se houver uma perda de ambiente em todo o anel do Service Fabric, geralmente podemos esperar dois eventos (ambos os lados da lacuna serão relatados). Se houver mais perdas de ambiente, haverá mais eventos.

O relatório especifica o tempo limite de concessão global como o tempo de vida útil. O relatório é enviado novamente a cada metade da duração do tempo de vida útil, desde que a condição permaneça ativa. O evento é removido automaticamente quando expira, de modo que se o modo de relatório for desativado, ele ainda será removido do repositório de integridade corretamente.

- **SourceId**: System.Federation
- **Propriedade**: começa com **Ambiente** e inclui informações sobre o nó.
- **Próximas etapas**: investigue por que o ambiente foi perdido (por exemplo, verifique a comunicação entre os nós do cluster).

## Relatórios de integridade do sistema de nó
**System.FM**, que representa o serviço Gerenciador de Failover, é a autoridade que gerencia informações sobre nós de cluster. Cada nó deve ter um relatório de System.FM mostrando seu estado. As entidades de nó são removidas quando o nó é desabilitado.

### Nó ativo/inativo
System.FM relata OK quando o nó ingressa no anel (está em execução). Ele relata um erro quando o nó é removido do anel (ele está desativado, seja para atualização ou simplesmente porque falhou). A hierarquia de integridade criada pelo repositório de integridade age nas entidades implantadas em correlação com relatórios de nó do System.FM. Ela considera o nó como um pai virtual de todas as entidades implantadas. As entidades implantadas nesse nó não serão expostas por meio de consultas se o nó estiver inativo ou se não for relatado, ou se o nó tiver uma instância diferente da instância associada às entidades. Quando System.FM relata o nó inativo ou reiniciado (nova instância), o repositório de integridade remove automaticamente as entidades implantadas que podem existir apenas no nó inativo ou na instância anterior do nó.

- **SourceId**: System.FM
- **Propriedade**: Estado
- **Próximas etapas**: se o nó estiver inativo para uma atualização, ele deverá aparecer novamente depois que for atualizado. Nesse caso, o estado de integridade deve ser alternado de volta para OK. Se o nó não voltar ou falhar, será preciso investigar mais.

A seguir, o evento System.FM com estado de integridade OK para o nó ativo:

```powershell

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 2
                        SentAt                : 4/24/2015 5:27:33 PM
                        ReceivedAt            : 4/24/2015 5:28:50 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 5:28:50 PM

```


### Expiração de certificado
**System.FabricNode** relata Aviso quando os certificados usados pelo nó estão prestes a expirar. Há três certificados por nó: **Certificate\_cluster**, **Certificate\_server** e **Certificate\_default\_client**. Quando falta pelo menos duas semanas para expirar, o estado de integridade do relatório é OK. Quando a expiração é dentro de duas semanas, o tipo de relatório é um aviso. O tempo de vida útil desses eventos é infinito; eles são removidos quando um nó deixa o cluster.

- **SourceId**: System.FabricNode
- **Propriedade**: começa com **Certificate** e contém mais informações sobre o tipo de certificado.
- **Próximas etapas**: atualize os certificados se eles estiverem prestes a expirar.

### Violação da capacidade de carga
O balanceador de carga do Service Fabric relata um aviso se detectar uma violação da capacidade do nó.

 - **SourceId**: System.PLB
 - **Propriedade**: começa com **Capacidade**.
 - **Próximas etapas**: verifique as métricas fornecidas e exiba a capacidade atual do nó.

## Relatórios de integridade do sistema de aplicativo
**System.CM**, que representa o serviço Gerenciador de Cluster, é a autoridade que gerencia informações sobre um aplicativo.

### Estado
System.CM relata OK quando o aplicativo é criado ou atualizado. Ele informa ao repositório de integridade quando o aplicativo é excluído para que este possa ser removido do repositório.

- **SourceId**: System.CM
- **Propriedade**: Estado
- **Próximas etapas**: se o aplicativo tiver sido criado, ele deve incluir o relatório de integridade do Gerenciador de Cluster. Caso contrário, verifique o estado do aplicativo emitindo uma consulta (por exemplo, cmdlet do PowerShell **Get-ServiceFabricApplication -ApplicationName *applicationName***).

Abaixo temos o evento State no aplicativo **fabric:/WordCount**:

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesHealthStateFilter ([System.Fabric.Health.HealthStateFilter]::None) -DeployedApplicationsHealthStateFilter ([System.Fabric.Health.HealthStateFilter]::None)

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 82
                                  SentAt                : 4/24/2015 6:12:51 PM
                                  ReceivedAt            : 4/24/2015 6:12:51 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/24/2015 6:12:51 PM
```

## Relatórios de integridade do sistema de serviço
**System.FM**, que representa o serviço Gerenciador de Failover, é a autoridade que gerencia informações sobre serviços.

### Estado
System.FM relata OK quando o serviço é criado. Ele exclui a entidade do repositório de integridade quando o serviço é excluído.

- **SourceId**: System.FM
- **Propriedade**: Estado

Veja a seguir o evento State no serviço **fabric:/WordCount/WordCountService**:

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService

ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Ok
PartitionHealthStates :
                        PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:01 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:01 PM
```

### Violação de réplicas não posicionadas
**System.PLB** relatará um aviso se não for possível encontrar um posicionamento para uma ou mais réplicas de serviço. O relatório é removido quando expira.

- **SourceId**: System.FM
- **Propriedade**: Estado
- **Próximas etapas**: verifique as restrições de serviço e o estado atual do posicionamento.

## Relatórios de integridade do sistema de partição
**System.FM**, que representa o serviço Gerenciador de Failover, é a autoridade que gerencia informações sobre partições de serviço.

### Estado
System.FM relata OK quando a partição é criada e está íntegra. Ele exclui a entidade do repositório de integridade quando a partição é excluída.

Se a partição estiver abaixo da contagem mínima de réplica, ela relatará um erro. Se a partição não estiver abaixo da contagem mínima de réplica, mas estiver abaixo da contagem de réplica de destino, ele relatará um aviso. Se a partição estiver na perda de quórum, o System.FM relatará um erro.

Outros eventos importantes incluem avisos quando a reconfiguração demorar mais que o esperado e quando a compilação demorar mais que o esperado. Os tempos esperados para compilação ou reconfiguração podem ser configurados baseados nos cenários de serviço. Por exemplo, se um serviço tem um terabyte de estado, como o Banco de Dados SQL, a compilação demora mais do que o necessário para um serviço com uma pequena quantidade de estado.

- **SourceId**: System.FM
- **Propriedade**: Estado
- **Próximas etapas**: se o estado de integridade não estiver OK, é possível que algumas réplicas não sejam criadas/abertas/promovidas para o local primário ou secundário corretamente. Em muitos casos, a causa raiz é um bug de serviço na implementação da função de abertura ou de alteração.

Veja a seguir uma partição íntegra.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/StatelessPiApplication/StatelessPiService | Get-ServiceFabricPartitionHealth
PartitionId           : 29da484c-2c08-40c5-b5d9-03774af9a9bf
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 38
                        SentAt                : 4/24/2015 6:33:10 PM
                        ReceivedAt            : 4/24/2015 6:33:31 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:33:31 PM
```

O exemplo a seguir mostra a integridade de uma partição que está abaixo da contagem de réplica de destino. A próxima etapa é obter a descrição da partição, que mostra como ela é configurada: **MinReplicaSetSize** é dois e **TargetReplicaSetSize** é sete. Em seguida, obtenha o número de nós no cluster: cinco. Nesse caso, duas réplicas não podem ser posicionadas.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasHealthStateFilter ([System.Fabric.Health.HealthStateFilter]::None)

PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 37
                        SentAt                : 4/24/2015 6:13:12 PM
                        ReceivedAt            : 4/24/2015 6:13:31 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/24/2015 6:13:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService

PartitionId            : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
PartitionKind          : Int64Range
PartitionLowKey        : 1
PartitionHighKey       : 26
PartitionStatus        : Ready
LastQuorumLossDuration : 00:00:00
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 7
HealthState            : Warning
DataLossNumber         : 130743727710830900
ConfigurationNumber    : 8589934592


PS C:\> @(Get-ServiceFabricNode).Count
5
```

### Violação da restrição de réplica
**System.PLB** relatará um aviso se detectar violação da restrição de réplica e não puder posicionar réplicas da partição.

- **SourceId**: System.PLB
- **Propriedade**: começa com **ReplicaConstraintViolation**.

## Relatórios de integridade do sistema de réplica
**System.RA**, que representa o componente agente de reconfiguração, é a autoridade do estado da réplica.

### Estado
**System.RA** relata OK quando a réplica é criada.

- **SourceId**: System.RA
- **Propriedade**: Estado

Veja a seguir uma réplica íntegra:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth
PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
ReplicaId             : 130743727717237310
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743727718018580
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:02 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:02 PM
```

### Status aberto da réplica
A descrição desse relatório de integridade contém a hora de início (UTC) da chamada à API invocada.

**System.RA** relatará um aviso se a réplica aberta demorar mais que o período configurado (padrão: 30 minutos). Quando a API afeta a disponibilidade do serviço, o relatório é emitido muito mais rapidamente (intervalo configurável, padrão de 30 segundos). Isso inclui o tempo necessário para abrir o replicador e abrir o serviço. A propriedade muda para OK quando a abertura é concluída.

- **SourceId**: System.RA
- **Propriedade**: **ReplicaOpenStatus**.
- **Próximas etapas**: se o estado de integridade não estiver OK, investigue por que a réplica aberta demora mais que o esperado.

### Chamada à API para serviço lento
**System.RAP** e **System.Replicator** relatam um aviso se uma chamada no código do serviço do usuário demora mais que o tempo configurado. Esse status é removido quando a chamada é concluída.

- **SourceId**: System.RAP ou System.Replicator
- **Propriedade**: o nome da API lenta. A descrição fornece mais detalhes sobre o tempo em que a API esteve pendente.
- **Próximas etapas**: investigue por que a chamada demora mais que o esperado.

O exemplo a seguir mostra uma partição nas etapas de investigação e na perda de quórum realizadas para entender o motivo. Uma das réplicas tem o estado de integridade de aviso; portanto, obtemos sua integridade. Ele mostra que a operação do serviço demora mais que o esperado, um evento relatado por System.RAP. Depois que a informação é recebida, a próxima etapa será examinar o código de serviço e investigá-lo. Nesse caso, a implementação de **RunAsync** do serviço com estado lança uma exceção sem tratamento. Observe que as réplicas são recicladas, de modo que você não pode ver nenhuma réplica no estado de aviso. Você pode tentar novamente obter o estado de integridade e examinar as diferenças na ID de réplica. Em alguns casos, isso pode lhe dar pistas.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful | Get-ServiceFabricPartitionHealth

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
AggregatedHealthState : Error
UnhealthyEvaluations  :
                        Error event: SourceId='System.FM', Property='State'.

ReplicaHealthStates   :
                        ReplicaId             : 130743748372546446
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746168084332
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746195428808
                        AggregatedHealthState : Warning

                        ReplicaId             : 130743746195428807
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Error
                        SequenceNumber        : 182
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:31 PM
                        TTL                   : Infinite
                        Description           : Partition is in quorum loss.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 4/24/2015 6:51:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful

PartitionId            : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
PartitionKind          : Int64Range
PartitionLowKey        : -9223372036854775808
PartitionHighKey       : 9223372036854775807
PartitionStatus        : InQuorumLoss
LastQuorumLossDuration : 00:00:13
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 3
HealthState            : Error
DataLossNumber         : 130743746152927699
ConfigurationNumber    : 227633266688

PS C:\> Get-ServiceFabricReplica 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

ReplicaId           : 130743746195428808
ReplicaAddress      : PartitionId: 72a0fb3e-53ec-44f2-9983-2f272aca3e38, ReplicaId: 130743746195428808
ReplicaRole         : Primary
NodeName            : Node.3
ReplicaStatus       : Ready
LastInBuildDuration : 00:00:01
HealthState         : Warning

PS C:\> Get-ServiceFabricReplicaHealth 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
ReplicaId             : 130743746195428808
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.RAP', Property='ServiceOpenOperationDuration', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743756170185892
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:33 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 7:00:33 PM

                        SourceId              : System.RAP
                        Property              : ServiceOpenOperationDuration
                        HealthState           : Warning
                        SequenceNumber        : 130743756399407044
                        SentAt                : 4/24/2015 7:00:39 PM
                        ReceivedAt            : 4/24/2015 7:00:59 PM
                        TTL                   : Infinite
                        Description           : Start Time (UTC): 2015-04-24 19:00:17.019
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/24/2015 7:00:59 PM
```

Ao iniciar o aplicativo com defeito no depurador, as janelas Eventos de Diagnóstico mostram a exceção lançada por RunAsync:

![Eventos de diagnóstico do Visual Studio 2015: falha de RunAsync em fabric:/HelloWorldStatefulApplication.][1]

Eventos de diagnóstico do Visual Studio 2015: falha de RunAsync em **fabric:/HelloWorldStatefulApplication**.

[1]: ./media/service-fabric-understand-and-troubleshoot-with-system-health-reports/servicefabric-health-vs-runasync-exception.png


### Fila de replicação cheia
**System.Replicator** relatará um aviso se a fila de replicação estiver cheia. No local primário, isso geralmente acontece porque uma ou mais réplicas secundárias estão lentas para confirmar as operações. No local secundário, isso geralmente acontece quando o serviço está lento para aplicar as operações. O aviso será removido quando a fila não estiver mais cheia.

- **SourceId**: System.Replicator
- **Propriedade**: **PrimaryReplicationQueueStatus** ou **SecondaryReplicationQueueStatus**, dependendo da função da réplica.

## Relatórios de integridade do sistema DeployedApplication
**System.Hosting** é a autoridade em entidades implantadas.

### Ativação
System.Hosting relata OK quando um aplicativo é ativado com êxito no nó. Caso contrário, ele relata um erro.

- **SourceId**: System.Hosting
- **Propriedade**: ativação, incluindo a versão de distribuição
- **Próximas etapas**: se o aplicativo não está íntegro, investigue por que a ativação falhou.

Veja a seguir uma ativação com êxito:

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName Node.1 -ApplicationName fabric:/WordCount

ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130743727751144415
                                     SentAt                : 4/24/2015 6:12:55 PM
                                     ReceivedAt            : 4/24/2015 6:13:03 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### Baixar
**System.Hosting** relatará um erro se o download do pacote de aplicativos falhar.

- **SourceId**: System.Hosting
- **Propriedade**: **Download:*RolloutVersion***
- **Próximas etapas**: investigue por que o download falhou no nó.

## Relatórios de integridade do sistema DeployedServicePackage
**System.Hosting** é a autoridade em entidades implantadas.

### Ativação do pacote de serviço
System.Hosting relatará OK se a ativação do pacote de serviço no nó for bem-sucedida. Caso contrário, ele relata um erro.

- **SourceId**: System.Hosting
- **Propriedade**: Ativação
- **Próximas etapas**: investigue por que a ativação falhou.

### Ativação do pacote de códigos
**System.Hosting** relatará OK para cada pacote de códigos se a ativação for bem-sucedida. Se houver falha na ativação, ele relatará um aviso conforme configurado. Se **CodePackage** falhar na ativação ou encerrar com um erro maior que o **CodePackageHealthErrorThreshold** configurado, a hospedagem relatará um erro. Se um pacote de serviço contém vários pacotes de código, um relatório de ativação é gerado para cada um.

- **SourceId**: System.Hosting
- **Propriedade**: usa o prefixo **CodePackageActivation** e contém o nome do pacote de códigos e o ponto de entrada como **CodePackageActivation:*CodePackageName*:*SetupEntryPoint/EntryPoint*** (por exemplo, **CodePackageActivation:Code:SetupEntryPoint**)

### Registro do tipo de serviço
**System.Hosting** relatará OK se o tipo de serviço for registrado com êxito. Ele relatará um erro se o registro não for feito na hora (configurada usando **ServiceTypeRegistrationTimeout**). Se o tipo de serviço é removido do registro do nó, isso ocorre porque o tempo de execução foi fechado. A hospedagem relata um aviso.

- **SourceId**: System.Hosting
- **Propriedade**: usa o prefixo **ServiceTypeRegistration** e contém o nome do tipo de serviço (por exemplo, **ServiceTypeRegistration:FileStoreServiceType**)

Veja a seguir um pacote de serviço íntegro implantado.

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName Node.1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130743727751456915
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130743727751613185
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 130743727753644473
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### Baixar
**System.Hosting** relatará um erro se o download do pacote de serviço falhar.

- **SourceId**: System.Hosting
- **Propriedade**: **Download:*RolloutVersion***
- **Próximas etapas**: investigue por que o download falhou no nó.

### Validação da atualização
**System.Hosting** relatará um erro se a validação durante a atualização falhar ou se a atualização falhar no nó.

- **SourceId**: System.Hosting
- **Propriedade**: usa o prefixo **FabricUpgradeValidation** e contém a versão de atualização.
- **Descrição**: aponta para o erro encontrado.

## Próximas etapas
[Como exibir relatórios de integridade do Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização de aplicativos do Service Fabric](service-fabric-application-upgrade.md)

<!---HONumber=AcomDC_0121_2016-->