<properties
   pageTitle="Adicionar relatórios de integridade personalizados do Service Fabric | Microsoft Azure"
   description="Descreve como enviar relatórios de integridade personalizados para entidades de integridade da Malha de Serviço do Azure. Fornece recomendações para projetar e implementar relatórios de integridade de qualidade."
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
   ms.date="10/23/2015"
   ms.author="oanapl"/>

# Adicionando relatórios de integridade personalizados da Malha de Serviço
A Malha de Serviço apresenta um [Modelo de Integridade](service-fabric-health-introduction.md) desenvolvido para sinalizar condições de cluster ou aplicativo não íntegros em entidades específicas. Isso é feito usando **relatores de integridade** (componentes do sistema e watchdogs). O objetivo é facilitar e agilizar o diagnóstico e o reparo. Os criadores de serviço precisam pensar à frente sobre a integridade. Qualquer condição que possa afetar a integridade deve ser apontada, especialmente se ela puder ajudar a sinalizar problemas próximos da raiz. É possível evitar o tempo gasto com depuração e investigação depois que o serviço estiver funcionando em escala na nuvem (privada ou no Azure).

Os relatores da Malha de Serviço monitoram condições identificadas de interesse. Eles informam essas condições com base na respectiva exibição local. O [Repositório de Integridade](service-fabric-health-introduction.md#Health-Store) agrega dados de integridade enviados por todos os relatores para determinar se as entidades estão globalmente íntegras. O modelo foi desenvolvido com a intenção de ser completo, flexível e fácil de usar. A qualidade dos relatórios de integridade determina a precisão da exibição de integridade do cluster. Falsos positivos que mostram incorretamente problemas de não integridade podem afetar de forma negativa as atualizações ou outros serviços que usam dados de integridade, como serviços de reparo ou mecanismos de geração de alerta. Portanto, é necessário um pouco de criatividade para fornecer relatórios que capturem condições de interesse da melhor forma possível.

Para projetar e implementar relatórios de integridade, watchdogs e componentes do sistema devem:

- Definir a condição de interesse, como ela será monitorada e o impacto sobre a funcionalidade do cluster/aplicativo. Isso define a propriedade do relatório de integridade e o estado da integridade.

- Determinar a [entidade](service-fabric-health-introduction.md#health-entities-and-hierarchy) à qual o relatório se aplica.

- Determinar a origem do relatório, se do watchdog dentro do serviço, interno ou externo.

- Definir uma fonte usada para identificar o relator.

- Escolher uma estratégia de relatório: periodicamente ou nas transições. A forma recomendada é periodicamente, pois exige código mais simples e, portanto, está menos sujeito a erros.

- Determinar por quanto tempo o relatório de condições não íntegras deve permanecer no repositório de integridade e como ele deve ser eliminado. Isso define a vida útil do relatório e a remoção no comportamento de expiração.

Conforme mencionado acima, o relatório pode ser gerado:

- Na réplica do serviço da Malha de Serviço monitorada.

- Em watchdogs internos implantados como um serviço da Malha de Serviço. Por exemplo, um serviço sem estado da Malha de Serviço que monitora relatórios de problemas e condições. Os watchdogs podem ser implantados em todos os nós ou podem ser relacionados por afinidade com o serviço monitorado.

- Em watchdogs internos que são executados nos nós da Malha de Serviço **não** implementados como serviços da Malha de Serviço.

- Em watchdogs externos que estão investigando o recurso de **fora** do cluster da Malha de Serviço. Por exemplo, Gomez como serviço de monitoramento.

> [AZURE.NOTE]Pronto para uso, o cluster é populado com relatórios de integridade enviados pelos componentes do sistema. Leia mais em [Usando relatórios de integridade do sistema para solução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Os relatórios do usuário devem ser enviados em [entidades de integridade](service-fabric-health-introduction.md#health-entities-and-hierarchy) já criadas pelo sistema.

Depois que o design do relatório de integridade estiver limpo, o envio dos relatório de integridade é fácil. Isso pode ser feito pela API usando FabricClient.HealthManager.ReportHealth, por meio do Powershell ou de REST. Internamente, todos os métodos usam um cliente de integridade contido em um cliente de malha. Existem botões de configuração que dividem os relatórios em lote para melhoria do desempenho.

> [AZURE.NOTE]A integridade do relatório é sincronizada e representa apenas o trabalho de validação no lado do cliente. O fato de que o relatório é aceito pelo cliente de integridade não significa que ele é aplicado no repositório; ele será enviado de modo assíncrono, possivelmente em lote com outros relatórios e o processamento no servidor pode falhar (por exemplo, número de sequência obsoleto, a entidade em que o relatório deve ser aplicado foi excluída etc.).

## Cliente de integridade
Os relatórios de integridade são enviados ao Repositório de Integridade usando um cliente de integridade, que reside dentro do cliente de malha. O cliente de integridade pode ser configurado com o seguinte:

- HealthReportSendInterval. O atraso entre a hora em que o relatório é adicionado ao cliente e a hora em que ele é enviado ao Repositório de Integridade. Usado para agrupar relatórios em lotes em uma única mensagem, em vez de enviar uma mensagem para cada relatório, a fim de melhorar o desempenho. Padrão: 30 segundos.

- HealthReportRetrySendInterval. O intervalo no qual o cliente de integridade reenvia relatórios de integridade acumulados ao Repositório de Integridade. Padrão: 30 segundos.

- HealthOperationTimeout. O tempo limite de uma mensagem de relatório enviada ao Repositório de Integridade. Se o tempo limite de uma mensagem se esgotar, o cliente de integridade fará nova tentativa até que o Repositório de Integridade confirme que os relatórios foram processados. Padrão: 2 minutos.

> [AZURE.NOTE]Quando os relatórios são agrupados em lotes, o cliente de malha deve ser mantido em atividade até que, pelo menos, HealthReportSendInterval garanta que eles sejam enviados. Se a mensagem for perdida ou o Repositório de Integridade não puder aplicá-los devido aos erros transitórios, o cliente de malha deverá ser mantido em atividade por mais tempo para que tenha a chance de fazer nova tentativa.

O armazenamento em buffer no cliente leva a exclusividade dos relatórios em consideração. Por exemplo, se um relator específico com problemas estiver relatando 100 relatórios por segundo na mesma propriedade da mesma entidade, os relatórios serão substituídos pela versão mais recente. No máximo, tal relatório existe na fila do cliente. Se o envio em lote for configurado, o número de relatórios enviados ao Repositório de Integridade será de apenas um por intervalo de envio, o último relatório adicionado, que reflete o estado mais atual da entidade. Todos os parâmetros de configuração podem ser especificados na criação de FabricClient, passando FabricClientSettings com os valores desejados para as entradas relacionadas à integridade.

O exemplo a seguir cria um cliente de fábrica e especifica que os relatórios devem ser enviados assim que forem adicionados. No caso de erros com nova tentativa ou tempos limite, as repetições ocorrem a cada 40 segundos.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Os mesmos parâmetros podem ser especificados na criação de uma conexão com um cluster por meio do Powershell. Veja abaixo o início de uma conexão com um cluster local:

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

> [AZURE.NOTE]Para garantir que os serviços não autorizados não possam relatar a integridade em relação às entidades no cluster, o servidor pode ser configurado para aceitar somente solicitações de clientes protegidos. Como o relatório é gerado pelo FabricClient, isso significa que FabricClient deve ter a segurança habilitada para que possa se comunicar com o cluster, por exemplo, com autenticação de certificado ou Kerberos.

## Projetar relatórios de integridade
A primeira etapa da geração de relatórios de alta qualidade é identificar condições que possam afetar a integridade do serviço. Qualquer condição que possa ajudar a sinalizar problemas no serviço ou cluster ainda no início ou, melhor ainda, antes que eles ocorram, poderá economizar muito dinheiro. Pense em menos tempo de inatividade, menos horas sem dormir investigando e corrigindo problemas, alta satisfação do cliente.

Assim que as condições são identificadas, os desenvolvedores de watchdog precisam descobrir a melhor maneira de monitorá-las para atingir o melhor equilíbrio entre sobrecarga e utilidade. Por exemplo, pense em um serviço que faça alguns cálculos complexos usando alguns arquivos temporários em um compartilhamento. Um watchdog pode monitorar o compartilhamento para garantir disponibilidade de espaço suficiente. Ele pode escutar notificações de alterações em arquivo/diretório. Também pode emitir um aviso se um limite inicial for atingido e um erro se o compartilhamento estiver cheio. No aviso, um sistema de reparo pode começar a remover arquivos mais antigos do compartilhamento. No erro, um sistema de reparo pode mover a réplica de serviço para outro nó. Observe como os estados de condição são descritos em termos de integridade: qual é o estado da condição que pode ser considerada íntegra ou não íntegra (aviso ou erro).

Assim que os detalhes do monitoramento forem definidos, o criador do watchdog precisará descobrir como implementar o watchdog. Se as condições puderem ser determinadas dentro do serviço, o watchdog poderá fazer parte do próprio serviço monitorado. Por exemplo, o código do serviço pode verificar o uso do compartilhamento e o relatório usando um cliente de malha local toda vez que tentar gravar um arquivo. A vantagem dessa abordagem é que o relatório é simples. É preciso ter cuidado para evitar que bugs no watchdog afetem a funcionalidade do serviço.

Os relatórios de dentro do serviço monitorado nem sempre são uma opção. Um watchdog dentro do serviço não pode detectar as condições: ele não tem a lógica ou os dados para fazer a identificação, ou a sobrecarga do monitoramento da condição é alta, ou as condições não são específicas a um serviço, mas afetam as interações entre os serviços. Outra opção é ter watchdogs no cluster como processos separados. Os watchdogs simplesmente monitoram as condições e o relatório, sem afetar os principais serviços. Esses watchdogs podem, por exemplo, ser implementados como serviços sem estado no mesmo aplicativo, implantados em todos os nós ou nos mesmos nós do serviço.

Às vezes, um watchdog em execução no cluster também não é uma opção. Se as condições monitoradas forem a disponibilidade ou a funcionalidade do serviço como os usuários a veem, é melhor ter o watchdog no mesmo local dos clientes do usuário, testando as operações da mesma forma que os usuários as chamam. Por exemplo, podemos ter um watchdog do lado de fora do cluster e emitindo solicitações para o serviço, verificando a latência e a exatidão do resultado (para um serviço de cálculo, 2+2 retorna 4 em tempo aceitável?).

Depois que os detalhes do watchdog tiverem sido finalizados, escolha uma ID de origem que o identifique de modo exclusivo. Se vários watchdogs do mesmo tipo residirem no cluster, eles deverão relatar diferentes entidades, ou se relatarem a mesma entidade, assegure-se de que a ID de origem ou a propriedade seja diferente para que os relatórios possam coexistir. A propriedade do relatório de integridade deve capturar a condição monitorada. Por exemplo, no exemplo anterior, a propriedade pode ser ShareSize. Se vários dados forem aplicados à mesma condição, a propriedade deverá conter algumas informações dinâmicas para permitir que os relatórios coexistam. Por exemplo, se houver vários compartilhamentos que precisem ser monitorados, o nome da propriedade poderá ser ShareSize-nomedocompartilhamento.

> [AZURE.NOTE]O repositório de integridade **não** deve ser usado para manter informações de status. Apenas informações relacionadas à integridade devem ser relatadas como integridade, informações que afetam a avaliação de integridade de uma entidade. O repositório de integridade não foi desenvolvido como um repositório para fins gerais. Ele usa a lógica de avaliação de integridade para agregar todos os dados em estado de integridade. O envio de informações relacionadas a não integridade (por exemplo, status de relatório com estado de integridade OK) não afetará o estado de integridade agregado, mas pode afetar negativamente o desempenho do repositório de integridade.

O próximo ponto de decisão é sobre qual entidade relatar. Na maioria das vezes, isso fica óbvio com base na condição. Escolha a entidade com a melhor granularidade possível. Se uma condição afeta todas as réplicas em uma partição, reporte a partição, não o serviço. Há situações extremas onde é preciso ter mais cuidado. Se a condição afeta uma entidade (por exemplo, réplica), mas o que se quer é ter a condição sinalizada por mais tempo do que a duração da vida útil da réplica, ela deve ser relatada na partição. Caso contrário, quando a réplica é excluída, todos os relatórios associados a ela são removidos do repositório. Isso significa que os criadores do watchdog também precisam pensar sobre o tempo de vida da entidade e do relatório. Deve ficar claro quando um relatório deve ser eliminado de um repositório (por exemplo, quando um erro relatado em uma entidade não estiver valendo mais).

Vejamos um exemplo para juntar os pontos acima. Pense em um aplicativo da Malha de Serviço composto por um serviço com estado persistente Mestre e serviços sem estado Subordinados implantados em todos os nós (um tipo de serviço Subordinado para um tipo de tarefa). O Mestre tem uma fila de processamento com os comandos a serem executados pelos subordinados. Os subordinados executam as solicitações de entrada e enviam confirmações de volta. Uma condição que pode ser monitorada tem comprimento de fila de processamento Mestre. Se o comprimento da fila mestre atingir um limite, reporte Aviso, pois isso significa que os subordinados não podem lidar com a carga. Se a fila atingiu o comprimento máximo e os comandos forem descartados, reporte Erro, pois o serviço não pode ser recuperado. Os relatórios podem estar na propriedade "QueueStatus". O watchdog reside no serviço e é enviado periodicamente na réplica primária Mestre. A TTL é de 2 minutos e ele é enviado periodicamente a cada 30 segundos. Se a primária falhar, o relatório será automaticamente removido do repositório. Se a réplica do serviço estiver ativa, mas travada ou apresentar outros problemas, o relatório vai expirar no repositório de integridade e a entidade será avaliada com erro.

Outra condição que pode ser monitorada é o tempo de execução da tarefa. O Mestre distribui as tarefas aos subordinados com base no tipo de tarefa. Dependendo do design, o Mestre pode sondar os subordinados quanto ao status da tarefa ou pode aguardar que os subordinados enviem confirmações quando tiverem acabado. No segundo caso, é preciso ter cuidado para detectar situações em que os subordinados são desativados ou as mensagens são perdidas. Uma opção é que o Mestre envie uma solicitação de ping para o mesmo Subordinado, que envia o status de volta. Se nenhum status for recebido, considere a falha e o reagendamento da tarefa. Isso pressupõe que as tarefas sejam idempotentes. Poderemos traduzir a condição monitorada como aviso se a tarefa não foi feita em determinado tempo t1 (por exemplo, 10 minutos) e erro se a tarefa não foi concluída no tempo t2 (por exemplo, 20 minutos). Esse relatório pode ser gerado de várias maneiras:

- A réplica primária Mestre relata a si mesma periodicamente. Podemos ter uma propriedade para todas as tarefas pendentes na fila: se, pelo menos, uma tarefa demorar mais, reporte aviso ou erro da propriedade "PendingTasks", conforme apropriado. Se não houver tarefas pendentes ou todas acabaram de ser iniciadas, reporte OK. As tarefas são persistentes, de modo que se a principal falhar, a nova principal promovida poderá continuar relatando adequadamente.

- Outro processo do watchdog (na nuvem ou externo) verifica as tarefas (do lado de fora, com base no resultado da tarefa desejado) para ver se estão concluídas. Se elas não respeitarem os limites, reporte o serviço Mestre. Relate cada tarefa e inclua o identificador de tarefa (por exemplo, PendingTask+iddatarefa). Relate somente os estados não íntegros. Defina a TTL para alguns minutos e marque os relatórios a serem removidos quando expirados para garantir a limpeza.

- O subordinado que está executando uma tarefa será relatado se ele demorar mais do que o esperado para executá-la. Ele relata a instância do serviço na propriedade "PendingTasks". Isso identifica a instância do serviço que tem problemas, mas não captura a situação em que a instância é desativada. Os relatórios são removidos nesse momento. O serviço Subordinado pode ser relatado; se o Subordinado concluir a tarefa, a instância subordinada removerá o relatório do repositório. Isso não captura a situação em que a mensagem de confirmação é perdida e a tarefa não é finalizada do ponto de vista Mestre.

No entanto, o relatório é gerado nos casos descritos acima; eles serão capturados na integridade do aplicativo quando a integridade for avaliada.

## Relatar periodicamente x na transição
Usando o modelo de relatório de integridade, os watchdogs podem enviar relatórios periodicamente ou nas transições. A forma recomendada é periodicamente, pois o código é muito mais simples e, portanto, menos sujeito a erros. Os watchdogs devem ser sempre os mais simples possíveis para evitar bugs que disparam relatórios incorretos. O relatório incorreto de não integridade afetará a avaliação de integridade e os cenários baseados na integridade, como atualizações. Os relatórios incorretos de integridade ocultam problemas no cluster, o que não é desejado.

Para relatórios periódicos, o watchdog pode ser implementado com um temporizador. No retorno de chamada do temporizador, o watchdog pode verificar o estado e enviar um relatório com base no estado atual. Não é necessário ver qual relatório foi enviado anteriormente ou fazer nenhuma otimização em termos de mensagens. O cliente de integridade tem lógica de envio em lote para ajudar com isso. Desde que o cliente de integridade seja mantido em atividade, ele fará nova tentativa até que o relatório seja confirmado pelo repositório de integridade ou o watchdog gere um relatório mais recente com a mesma entidade, propriedade e origem.

O relatório na transição exige tratamento cuidadoso do estado. O watchdog monitora algumas condições e relata apenas quando as condições mudam. O lado bom é que menos relatórios são necessários. O lado ruim é que a lógica do watchdog é complexa. As condições ou os relatórios devem ser mantidos para que possam ser inspecionados para determinar as mudanças de estado. No failover, é preciso ter cuidado para enviar um relatório que pode não ter sido enviado anteriormente (enfileirado, mas ainda não enviado ao repositório de integridade). O número de sequência sempre deve aumentar, ou os relatórios serão rejeitados devido à obsolescência. Em raros casos, em que ocorre a perda de dados, pode ser necessária a sincronização entre o estado do relator e o estado do repositório de integridade.

## Implementar relatório de integridade
Depois que os detalhes da entidade e do relatório forem limpos, o envio de relatório de integridade pode ser feito usando API, Powershell ou REST.

### API
Para relatar usando API, os usuários precisam criar um relatório de integridade específico ao tipo de entidade que desejam relatar e, em seguida, fornecê-lo a um cliente de integridade.

O exemplo a seguir mostra um relatório periódico de um watchdog no cluster. O inspetor verifica se um recurso externo pode ser acessado em um nó. O recurso é necessário para um manifesto do serviço no aplicativo. Mesmo se o recurso não estiver disponível, os outros serviços no aplicativo poderão funcionar corretamente. Desse modo, o relatório é enviado na entidade do pacote de serviço implantado, periodicamente, a cada 30 segundos.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snipet brevity.
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### Powershell
Os usuários podem enviar relatórios de integridade com Send-ServiceFabric*EntityType*HealthReport.

O exemplo a seguir mostra um relatório periódico sobre valores de CPU em um nó. Os relatórios devem ser enviados a cada 30 segundos e têm uma TTL de 2 minutos. Se eles expirarem, isso significa que o relator está com problemas, de modo que o nó é avaliado com erro. Quando a CPU está acima de um limite, o relatório apresenta o estado de integridade Aviso, se a CPU estiver acima do limite por mais do que o tempo configurado, ela será relatada como Erro. Caso contrário, o relator enviará OK.

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

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

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

O exemplo a seguir relata um Aviso transitório em uma réplica. Em primeiro lugar, é obtida a ID da partição e a ID da réplica para o serviço de interesse, em seguida, um relatório é enviado de PowershellWatcher na propriedade ResourceDependency. O relatório é de interesse apenas por 2 minutos, e será automaticamente removido do repositório.

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

## Próximas etapas

Com base nos dados de integridade, os criadores de serviço e administradores de cluster/aplicativo podem pensar em maneiras de consumir as informações. Por exemplo, eles podem configurar alertas com base no status de integridade para capturar problemas graves antes que eles provoquem interrupções. Eles podem configurar sistemas de reparo automático para corrigir problemas automaticamente.

[Introdução ao monitoramento da integridade da Malha de Serviço](service-fabric-health-introduction.md)

[Como exibir relatórios de integridade da Malha do Serviço](service-fabric-view-entities-aggregated-health.md)

[Usando relatórios de integridade do sistema para solução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Como monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização de aplicativo da Malha do Serviço](service-fabric-application-upgrade.md)
 

<!---HONumber=Nov15_HO1-->