---
title: Usar os contadores de desempenho no diagnóstico do Azure | Microsoft Docs
description: Use contadores de desempenho em serviços de nuvem ou máquinas virtuais do Azure para localizar afunilamentos e ajustar o desempenho.
services: cloud-services
documentationcenter: .net
author: rboucher
manager: jwhit
editor: tysonn

ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/29/2016
ms.author: robb

---
# Criar e usar contadores de desempenho em um aplicativo do Azure
Este artigo descreve os benefícios de e como colocar os contadores de desempenho no seu aplicativo do Azure. Você pode usá-los para coletar dados, encontrar afunilamentos e ajustar o desempenho do sistema e do aplicativo.

Os contadores de desempenho disponíveis para o Windows Server, o IIS e o ASP.NET podem ser coletados e usados para determinar a integridade das suas funções Web, funções de trabalho e Máquinas Virtuais do Azure. Você também pode criar e usar contadores de desempenho personalizados.

É possível examinar os dados do contador de desempenho

1. Diretamente no host do aplicativo com a ferramenta Monitor de Desempenho acessada usando a Área de Trabalho Remota
2. Com o System Center Operations Manager, usando o Pacote de Gerenciamento do Azure
3. Com outras ferramentas de monitoramento que acessam os dados de diagnóstico transferidos para o armazenamento do Azure. Consulte [Armazenar e exibir dados de diagnóstico no armazenamento do Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx) para saber mais.  

Para saber mais sobre como monitorar o desempenho do seu aplicativo no [portal clássico do Azure](http://manage.azure.com/), confira [Como monitorar Serviços de Nuvem](https://www.azure.com/manage/services/cloud-services/how-to-monitor-a-cloud-service/).

Para obter orientações detalhadas adicionais sobre como criar uma estratégia de registro em log e rastreamento, e usar diagnóstico e outras técnicas para solucionar problemas e otimizar os aplicativos do Azure, consulte [Práticas recomendadas para a solução de problemas no desenvolvimento de aplicativos do Azure](https://msdn.microsoft.com/library/azure/hh771389.aspx).

## Habilitar o monitoramento do contador de desempenho
Os contadores de desempenho não estão habilitados por padrão. O aplicativo ou uma tarefa de inicialização devem modificar a configuração padrão do agente de diagnóstico para incluir contadores de desempenho específicos que você deseja monitorar a cada instância de função.

### Contadores de desempenho disponíveis para o Microsoft Azure
O Azure fornece um subconjunto de contadores de desempenho disponíveis para o Windows Server, o IIS e a pilha ASP.NET. A tabela a seguir lista alguns dos contadores de desempenho relevantes para aplicativos do Azure.

| Categoria do contador: Objeto (instância) | Nome do contador | Referência |
| --- | --- | --- |
| Exceções CLR do .NET (*globais*) |Nº de Exceções Iniciadas/s |Contadores de Desempenho de Exceção |
| Memória CLR do .NET (*global*) |% de Tempo na GC |Contadores de Desempenho de Memória |
| ASP.NET |Reinicializações de Aplicativo |Contadores de Desempenho do ASP.NET |
| ASP.NET |Tempo de Execução de Solicitação |Contadores de Desempenho do ASP.NET |
| ASP.NET |Solicitações Desconectadas |Contadores de Desempenho do ASP.NET |
| ASP.NET |Reinicializações do processo de trabalho |Contadores de Desempenho do ASP.NET |
| Aplicativos ASP.NET (**total**) |Total de Solicitações |Contadores de Desempenho do ASP.NET |
| Aplicativos ASP.NET (**total**) |Solicitações/s |Contadores de Desempenho do ASP.NET |
| ASP.NET v4.0.30319 |Tempo de Execução de Solicitação |Contadores de Desempenho do ASP.NET |
| ASP.NET v4.0.30319 |Tempo de Espera da Solicitação |Contadores de Desempenho do ASP.NET |
| ASP.NET v4.0.30319 |Solicitações Atuais |Contadores de Desempenho do ASP.NET |
| ASP.NET v4.0.30319 |Solicitações Enfileiradas |Contadores de Desempenho do ASP.NET |
| ASP.NET v4.0.30319 |Solicitações Rejeitadas |Contadores de Desempenho do ASP.NET |
| Memória |MBytes Disponíveis |Contadores de Desempenho de Memória |
| Memória |Bytes Confirmados |Contadores de Desempenho de Memória |
| Processador(\_Total) |% de Tempo do Processador |Contadores de Desempenho do ASP.NET |
| TCPv4 |Falhas de Conexão |Objeto TCP |
| TCPv4 |Conexões Estabelecidas |Objeto TCP |
| TCPv4 |Conexões Estabelecidas |Objeto TCP |
| TCPv4 |Segmentos Enviados/sec |Objeto TCP |
| Interface de Rede(*) |Bytes Recebidos/sec |Objeto de Interface de Rede |
| Interface de Rede(*) |Bytes Enviados/sec |Objeto de Interface de Rede |
| Interface de Rede(Microsoft Virtual Machine Bus Network Adapter \_2) |Bytes Recebidos/sec |Objeto de Interface de Rede |
| Interface de Rede(Microsoft Virtual Machine Bus Network Adapter \_2) |Bytes Enviados/sec |Objeto de Interface de Rede |
| Interface de Rede(Microsoft Virtual Machine Bus Network Adapter \_2) |Bytes Total/sec |Objeto de Interface de Rede |

## Criar e adicionar contadores de desempenho personalizados ao seu aplicativo
O Azure dá suporte à criação e modificação de contadores de desempenho personalizados para funções Web e funções de trabalho. Os contadores podem ser usados para controlar e monitorar o comportamento específico do aplicativo. Você pode criar e excluir categorias de contador de desempenho personalizados e especificadores de uma tarefa de inicialização, função Web ou função de trabalho com permissões elevadas.

> [!NOTE]
> O código que faz alterações aos contadores de desempenho personalizados deve ter permissões elevadas para ser executado. Se o código estiver em uma função Web ou função de trabalho, a função deverá incluir a marca <Runtime executionContext="elevated" /> no arquivo ServiceDefinition.csdef para a função ser inicializada corretamente.
> 
> 

Você pode enviar dados personalizados de contador de desempenho para o armazenamento do Azure usando o agente de diagnóstico.

Os dados padrão do contador de desempenho são gerados pelos processos do Azure. Os dados personalizados de contador de desempenho devem ser criados pela sua função de trabalho ou aplicativo Web de função de trabalho. Consulte [Tipos de contador de desempenho](https://msdn.microsoft.com/library/z573042h.aspx) para obter informações sobre os tipos de dados que podem ser armazenados em contadores de desempenho personalizados. Consulte [Exemplo de PerformanceCounters](http://code.msdn.microsoft.com/azure/) para ver um exemplo que cria e define os dados do contador de desempenho personalizados em uma função Web.

## Armazenar e exibir dados do contador de desempenho
O Azure armazena em cache os dados do contador de desempenho com outras informações de diagnóstico. Esses dados estão disponíveis para o monitoramento remoto enquanto a instância de função está em execução usando o acesso de área de trabalho remoto para exibir ferramentas como o Monitor de Desempenho. Para manter os dados fora da instância de função, o agente de diagnóstico deve transferir os dados para o armazenamento do Azure. O limite de tamanho dos dados de contador de desempenho em cache pode ser configurado no agente de diagnóstico, ou pode ser configurado para ser parte de um limite compartilhado para todos os dados de diagnóstico. Para saber mais sobre como definir o tamanho do buffer, consulte [OverallQuotaInMB](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitorconfiguration.overallquotainmb.aspx) e [DirectoriesBufferConfiguration](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.directoriesbufferconfiguration.aspx). Consulte [Armazenar e exibir dados de diagnóstico no armazenamento do Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx) para obter uma visão geral de como configurar o agente de diagnóstico para transferir dados para uma conta de armazenamento.

Cada instância do contador de desempenho configurado é registrada em uma taxa de amostragem especificada e os dados de amostrados são transferidos para a conta de armazenamento por uma solicitação de transferência agendada ou uma solicitação de transferência sob demanda. As transferências automáticas podem ser agendadas com uma frequência de até uma vez por minuto. Os dados do contador de desempenho transferidos pelo agente de diagnóstico são armazenados em uma tabela, WADPerformanceCountersTable, na conta de armazenamento. Essa tabela pode ser acessada e consultada com métodos padrão da API do armazenamento do Azure. Consulte [Exemplo de PerformanceCounters do Microsoft Azure](http://code.msdn.microsoft.com/Windows-Azure-PerformanceCo-7d80ebf9) para obter um exemplo de como consultar e exibir dados de contador de desempenho da tabela WADPerformanceCountersTable.

> [!NOTE]
> Dependendo da frequência de transferência do agente de diagnóstico e da latência da fila, os dados mais recentes do contador de desempenho na conta de armazenamento podem estar desatualizados em vários minutos.
> 
> 

## Habilitar os contadores de desempenho usando o arquivo de configuração de diagnóstico
Use o procedimento a seguir para habilitar os contadores de desempenho em seu aplicativo do Azure.

## Pré-requisitos
Este artigo pressupõe que você tenha importado o monitor de diagnóstico para seu aplicativo e adicionado o arquivo de configuração à sua solução do Visual Studio (diagnostics.wadcfg no SDK 2.4 e inferior ou diagnostics.wadcfgx no SDK 2.5 e superior). Para saber mais, consulte as etapas 1 e 2 em [Habilitando o diagnóstico nos serviços de nuvem e máquinas virtuais do Azure](cloud-services-dotnet-diagnostics.md).

## Etapa 1: Coletar e armazenar dados de contadores de desempenho
Depois de ter adicionado o arquivo de diagnóstico à solução do Visual Studio, você pode configurar a coleta e o armazenamento de dados do contador de desempenho em um aplicativo do Azure. Isso é feito adicionando contadores de desempenho ao arquivo de diagnóstico. Os dados de diagnóstico, incluindo os contadores de desempenho são primeiro coletados na instância. Os dados são persistidos na tabela WADPerformanceCountersTable no serviço Tabela do Azure, portanto, você também precisa especificar a conta de armazenamento em seu aplicativo. Se estiver testando o aplicativo localmente no Emulador de Computação, você também poderá armazenar dados de diagnóstico localmente no Emulador de Armazenamento. Antes de armazenar dados de diagnóstico, você deve primeiro ir para o [portal clássico do Azure](http://manage.windowsazure.com/) e criar uma conta de armazenamento. Uma prática recomendada é localizar sua conta de armazenamento na mesma localização geográfica de seu aplicativo do Azure para evitar pagar os custos de largura de banda externa e para reduzir a latência.

### Adicionar contadores de desempenho ao arquivo de diagnóstico
Há muitos contadores que você pode usar. O exemplo a seguir mostra vários contadores de desempenho que são recomendados para monitoramento de funções Web e de trabalho.

Abra o arquivo de diagnóstico (diagnostics.wadcfg no SDK 2.4 e inferior ou diagnostics.wadcfgx no SDK 2.5 e posterior) e adicione o seguinte ao elemento DiagnosticMonitorConfiguration:

```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
       <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT30S" />

    <!-- Use the Process(w3wp) category counters in a web role -->

       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Thread Count" sampleRate="PT30S" />

    <!-- Use the Process(WaWorkerHost) category counters in a worker role.
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Thread Count" sampleRate="PT30S" />
    -->

       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)# of marshalling" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)# Bytes in all Heaps" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
    </PerformanceCounters>
```

O atributo bufferQuotaInMB, que especifica a quantidade máxima de armazenamento do sistema de arquivos que está disponível para o tipo de coleta de dados (logs do Azure, logs do IIS etc.). O padrão é 0. Quando a cota é atingida, os dados mais antigos são excluídos enquanto novos dados são adicionados. A soma de todas as propriedades de bufferQuotaInMB deve ser maior que o valor do atributo OverallQuotaInMB. Para obter uma discussão mais detalhada sobre como determinar quanto armazenamento será necessário para a coleta de dados de diagnóstico, consulte a seção Configurar WAD de [Solução de problemas de práticas recomendadas para o desenvolvimento de aplicativos do Azure](https://msdn.microsoft.com/library/windowsazure/hh771389.aspx).

O atributo scheduledTransferPeriod que especifica o intervalo entre transferências agendadas de dados, arredondado para o minuto mais próximo. Nos exemplos a seguir ele é definido como PT30M (30 minutos). A definição do período de transferência para um valor pequeno, como 1 minuto, afetará negativamente o desempenho de seu aplicativo na produção, mas pode ser útil para ver o diagnóstico funcionar rapidamente quando você estiver testando. O período de transferência agendada deve ser pequeno o suficiente para garantir que os dados de diagnóstico não sejam substituídos na instância, mas grande o suficiente para que não afete o desempenho de seu aplicativo.

O atributo counterSpecifier especifica o contador de desempenho a ser coletado. O atributo sampleRate especifica a taxa em que o contador de desempenho deve ser amostrado, neste caso, 30 segundos.

Depois de adicionar os contadores de desempenho que deseja coletar, salve as alterações no arquivo de diagnóstico. Em seguida, você precisa especificar a conta de armazenamento na qual os dados de diagnóstico serão persistidos.

### Especificar a conta de armazenamento
Para persistir as informações de diagnóstico em sua conta de Armazenamento do Azure, você deve especificar uma cadeia de conexão no arquivo de configuração do serviço (ServiceConfiguration.csfg).

No SDK do Azure 2.5, a Conta de Armazenamento pode ser especificada no arquivo diagnostics.wadcfgx.

> [!NOTE]
> Essas instruções se aplicam somente ao SDK 2.4 do Azure e inferior. No SDK do Azure 2.5, a Conta de Armazenamento pode ser especificada no arquivo diagnostics.wadcfgx.
> 
> 

Para definir as cadeias de conexão:

1. Abra o arquivo ServiceConfiguration.Cloud.cscfg usando o seu editor de texto preferido e defina a cadeia de conexão da sua conta de armazenamento. Os valores de *AccountName* e *AccountKey* são encontrados no portal clássico do Azure, no painel de Conta de Armazenamento, em Gerenciar Chaves.
   
    ```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
    </ConfigurationSettings>
    ```
2. Salve o arquivo ServiceConfiguration.Cloud.cscfg.
3. Abra o arquivo ServiceConfiguration.Local.cscfg e verifique se UseDevelopmentStorage está definido como verdadeiro.
   
    ```
    <ConfigurationSettings>
      <Settingname="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true"/>
    </ConfigurationSettings>
    ```
   Agora que as cadeias de conexão estão definidas, seu aplicativo irá persistir dados de diagnóstico em sua conta de armazenamento quando o aplicativo for implantado.
4. Salve e compile seu projeto e implante seu aplicativo.

## Etapa 2: (Opcional) Criar contadores de desempenho personalizados
Além dos contadores de desempenho predefinidos, você pode adicionar seus próprios contadores de desempenho personalizados para monitorar as funções Web ou de trabalho. Os contadores de desempenho personalizados podem ser usados para acompanhar e monitorar o comportamento específico do aplicativo e podem ser criados ou excluídos em tarefas de inicialização, função Web ou função de trabalho com permissões elevadas.

O agente de diagnóstico do Azure atualiza a configuração do contador de desempenho do arquivo .wadcfg um minuto após o início. Se você criar contadores de desempenho personalizados no método OnStart e suas tarefas de inicialização demorarem mais de um minuto para serem executadas, os seus contadores de desempenho personalizados não terão sido criados quando o agente de diagnóstico do Azure tentar carregá-los. Nesse cenário, você verá que o diagnóstico do Azure captura corretamente todos os dados de diagnóstico exceto os seus contadores de desempenho personalizados. Para resolver esse problema, crie os contadores de desempenho em uma tarefa de inicialização ou mova alguns trabalhos da sua tarefa de inicialização para o método OnStart depois de criar os contadores de desempenho.

Execute as seguintes etapas para criar um contador de desempenho personalizado simples chamado "\\MyCustomCounterCategory\\MyButton1Counter":

1. Abra o arquivo de definição do serviço (CSDEF) de seu aplicativo.
2. Adicione o elemento Runtime ao elemento WebRole ou WorkerRole para permitir a execução com privilégios elevados:
   
    ```
    <runtime executioncontext="elevated"/>
    ```
3. Salve o arquivo.
4. Abra o arquivo de diagnóstico (diagnostics.wadcfg no SDK 2.4 e inferior ou diagnostics.wadcfgx no SDK 2.5 e posterior) e adicione o seguinte ao elemento DiagnosticMonitorConfiguration 
   
    ```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
     <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
    </PerformanceCounters>
    ```
5. Salve o arquivo.
6. Crie a categoria do contador de desempenho personalizado no método OnStart de sua função, antes de invocar base.OnStart. O exemplo de C# a seguir criará uma nova categoria personalizada, caso ela ainda não exista:
   
    ```
    public override bool OnStart()
    {
    if (!PerformanceCounterCategory.Exists("MyCustomCounterCategory"))
    {
       CounterCreationDataCollection counterCollection = new CounterCreationDataCollection();
   
       // add a counter tracking user button1 clicks
       CounterCreationData operationTotal1 = new CounterCreationData();
       operationTotal1.CounterName = "MyButton1Counter";
       operationTotal1.CounterHelp = "My Custom Counter for Button1";
       operationTotal1.CounterType = PerformanceCounterType.NumberOfItems32;
       counterCollection.Add(operationTotal1);
   
       PerformanceCounterCategory.Create(
         "MyCustomCounterCategory",
         "My Custom Counter Category",
         PerformanceCounterCategoryType.SingleInstance, counterCollection);
   
       Trace.WriteLine("Custom counter category created.");
    }
    else{
       Trace.WriteLine("Custom counter category already exists.");
    }
   
    return base.OnStart();
    }
    ```
7. Atualize os contadores dentro de seu aplicativo. O exemplo a seguir atualiza um contador de desempenho personalizado em eventos Button1\_Click:
   
    ```
    protected void Button1_Click(object sender, EventArgs e)
        {
         PerformanceCounter button1Counter = new PerformanceCounter(
           "MyCustomCounterCategory",
           "MyButton1Counter",
           string.Empty,
           false);
         button1Counter.Increment();
        this.Button1.Text = "Button 1 count: " +
           button1Counter.RawValue.ToString();
        }
    ```
8. Salve o arquivo.  

Os dados personalizados do contador de desempenho agora serão coletados pelo monitor de diagnóstico.

## Etapa 3: Consultar dados do contador de desempenho
Depois que o aplicativo for implantado e estiver em execução, o monitor de diagnóstico começará a coletar contadores de desempenho e a persistir esses dados no Armazenamento do Azure. Você usa ferramentas como o Gerenciador de Servidores no Visual Studio, [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/), ou o [Azure Diagnostics Manager](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx) da Cerebrata, para exibir os dados dos contadores de desempenho na tabela WADPerformanceCountersTable. Você também pode consultar o serviço Tabela de forma programática usando [C#](../storage/storage-dotnet-how-to-use-tables.d), [Java](../storage/storage-java-how-to-use-table-storage.md), [Node.js](../storage/storage-nodejs-how-to-use-table-storage.md), [Python](../storage/storage-python-how-to-use-table-storage.md), [Ruby](../storage/storage-ruby-how-to-use-table-storage.md) ou [PHP](../storage/storage-php-how-to-use-table-storage.md).

O seguinte exemplo de C# mostra uma consulta simples na tabela WADPerformanceCountersTable e salva os dados de diagnóstico em um arquivo CSV. Depois que os contadores de desempenho forem salvos em um arquivo CSV, você poderá usar os recursos gráficos do Microsoft Excel ou alguma outra ferramenta para visualizar os dados. Certifique-se de adicionar uma referência ao Microsoft.WindowsAzure.Storage.dll, que está incluído no SDK do Azure para .NET de outubro 2012 e posterior. O assembly é instalado no diretório %Program Files%\\Microsoft SDKs\\Microsoft Azure.NET SDK\\version-num\\ref\\ directory.

```
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ...

    // Get the connection string. When using Microsoft Azure Cloud Services, it is recommended
    // you store your connection string using the Microsoft Azure service configuration
    // system (*.csdef and *.cscfg files). You can you use the CloudConfigurationManager type
    // to retrieve your storage connection string.  If you're not using Cloud Services, it's
    // recommended that you store the connection string in your web.config or app.config file.
    // Use the ConfigurationManager type to retrieve your storage connection string.

    string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    //string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;

    // Get a reference to the storage account using the connection string.  You can also use the development
    // storage account (Storage Emulator) for local debugging.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    //CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "WADPerformanceCountersTable" table.
    CloudTable table = tableClient.GetTableReference("WADPerformanceCountersTable");

    // Create the table query, filter on a specific CounterName, DeploymentId and RoleInstance.
    TableQuery<PerformanceCountersEntity> query = new TableQuery<PerformanceCountersEntity>()
       .Where(
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("CounterName", QueryComparisons.Equal, @"\Processor(_Total)\% Processor Time"),
          TableOperators.And,
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("DeploymentId", QueryComparisons.Equal, "ec26b7a1720447e1bcdeefc41c4892a3"),
          TableOperators.And,
          TableQuery.GenerateFilterCondition("RoleInstance", QueryComparisons.Equal, "WebRole1_IN_0")
          )
       )
    );

    // Execute the table query.
    IEnumerable<PerformanceCountersEntity> result = table.ExecuteQuery(query);

    // Process the query results and build a CSV file.
    StringBuilder sb = new StringBuilder("TimeStamp,EventTickCount,DeploymentId,Role,RoleInstance,CounterName,CounterValue\n");

    foreach (PerformanceCountersEntity entity in result)
    {
       sb.Append(entity.Timestamp + "," + entity.EventTickCount + "," + entity.DeploymentId + ","
          + entity.Role + "," + entity.RoleInstance + "," + entity.CounterName + "," + entity.CounterValue+"\n");
    }

    StreamWriter sw = File.CreateText(@"C:\temp\PerfCounters.csv");
    sw.Write(sb.ToString());
    sw.Close();
```

As entidades mapeiam para objetos C# usando uma classe personalizada derivada de **TableEntity**. O código a seguir define uma classe de entidade que representa um contador de desempenho na tabela **WADPerformanceCountersTable**.

    public class PerformanceCountersEntity : TableEntity
    {
       public long EventTickCount { get; set; }
       public string DeploymentId { get; set; }
       public string Role { get; set; }
       public string RoleInstance { get; set; }
       public string CounterName { get; set; }
       public double CounterValue { get; set; }
    }


## Próximas etapas
[Exibir artigos adicionais sobre o Diagnóstico do Azure](../azure-diagnostics.md)

<!---HONumber=AcomDC_0302_2016-->