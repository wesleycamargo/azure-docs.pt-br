<properties
   pageTitle="Monitoramento de integridade do Service Fabric | Microsoft Azure"
   description="Uma introdução ao modelo de Monitoramento de integridade do Service Fabric do Azure, que fornece monitoramento do cluster e seus aplicativos e serviços."
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
   ms.date="01/26/2016"
   ms.author="oanapl"/>

# Introdução ao monitoramento da integridade do Service Fabric
O Service Fabric do Azure introduz um modelo de integridade que fornece avaliação e relatório de integridade avançados, flexíveis e extensíveis. Isso inclui monitoramento do estado quase em tempo real do cluster e dos serviços que são executados nele. Você pode obter facilmente as informações sobre integridade e tomar medidas para corrigir possíveis problemas antes que eles se espalhem e causem interrupções massivas. No modelo comum, os serviços enviam relatórios com base na respectiva exibição local e as informações são agregadas para fornecer uma exibição geral no nível de cluster.

Os componentes da Malha do Serviço usam esse modelo de integridade para reportar o respectivo estado atual. É possível usar o mesmo mecanismo para reportar a integridade de seus aplicativos. A qualidade e o detalhamento do relatório de integridade específicos às suas condições personalizadas determinam com que facilidade você poderá detectar e corrigir problemas dos aplicativos em execução.

> [AZURE.NOTE] Iniciamos o subsistema de integridade para atender à necessidade de atualizações monitoradas. O Service Fabric fornece atualizações monitoradas que sabem como atualizar um cluster ou um aplicativo sem tempo de inatividade, com mínima e nenhuma intervenção do usuário e com disponibilidade total do cluster e do aplicativo. Para isso, a atualização verifica a integridade com base em políticas de atualização configuradas e só prossegue quando a integridade respeita os limites desejados. Caso contrário, a atualização é automaticamente revertida ou pausada para dar aos administradores a oportunidade de corrigir os problemas. Para saber mais sobre atualizações de aplicativo, consulte [este artigo](service-fabric-application-upgrade.md).

## Repositório de Integridade
O Repositório de Integridade mantém informações relacionadas à integridade sobre entidades no cluster para facilidade de recuperação e avaliação. Ele é implementado como um serviço com estado persistente da Malha do Serviço, garantindo alta disponibilidade e escalabilidade. O Repositório de Integridade faz parte do aplicativo **fabric:/System** e é disponibilizado assim que o cluster é ativado e está em execução.

## Hierarquia e entidades de integridade
As entidades de integridade são organizadas em uma hierarquia lógica que captura as interações e dependências entre diferentes entidades. As entidades e a hierarquia são criadas automaticamente pelo Repositório de Integridade com base nos relatórios recebidos dos componentes do Service Fabric.

As entidades de integridade espelham as entidades do Service Fabric. (Por exemplo, a **entidade de aplicativo de integridade** corresponde a uma instância do aplicativo implantada no cluster, a **entidade de nó de integridade** corresponde a um nó de cluster do Service Fabric.) A hierarquia de integridade captura as interações das entidades do sistema e é a base para a avaliação avançada de integridade. Você pode aprender sobre os principais conceitos do Service Fabric em [Visão geral técnica do Service Fabric](service-fabric-technical-overview.md). Para saber mais sobre aplicativos, vá para [Modelo de aplicativo do Service Fabric](service-fabric-application-model.md).

As entidades de integridade e a hierarquia proporcionam relatório, depuração e monitoramento efetivos do cluster e dos aplicativos. O modelo de integridade oferece uma representação precisa e *granular* da integridade das muitas partes móveis do cluster.

![Entidades de integridade.][1] As entidades de integridade, organizadas em uma hierarquia com base nas relações de pai/filho.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

As entidades de integridade são:

- **Cluster**. Representa a integridade de um cluster da Malha do Serviço. Os relatórios de integridade do cluster descrevem as condições que afetam o cluster inteiro e não podem ser limitados a um ou mais filhos não íntegros. O exemplo inclui a personalidade do cluster sendo dividida devido ao particionamento da rede ou problemas de comunicação.

- **Nó**. Representa a integridade de um nó da Malha do Serviço. Os relatórios de integridade do nó descrevem condições que afetam toda a funcionalidade do nó. Normalmente, elas afetam todas as entidades implantadas em execução. Os exemplos incluem quando um nó não tem espaço em disco (ou outra propriedade em todo o computador, como memória, conexões) ou quando o nó está desativado. A entidade de nó é identificada pelo nome do nó (cadeia de caracteres).

- **Aplicativo**. Representa a integridade de uma instância do aplicativo em execução no cluster. Os relatórios de integridade do aplicativo descrevem condições que afetam a integridade geral do aplicativo. Elas não podem ser limitadas a filhos individuais (serviços ou aplicativos implantados). Os exemplos incluem a interação de ponta a ponta entre diferentes serviços no aplicativo. A entidade de aplicativo é identificada pelo nome do aplicativo (URI).

- **Serviço**. Representa a integridade de um serviço em execução no cluster. Os relatórios de integridade do serviço descrevem condições que afetam a integridade geral do serviço e as mesmas não podem ser limitadas a uma partição ou réplica. Os exemplos incluem uma configuração de serviço (como compartilhamento de arquivos externos ou porta) que está causando problemas em todas as partições. A entidade de serviço é identificada pelo nome do serviço (URI).

- **Partição**. Representa a integridade de uma partição de serviço. Os relatórios de integridade da partição descrevem condições que afetam todo o conjunto de réplicas. Os exemplos incluem quando o número de réplicas está abaixo da contagem de destino e quando a partição está na perda do quórum. A entidade de partição é identificada pela ID da partição (GUID).

- **Réplica**. Representa a integridade de uma réplica de serviço com estado ou uma instância de serviço sem estado. Essa é a menor unidade que os watchdogs e componentes do sistema podem reportar para um aplicativo. Para serviços com estado, os exemplos incluem um relatório de réplica primária quando a mesma não puder replicar operações em locais secundários e quando a replicação não está seguindo no ritmo esperado. Além disso, uma instância sem estado pode relatar quando estiver em execução sem recursos ou tiver problemas de conectividade. A entidade de réplica é identificada pela ID da partição (GUID) e pela ID de réplica ou instância (longo).

- **DeployedApplication**. Representa a integridade de um *aplicativo que é executado em um nó*. Os relatórios de integridade do aplicativo implantado descrevem condições específicas ao aplicativo no nó que não podem ser limitados aos pacotes de serviço implantados no mesmo nó. Os exemplos incluem quando o pacote de aplicativos não pode ser baixado nesse nó e quando há um problema na configuração das entidades de segurança do aplicativo no nó. O aplicativo implantado é identificado pelo nome do aplicativo (URI) e pelo nome do nó (cadeia de caracteres).

- **DeployedServicePackage**. Representa a integridade de um pacote de serviço de um aplicativo em execução em um nó no cluster. Descreve as condições específicas a um pacote de serviço que não afetam os outros pacotes de serviço no mesmo nó para o mesmo aplicativo. Os exemplos incluem um pacote de códigos no pacote de serviço que não pode ser iniciado e um pacote de configuração que não pode ser lido. O pacote de serviço implantado é identificado pelo nome do aplicativo (URI), o nome do nó (cadeia de caracteres) e o nome do manifesto do serviço (cadeia de caracteres).

A granularidade do modelo de integridade facilita a detecção e correção dos problemas. Por exemplo, se um serviço não estiver respondendo, é possível relatar que a instância do aplicativo não está íntegra. Isso não é ideal, no entanto, porque o problema talvez não esteja afetando todos os serviços no aplicativo. O relatório deve ser aplicado no serviço não íntegro ou partição filha específica, se mais informações apontarem para essa partição. Os dados emergem automaticamente pela hierarquia e uma partição não íntegra ficará visível nos níveis de serviço e aplicativo. Isso ajuda a identificar e resolver a causa raiz do problema mais rapidamente.

A hierarquia de integridade é composta por relações de pai/filho. Um cluster é composto de nós e aplicativos. Os aplicativos possuem serviços e aplicativos implantados. Os aplicativos implantados possuem pacotes de serviço implantados. Os serviços têm partições, e cada partição tem uma ou mais réplicas. Há uma relação especial entre nós e entidades implantadas. Se um nó não estiver íntegro conforme reportado por seu componente de sistema de autoridade (Serviço do Gerenciador de Failover), ele afetará os aplicativos implantados, pacotes de serviço e réplicas implantadas nele.

A hierarquia de integridade representa o estado mais recente do sistema com base em relatórios de integridade mais recentes, que é uma informação quase em tempo real. Watchdogs internos e externos podem reportar as mesmas entidades com base na lógica específica do aplicativo ou em condições monitoradas personalizadas. Os relatórios do usuário coexistem com os relatórios do sistema.

O tempo investido no planejamento de como relatar a integridade e reagir enquanto o serviço de nuvem amplo é projetado facilita a depuração, o monitoramento e as operações posteriores.

## Estados de integridade
O Service Fabric usa três estados de integridade para descrever se uma entidade está íntegra ou não: OK, Aviso e Erro. Qualquer relatório enviado ao Repositório de Integridade deve especificar um desses estados. O resultado da avaliação de integridade é um desses estados.

Os estados de integridade possíveis são:

- **OK** A entidade está íntegra. Não há nenhum problema conhecido reportado em seus filhos (quando aplicável).

- **Aviso**. A entidade apresenta alguns problemas, mas ainda não está em estado não íntegro (isto é, atraso inesperado que não está causando nenhum problema funcional). Em alguns casos, a condição de aviso pode se corrigir sem nenhuma intervenção especial, e é útil para proporcionar visibilidade do que está acontecendo. Em outros casos, a condição de Aviso pode se transformar em um problema grave sem intervenção do usuário.

- **Erro**. A entidade não está íntegra. Uma medida deve ser tomada para corrigir o estado da entidade, pois ela não funciona corretamente.

- **Desconhecido**. A entidade não existe no repositório de integridade. Esse resultado pode ser obtido em consultas distribuídas que mesclam resultados de vários componentes. Eles podem incluir a consulta para obter a lista de nós do Service Fabric, que vai para o **FailoverManager** e o **HealthManager**, ou a consulta para obter a lista de aplicativos, que vai para a **ClusterManager** e **HealthManager**. Essas consultas mesclam os resultados de vários componentes do sistema. Se outro componente do sistema tiver uma entidade que ainda não atingiu o repositório de integridade ou que foi removida do repositório de integridade, a consulta mesclada populará o resultado de integridade com o estado desconhecido.

## Políticas de integridade
O Repositório de Integridade aplica políticas de integridade para determinar se uma entidade está íntegra com base em seus relatórios e filhos.

> [AZURE.NOTE] As políticas de integridade podem ser especificadas no manifesto do cluster (para avaliação de integridade do cluster e do nó) ou no manifesto do aplicativo (para avaliação de aplicativo e qualquer um de seus filhos). As solicitações de avaliação de integridade também podem passar pelas políticas de avaliação de integridade personalizadas, que serão usadas apenas para avaliação.

Por padrão, o Service Fabric aplica regras rígidas (tudo deve estar íntegro) para a relação hierárquica pai-filho. Se mesmo um dos filhos tem um evento não íntegro, o pai será considerado não íntegro.

### Política de integridade do cluster
A política de integridade do cluster é usada para avaliar o estado de integridade do cluster e os estados de integridade do nó. A política pode ser definida no manifesto do cluster. Se não estiver presente, a política padrão (zero falhas toleradas) é usada. A política de integridade do cluster contém:

- **ConsiderWarningAsError**. Especifica se os relatórios de integridade Aviso devem ser tratados como erros durante a avaliação de integridade. Padrão: falso.

- **MaxPercentUnhealthyApplications**. Especifica a porcentagem máxima tolerada de aplicativos que podem estar não íntegros antes de o cluster ser considerado com erro.

- **MaxPercentUnhealthyNodes**. Especifica a porcentagem máxima tolerada de nós que podem estar não íntegros antes de o cluster ser considerado com erro. Em clusters grandes, sempre haverá nós desativados ou ausentes para reparo, de modo que essa porcentagem deve ser configurada para tolerá-los.

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
A política de integridade do aplicativo descreve como a avaliação da agregação dos estados de eventos e filhos é feita para aplicativos e seus filhos. Ela pode ser definida no manifesto do aplicativo, **ApplicationManifest.xml**, no pacote de aplicativos. Se nenhuma política for especificada, o Service Fabric suporá que a entidade não está íntegra se ela tiver um relatório de integridade ou um filho no estado de integridade com erro ou aviso. As políticas configuráveis são:

- **ConsiderWarningAsError**. Especifica se os relatórios de integridade Aviso devem ser tratados como erros durante a avaliação de integridade. Padrão: falso.

- **MaxPercentUnhealthyDeployedApplications**. Especifica a porcentagem máxima tolerada de aplicativos implantados que podem estar não íntegros antes de o aplicativo ser considerado com erro. A porcentagem é calculada pela divisão do número de aplicativos implantados não íntegros pelo número de nós em que os aplicativos estão atualmente implantados no cluster. O cálculo é arredondado para cima para tolerar uma falha em um número pequeno de nós. Porcentagem padrão: zero.

- **DefaultServiceTypeHealthPolicy**. Especifica a política de integridade do tipo de serviço padrão, que substituirá a política de integridade padrão para todos os tipos de serviço no aplicativo.

- **ServiceTypeHealthPolicyMap**. Fornece um mapa de políticas de integridade do serviço por tipo de serviço. Elas substituem as políticas de integridade do tipo de serviço padrão para cada tipo de serviço de especificado. Por exemplo, em um aplicativo que contenha um tipo de serviço gateway sem estado e um tipo de serviço mecanismo com estado, as políticas de integridade para os serviços sem e com estado pode ser configurada de modo diferente. Ao especificar a política por tipo de serviço você permite um controle mais granular da integridade do serviço.

### Políticas de integridade do tipo de serviço
A política de integridade do tipo de serviço especifica como avaliar e agregar filhos dos serviços. A política contém:

- **MaxPercentUnhealthyPartitionsPerService**. Especifica a porcentagem máxima tolerada de partições não íntegras antes de um serviço ser considerado não íntegro. Porcentagem padrão: zero.

- **MaxPercentUnhealthyReplicasPerPartition**. Especifica a porcentagem máxima tolerada de réplicas não íntegras antes de uma partição ser considerada não íntegro. Porcentagem padrão: zero.

- **MaxPercentUnhealthyServices**. Especifica a porcentagem máxima tolerada de serviços não íntegros antes de um aplicativo ser considerado não íntegro. Porcentagem padrão: zero.

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

### Agregação do relatório de integridade
Uma entidade pode ter vários relatórios de integridade enviados por diferentes relatores (componentes do sistema ou watchdogs) em propriedades diferentes. A agregação usa as políticas de integridade associadas, especificamente o membro ConsiderWarningAsError da política de integridade do aplicativo ou cluster. Ela especifica como avaliar avisos.

O estado de integridade agregada é disparado pelos *piores* relatórios de integridade da entidade. Se houver pelo menos um relatório de integridade com Erro, o estado de integridade agregada será um erro.

![Agregação de relatório de integridade com o relatório de erro.][2]

Um relatório de integridade de erro dispara a entidade de integridade para estar no estado de erro.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Se não houver relatórios de erro, e um ou mais avisos, o estado de integridade agregada será aviso ou erro, dependendo do sinalizador de política ConsiderWarningAsError.

![Agregação do relatório de integridade com o relatório de aviso e ConsiderWarningAsError false.][3]

A agregação do relatório de integridade com relatório de aviso e ConsiderWarningAsError definido como falso (padrão).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### Agregação de integridade de filhos
O estado de integridade agregada de uma entidade reflete os estados de integridade dos filhos (quando aplicável). O algoritmo para agregar estados de integridade dos filhos usa as políticas aplicáveis de integridade com base no tipo de entidade.

![Agregação de integridade de entidades filho.][4]

Agregação de filhos com base nas políticas de integridade.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Depois que o repositório de integridade avaliou todos os filhos, ele agrega seus estados de integridade com base na porcentagem máxima configurada de filhos não íntegros. Isso é obtido da política com base no tipo de entidade e filho.

- Se todos os filhos tiverem estados OK, o estado de integridade agregada dos filhos será OK.

- Se todos os filhos tiverem estados OK e aviso, o estado de integridade agregada dos filhos é um aviso.

- Se houver filhos com estados de erro que não respeitarem a porcentagem máxima permitida de filhos não íntegros, o estado de integridade agregada é um erro.

- Se os filhos com estados de erro respeitarem a porcentagem máxima permitida de filhos não íntegros, o estado de integridade agregada é um aviso.

## Relatório de integridade
Os componentes do sistema e watchdogs internos/externos podem ser relatados em relação às entidades do Service Fabric. Os relatores fazem uma determinação *local* da integridade da entidades monitoradas com base em algumas condições que estão monitorando. Eles não precisam observar nenhum estado global ou dados agregados. Isso não é desejado, pois tornaria os relatores organismos complexos que precisariam observar muitas coisas para inferir quais informações enviar.

Para enviar dados de integridade ao repositório de integridade, o relator precisa identificar a entidade afetada e criar um relatório de integridade. O relatório pode então ser enviado por meio da API usando **FabricClient.HealthManager.ReportHealth**, por meio do Powershell ou da REST.

### Relatórios de integridade
Os relatórios de integridade de cada uma das entidades no cluster contêm as seguintes informações:

- **SourceId**. Uma cadeia de caracteres que identifica exclusivamente o relator do evento de integridade.

- **Identificador de entidade**. Identifica a entidade na qual o relatório é aplicado. Varia de acordo com o [tipo de entidade](service-fabric-health-introduction.md#health-entities-and-hierarchy):

  - Cluster. Nenhum.

  - Nó. Nome do nó (cadeia de caracteres).

  - Console. Nome do aplicativo (URI). Representa o nome da instância do aplicativo implantado no cluster.

  - Serviço. Nome do serviço (URI). Representa o nome da instância do serviço implantado no cluster.

  - Partição. ID da partição (GUID). Representa o identificador exclusivo da partição.

  - Réplica. A ID de réplica do serviço com estado ou a ID da instância do serviço sem estado (INT64).

  - DeployedApplication. Nome do aplicativo (URI) e o nome do nó (cadeia de caracteres).

  - DeployedServicePackage. Nome do aplicativo (URI), nome do nó (cadeia de caracteres) e nome do manifesto do serviço (cadeia de caracteres).

- **Propriedade**. Um *cadeia de caracteres* (não uma enumeração fixa) que permite ao relator classificar o evento de integridade para uma propriedade específica da entidade. Por exemplo, o relator A pode relatar a integridade da propriedade “storage” de Node01 e o relator B pode relatar a integridade na propriedade “connectivity” de Node01. No Repositório de Integridade, esses relatórios são tratados como eventos de integridade distintos para a entidade Node01.

- **Descrição**. Uma cadeia de caracteres que permite ao relator fornecer informações detalhadas sobre o evento de integridade. **SourceId**, **Property** e **HealthState** devem descrever o relatório por completo. A descrição adiciona informações legíveis sobre o relatório. Isso facilita o entendimento para os administradores e usuários.

- **HealthState**. Uma [enumeração](service-fabric-health-introduction.md#health-states) que descreve o estado de integridade do relatório. Os valores aceitáveis são OK, Aviso e Erro.

- **TimeToLive**. Um período de tempo que indica por quanto tempo o relatório de integridade é válido. Juntamente com **RemoveWhenExpired**, permite que o Repositório de Integridade saiba como avaliar eventos expirados. Por padrão, o valor é infinito e o relatório é válido indefinidamente.

- **RemoveWhenExpired**. Um valor booleano. Se definido como verdadeiro, o relatório de integridade expirado será removido automaticamente do repositório de integridade e o relatório não afeta a avaliação da integridade da entidade. É usado quando o relatório é válido apenas por um período especificado de tempo e o relator não precisa removê-lo explicitamente. Ele também é usado para excluir relatórios do repositório de integridade (por exemplo, um watchdog é alterado e interrompe o envio de relatórios com propriedade e origem anteriores). Portanto, ele pode enviar um relatório com TimeToLive curto e RemoveWhenExpired para eliminar qualquer estado anterior do Repositório de Integridade. Se o valor for definido como false, o relatório expirado será tratado como erro na avaliação de integridade. O valor false indica ao repositório de integridade que a origem deve relatar periodicamente essa propriedade. Caso contrário, deve haver algo errado com o watchdog. A integridade do watchdog é capturada ao considerar o evento como um erro.

- **SequenceNumber**. Um inteiro positivo que sempre precisa ser aumentado, pois ele representa a ordem dos relatórios. Ele é usado pelo Repositório de Integridade para detectar relatórios obsoletos que são recebidos em atraso devido a atrasos na rede ou outros problemas. Um relatório é rejeitado se o número da sequência for menor ou igual ao aplicado mais recentemente para a mesma entidade, origem e propriedade. Se não for especificado, o número de sequência é gerado automaticamente. É necessário colocar o número de sequência apenas ao relatar transições de estado. Nessa situação, a origem precisa lembrar quais relatórios enviou e manter as informações de recuperação no failover.

Essas quatro informações: SourceId, identificador da entidade, Propriedade e HealthState são obrigatórios para cada relatório de integridade. A cadeia de caracteres SourceId não pode iniciar com o prefixo “**System.**”, que é reservado para relatórios do sistema. Para a mesma entidade, há apenas um relatório para a mesma origem e propriedade. Se vários relatórios forem gerados para a mesma fonte e propriedade, eles substituirão uns aos outros, seja no lado do cliente de integridade (se estiverem divididos em lotes), seja no lado do repositório de integridade. A substituição é baseada nos números da sequência: relatórios mais recentes (com número de sequência mais alto) substituem os relatórios mais antigos.

### Eventos de integridade
Internamente, o repositório de integridade mantém os eventos de integridade, que contêm todas as informações de relatórios, bem como metadados adicionais. Isso inclui a hora que o relatório foi fornecido ao cliente de integridade e a hora em que ele foi modificado no lado do servidor. Os eventos de integridade são retornados pelas [consultas de integridade](service-fabric-view-entities-aggregated-health.md#health-queries).

Os metadados adicionados contêm:

- **SourceUtcTimestamp**. A hora que o relatório foi fornecido ao cliente de integridade (Coordinated Universal Time)

- **LastModifiedUtcTimestamp**. A hora em que o relatório foi modificado pela última vez no lado do servidor (Coordinated Universal Time).

- **IsExpired**. Um sinalizador para indicar se o relatório estava expirado no momento que a consulta foi executada pelo Repositório de Integridade. Um evento pode ter expirado somente se RemoveWhenExpired for false. Caso contrário, o evento não é retornado pela consulta e é removido do armazenamento.

- **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. A última hora para transições OK/aviso/erro. Esses campos fornecem o histórico das transições de estado de integridade para o evento.

Os campos de transição de estado podem ser usados para gerar alertas mais inteligentes ou informações “históricas” de evento de integridade. Eles permitem cenários como:

- Alertar quando uma propriedade estiver no estado aviso/erro por mais de X minutos. Isso evita a geração de alertas em condições temporárias. Por exemplo, um alerta se o estado da integridade estiver em aviso por mais de 5 minutos pode ser traduzido em (HealthState == Aviso e Agora - LastWarningTransitionTime > 5 minutos).

- Alertar apenas em condições que mudaram nos últimos X minutos. Se um relatório estiver em estado de erro desde antes da hora especificada, ele poderá ser ignorado pois já tinha sido sinalizado anteriormente.

- Se uma propriedade está alternando entre aviso e erro, determine por quanto tempo ele esteve não íntegro (isto é, não OK). Por exemplo, um alerta se a propriedade não esteve íntegra por mais de 5 minutos pode ser traduzido em: (HealthState != Ok e Agora - LastOkTransitionTime > 5 minutos).

## Exemplo: relatar e avaliar integridade do aplicativo
O exemplo a seguir envia um relatório de integridade por meio PowerShell no aplicativo **fabric:/WordCount** da origem **MyWatchdog**. O relatório de integridade contém informações sobre a propriedade de integridade disponibilidade em um estado de integridade de Erro, com TimeToLive infinito. Em seguida, ele consulta a integridade do aplicativo, que retornará erro do estado de integridade agregada e o evento de integridade relatado como parte da lista de eventos de integridade.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount

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
O modelo de integridade permite que os serviços de nuvem e a plataforma subjacente do Service Fabric ajustem a escala, pois o monitoramento e a determinação da integridade são distribuídos entre os diferentes monitores no cluster. Outros sistemas têm um único serviço centralizado no nível de cluster que analisa todas as informações *potencialmente* úteis emitidas pelos serviços. Essa abordagem impede a escalabilidade dos mesmos. Isso também impede que eles coletem informações bastante específicas que ajudam a identificar problemas e problemas potenciais mais próximos possíveis da causa raiz.

O modelo de integridade é muito usado para monitoramento e diagnóstico, para avaliar a integridade do cluster e do aplicativo, bem como para atualizações monitoradas. Outros serviços usam os dados de integridade para executar reparos automáticos, para criar histórico de integridade do cluster e para emitir alertas em determinadas condições.

## Próximas etapas
[Como exibir relatórios de integridade do Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Usar relatórios de integridade do sistema para solução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Adicionar relatórios de integridade personalizados do Service Fabric](service-fabric-report-health.md)

[Monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização de aplicativos do Service Fabric](service-fabric-application-upgrade.md)

<!---HONumber=AcomDC_0128_2016-->