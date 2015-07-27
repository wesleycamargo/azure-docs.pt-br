<properties 
	pageTitle="Análise de Armazenamento" 
	description="Como gerenciar a simultaneidade para os serviços Blob, Fila, Tabela e Arquivo" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="tamram"/>

# Habilitando métricas de armazenamento e exibição de dados de métricas

Por padrão, as métricas de armazenamento não estão habilitadas para os serviços de armazenamento. Você pode habilitar o monitoramento usando o Portal de Gerenciamento do Azure, Windows PowerShell ou com programação por meio de uma API de armazenamento.

Quando você habilita as métricas de armazenamento, você deve escolher um período de retenção para os dados: este período determina quanto tempo o serviço de armazenamento mantém as métricas e as cobranças para o espaço necessário para armazená-los. Normalmente, você deve usar um período de retenção mais curto para métricas de minuto em vez de métricas por hora por causa do espaço extra significativo necessário para métricas de minuto. Você deve escolher um período de retenção que você tenha tempo suficiente para analisar os dados e baixar qualquer métricas que você deseja manter para análise offline ou para fins de relatório. Lembre-se de que você também será cobrado para baixar dados de métrica de sua conta de armazenamento.

## Como habilitar métricas de armazenamento usando o portal de gerenciamento do Azure

No Portal de gerenciamento do Azure, você pode usar a página Configurar para uma conta de armazenamento para controlar métricas de armazenamento. Para monitoramento, você pode definir um nível e um período de retenção em dias para cada um dos blobs, tabelas e filas. Em cada caso, o nível é um dos seguintes:


- Desativado — isso significa que nenhuma métrica será coletada.

- Mínimo — as métricas de armazenamento coletam um conjunto básico de métricas como ingresso/egresso, disponibilidade, latência e porcentagens de êxitos, que são agregadas para os serviços Blob, tabela e fila.

- Detalhado — as métricas de armazenamento coletam um conjunto completo de métricas que inclui as mesmas métricas para cada operação de API, além das métricas de nível de serviço de armazenamento. As métricas no modo detalhado permitem uma análise mais próxima dos problemas que ocorrem durante operações de aplicativo.

Observe que o Portal de gerenciamento não permite atualmente configurar métricas por minuto em sua conta de armazenamento. Você deve habilitar a métrica de minutos usando o PowerShell ou programaticamente.


## Como habilitar métricas de armazenamento usando o PowerShell

Você pode usar o PowerShell no computador local para configurar as métricas de armazenamento na sua conta de armazenamento usando o cmdlet do PowerShell do Azure Get-AzureStorageServiceMetricsProperty para recuperar as configurações atuais, e o cmdlet Set-AzureStorageServiceMetricsProperty para alterar as configurações atuais.

Os cmdlets que controlam as métricas de armazenamento usam os seguintes parâmetros:

- Os valorespossíveis de MetricsType são hora e minuto.

- Os valores possíveis de ServiceType são Blob, fila e tabela.

- Os valores possíveis de MetricsLevel são None (equivalente a desativado no Portal de gerenciamento), Service (equivalente ao mínimo no Portal de gerenciamento) e ServiceAndApi (equivalente a detalhado no Portal de gerenciamento).

Por exemplo, o comando a seguir ativa a métrica de minutos para o serviço blob na conta de armazenamento padrão com o período de retenção definido para cinco dias:

`Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`

O comando a seguir recupera o nível de métricas por hora atual e dias de retenção para o serviço blob na conta de armazenamento padrão:

`Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob`

Para saber mais sobre como configurar os cmdlets do PowerShell do Azure para funcionar com sua assinatura do Azure e como selecionar a conta de armazenamento padrão para usar, consulte: [Como instalar e configurar o PowerShell do Azure](../install-configure-powershell.md).

## Como habilitar métricas de armazenamento por meio de programação

Além de usar o Portal de gerenciamento do Azure ou os cmdlets do PowerShell do Azure para controlar métricas de armazenamento, você também pode usar uma das APIs de armazenamento do Azure. Por exemplo, se você estiver usando uma linguagem .NET, você pode usar a biblioteca de cliente de armazenamento.

As classes CloudBlobClient, CloudQueueClient e CloudTableClient têm métodos como SetServiceProperties e SetServicePropertiesAsync que utilizam um objeto ServiceProperties como um parâmetro. Você pode usar o objeto ServiceProperties para configurar métricas de armazenamento. Por exemplo, o trecho C# a seguir mostra como alterar os dias de retenção e nível de métricas para as métricas de fila por hora:

    var storageAccount = CloudStorageAccount.Parse(connStr);
    var queueClient = storageAccount.CreateCloudQueueClient();
    var serviceProperties = queueClient.GetServiceProperties();
     
    serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;
    serviceProperties.HourMetrics.RetentionDays = 10;
     
    queueClient.SetServiceProperties(serviceProperties);
    
## Exibindo as métricas de armazenamento

Quando você tiver configurado as métricas de armazenamento para monitorar sua conta de armazenamento, ela registra as métricas em um conjunto de tabelas conhecido na sua conta de armazenamento. Você pode usar a página de Monitorar para sua conta de armazenamento no Portal de gerenciamento para exibir as métricas de hora assim que forem disponibilizadas em um gráfico. Nesta página no Portal de gerenciamento, você pode:

- Selecionar quais métricas plotar no gráfico (a opção de métricas disponíveis dependerá se você escolheu o monitoramento detalhado ou mínimo para o serviço na página Configurar).


- Selecionar o intervalo de tempo para as métricas exibidas no gráfico.


- Optar por usar um dimensionamento absoluto ou relativo para plotar as métricas.


- Configurar alertas de email para notificar quando uma métrica específica atinge um determinado valor.


Se você quiser baixar as métricas para armazenamento a longo prazo ou para analisá-las localmente, você precisará usar uma ferramenta ou escrever um código para ler as tabelas. Você deve baixar a métrica de minutos para análise. As tabelas não aparecem quando você lista todas as tabelas em sua conta de armazenamento, mas você pode acessá-las diretamente por nome. Muitas ferramentas de navegação de armazenamento de terceiros estão cientes dessas tabelas e permitem que você as exiba diretamente (consulte a postagem do blog [Gerenciadores de armazenamento do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) para obter uma lista de ferramentas disponíveis).

### Métricas por hora
- $MetricsHourPrimaryTransactionsBlob
- $MetricsHourPrimaryTransactionsTable
- $MetricsHourPrimaryTransactionsQueue

### Métricas por minuto
- $MetricsMinutePrimaryTransactionsBlob
- $MetricsMinutePrimaryTransactionsTable
- $MetricsMinutePrimaryTransactionsQueue

### Capacidade
- $MetricsCapacityBlob

Você pode encontrar detalhes completos dos esquemas para essas tabelas no [Esquema da tabela de métricas da análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343264.aspx). As linhas de exemplo a seguir mostram apenas um subconjunto das colunas disponíveis, mas ilustram alguns recursos importantes da maneira como as métricas de armazenamento salvam essas métricas:

| PartitionKey | RowKey | Timestamp | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Disponibilidade | AverageE2ELatency | AverageServerLatency | PercentSuccess |
|---------------|:------------------:|-----------------------------:|---------------|-----------------------|--------------|-------------|--------------|-------------------|----------------------|----------------|
| 20140522T1100 | user;All | 2014-05-22T11:01:16.7650250Z | 7 | 7 | 4003 | 46801 | 100 | 104\.4286 | 6\.857143 | 100 |
| 20140522T1100 | usuário;QueryEntities | 2014-05-22T11:01:16.7640250Z | 5 | 5 | 2694 | 45951 | 100 | 143\.8 | 7\.8 | 100 |
| 20140522T1100 | user;QueryEntity | 2014-05-22T11:01:16.7650250Z | 1 | 1 | 538 | 633 | 100 | 3 | 3 | 100 |
| 20140522T1100 | user;UpdateEntity | 2014-05-22T11:01:16.7650250Z | 1 | 1 | 771 | 217 | 100 | 9 | 6 | 100 |

Neste exemplo dos dados de métrica de minutos, a chave de partição usa o tempo de resolução minuto. A chave de linha identifica o tipo de informação que é armazenado na linha, e isso é composto de duas partes de informações, o tipo de acesso e o tipo de solicitação:

- O tipo de acesso é um usuário ou sistema, onde o usuário refere-se a todas as solicitações de usuário para o serviço de armazenamento e o sistema refere-se às solicitações feitas pela análise de armazenamento.

- O tipo de solicitação é tudo que nesse caso é uma linha de resumo, ou ele identifica a API específica, como QueryEntity ou UpdateEntity.


Os dados de exemplo acima mostram todos os registros de um minuto (iniciando às 11h00), para o número de solicitações de QueryEntities mais o número de solicitações de QueryEntity mais o número de solicitações de UpdateEntity adicionam até sete, que é o total mostrado na linha user:All. Da mesma forma, você pode derivar a latência média de ponta a ponta 104.4286 na linha user:All ao calcular ((143.8 * 5) + 3 + 9)/7.

Considere configurar alertas no Portal de gerenciamento na página de Monitorar para que as métricas de armazenamento possa notificá-lo automaticamente de todas as alterações importantes no comportamento dos seus serviços de armazenamento. Se você usar uma ferramenta de Gerenciador de armazenamento para baixar esses dados de métricas em um formato delimitado, você pode usar o Microsoft Excel para analisar os dados. Consulte a postagem do blog [Gerenciadores de armazenamento do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) para obter uma lista das ferramentas do Gerenciador de armazenamento disponível.



## Acessando dados de métrica programaticamente

A listagem a seguir mostra o código C# de exemplo, que acessa a métrica de minutos para um intervalo de minutos e exibe os resultados em uma janela de console. Ele usa a biblioteca de armazenamento do Azure versão 4, o que inclui a classe CloudAnalyticsClient que simplifica o acesso às tabelas de métricas no armazenamento.

    private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)
    {
    // Convert the dates to the format used in the PartitionKey
    var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    
    var services = Enum.GetValues(typeof(StorageService));
    foreach (StorageService service in services)
    {
    Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);
    var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);
    var t = analyticsClient.GetMinuteMetricsTable(service);
    var opContext = new OperationContext();
    var query =
    from entity in metricsQuery
    // Note, you can't filter using the entity properties Time, AccessType, or TransactionType
    // because they are calculated fields in the MetricsEntity class.
    // The PartitionKey identifies the DataTime of the metrics.
    where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0 
    select entity;
    
    // Filter on "user" transactions after fetching the metrics from Table Storage.
    // (StartsWith is not supported using LINQ with Azure table storage)
    var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));
    var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();
    Console.WriteLine(resultString);
    }
    }
    
    private static string MetricsString(MetricsEntity entity, OperationContext opContext)
    {
    var entityProperties = entity.WriteEntity(opContext);
    var entityString =
    string.Format("Time: {0}, ", entity.Time) +
    string.Format("AccessType: {0}, ", entity.AccessType) +
    string.Format("TransactionType: {0}, ", entity.TransactionType) +
    string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));
    return entityString;
    
    }




## Quais taxas você provoca quando habilita a métrica de armazenamento?

Gravar solicitações para criar entidades de tabela para métricas são cobradas de acordo com as taxas padrão aplicáveis a todas as operações de armazenamento do Azure.

Solicitações de leitura e exclusão por um cliente para dados de métrica também são faturáveis em taxas padrão. Se você configurou uma política de retenção de dados, que não seja cobrado quando o armazenamento do Azure excluir dados de métricas antigos. No entanto, se você excluir dados de análise, sua conta será cobrada para as operações de exclusão.

A capacidade usada pelas tabelas de métricas também é faturável. Você pode usar o seguinte para estimar a quantidade de capacidade usada para armazenar dados de métricas:

- Se cada hora de um serviço utiliza todas as APIs de cada serviço, então aproximadamente 148KB de dados serão armazenados nas tabelas de transações de métricas a cada hora se você tiver habilitado o serviço e o resumo de nível de API.

- Se cada hora de um serviço utiliza todas as APIs de cada serviço, então cerca de 12KB de dados serão armazenados nas tabelas de transação de métricas a cada hora se você tiver habilitado apenas o resumo do nível de serviço.

- A tabela de capacidade para blobs tem duas linhas adicionadas por dia (fornecida pelo usuário que optou pelos logs). Isso implica que todos os dias o tamanho dessa tabela aumenta em até aproximadamente 300 bytes.

## Próximas etapas:
[Habilitando o armazenamento de log e acessando os dados de log](https://msdn.microsoft.com/library/dn782840.aspx)
 

<!---HONumber=July15_HO3-->