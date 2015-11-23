<properties
   pageTitle="Diagnóstico e monitoramento de atores | Microsoft Azure"
   description="Este artigo descreve os recursos de diagnóstico e monitoramento de desempenho no tempo de execução dos Reliable Actors do Service Fabric, incluindo os eventos e os contadores de desempenho emitidos por ele."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/15/2015"
   ms.author="abhisram"/>

# Diagnóstico e monitoramento de desempenho para Atores Confiáveis
O tempo de execução dos Atores Confiáveis emite eventos [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) e [contadores de desempenho](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx) que fornecem informações sobre como o tempo de execução está funcionando e que ajudam na solução de problemas e no monitoramento do desempenho.

## Eventos EventSource
O nome do provedor do EventSource do tempo de execução dos Reliable Actors é "Microsoft-ServiceFabric-Actors". Os eventos dessa origem de eventos aparecem na janela [Eventos de Diagnóstico](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) quando o aplicativo de ator está sendo [depurado no Visual Studio](service-fabric-debugging-your-application.md).

Exemplos de ferramentas e tecnologias que ajudam a coletar e/ou visualizar eventos EventSource são [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Diagnóstico do Azure](../cloud-services-dotnet-diagnostics.md), [Log de Semântica](https://msdn.microsoft.com/library/dn774980.aspx) e [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### Palavras-chave
Todos os eventos que pertencem ao EventSource dos Atores Confiáveis são associados a uma ou mais palavras-chave. Isso permite a filtragem dos eventos que são coletados. Os bits de palavras-chave são definidos a seguir:

|Bit|Descrição|
|---|---|
|0x1|Conjunto de eventos importantes que resumem a operação do tempo de execução dos Atores da Malha.|
|0x2|Conjunto de eventos que descrevem as chamadas de métodos do ator. Para obter mais informações, consulte o [tópico introdutório sobre atores](service-fabric-reliable-actors-introduction.md#actors).|
|0x4|Conjunto de eventos relacionados ao estado do ator. Para obter mais informações, consulte o tópico sobre [atores com estado](service-fabric-reliable-actors-introduction.md#stateful-actors).|
|0x8|Conjunto de eventos relacionados à simultaneidade baseada no turno do ator. Para obter mais informações, consulte o tópico sobre [simultaneidade](service-fabric-reliable-actors-introduction.md#concurrency).|

## Contadores de desempenho
O tempo de execução dos Atores Confiáveis define as categorias de contador de desempenho a seguir.

|Categoria|Descrição|
|---|---|
|Ator da Malha do Serviço|Contadores específicos aos atores da Malha do Serviço; por exemplo, tempo que se leva para salvar o estado do ator.|
|Método de ator da Malha do Serviço|Contadores específicos aos métodos implementados pelos atores da Malha do Serviço; por exemplo, quantas vezes um método de ator é invocado.|

Cada uma das categorias acima tem um ou mais contadores.

O aplicativo [Monitor de Desempenho do Windows](https://technet.microsoft.com/library/cc749249.aspx) está disponível por padrão no sistema operacional Windows e pode ser usado para coletar e exibir dados do contador de desempenho. O [Diagnóstico do Azure](../cloud-services-dotnet-diagnostics.md) é outra opção para coletar dados do contador de desempenho e carregá-los nas tabelas do Azure.

### Nomes da instância do contador de desempenho
Um cluster com um grande número de serviços de ator ou partições de serviço de ator terá um grande número de instâncias do contador de desempenho de ator. Os nomes das instâncias do contador de desempenho podem ajudar a identificar a [partição](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) e o método de ator (se aplicável) aos quais a instância do contador de desempenho está associada.

#### Categoria `Service Fabric Actor`
Para a categoria `Service Fabric Actor`, os nomes da instância do contador estão no seguinte formato:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID*: é a representação da cadeia de caracteres da ID da partição da Malha do Serviço à qual a instância do contador de desempenho está associada. A ID da partição é um GUID e sua representação da cadeia de caracteres é gerada usando o método [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) com o especificador de formato "D".

*ActorRuntimeInternalID*: é a representação da cadeia de caracteres de um inteiro de 64 bits que é gerada pelo tempo de execução dos Atores da Malha para seu uso interno. Isso está incluído no nome da instância do contador de desempenho para garantir sua exclusividade e evitar conflito com outros nomes da instância do contador de desempenho. Os usuários não devem tentar interpretar essa parte do nome da instância do contador de desempenho.

Veja a seguir o exemplo de um nome da instância do contador para um contador que pertence à categoria 'Service Fabric Actor':

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

No exemplo acima, `2740af29-78aa-44bc-a20b-7e60fb783264` é a representação da cadeia de caracteres da ID da partição da Malha do Serviço e `635650083799324046` é a ID de 64 bits que é gerada para uso interno do tempo de execução.

#### Categoria `Service Fabric Actor Method`
Para a categoria `Service Fabric Actor Method`, os nomes da instância do contador estão no seguinte formato:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName*: é o nome do método de ator ao qual a instância do contador de desempenho está associada. O formato do nome do método é determinado com base em alguma lógica no tempo de execução dos Atores da Malha que equilibra a legibilidade do nome com restrições no comprimento máximo dos nomes da instância do contador de desempenho no Windows.

*ActorsRuntimeMethodId*: é a representação da cadeia de caracteres de um inteiro de 32 bits que é gerada pelo tempo de execução dos Atores da Malha para seu uso interno. Isso está incluído no nome da instância do contador de desempenho para garantir sua exclusividade e evitar conflito com outros nomes da instância do contador de desempenho. Os usuários não devem tentar interpretar essa parte do nome da instância do contador de desempenho.

*ServiceFabricPartitionID*: é a representação da cadeia de caracteres da ID da partição da Malha do Serviço à qual a instância do contador de desempenho está associada. A ID da partição é um GUID e sua representação da cadeia de caracteres é gerada usando o método [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) com o especificador de formato "D".

*ActorRuntimeInternalID*: é a representação da cadeia de caracteres de um inteiro de 64 bits que é gerada pelo tempo de execução dos Atores da Malha para seu uso interno. Isso está incluído no nome da instância do contador de desempenho para garantir sua exclusividade e evitar conflito com outros nomes da instância do contador de desempenho. Os usuários não devem tentar interpretar essa parte do nome da instância do contador de desempenho.

Veja a seguir o exemplo de um nome da instância do contador para um contador que pertence à categoria 'Service Fabric Actor Method':

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

No exemplo acima, `ivoicemailboxactor.leavemessageasync` é o nome do método, `2` é a ID de 32 bits gerada para uso interno do tempo de execução, `89383d32-e57e-4a9b-a6ad-57c6792aa521` é a representação da cadeia de caracteres da ID da partição da Malha do Serviço e `635650083804480486` é a ID de 64 bits gerada para uso interno do tempo de execução.

## Lista de eventos e contadores de desempenho

### Eventos e contadores de desempeno do método de ator
O tempo de execução dos Atores Confiáveis emite os eventos relacionados aos [métodos de ator](service-fabric-reliable-actors-introduction.md#actors) a seguir.

|Nome do evento|ID do evento|Nível|Palavras-chave|Descrição|
|---|---|---|---|---|
|ActorMethodStart|7|Detalhado|0x2|O tempo de execução dos atores está prestes a invocar um método de ator|
|ActorMethodStop|8|Detalhado|0x2|Um método de ator acabou de ser executado, isto é, a chamada assíncrona do tempo de execução para o método de ator foi retornada e a Tarefa retornada pelo método de ator foi concluída.|
|ActorMethodThrewException|9|Aviso|0x3|Uma exceção foi lançada durante a execução de um método de ator, ou durante a chamada assíncrona do tempo de execução para o método de ator, ou durante a execução da Tarefa retornada pelo método de ator. Esse evento indica algum tipo de falha no código do ator que precisa de investigação.|

O tempo de execução dos Atores Confiáveis publica os contadores de desempenho relacionados à execução dos métodos de ator a seguir.

|Nome da categoria|Nome do contador|Descrição|
|---|---|---|
|Método de ator da Malha do Serviço|Invocação/s|Número de vezes que o método de serviço do ator é invocado por segundo|
|Método de ator da Malha do Serviço|Média de milissegundos por invocação|Tempo usado para executar o método de serviço do ator em milissegundos|
|Método de ator da Malha do Serviço|Exceções lançadas/s|Número de vezes que o método de serviço do ator lançou uma exceção por segundo|

### Contadores de desempenho e eventos simultâneos
O tempo de execução dos Atores Confiáveis emite os eventos relacionados à [simultaneidade](service-fabric-reliable-actors-introduction.md#concurrency) a seguir.

|Nome do evento|ID do evento|Nível|Palavras-chave|Descrição|
|---|---|---|---|---|
|ActorMethodCallsWaitingForLock|12|Detalhado|0x8|Esse evento é escrito no início de cada novo turno em um ator. Ele contém o número chamadas do ator pendentes aguardando para adquirir o bloqueio por ator que aplica a simultaneidade baseada em turno.|

O tempo de execução dos Atores Confiáveis publica os contadores de desempenho a seguir relacionados à simultaneidade.

|Nome da categoria|Nome do contador|Descrição|
|---|---|---|
|Ator da Malha do Serviço|Número de chamadas do ator aguardando o bloqueio do ator|Número de chamadas do ator pendentes aguardando para adquirir o bloqueio por ator que aplica a simultaneidade baseada em turno.|

### Contadores de desempenho e eventos do gerenciamento de estado do ator
O tempo de execução dos Atores Confiáveis emite os eventos a seguir relacionados ao [gerenciamento de estado do ator](service-fabric-reliable-actors-introduction.md#actor-state-management).

|Nome do evento|ID do evento|Nível|Palavras-chave|Descrição|
|---|---|---|---|---|
|ActorSaveStateStart|10|Detalhado|0x4|O tempo de execução dos atores está prestes a salvar o estado do ator.|
|ActorSaveStateStop|11|Detalhado|0x4|O tempo de execução dos atores acabou de salvar o estado do ator.|

O tempo de execução dos Atores Confiáveis publica os contadores de desempenho a seguir relacionados ao gerenciamento de estado do ator.

|Nome da categoria|Nome do contador|Descrição|
|---|---|---|
|Ator da Malha do Serviço|Milissegundos em média por operação de salvamento do estado|Tempo necessário para salvar o estado do ator em milissegundos|

### Eventos relacionados às instâncias de ator sem estado
O tempo de execução dos Atores Confiáveis emite os eventos a seguir relacionados às [instâncias de ator sem monitoração de estado](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-stateless-actors).

|Nome do evento|ID do evento|Nível|Palavras-chave|Descrição|
|---|---|---|---|---|
|ServiceInstanceOpen|3|Informativo|0x1|Instância do ator sem estado aberta. Isso significa que os atores para essa partição podem ser criados dentro dessa instância (e possivelmente outras instâncias também).|
|ServiceInstanceClose|4|Informativo|0x1|Instância do ator sem estado fechada. Isso significa que os atores dessa partição não serão mais criados dentro dessa instância. Nenhuma nova solicitação será entregue aos atores já criados dentro dessa instância. Os atores serão destruídos depois que todas as solicitações em andamento forem concluídas.|

### Eventos relacionados às réplicas de ator com estado
O tempo de execução dos Atores Confiáveis emite os eventos a seguir relacionados às [réplicas de ator com estado](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-stateful-actors).

|Nome do evento|ID do evento|Nível|Palavras-chave|Descrição|
|---|---|---|---|---|
|ReplicaChangeRoleToPrimary|1|Informativo|0x1|Função alterada pela réplica de ator com estado para Primária. Isso significa que os atores dessa partição serão criados dentro dessa réplica.|
|ReplicaChangeRoleFromPrimary|2|Informativo|0x1|Função alterada pela réplica de ator com estado para não Primária. Isso significa que os atores dessa partição não serão mais criados dentro dessa réplica. Nenhuma nova solicitação será entregue aos atores já criados dentro dessa réplica. Os atores serão destruídos depois que todas as solicitações em andamento forem concluídas.|

### Eventos de ativação e desativação do ator
O tempo de execução dos Atores Confiáveis emite os eventos a seguir relacionados à [ativação e desativação do ator](service-fabric-reliable-actors-lifecycle.md).

|Nome do evento|ID do evento|Nível|Palavras-chave|Descrição|
|---|---|---|---|---|
|ActorActivated|5|Informativo|0x1|Um ator foi ativado.|
|ActorDeactivated|6|Informativo|0x1|Um ator foi desativado.|

<!---HONumber=Nov15_HO3-->