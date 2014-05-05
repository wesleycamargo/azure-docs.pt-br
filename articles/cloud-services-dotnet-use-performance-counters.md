<properties linkid="dev-net-commons-tasks-profiling" urlDisplayName="Criação de perfil de desempenho" pageTitle="Usar contadores de desempenho no Azure (.NET)" metaKeywords="contadores de desempenho do Azure, criação de perfil de desempenho do Azure, contadores de desempenho do Azure C#, criação de perfil de desempenho do Azure C#" description="Saiba como habilitar e coletar dados de contadores de desempenho em aplicativos do Azure. " metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Usando contadores de desempenho no Azure" authors="ryanwi" solutions="" manager="" editor="" />





# Usando contadores de desempenho no Azure

Você pode usar os contadores de desempenho em um aplicativo do Azure para coletar dados que podem ajudar a determinar os afunilamentos do sistema e ajustar o desempenho do sistema e do aplicativo. Os contadores de desempenho disponíveis para o Windows Server 2008, o Windows Server 2012, o IIS e o ASP.NET podem ser coletados e usados para determinar a integridade de seu aplicativo do Azure. 

Este tópico explica como ativar os contadores de desempenho em seu aplicativo usando o arquivo de configuração diagnostics.wadcfg. Para obter informações sobre como monitorar o desempenho do seu aplicativo no [Portal de Gerenciamento do Azure][], consulte [Como monitorar Serviços de Nuvem][]. Para obter orientações detalhadas adicionais sobre como criar um registro em log e uma estratégia de rastreamento e usar diagnóstico e outras técnicas para solucionar problemas e otimizar os aplicativos do Azure, consulte [Práticas recomendadas para a solução de problemas no desenvolvimento de aplicativos do Azure][].

Esta tarefa inclui as seguintes etapas:

-   [Pré-requisitos][]
-   [Etapa 1: Coletar e armazenar dados de contadores de desempenho][]
-   [Etapa 2: (Opcional) Criar contadores de desempenho personalizados][]
-   [Etapa 3: Consultar dados do contador de desempenho][]
-   [Próximas etapas][]
-   [Recursos adicionais][]

## <a name="prereqs"> </a>Pré-requisitos

Este artigo pressupõe que você tenha importado o monitor de diagnóstico para seu aplicativo e adicionado o arquivo de configuração diagnostics.wadcfg à sua solução do Visual Studio.  Consulte as etapas 1 e 2 [Habilitando o diagnóstico no Azure][] para obter mais informações.

## <a name="step1"> </a>Etapa 1: Coletar e armazenar dados de contadores de desempenho

Depois de ter adicionado o arquivo diagnostics.wadcfg à solução do Visual Studio, você pode configurar a coleta e o armazenamento de dados do contador de desempenho em um aplicativo do Azure.  Isso é feito adicionando contadores de desempenho ao arquivo diagnostics.wadcfg.  Os dados de diagnóstico, incluindo os contadores de desempenho são primeiro coletados na instância.  Os dados são persistidos na tabela **WADPerformanceCountersTable** no serviço Tabela do Azure, portanto, você também precisa especificar a conta de armazenamento em seu aplicativo. Se estiver testando o aplicativo localmente no Emulador de Computação, você também poderá armazenar dados de diagnóstico localmente no Emulador de Armazenamento. Antes de armazenar dados de diagnóstico, você deve primeiro ir para o [Portal de Gerenciamento do Azure][] e criar uma conta de armazenamento. Uma prática recomendada é localizar sua conta de armazenamento na mesma localização geográfica de seu aplicativo do Azure para evitar pagar os custos de largura de banda externa e para reduzir a latência.

### Adicionar contadores de desempenho ao arquivo diagnostics.wadcfg

Existem muitos  contadores de desempenho que você pode coletar, o exemplo a seguir mostra vários contadores de desempenho que são recomendados para monitoramento da função Web e de trabalho. 

Abra o arquivo diagnostics.wadcfg e adicione o seguinte ao elemento **DiagnosticMonitorConfiguration**:

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
		<PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)\# of marshalling" sampleRate="PT30S" />
		<PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
		<PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
		<PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)\# Bytes in all Heaps" sampleRate="PT30S" />
		<PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
		<PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
		<PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
		</PerformanceCounters>    

O atributo **bufferQuotaInMB**, que especifica a quantidade máxima de armazenamento do sistema de arquivos que está disponível para o tipo de coleta de dados (logs do Azure, logs do IIS etc.). O padrão é 0. Quando a cota é atingida, os dados mais antigos são excluídos enquanto novos dados são adicionados. A soma de todas as propriedades de **bufferQuotaInMB** deve ser maior que o valor do atributo **OverallQuotaInMB**.  Para obter uma discussão mais detalhada sobre como determinar quanto armazenamento será necessário para a coleta de dados de diagnóstico, consulte a seção Configurar WAD de [Práticas recomendadas para a solução de problemas no desenvolvimento de aplicativos do Azure][].

O atributo **scheduledTransferPeriod** que especifica o intervalo entre transferências agendadas de dados, arredondado para o minuto mais próximo. Nos exemplos a seguir ele é definido como PT30M (30 minutos). A definição do período de transferência para um valor pequeno, como 1 minuto, afetará negativamente o desempenho de seu aplicativo na produção, mas pode ser útil para ver o diagnóstico funcionar rapidamente quando você estiver testando.  O período de transferência agendada deve ser pequeno o suficiente para garantir que os dados de diagnóstico não sejam substituídos na instância, mas grande o suficiente para que não afete o desempenho de seu aplicativo.

O atributo **counterSpecifier** especifica o contador de desempenho a ser coletado.

O atributo **sampleRate** especifica a taxa em que o contador de desempenho deve ser amostrado, neste caso, 30 segundos.

Depois de adicionar os contadores de desempenho que você deseja coletar, salve as alterações no arquivo diagnostics.wadcfg.  Em seguida, você precisa especificar a conta de armazenamento na qual os dados de diagnóstico serão persistidos.

### Especificar a conta de armazenamento

Para persistir as informações de diagnóstico em sua conta de Armazenamento do Azure, você deve especificar uma cadeia de conexão no arquivo de configuração do serviço (ServiceConfiguration.csfg).  As Ferramentas do Azure para Visual Studio versão 1.4 (agosto de 2011) e posterior permitem que você tenha arquivos de configuração diferentes (ServiceConfiguration.cscfg) para o Local e a Nuvem. Várias configurações de serviço são úteis para fins de diagnóstico, porque você pode usar o Emulador de Armazenamento para testes locais gratuitos enquanto mantém um arquivo de configuração separado para produção.

Para definir as cadeias de conexão:

1.  Abra o arquivo ServiceConfiguration.Cloud.cscfg usando um editor de texto preferido e defina a cadeia de conexão para sua conta de armazenamento:

        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
        </ConfigurationSettings>

    Os valores de **AccountName** e **AccountKey** são encontrados no Portal de Gerenciamento, no painel de Conta de Armazenamento, sob **Gerenciar Chaves**.

2.  Salve o arquivo ServiceConfiguration.Cloud.cscfg.
3.  Abra o arquivo ServiceConfiguration.Local.cscfg e verifique se **UseDevelopmentStorage** está definida como true (o padrão).

		<ConfigurationSettings>
      	   <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
		</ConfigurationSettings>

	Agora que as cadeias de conexão estão definidas, seu aplicativo irá persistir dados de diagnóstico em sua conta de armazenamento quando o aplicativo for implantado.  
4. Salve e compile seu projeto e implante seu aplicativo.

## <a name="step2"> </a>Etapa 2: (Opcional) Criar contadores de desempenho personalizados

Além dos contadores de desempenho predefinidos, você pode adicionar seus próprios contadores de desempenho personalizados para monitorar as funções Web ou de trabalho.  Os contadores de desempenho personalizados podem ser usados para acompanhar e monitorar o comportamento específico do aplicativo e podem ser criados ou excluídos em tarefas de inicialização, função Web ou função de trabalho com permissões elevadas.

Execute as etapas a seguir para criar um contador de desempenho personalizado simples chamado "\MyCustomCounterCategory\MyButton1Counter":

1.  Abra o arquivo de definição do serviço (CSDEF) de seu aplicativo.
2.  Adicione o elemento **Runtime** ao elemento **WebRole** ou **WorkerRole** para permitir a execução com privilégios elevados:

        <Runtime executionContext="elevated" />

3.  Salve o arquivo.
4.  Abra o arquivo diagnostics.wadcfg e adicione o seguinte ao elemento **DiagnosticMonitorConfiguration**:

		<PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
		<PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
		</PerformanceCounters>		

5. 	Salve o arquivo.
6.  Crie a categoria do contador de desempenho personalizado no método **OnStart** de sua função, antes de invocar **base.OnStart**. O exemplo de C# a seguir criará uma nova categoria personalizada, caso ela ainda não exista:

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

7.  Atualize os contadores dentro de seu aplicativo. O exemplo a seguir atualiza um contador de desempenho personalizado em eventos **Button1_Click**:

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

8.  Salve o arquivo.

Quando essas etapas forem concluídas, os dados do contador de desempenho personalizado serão coletados pelo monitor de diagnóstico do Azure.

## <a name="step3"> </a>Etapa 3: Consultar dados do contador de desempenho

Depois que o aplicativo for implantado e estiver em execução, o monitor de diagnóstico começará a coletar contadores de desempenho e a persistir esses dados no Armazenamento do Azure. Você usa ferramentas, como o **Gerenciador de Servidores no Visual Studio**, [Gerenciador de Armazenamento do Azure][], ou [Gerenciador de Diagnóstico do Azure][] da Cerebrata, para exibir os dados dos contadores de desempenho na tabela **WADPerformanceCountersTable**.  Você também pode consultar o serviço Tabela de forma programática usando [C#][], [Java][], [Node.js][], [Python][] ou [PHP][].  

O seguinte exemplo do C# mostra uma consulta simples na tabela **WADPerformanceCountersTable** e salva os dados de diagnóstico em um arquivo CSV. Depois que os contadores de desempenho forem salvos em um arquivo CSV, você poderá usar os recursos gráficos do Microsoft Excel ou alguma outra ferramenta para visualizar os dados.  Certifique-se de adicionar uma referência ao Microsoft.WindowsAzure.Storage.dll, que está incluído no SDK do Azure para .NET de outubro 2012 e posterior. O assembly é instalado no diretório %Arquivos de Programas%\Microsoft SDKs\Windows Azure\ .NET SDK\número_da_versão\ref\.

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Table;

		...

		// Get the connection string. When using Azure Cloud Services, it is recommended 
		// you store your connection string using the Azure service configuration
		// system (*.csdef and *.cscfg files). You can you use the CloudConfigurationManager type 
		// to retrieve your storage connection string.  If you're not using Cloud Services, it's
		// recommended that you store the connection string in your web.config or app.config file.
		// Use the ConfigurationManager type to retrieve your storage connection string.
		
		string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
		//string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;
            
		// Get a reference to the storage account using the connection string.  You can also use the development storage account (Storage Emulator)
		// for local debugging.              
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

## <a name="nextsteps"> </a>Próximas etapas

Agora que você aprendeu os conceitos básicos de como coletar contadores de desempenho, siga estes links para saber como implementar cenários de solução de problemas mais complexos.

- [Práticas recomendadas para a solução de problemas no desenvolvimento de aplicativos do Azure][]
- [Como monitorar serviços de nuvem][]
- [Como usar o Bloco de Aplicativos de Dimensionamento Automático][]
- [Criando aplicativos de nuvem elásticos e resilientes]

## <a name="additional"> </a>Recursos adicionais

- [Habilitando o diagnóstico no Azure][]
- [Coletando dados do log usando o Diagnóstico do Azure][]
- [Depurando um aplicativo do Azure][]


  [Visão geral de como criar e usar contadores de desempenho em um aplicativo do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh411520.aspx
  [Pré-requisitos]: #prereqs
  [Etapa 1: Coletar e armazenar dados de contadores de desempenho]: #step1
  [Etapa 2: (Opcional) Criar contadores de desempenho personalizados]: #step2
  [Etapa 3: Consultar dados do contador de desempenho]: #step3
  [Próximas etapas]: #nextsteps
  [Recursos adicionais]: #additional
  
  [Coletando dados do log usando o Diagnóstico do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433048.aspx
  [Depurando um aplicativo do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee405479.aspx
  [Como usar o Bloco de Aplicativos de Dimensionamento Automático]: http://www.windowsazure.com/pt-br/develop/net/how-to-guides/autoscaling/
  [Práticas recomendadas para a solução de problemas no desenvolvimento de aplicativos do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh771389.aspx
  [Habilitando o diagnóstico no Azure]: https://www.windowsazure.com/pt-br/develop/net/common-tasks/diagnostics/
  [Como usar o Serviço de Armazenamento de Tabela]: http://www.windowsazure.com/pt-br/develop/net/how-to-guides/table-services/
  [Gerenciador de Armazenamento do Azure]: http://azurestorageexplorer.codeplex.com/
  
  [Java]: http://www.windowsazure.com/pt-br/develop/java/how-to-guides/table-service/
  [Python]: http://www.windowsazure.com/pt-br/develop/python/how-to-guides/table-service/
  [PHP]: http://www.windowsazure.com/pt-br/develop/php/how-to-guides/table-service/
  
  [Criando aplicativos de nuvem elásticos e resilientes]: http://msdn.microsoft.com/pt-br/library/hh680949(PandP.50).aspx
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Gerenciador de diagnóstico do Azure]: http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx
  [Como monitorar serviços de nuvem]: https://www.windowsazure.com/pt-br/manage/services/cloud-services/how-to-monitor-a-cloud-service/

