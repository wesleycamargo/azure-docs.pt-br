<properties
   pageTitle="Solucionar problemas de atualizações de aplicativa Malha do Serviço"
   description="Este artigo aborda alguns problemas comuns em torno da atualização de um aplicativa Malha do Serviço e como resolvê-los."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/11/2015"
   ms.author="subramar"/>

# Solucionar problemas de atualizações de aplicativo

Este artigo aborda alguns dos problemas comuns na atualização de um aplicativa Malha do Serviço e como resolvê-los.

## Solucionar problemas de atualização de um aplicativo com falha

Quando uma atualização falha, a saída do comando **Get-ServiceFabricApplicationUpgrade** contém algumas informações adicionais para depurar a falha. Essas informações podem ser usadas para:

1. Identificar o tipo de falha
2. Identificar o motivo da falha
3. Isolar os componentes com falha para obter mais informações

Essas informações estarão disponíveis assim que a Malha do Serviço detectar a falha, independentemente de a **FailureAction** reverter ou suspender a atualização.

### Identificar o tipo de falha

Na saída de **Get-ServiceFabricApplicationUpgrade**, o **FailureTimestampUtc** identifica o carimbo de hora (UTC) em que uma falha de atualização foi detectada pela Malha do Serviço e o **FailureAction**, acionado. O **FailureReason** identifica uma das três possíveis causas de alto nível da falha:

1. UpgradeDomainTimeout: indica que um determinado domínio de atualização demorou muito para ser concluído e o **UpgradeDomainTimeout** expirou.
2. OverallUpgradeTimeout: indica que a atualização global demorou muito para ser concluída e o **UpgradeTimeout** expirou.
3. HealthCheck: indica que, após a atualização de um Domínio de Atualização, o aplicativo permaneceu não íntegro de acordo com as políticas de integridade especificadas e o **HealthCheckRetryTimeout** expirou.

Essas entradas somente serão exibidas na saída quando a atualização falhar e começar a reversão. Outras Informações serão exibidas dependendo do tipo de falha.

### Investigar tempos limite de atualização

As falhas de tempo de execução de atualização são geralmente causadas por problemas de disponibilidade do serviço. A saída abaixo é típica de atualizações em que as réplicas ou instâncias de serviço não conseguem iniciar na nova versão do código. O campo **UpgradeDomainProgressAtFailure** captura um instantâneo de trabalhos de atualização pendentes no momento da falha.

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
~~~

Neste exemplo, podemos ver que a atualização falhou no domínio de atualização *MYUD1* e duas partições (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* e *4b43f4d8-b26b-424e-9307-7a7a62e79750*) ficaram presas, incapazes de colocar as réplicas primárias (*WaitForPrimaryPlacement*) nos nós de destino *Node1* e *Node4*. O comando **Get-ServiceFabricNode** pode ser usado para verificar se esses dois nós estão no domínio de atualização *MYUD1*. O *UpgradePhase* diz *PostUpgradeSafetyCheck*, que significa que essas verificações de segurança estão ocorrendo depois que todos os nós no domínio de atualização concluíram a atualização. Todas essas informações combinadas apontam para um possível problema com a nova versão do código do aplicativo. Os problemas mais comuns são erros de serviço em aberto ou promoção para caminhos de código principal.

Um *UpgradePhase* de *PreUpgradeSafetyCheck* significa que houve problemas na preparação do domínio de atualização antes de realmente executar a atualização. Nesse caso, os problemas mais comuns são erros de serviço no fechamento ou rebaixamento de caminhos de código principal.

O **UpgradeState** atual é *RollingBackCompleted*; portanto, a atualização original deve ter sido realizada com uma reversão **FailureAction**, que automaticamente reverteu a atualização em caso de falha. Se a atualização original tiver sido realizada com um **FailureAction** manual, a atualização estaria em um estado suspenso para permitir a depuração dinâmica do aplicativo.

### Investigar falhas de verificação de integridade

As falhas de verificação de integridade podem ser acionadas por uma variedade de outros problemas que podem ocorrer depois que todos os nós em um domínio de atualização terminam a atualização e passam por todas as verificações de segurança. A saída abaixo é típica de uma falha na atualização devido a falhas de verificação de integridade. O campo **UnhealthyEvaluations** captura um instantâneo de todas as verificações de integridade com falha no momento da falha de atualização de acordo com a [política de integridade](service-fabric-health-introduction.md) definida pelo usuário.

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
~~~

A investigação das falhas de verificação de integridade exige primeiro uma compreensão do modelo de integridade da Malha do Serviço, mas mesmo sem essa compreensão detalhada, podemos ver que dois serviços não estão íntegros: *malha:/DemoApp/Svc3* e *malha:/DemoApp/Svc2*, junto com os relatórios de erro de integridade ("InjectedFault" nesse caso). Neste exemplo, dois de quatro serviços não estão íntegros, abaixo da meta padrão de 0% de não integridade (*MaxPercentUnhealthyServices*).

A atualização foi suspensa com a falha pela especificação de uma **FailureAction** manual quando iniciada a atualização, para que possamos investigar o sistema dinâmico no estado de falha, se for o caso, antes de tomar medidas adicionais.

### Recuperar de uma atualização suspensa

Com uma reversão **FailureAction**, não há nenhuma recuperação necessária, já que a atualização será automaticamente revertida após a falha. Com uma **FailureAction** manual, há várias opções de recuperação:

1. Disparar manualmente uma reversão
2. Continuar com o restante da atualização manualmente
3. Continuar a atualização monitorada

O comando **Start-ServiceFabricApplicationRollback** pode ser usado a qualquer momento para iniciar a reversão do aplicativo. Depois que o comando retornar com êxito, a solicitação de reversão foi registrada no sistema e começará em breve.

O comando **ServiceFabricApplicationUpgrade retomar** pode ser usado para percorrer o restante da atualização manualmente, um domínio de atualização de cada vez. Nesse modo, somente as verificações de segurança serão realizadas pelo sistema; nenhuma outra verificação de integridade será realizada. Esse comando só pode ser usado quando o *UpgradeState* mostra *RollingForwardPending*, o que significa que o domínio de atualização atual concluiu a atualização, mas o domínio de atualização seguinte ainda não foi iniciado (pendente).

O comando **ServiceFabricApplicationUpgrade atualização** pode ser usado para retomar a atualização monitorada com a realização tanto da verificação de segurança quanto da de integridade.

~~~
PS D:\temp> Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored

UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :

PS D:\temp>
~~~

A atualização continuará do domínio de atualização onde ela foi suspensa da última vez e usará os mesmos parâmetros de atualização e políticas de integridade de antes. Se necessário, qualquer um dos parâmetros de atualização e políticas de integridade mostrados na saída acima pode ser alterado no mesmo comando ao retomar a atualização. Nesse exemplo, a atualização foi reiniciada no modo monitorado com todos os outros parâmetros inalterados; logo, usando os mesmos parâmetros e políticas de integridade de antes.

## Solução de problemas adicionais

### A Malha do Serviço não está seguindo as políticas de integridade especificadas

Possível causa 1:

A Malha do Serviço converte todas as porcentagens em números reais de entidades (por exemplo, réplicas, partições e serviços) para avaliação de integridade e sempre arredonda para cima para o próximo número de entidades inteiro. Por exemplo, se o máximo _MaxPercentUnhealthyReplicasPerPartition_ é 21% e há 5 réplicas, a Malha do Serviço permitirá que até duas réplicas (ou seja, `Math.Ceiling (5*0.21)`) sejam não íntegras quando avaliar a integridade da partição. As políticas de integridade devem ser definidas adequadamente para levar isso em conta.

Possível causa 2:

as políticas de integridade são especificadas em termos de percentuais do total de serviço e não em instâncias de serviço específicas. Por exemplo, antes de uma atualização, suponha que um aplicativo tem quatro instâncias A, B, C e D, onde o serviço D não está íntegro, mas não tem impacto significativo no aplicativo. Vamos ignorar o serviço conhecido não íntegro D durante a atualização e definir o parâmetro *MaxPercentUnhealthyServices* a 25%, supondo que apenas A, B e C precisam ser íntegros. No entanto, durante a atualização, D podem se tornar íntegro enquanto C se torna não íntegro. A atualização continuaria concluída com êxito nesse caso, já que somente 25% dos serviços estão não íntegros, o que pode resultar em erros inesperados devido ao fato de C estar inesperadamente não íntegro em vez de D. Nessa situação, D deve ser modelado como um tipo diferente de serviço de A, B e C. Como as políticas de integridade podem ser especificadas baseadas no tipo de serviço, isso permite aplicar limites de porcentagem de não integridade diferentes para diferentes serviços baseados em suas funções no aplicativo.

### Eu não especifiquei uma política de integridade para a atualização do aplicativo, mas a atualização ainda falha em alguns tempos limite que eu nunca especifiquei

Quando as políticas de integridade não são fornecidas para a solicitação de atualização, elas são tiradas do *ApplicationManifest.xml* da versão atual do aplicativo (por exemplo, se a atualização do aplicativo X for de v1 para v2, as políticas de integridade do aplicativo especificadas para o aplicativo X v1 serão usadas). Se uma política de integridade diferente tiver que ser usada para a atualização, a política precisa ser especificada como parte da chamada de API de atualização de aplicativo. Observe que as políticas especificadas como parte da chamada de API somente se aplicam durante a atualização. Depois que a atualização é concluída, as políticas especificadas no *ApplicationManifest.xml* são usados.

### Tempo limite incorreto especificado.

Os usuários podem já ter se perguntado sobre o que acontece se os tempos limite forem definidos de forma inconsistente, por exemplo, ter um *UpgradeTimeout* menor que o *UpgradeDomainTimeout*. A resposta é que um erro é retornado. Outros casos em que isso pode acontecer é se *UpgradeDomainTimeout* for menor do que a soma de *HealthCheckWaitDuration* e *HealthCheckRetryTimeout*, ou se *UpgradeDomainTimeout* for menor do que a soma de *HealthCheckWaitDuration* e *HealthCheckStableDuration*.

### Minhas atualizações estão demorando muito

O tempo necessário para concluir uma atualização depende de várias verificações de integridade e tempos limite especificados, que por sua vez são dependentes do tempo necessário para seu aplicativo atualizar (incluindo a cópia do o pacote, a implantação e a estabilização). Muita agressividade com tempos limite pode significar mais atualizações com falha e, portanto, recomendamos um início mais conservador com tempos limites mais longos.

Vamos recapitular como os tempos limite interagem com os tempos de atualização:

A atualização de um domínio de atualização não pode ser concluída mais rapidamente do que *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

A falha de atualização não pode ocorrer mais rápido do que *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

O tempo de atualização para um domínio de atualização é limitado pelo *UpgradeDomainTimeout*. Se *HealthCheckRetryTimeout* e *HealthCheckStableDuration* são diferentes de zero e a integridade do aplicativo alterna entre boa e ruim, a atualização poderá esgotar o tempo limite em *UpgradeDomainTimeout*. O *UpgradeDomainTimeout* inicia a contagem regressiva quando a atualização do domínio de atualização atual começa.

## Próximas etapas

[Tutorial de atualização](service-fabric-application-upgrade-tutorial.md)

[Parâmetros de atualização](service-fabric-application-upgrade-parameters.md)

[Tópicos avançados](service-fabric-application-upgrade-advanced.md)

[Serialização de dados](service-fabric-application-upgrade-data-serialization.md)
 

<!---HONumber=Nov15_HO1-->