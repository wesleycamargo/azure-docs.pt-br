<properties
   pageTitle="Introdução ao Monitoramento de Integridade da Malha do Serviço"
   description="Este artigo descreve o modelo de Monitoramento de Integridade da Malha do Serviço do Azure, incluindo entidades de integridade, relatório e avaliação."
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
   ms.date="06/16/2015"
   ms.author="oanapl"/>

# Introdução ao Monitoramento de Integridade da Malha do Serviço
A Malha do Serviço introduz um modelo de integridade que fornece avaliação e relatório de integridade avançados, flexíveis e extensíveis. Isso inclui monitoramento do estado quase em tempo real do cluster e dos serviços que são executados nele. Você pode obter facilmente as informações sobre integridade e tomar medidas para corrigir possíveis problemas antes que eles se espalhem e causem interrupções massivas. No modelo comum, esses serviços enviam relatórios com base na respectiva exibição local e as informações são agregadas para fornecer uma exibição geral no nível de cluster.

Os componentes da Malha do Serviço usam esse modelo de integridade para reportar o respectivo estado atual. E você pode usar o mesmo mecanismo para reportar a integridade de seus aplicativos. A qualidade e o detalhamento do relatório de integridade específicos às suas condições personalizadas determinarão com que facilidade você poderá detectar e corrigir problemas dos aplicativos em execução.

> [AZURE.NOTE]Iniciamos o subsistema Integridade conforme a necessidade de atualizações monitoradas. A Malha do Serviço fornece atualizações monitoradas que sabem como atualizar um cluster ou um aplicativo sem tempo de inatividade, com pouca ou nenhuma intervenção do usuário e com disponibilidade total do cluster e do aplicativo. Para isso, a atualização verifica a integridade com base em políticas de atualização configuradas e só prossegue quando a integridade respeita os limites desejados. Caso contrário, a atualização é automaticamente revertida ou pausada para dar aos administradores a oportunidade de corrigir os problemas. Para saber mais sobre atualizações de aplicativo, consulte [este artigo](service-fabric-application-upgrade.md).

## Repositório de Integridade
O Repositório de Integridade mantém informações relacionadas à integridade sobre entidades no cluster para facilidade de recuperação e avaliação. Ele é implementado como um serviço com estado persistente da Malha do Serviço, garantindo alta disponibilidade e escalabilidade. Ele faz parte do aplicativo fabric:/System e é disponibilizado assim que o cluster é ativado.

## Hierarquia e entidades de integridade
As entidades de integridade são organizadas em uma hierarquia lógica que captura as interações e dependências entre diferentes entidades. As entidades e a hierarquia são criadas automaticamente pelo Repositório de Integridade com base nos relatórios recebidos dos componentes da Malha do Serviço.

As entidades de integridade espelham as entidades da Malha do Serviço (por ex., a entidade de aplicativo de integridade corresponde a uma instância do aplicativo implantada no cluster, a entidade de nó de integridade corresponde a um nó de cluster da Malha do Serviço). A hierarquia de integridade captura as interações das entidades do sistema e é a base para a avaliação avançada de integridade. Você pode aprender sobre os principais conceitos da Malha do Serviço em [Visão geral técnica da Malha do Serviço](service-fabric-technical-overview.md). Para saber mais sobre aplicativos, vá para [Modelo de aplicativo da Malha do Serviço](service-fabric-application-model.md).

As entidades de integridade e a hierarquia proporcionam relatório, depuração e monitoramento efetivos do cluster e de aplicativos. O modelo de integridade permite uma representação precisa e **granular** da integridade das muitas partes móveis do cluster.

![Entidades de integridade.][1] As entidades de integridade, organizadas em uma hierarquia com base nas relações de pai/filho.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

As entidades de integridade são:

- **Cluster**. Representa a integridade de um cluster da Malha do Serviço. Os relatórios de integridade do cluster descrevem as condições que afetam o cluster inteiro e não podem ser limitados a um ou mais filhos não íntegros. Exemplo: dupla personalidade do cluster devido ao particionamento da rede ou problemas de comunicação.

- **Nó**. Representa a integridade de um nó da Malha do Serviço. Os relatórios de integridade do nó descrevem condições que afetam a funcionalidade do nó e, geralmente, afetam todas as entidades implantadas em execução nele. Exemplo: o nó não tem espaço em disco (ou outra propriedade em todo o computador, como memória, conexões, etc.) ou o nó está desativado. A entidade de nó é identificada pelo nome do nó (cadeia de caracteres).

- **Aplicativo**. Representa a integridade de uma instância do aplicativo em execução no cluster. Os relatórios de integridade do aplicativo descrevem condições que afetam a integridade geral do aplicativo e não podem ser limitados a filhos individuais (aplicativos de serviço ou implantados). Exemplo: a interação de ponta a ponta entre diferentes serviços no aplicativo. A entidade de aplicativo é identificada pelo nome do aplicativo (URI).

- **Serviço**. Representa a integridade de um serviço em execução no cluster. Os relatórios de integridade do serviço descrevem condições que afetam a integridade geral do serviço e não podem ser limitados a uma partição ou réplica. Exemplo: uma configuração de serviço (como compartilhamento de arquivos externos ou porta) que está causando problemas em todas as partições. A entidade de serviço é identificada pelo nome do serviço (URI).

- **Partição**. Representa a integridade de uma partição de serviço. Os relatórios de integridade da partição descrevem condições que afetam todo o conjunto de réplicas. Exemplo: o número de réplicas está abaixo da contagem de destino ou a partição está na perda do quórum. A entidade de partição é identificada pela ID da partição (GUID).

- **Réplica**. Representa a integridade de uma réplica de serviço com estado ou uma instância de serviço sem estado. Essa é a menor unidade que os watchdogs e componentes do sistema podem reportar para um aplicativo. Exemplo: para serviços com estado, a réplica primária poderá ser reportada se não puder replicar operações em locais secundários ou a replicação não está seguindo no ritmo esperado. Uma instância sem estado pode ser reportada se estiver em execução sem recursos ou tiver problemas de conectividade. A entidade de réplica é identificada pela ID da partição (GUID) e pela ID de réplica ou instância (longo).

- **DeployedApplication**. Representa a integridade de um *aplicativo que é executado em um nó*. Os relatórios de integridade do aplicativo implantado descrevem condições específicas ao aplicativo no nó que não podem ser limitados aos pacotes de serviço implantados no mesmo nó. Exemplo: o pacote do aplicativo não pode ser baixado nesse nó ou há um problema na configuração das entidades de segurança do aplicativo no nó. O aplicativo implantado é identificado pelo nome do aplicativo (URI) e pelo nome do nó (cadeia de caracteres).

- **DeployedServicePackage**. Representa a integridade de um pacote de serviço de um aplicativo em execução em um nó no cluster. Descreve as condições específicas a um pacote de serviço que não afetam os outros pacotes de serviço no mesmo nó para o mesmo aplicativo. Exemplo: um pacote de códigos no pacote de serviço não pode ser iniciado ou o pacote de configuração não pode ser lido. O pacote de serviço implantado é identificado pelo nome do aplicativo (URI), o nome do nó (cadeia de caracteres) e o nome do manifesto do serviço (cadeia de caracteres).

A granularidade do modelo de integridade facilita a detecção e correção dos problemas. Por exemplo, se um serviço não estiver respondendo, é possível reportar que a instância do aplicativo não está íntegra. No entanto, não é o ideal porque o problema talvez não esteja afetando todos os serviços nesse aplicativo. O relatório deve ser aplicado no serviço não íntegro ou, se mais informações apontarem para uma partição filho específica, nessa partição. Os dados emergem automaticamente pela hierarquia: uma partição não íntegra ficará visível nos níveis de serviço e aplicativo. Isso ajuda a identificar e resolver a causa raiz dos problemas mais rapidamente.

A hierarquia de integridade é composta por relações de pai/filho. O cluster é composto por nós e aplicativos; os aplicativos têm aplicativos implantados e serviços; aplicativos implantados têm pacotes de serviço implantados. Os serviços têm partições, e cada partição tem uma ou mais réplicas. Há uma relação especial entre nós e entidades implantadas. Se um nó não estiver íntegro conforme reportado por seu componente de sistema de autoridade (Serviço do Gerenciador de Failover), ele afetará os aplicativos implantados, pacotes de serviço e réplicas implantadas nele.

A hierarquia de integridade representa o estado mais recente do sistema com base em relatórios de integridade mais recentes, que é uma informação quase em tempo real. Watchdogs internos e externos podem reportar as mesmas entidades com base na lógica específica do aplicativo ou condições monitoradas personalizadas. Os relatórios do usuário coexistem com os relatórios do sistema.

Investir tempo no planejamento de como reportar a integridade e reagir enquanto projeta o serviço facilita a depuração, o monitoramento e as operações subsequentes dos serviços de nuvens amplos.

## Estados de integridade
A Malha do Serviço usa três estados de integridade para descrever se uma entidade está íntegra ou não: OK, Aviso e Erro. Qualquer relatório enviado ao Repositório de Integridade deve especificar um desses estados. O resultado da avaliação de integridade é um desses estados.

Os estados de integridade possíveis são:

- OK: a entidade está íntegra. Não há nenhum problema conhecido reportado em seus filhos (quando aplicável).

- Aviso: a entidade apresenta alguns problemas, mas ainda não está em estado não íntegro (isto é, atraso inesperado que não está causando nenhum problema funcional). Em alguns casos, a condição de aviso pode se corrigir sem nenhuma intervenção especial, e é útil para proporcionar visibilidade do que está acontecendo. Em outros casos, a condição de Aviso pode se transformar em um problema grave sem intervenção do usuário.

- Erro: a entidade não está íntegra. Uma medida deve ser tomada para corrigir o estado da entidade, pois ela não funciona corretamente.

- Desconhecido: a entidade não existe no repositório de integridade. Esse resultado pode ser obtido em consultas distribuídas, como obter nós ou aplicativos da Malha do Serviço. Essas consultas mesclam os resultados de vários componentes do sistema. Se outro componente do sistema tiver uma entidade que ainda não atingiu o repositório de integridade ou que foi removida do repositório de integridade, a consulta mesclada populará o resultado de integridade com o estado ‘Desconhecido’.

## Políticas de integridade
O Repositório de Integridade aplica políticas de integridade para determinar se uma entidade está íntegra com base em seus relatórios e filhos.

> [AZURE.NOTE]As políticas de integridade podem ser especificadas no manifesto do cluster (para avaliação de integridade do cluster e do nó) ou no manifesto do aplicativo (para avaliação de aplicativo e qualquer um de seus filhos). As solicitações de avaliação de integridade também podem passar pelas políticas de avaliação de integridade personalizadas, que serão usadas apenas para avaliação.

Por padrão, a Malha do Serviço aplica regras rígidas (tudo deve estar íntegro) na relação hierárquica entre pai e filho; se um dos filhos apresentar um evento não íntegro, o pai será considerado não íntegro.

### Política de integridade do cluster
A política de integridade do cluster é usada para avaliar o estado de integridade do cluster e os estados de integridade do nó. Ela pode ser definida no manifesto do cluster. Se não estiver presente, a política padrão será usada (0 falhas toleradas). Ela contém:

- **ConsiderWarningAsError**. Especifica se os relatórios de integridade Aviso devem ser tratados como erros durante a avaliação de integridade. Padrão: Falso.

- **MaxPercentUnhealthyApplications**. Porcentagem máxima tolerada de aplicativos que podem estar não íntegros antes de o cluster ser considerado Erro.

- **MaxPercentUnhealthyNodes**. Porcentagem máxima tolerada de nós que podem estar não íntegros antes de o cluster ser considerado Erro. Em clusters grandes, sempre haverá nós desativados ou ausentes para reparo, de modo que essa porcentagem deve ser configurada para tolerá-los.

Veja a seguir o trecho de um manifesto do cluster:

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />
    <Parameter Name="MaxPercentUnhealthyApplications" Value="0" />
    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
  </Section>
</FabricSettings>
```

### Política de integridade do aplicativo
A política de integridade do aplicativo descreve como a avaliação da agregação dos estados de eventos e filhos é feita para aplicativos e seus filhos. Ela pode ser definida no manifesto do aplicativo, .ApplicationManifest.xml, no pacote do aplicativo. Se não for especificada, a Malha do Serviço suporá que a entidade não está íntegra se ela tiver um relatório de integridade ou um filho no estado de integridade Aviso ou Erro. As políticas configuráveis são:

- **ConsiderWarningAsError**. Especifica se os relatórios de integridade Aviso devem ser tratados como erros durante a avaliação de integridade. Padrão: Falso.

- **MaxPercentUnhealthyDeployedApplications**. Porcentagem máxima tolerada de aplicativos implantados que podem estar não íntegros antes de o aplicativo ser considerado Erro. A porcentagem é calculada pela divisão do número de aplicativos implantados não íntegros pelo número de nós em que os aplicativos estão atualmente implantados no cluster. O cálculo é arredondado para cima para tolerar uma falha em um número pequeno de nós. Padrão: 0%.

- **DefaultServiceTypeHealthPolicy**. Especifica a política de integridade do tipo de serviço padrão, que substituirá a política de integridade padrão para todos os tipos de serviço no aplicativo.

- **ServiceTypeHealthPolicyMap**. Mapa com políticas de integridade de serviço por tipo de serviço, que substituem a política de integridade do tipo de serviço padrão para os tipos de serviço especificados. Por exemplo, em um aplicativo que contenha um tipo de serviço Gateway sem estado e um tipo de serviço Mecanismo com estado, a política de integridade para o serviço sem e com estado pode ser configurada de modo diferente. Especificar política por tipos de serviço permite um controle mais granular da integridade do serviço.

### Políticas de integridade do tipo de serviço
A política de integridade do tipo de serviço especifica como avaliar e agregar filhos do serviço. Ela contém:

- **MaxPercentUnhealthyPartitionsPerService**. Porcentagem máxima tolerada de partições não íntegras antes de um serviço ser considerado não íntegro. Padrão: 0%.

- **MaxPercentUnhealthyReplicasPerPartition**. Porcentagem máxima tolerada de réplicas não íntegras antes de uma partição ser considerada não íntegra. Padrão: 0%.

- **MaxPercentUnhealthyServices**. Porcentagem máxima tolerada de serviços não íntegros antes de o aplicativo ser considerado não íntegro. Padrão: 0%

Veja a seguir o trecho de um manifesto do aplicativo:

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## Avaliação da integridade
Os usuários ou serviços automatizados podem avaliar a integridade de qualquer entidade a qualquer momento. Para avaliar a integridade de uma entidade, o Repositório de Integridade agrega todos os relatórios de integridade na entidade e avalia todos os seus filhos (quando aplicável). O algoritmo de agregação de integridade usa políticas de integridade que especificam como avaliar os relatórios de integridade e como agregar estados de integridade dos filhos (quando aplicável).

### Agregação de relatórios de integridade
Uma entidade pode ter vários relatórios de integridade enviados por diferentes relatores (componentes do sistema ou watchdogs) em propriedades diferentes. A agregação usa as políticas de integridade associadas, especificamente o membro ConsiderWarningAsError da política de integridade do aplicativo ou cluster, que especifica como avaliar avisos.

O estado de integridade agregada é disparado pelos **piores** relatórios de integridade da entidade. Se houver pelo menos um relatório de integridade com Erro, o estado de integridade agregada será Erro.

![Agregação de relatório de integridade com o relatório de Erro.][2]

Relatório de integridade de erro dispara a entidade de integridade para estar no estado Erro.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Se não houver relatórios de Erro, e um ou mais Avisos, o estado de integridade agregada será Aviso ou Erro, dependendo do sinalizador de política ConsiderWarningAsError.

![Agregação do relatório de integridade com o relatório de Aviso e ConsiderWarningAsError falso.][3]

Agregação do relatório de integridade com relatório de Aviso e ConsiderWarningAsError falso (padrão).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### Agregação de integridade de filhos
O estado de integridade agregada de uma entidade reflete os estados de integridade dos filhos (quando aplicável). O algoritmo para agregar estados de integridade dos filhos usa as políticas aplicáveis de integridade com base no tipo de entidade.

![Agregação de integridade de entidades filho.][4]

Agregação de filhos com base nas políticas de integridade.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Depois de avaliar todos os filhos, o Repositório de Integridade agrega os estados de integridade com base na porcentagem máxima configurada de não integridade extraída da política com base no tipo de entidade e do filho.

- Se todos os filhos tiverem estados OK, o estado de integridade agregada dos filhos será OK.

- Se todos os filhos tiverem estados OK e Aviso, o estado de integridade agregada dos filhos será Aviso.

- Se houver filhos com estados de Erro que não respeitarem a porcentagem máxima permitida de filhos não íntegros, o estado de integridade agregada será Erro.

- Se os filhos com estados de Erro respeitarem a porcentagem máxima permitida de filhos não íntegros, o estado de integridade agregada será Aviso.

## Relatório de integridade
Os componentes do sistema e watchdogs internos/externos podem ser reportados em relação às entidades da Malha do Serviço. Os *relatores* fazem uma determinação **local** da integridade da entidade monitorada com base em algumas condições que estão monitorando. Eles não precisam observar nenhum estado global e dados agregados. Isso não é desejado, pois tornaria os relatores organismos complexos que precisariam observar muitas coisas de modo a inferir quais informações enviar.

Para enviar dados de integridade ao repositório de integridade, os relatores precisam identificar a entidade afetada e criar um relatório de integridade. O relatório pode então ser enviado por meio da API com FabricClient.HealthManager.ReportHealth, por meio do Powershell ou da REST.

### Relatórios de integridade
Os relatórios de integridade de cada uma das entidades no cluster contêm as seguintes informações:

- SourceId. Uma cadeia de caracteres que identifica exclusivamente o relator do evento de integridade.

- Identificador de entidade. Identifica a entidade na qual o relatório é aplicado. Varia de acordo com o [tipo de entidade](service-fabric-health-introduction.md#health-entities-and-hierarchy):

  - Cluster: nenhum

  - Nó: nome do nó (cadeia de caracteres).

  - Aplicativo: nome do aplicativo (URI). Representa o nome da instância do aplicativo implantado no cluster.

  - Serviço: nome do serviço (URI). Representa o nome da instância do serviço implantado no cluster.

  - Partição: ID da partição (GUID). Representa o identificador exclusivo da partição.

  - Réplica: a ID de réplica do serviço com estado ou a ID da instância do serviço sem estado (Int64).

  - DeployedApplication: nome do aplicativo (URI) e nome do nó (cadeia de caracteres).

  - DeployedServicePackage: nome do aplicativo (URI), nome do nó (cadeia de caracteres) e nome do manifesto do serviço (cadeia de caracteres).

- Propriedade. Um *cadeia de caracteres* (não uma enumeração fixa) que permite ao relator classificar o evento de integridade para uma propriedade específica da entidade. Por exemplo, o relator A pode reportar a integridade na propriedade “storage” de Node01 e o relator B pode reportar a integridade na propriedade “connectivity” de Node01. Os dois relatórios são tratados como eventos de integridade distintos no repositório de integridade para a entidade Node01.

- Descrição. Uma cadeia de caracteres que permite ao relator fornecer informações detalhadas sobre o evento de integridade. SourceId, Property e HealthState devem descrever o relatório por completo. A descrição adiciona informações legíveis sobre o relatório para que possam ser facilmente compreendidas por administradores e usuários.

- HealthState. Uma [enumeração](service-fabric-health-introduction.md#health-states) que descreve o estado de integridade do relatório. Os valores aceitáveis são OK, Aviso e Erro.

- TimeToLive. Um período de tempo que indica por quanto tempo o relatório de integridade é válido. Juntamente com RemoveWhenExpired, permite que o HealthStore saiba como avaliar eventos expirados. Por padrão, o valor é infinito e o relatório é válido indefinidamente.

- RemoveWhenExpired. Um booliano. Se definido como verdadeiro, o relatório de integridade expirado será removido automaticamente do repositório de integridade, não afetando a avaliação da integridade da entidade. É usado quando o relatório é válido apenas por um período de tempo e o relator não precisa removê-lo explicitamente. Também é usado para excluir relatório do repositório de integridade. Por exemplo, um watchdog é alterado e interrompe o envio de relatórios com propriedade e origem anteriores. Portanto, ele pode enviar um relatório com TTL curta e RemoveWhenExpired para eliminar qualquer estado anterior do Repositório de Integridade. Se definido como falso, o relatório expirado será tratado como erro na avaliação de integridade. Ele sinaliza para o repositório de integridade que a origem deve reportar periodicamente essa propriedade; se não o fizer, deve haver algo errado com o watchdog. A integridade do watchdog é capturada ao considerar o evento como erro.

- SequenceNumber. Um inteiro positivo que sempre precisa ser aumentado, pois ele representa a ordem dos relatórios. Ele é usado pelo Repositório de Integridade para detectar relatórios obsoletos, recebidos em atraso devido a atrasos na rede ou outros problemas. Os relatórios serão rejeitados se o número de sequência for menor ou igual ao aplicado mais recentemente para a mesma entidade, origem e propriedade. O número de sequência será gerado automaticamente se não for especificado. Só será necessário colocar o número de sequência ao relatar transições de estado: a origem precisa lembrar quais relatórios enviou e persistir as informações para recuperação no failover.

SourceId, identificador da entidade, Propriedade e HealthState são obrigatórios para cada relatório de integridade. A cadeia de caracteres SourceId não pode iniciar com o prefixo “System”, que é reservado para relatórios do sistema. Para a mesma entidade, há apenas um relatório para a mesma fonte e propriedade; se vários relatórios forem gerados para a mesma fonte e propriedade, eles substituirão uns aos outros, seja no lado do cliente de integridade (se estiverem divididos em lotes), seja no lado do repositório de integridade. A substituição será feita com base no número de sequência: relatórios mais recentes (com número de sequência mais alto) substituem os relatórios mais antigos.

### Eventos de integridade
Internamente, o Repositório de Integridade mantém os eventos de integridade, que contêm todas as informações dos relatórios mais os metadados adicionais, como a hora que o relatório foi recebido para o cliente de integridade e a hora em que foi modificado no lado do servidor. Os eventos de integridade são retornados pelas [consultas de integridade](service-fabric-view-entities-aggregated-health.md#health-queries).

Os metadados adicionados contêm:

- SourceUtcTimestamp: a hora que o relatório foi recebido para o cliente de integridade (UTC)

- LastModifiedUtcTimestamp: a hora em que o relatório foi modificado pela última vez no lado do servidor (UTC)

- IsExpired: sinalizador para indicar se o relatório estava expirado no momento que a consulta foi executada pelo Repositório de Integridade. Um evento pode ter expirado somente se RemoveWhenExpired for falso. Caso contrário, o evento não será retornado pela consulta; ele será removido do repositório.

- LastOkTransitionAt, LastWarningTransitionAt, LastErrorTransitionAt: última vez das transições OK/Aviso/Erro. Esses campos fornecem o histórico da transição de estados de integridade para o evento.

Os campos de transição de estado podem ser usados para gerar alertas mais inteligentes ou informações “históricas” de evento de integridade. Eles permitem cenários como:

- Alerta quando uma propriedade estiver no estado Aviso/Erro por mais de X minutos. Isso evita a geração de alertas em condições temporárias. Por exemplo, se o estado da integridade estiver em Aviso por mais de 5 minutos, ele pode ser traduzido em (HealthState == Aviso e Agora - LastWarningTransitionTime
> 

- Alerta apenas em condições que mudaram nos últimos X minutos. Se um relatório estiver em estado de Erro desde antes disso, ele poderá ser ignorado (pois ele já tinha sido sinalizado anteriormente).

- Se uma propriedade está alternando entre Aviso e Erro, determine por quanto tempo ele esteve não íntegro (isto é, não OK). Por exemplo: alerta se a propriedade não esteve íntegra por mais de 5 minutos pode ser traduzido em: (HealthState != Ok e Agora - LastOkTransitionTime > 5 minutos).

## Exemplo: reportar e avaliar integridade do aplicativo
O exemplo a seguir envia um relatório de integridade por meio PowerShell no aplicativo denominado fabric:/WordCount da origem MyWatchdog. O relatório de integridade contém informações sobre a propriedade de integridade Disponibilidade em um estado de integridade de Erro, com TTL infinita. Em seguida, ele consultará a integridade do aplicativo, que retornará erro do estado de integridade agregada e o evento de integridade reportado como parte da lista de eventos de integridade.

```powershell
PS C:> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:> Get-ServiceFabricApplicationHealth fabric:/WordCount

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Warning

                                  ServiceName           : fabric:/WordCount/WordCount.WebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.1
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.5
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.3
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 5102
                                  SentAt                : 4/15/2015 5:29:15 PM
                                  ReceivedAt            : 4/15/2015 5:29:15 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/15/2015 5:29:15 PM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 130736794527105907
                                  SentAt                : 4/16/2015 5:37:32 PM
                                  ReceivedAt            : 4/16/2015 5:37:32 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Error = 4/16/2015 5:37:32 PM

```

## Uso do modelo de integridade
O modelo de integridade permite que os serviços de nuvem e a plataforma subjacente da Malha do Serviço ajustem a escala, pois o monitoramento e a determinação da integridade são distribuídos entre os diferentes monitores no cluster. Outros sistemas têm um único serviço centralizado no nível de cluster que analisa todas as informações *potencialmente* úteis emitidas pelos serviços. Isso impede a respectiva escalabilidade e não permite que eles coletem informações bastante específicas que ajudam a identificar problemas e problemas potenciais mais próximos possíveis da causa raiz.

O modelo de integridade é muito usado para monitoramento e diagnóstico, para avaliar a integridade do cluster e do aplicativo, bem como para atualizações monitoradas. Outros serviços usam os dados de integridade para executar reparos automáticos, criar histórico de integridade do cluster e emitir alertas em determinadas condições.

## Próximas etapas
[Como exibir relatórios de integridade da Malha do Serviço](service-fabric-view-entities-aggregated-health.md)

[Usando relatórios de integridade do sistema para solução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Adicionando relatórios de integridade personalizados da Malha de Serviço](service-fabric-report-health.md)

[Como monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização de aplicativo da Malha do Serviço](service-fabric-application-upgrade.md)
 

<!---HONumber=July15_HO2-->