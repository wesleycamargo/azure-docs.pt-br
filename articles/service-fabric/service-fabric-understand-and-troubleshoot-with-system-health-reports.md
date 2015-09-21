<properties
   pageTitle="Usando relatórios de integridade do sistema para solução de problemas"
   description="Descreve os relatórios de Integridade do Sistema e como usá-los para solução de problemas de cluster ou problemas de aplicativos"
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

# Usando relatórios de integridade do sistema para solução de problemas

Relatório de componentes da Malha do Serviço pronto para uso sobre todas as entidade no cluster. O [Repositório de Integridade](service-fabric-health-introduction.md#health-store) cria e exclui entidades com base nos relatórios do sistema e as organiza em uma hierarquia que captura interações de entidade.

> [AZURE.NOTE]Leia mais sobre o [Modelo de Integridade da Malha do Serviço](service-fabric-health-introduction.md) para entender os conceitos relacionados à integridade.

Os relatórios de integridade do Sistema fornecem visibilidade da funcionalidade do cluster e de aplicativos e sinalizam problemas por meio da integridade. Para aplicativos e serviços, os relatórios de integridade do sistema verificam se as entidades são implementadas e estão se comportando corretamente da perspectiva da Malha do Serviço. Os relatórios não fornecem monitoramento de integridade da lógica de negócios do serviço nem detecção de processos travados. Os serviços de usuário podem enriquecer os dados de integridade com informações específicas à respectiva lógica.

> [AZURE.NOTE]Os relatórios de integridade dos watchdogs ficam visíveis apenas **depois** que os componentes do sistema criam uma entidade. Quando uma entidade é excluída, o Repositório de Integridade exclui automaticamente todos os relatórios de integridade associados a ela. O mesmo ocorre quando uma nova instância da entidade é criada (por exemplo, uma nova instância da réplica de serviço é criada): todos os relatórios associados à instância antiga são excluídos e removidos do repositório.

Os relatórios de componentes do sistema são identificados por origem, que começa com o prefixo *"System"*. Os watchdogs não podem usar o mesmo prefixo para suas origens, pois os relatórios serão rejeitados com parâmetros inválidos. Vamos examinar alguns relatórios do sistema, entender o que os dispara e como corrigir possíveis problemas que eles representam.

> [AZURE.NOTE]A Malha do Serviço continua adicionando relatórios para condições de interesse que melhoram a visibilidade do que está acontecendo no cluster/aplicativo.

## Relatórios de integridade do sistema de cluster
A entidade de integridade do cluster é criada automaticamente no repositório de integridade, de modo que se tudo estiver funcionando corretamente, ela não terá um relatório do sistema.

### Perda de ambiente
A Federação do Sistema relata um Erro quando detecta uma perda de ambiente. O relatório tem origem em nós individuais e a ID do nó é incluída no nome da propriedade. Se houver uma perda de ambiente em todo o anel da Malha do Serviço, geralmente, podemos esperar dois eventos (ambos os lados da lacuna serão relatados). Se houver mais perdas de ambiente, haverá mais eventos. O relatório especifica o tempo limite da Concessão Global como a TTL e o relatório é reenviado a cada metade da TTL, desde que a condição ainda esteja ativa. O evento é removido automaticamente quando expira, de modo que se o modo de relatório for desativado, ele ainda será removido corretamente do Repositório de Integridade.

- SourceId: System.Federation
- Propriedade: começa com "Ambiente" e inclui informações sobre o nó.
- Próximas etapas: investigue por que o ambiente foi perdido. Por exemplo, verifique a comunicação entre os nós de cluster.

## Relatórios de integridade do sistema de nó
System.FM, que representa o serviço Gerenciador de Failover, é a autoridade que gerencia informações sobre nós de cluster. Qualquer nó deve ter um relatório de System.FM mostrando seu estado. As entidades de nó são removidas quando o nó é desabilitado.

### Nó ativo/inativo
System.FM relata OK quando o nó se une ao anel (ele é ativado) e Erro quando o nó se afasta do anel (está desativado, para atualização ou simplesmente falhou). A hierarquia de integridade criada pelo Repositório de Integridade age nas entidades implantadas em correlação com relatórios de nó do System.FM. Ela considera o nó como um pai virtual de todas as entidades implantadas. As entidades implantadas nesse nó não serão expostas por meio de consultas se o nó estiver inativo ou se não for relatado, ou se o nó tiver uma instância diferente da instância associada às entidades. Quando FM relata o nó inativo ou reiniciado (nova instância), o Repositório de Integridade remove automaticamente as entidades implantadas que podem existir apenas no nó inativo ou na instância anterior do nó.

- SourceId: System.FM
- Propriedade: Estado
- Próximas etapas: se o nó estiver inativo para atualização, ele deverá aparecer depois de atualizado, caso em que o estado da integridade deverá ser alternado de volta para OK. Se o nó não voltar ou falhar, será preciso investigar mais.

A seguir, o evento System.FM com estado de integridade OK para nó ativo:

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
System.FabricNode relata Aviso quando os certificados usados pelo nó estão prestes a expirar. Há três certificados por nó: Certificate\_cluster, Certificate\_server e Certificate\_default\_client. Quando a expiração for em até pelo menos duas semanas, o tipo de relatório será OK; se a expiração ocorrer dentro de duas semanas, o tipo de relatório será Aviso. A TTL desses eventos é infinita; eles são removidos quando um nó deixa o cluster.

- SourceId: System.FabricNode
- Propriedade: começa com "Certificate" e contém mais informações sobre o tipo de certificado.
- Próximas etapas: atualize os certificados se eles estiverem prestes a expirar.

### Violação da capacidade de carga
O balanceador de carga da Malha do Serviço relata Aviso se detectar uma violação da capacidade do nó.

 - SourceId: System.PLB
 - Propriedade: começa com "Capacidade".
 - Próximas etapas: verifique as métricas fornecidas e exiba a capacidade atual do nó.

## Relatórios de integridade do sistema de aplicativo
System.CM, que representa o serviço do Gerenciador de Cluster, é a autoridade que gerencia informações sobre aplicativos.

### Estado
System.CM relata OK quando o aplicativo é criado ou atualizado. Ele informa o Repositório de Integridade quando o aplicativo é excluído para que ele possa ser removido do repositório.

- SourceId: System.CM
- Propriedade: Estado
- Próximas etapas: se o aplicativo foi criado, ele deve ter o relatório de integridade CM. Caso contrário, verifique o estado do aplicativo emitindo uma consulta (por exemplo, cmdlet Get-ServiceFabricApplication -ApplicationName <applicationName> do Powershell).

A seguir, o evento State no aplicativo fabric:/WordCount.

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
System.FM, que representa o serviço do Gerenciador de Failover, é a autoridade que gerencia informações sobre serviços.

### Estado
System.FM relata OK quando o serviço é criado. Ele exclui a entidade do Repositório de Integridade quando o serviço é excluído.

- SourceId: System.FM
- Propriedade: Estado.

Veja a seguir o evento de Estado no serviço fabric:/WordCount/WordCountService.

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
System.PLB relatará Aviso se não puder encontrar um posicionamento para uma ou mais das réplicas de serviço. O relatório é removido quando expira.

- SourceId: System.FM
- Propriedade: Estado.
- Próximas etapas: verifique as restrições do serviço. Verifique o estado atual do posicionamento.

## Relatórios de integridade do sistema de partição
System.FM, que representa o serviço do Gerenciador de Failover, é a autoridade que gerencia informações sobre partições de serviço.

### Estado
System.FM relata OK quando a partição é criada e está íntegra. Ele exclui a entidade do Repositório de Integridade quando a partição é excluída. Se a partição estiver abaixo da contagem mínima de réplica, ele relatará Erro. Se a partição não estiver abaixo da contagem mínima de réplica, mas abaixo da contagem de réplica de destino, ele relatará Aviso. Se a partição estiver na perda de quórum, o FM relatará Erro. Outros eventos importantes são: Aviso quando a reconfiguração demorar mais que o esperado e quando a compilação demorar mais que o esperado. Os tempos esperados para compilação ou reconfiguração podem ser configurados com base nos cenários de serviço. Por exemplo, se um serviço tiver TB de estado, como o SQL Azure, a compilação demorará mais que um serviço com uma pequena quantidade de estado.

- SourceId: System.FM
- Propriedade: Estado.
- Próximas etapas: se o estado de integridade não for OK, é possível que algumas réplicas não sejam criadas/abertas/promovidas para o local primário ou secundário corretamente. Em muitos casos, a causa raiz é um bug de serviço na implementação da função de abertura ou alteração.

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

O exemplo a seguir mostra a integridade de uma partição que está abaixo da contagem de réplica de destino. Próximas etapas: obtenha a descrição da partição, que mostra como ela foi configurada: MinReplicaSetSize é 2, TargetReplicaSetSize é 7. Em seguida, obtenha o número de nós no cluster: 5. Nesse caso, 2 réplicas não podem ser posicionadas.

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
System.PLB relatará Aviso se detectar violação da restrição de réplica e não puder posicionar réplicas da partição.

- SourceId: System.PLB
- Propriedade: começa com "ReplicaConstraintViolation".

## Relatórios de integridade do sistema de réplica
System.RA, que representa o componente Agente de Reconfiguração, é a autoridade do estado da réplica.

### Estado
System.RA relata OK quando a réplica é criada.

- SourceId: System.RA
- Propriedade: Estado.

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
A descrição desse relatório de integridade contém a hora de início (UTC) da chamada à API invocada. System.RA relatará Aviso se a réplica aberta demorar mais que o período configurado (padrão: 30 minutos). Quando a API afeta a disponibilidade do serviço, o relatório é emitido muito mais rapidamente (intervalo configurável, padrão de 30 segundos). Esse tempo inclui o tempo que o replicador e o serviço levam para serem abertos. A propriedade muda para OK quando a abertura é concluída.

- SourceId: System.RA
- Propriedade: ReplicaOpenStatus.
- Próximas etapas: se o estado de integridade não estiver OK, verifique por que a réplica aberta demora mais que o esperado.

### Chamada à API para serviço lento
System.RAP e System.Replicator relatam Aviso se uma chamada no código do serviço do usuário demorar mais que o tempo configurado. Esse status é removido quando a chamada é concluída.

- SourceId: System.RAP ou System.Replicator
- Propriedade: o nome da API lenta. A descrição fornece mais detalhes sobre o tempo que a API esteve pendente.
- Próximas etapas: investigue por que a chamada demora mais que o esperado.

O exemplo a seguir mostra uma partição nas etapas de investigação e na perda de quórum realizadas para entender o motivo. Uma das réplicas tem o estado de integridade Aviso, então obtemos sua integridade. Ele mostra que a operação do serviço demora mais que o esperado, evento relatado por System.RAP. Depois dessa informação, a próxima etapa é investigar o código de serviço. Nesse caso, a implementação de RunAsync do serviço com estado lança uma exceção sem tratamento. Observe que as réplicas são recicladas, de modo que você não pode ver nenhuma réplica no estado Aviso. Tente novamente obter a integridade e observe se há alguma diferença na ID da réplica que, em alguns casos, fornece dicas.

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

![Eventos de Diagnóstico do Visual Studio 2015: falha de RunAsync em fabric:/HelloWorldStatefulApplication.][1]

Eventos de Diagnóstico do Visual Studio 2015: falha de RunAsync em fabric:/HelloWorldStatefulApplication.

[1]: ./media/service-fabric-understand-and-troubleshoot-with-system-health-reports/servicefabric-health-vs-runasync-exception.png


### Fila de replicação cheia
System.Replicator relatará Aviso se a fila de replicação estiver cheia. No local primário, isso geralmente acontece porque uma ou mais réplicas secundárias são lentas para confirmar as operações. No local secundário, isso geralmente acontece quando o serviço é lento para aplicar as operações. O status Aviso será removido quando a fila não estiver mais cheia.

- SourceId: System.Replicator
- Propriedade: PrimaryReplicationQueueStatus ou SecondaryReplicationQueueStatus, dependendo da função da réplica.

## Relatórios de integridade do sistema de DeployedApplication
System.Hosting é a autoridade em entidades implantadas.

### Ativação
System.Hosting relata OK quando um aplicativo é ativado com êxito no nó; caso contrário, relata Erro.

- SourceId: System.Hosting
- Propriedade: Ativação. Inclui a versão de distribuição.
- Próximas etapas: se não íntegro, investigue por que a ativação falhou.

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
System.Hosting relatará Erro se o download do pacote de aplicativo falhar.

- SourceId: System.Hosting
- Propriedade: Download:<RolloutVersion>
- Próximas etapas: investigue por que o download falhou no nó.

## Relatórios de integridade do sistema de DeployedServicePackage
System.Hosting é a autoridade em entidades implantadas.

### Ativação do pacote de serviço
System.Hosting relatará OK se a ativação do pacote de serviço no nó for bem-sucedida; caso contrário, relatará Erro.

- SourceId: System.Hosting
- Propriedade: Ativação.
- Próximas etapas: investigue por que a ativação falhou.

### Ativação do pacote de códigos
System.Hosting relatará OK para cada pacote de códigos se a ativação for bem-sucedida. Se a ativação falhar, ele relatará Aviso, conforme configurado; se CodePackage falhou na ativação ou encerrou com mais erro do que o configurado, 'CodePackageHealthErrorThreshold', Hosting relatará Erro. Se houver vários pacotes de códigos em um pacote de serviço, haverá um relatório de Ativação gerado para cada um deles.

- SourceId: System.Hosting
- Propriedade: prefixo CodePackageActivation e contém o nome do pacote de códigos e o ponto de entrada. CodePackageActivation:<CodePackageName>:<SetupEntryPoint/EntryPoint>. Por exemplo, CodePackageActivation:Code:SetupEntryPoint.

### Registro do tipo de serviço
System.Hosting relatará OK se o tipo de serviço foi registrado com êxito. Ele relatará Erro se o registro não foi feito na hora (configurada com ServiceTypeRegistrationTimeout). Se o tipo de serviço não estiver registrado no nó é porque o tempo de execução foi encerrado. Hosting relata Aviso.

- SourceId: System.Hosting
- Propriedade: prefixo ServiceTypeRegistration e contém o nome do tipo de serviço. Por exemplo, "ServiceTypeRegistration:FileStoreServiceType"

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
System.Hosting relatará Erro se o download do pacote de serviço falhar.

- SourceId: System.Hosting
- Propriedade: Download:<RolloutVersion>
- Próximas etapas: investigue por que o download falhou no nó.

### Validação da atualização
System.Hosting relatará Erro se a validação durante a atualização falhar ou se a atualização falhar no nó.

- SourceId: System.Hosting
- Propriedade: prefixo FabricUpgradeValidation, contém a versão de atualização.
- Descrição: aponta para o erro encontrado.

## Próximas etapas
[Como exibir relatórios de integridade da Malha de Serviço](service-fabric-view-entities-aggregated-health.md)

[Como monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização de aplicativo da Malha de Serviço](service-fabric-application-upgrade.md)
 

<!---HONumber=Sept15_HO2-->