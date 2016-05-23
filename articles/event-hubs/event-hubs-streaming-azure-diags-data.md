<properties 
   pageTitle="Streaming de dados de diagnóstico do Azure no afunilamento usando os Hubs de Eventos"
   description="Ilustra como configurar de ponta a ponta o Diagnóstico do Azure com Hubs de Eventos, incluindo diretrizes para cenários comuns."
   services="event-hubs"
   documentationCenter="na"
   authors="tomarcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/08/2016"
   ms.author="tarcher" />

# Streaming de dados de diagnóstico do Azure no afunilamento usando os Hubs de Eventos

## Visão geral
O diagnóstico do Azure fornece maneiras flexíveis de coletar métricas e logs de VMs de computação e transferir para o Armazenamento do Azure. A partir de março de 2016 (SDK 2.9), há a capacidade de coletar diagnósticos do Azure em fontes de dados totalmente personalizadas e transferir dados pelo afunilamento em questão de segundos usando os Hubs de Eventos do Azure.

Entre os tipos de dados com suporte estão:

- Eventos de ETW
- Contadores de desempenho
- Logs de Eventos do Windows 
- Logs de aplicativo
- Logs de infraestrutura do Diagnóstico do Azure
 
Este artigo mostrará a configuração completa do diagnóstico do Azure com os Hubs de Eventos. Também há orientações para cenários comuns, por exemplo, personalizar quais logs e métricas são inseridas nos Hubs de eventos, como alterar a configuração em cada ambiente, um exemplo, dentre muitos, de como você pode exibir os dados transmitidos dos Hubs de eventos e também como solucionar problemas de conexão.

## Pré-requisitos
Há suporte para a coleta de diagnósticos do Azure nos Hubs de Eventos em todos os tipos de computação - Serviço de Nuvem, VM, VMSS e Servic Fabric - que oferecem suporte a WAD, começando no Azure SDK 2.9 e nas Ferramentas do Azure correspondentes para Visual Studio.
  
- O Diagnóstico do Azure extensão 1.6 ([SDK do Azure para .NET 2.9 ou superior](https://azure.microsoft.com/downloads/) resolve isso por padrão)
- [Visual Studio 2013 ou superior](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- Uma configuração bem-sucedida anterior à configuração do Diagnóstico do Azure no aplicativo usando um arquivo *.wadcfgx*, usando um dos métodos a seguir:
	- Visual Studio: [Configurando o diagnóstico para os serviços de nuvem do Azure e máquinas virtuais](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
	- Windows PowerShell: [Habilitar o diagnóstico nos Serviços de Nuvem do Azure usando o PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md)
- Namespace de Hubs de Eventos provisionado de acordo com o artigo [Get started with Event Hubs (Introdução aos Hubs de Eventos)](./event-hubs-csharp-ephcs-getstarted.md)

## Conexão do Diagnóstico do Azure ao coletor do Hubs de Eventos
O Diagnóstico do Azure sempre envia logs e métricas, por padrão, a uma conta de Armazenamento do Azure. Um aplicativo pode coletar adicionalmente nos Hubs de Eventos, adicionando uma nova seção **Coletores** ao elemento **WadCfg**, na seção **PublicConfig** do arquivo *.wadcfgx*. No Visual Studio, o arquivo *.wadcfgx* é armazenado em *Projeto do Serviço de Nuvem > Funções > (NomeDaFunção) > arquivo diagnostics.wadcfgx*.
  
	  <SinksConfig>
	    <Sink name="HotPath">
	      <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
	    </Sink>
	  </SinksConfig>

Neste exemplo, a URL do hub de eventos é definida como o namespace totalmente qualificado do hub de eventos (namespace ServiceBus + "/" + nome do hub de evento).

A URL do hub de eventos é exibida no [Portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885) no painel dos Hubs de Eventos.

O nome do **Coletor** pode ser definido como qualquer cadeia de caracteres válida, desde que o mesmo valor seja usado consistentemente no arquivo de configuração.

**Observação:** pode haver outros coletores configurados nesta seção, como "applicationInsights". O diagnóstico do Azure permite a definição de um ou mais coletores, desde que cada coletor também seja declarado na seção **PrivateConfig**.

O coletor do Hubs de Eventos também deve ser declarado e definido na seção **PrivateConfig** do arquivo de configuração *.wadcfgx*.

	  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <StorageAccount name="" key="" endpoint="" />
	    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="9B3SwghJOGEUvXigc6zHPInl2iYxrgsKHZoy4nm9CUI=" />
	  </PrivateConfig>

O **SharedAccessKeyName** deve corresponder a uma chave SAS e uma política definidas no namespace **ServiceBus/EventHub**. Isso pode ser feito navegando até o painel do hub de eventos no [Portal clássico do Azure](https://manage.windowsazure.com), clicando na guia **Configurar** e configurando uma política nomeada (por exemplo, "SendRule"), que tem permissões de *Envio*. **StorageAccount** também é declarada em **PrivateConfig**. Não é necessário alterar os valores aqui, especialmente se eles estiverem funcionando. Neste exemplo, deixamos os valores vazios, que é um sinal de que um ativo de downstream definirá os valores; por exemplo, o arquivo de configuração do ambiente *ServiceConfiguration.Cloud.cscfg* definirá os nomes e chaves apropriados do ambiente.

>[AZURE.WARNING] Lembre-se de que a chave SAS do hub de eventos é armazenada em texto sem formatação no arquivo *.wadcfgx*. Muitas vezes, isso é verificado no controle do código fonte ou como um ativo no servidor de compilação e, portanto, você deve proteger como apropriado. Recomendamos o uso de uma chave SAS com permissões *Somente de envio*, para que qualquer usuário mal-intencionado possa, no máximo, gravar no hub de eventos, mas nunca ouvir ou gerenciá-lo.

## Configuração de logs e de métricas do Diagnóstico do Azure para coletor com os Hubs de Eventos
Como discutido anteriormente, todos os dados de diagnóstico padrão e personalizados (por exemplo, métricas e logs) são automaticamente coletados no Armazenamento do Azure em intervalos configurados. Com os Hubs de Eventos (e qualquer coletor adicional), você tem a opção de especificar a coleta de qualquer nó raiz ou folha na hierarquia com o hub de eventos. Isso inclui eventos de ETW, contadores de desempenho, logs de eventos do Windows e logs de aplicativo.

É importante considerar quantos pontos de dados devem ser transferidos para os Hubs de Eventos. Normalmente, os desenvolvedores usam isso para dados de baixa latência de afunilamento que devem ser consumidos e interpretados rapidamente (por exemplo, sistemas de monitoramento e alerta ou regras de dimensionamento automático). Isso também pode ser usado para configurar uma repositório alternativo de pesquisa ou análise , por exemplo, Stream Analytics, ElasticSearch, um sistema de monitoramento personalizado ou seu sistema de monitoramento de terceiros favorito.

Veja a seguir alguns exemplos de configurações.

        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>

No exemplo a seguir, o coletor é aplicado ao nó **PerformanceCounters** pai na hierarquia, o que significa que todos os **PerformanceCounters** filhos serão enviados aos Hubs de Eventos.

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
        </PerformanceCounters>

No exemplo acima, o coletor é aplicado a apenas três contadores: Solicitações Enfileiradas, Solicitações Rejeitadas e % do Tempo do processador.

O exemplo a seguir mostra como um desenvolvedor pode assumir o controle e limitar a quantidade de dados enviados, a fim de definir as métricas essenciais usadas para a integridade deste serviço.

        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />

Neste exemplo, o coletor é aplicado aos logs e filtra apenas para o rastreamento de nível Erro.
 
## Implantação e atualização de uma configuração de diagnóstico e de aplicativo do Serviço de Nuvem

O caminho mais fácil para implantar o aplicativo juntamente com a configuração de coletor dos Hubs de Eventos é usando o Visual Studio. Para exibir e fazer as edições desejadas, abra o arquivo *.wadcfgx* no Visual Studio, que é armazenado em *Projeto do Serviço de Nuvem -> Funções -> (NomeDaFunção) -> arquivo diagnostics.wadcfgx* e salve ao concluir.

Neste ponto, todas as ações de implantação e atualização da implantação no Visual Studio, Visual Studio Team System ou comandos ou scripts com base em MSBUILD (usando /t:publish target) incluirão *.wadcfgx* no processo de empacotamento. Implante-o no Azure usando a extensão do agente WAD apropriada com suas VMs.
  
Após a implantação bem-sucedida do aplicativo e da configuração do Diagnóstico do Azure, você verá imediatamente a atividade no painel do hub de eventos. Essa é uma indicação de que você está pronto para começar a ver os dados do afunilamento no cliente ouvinte ou na ferramenta de análise de sua escolha.
 
Na figura a seguir, o painel do hub de eventos mostra o envio íntegro de dados de diagnóstico para o hub de eventos, começando um pouco após às 23h, quando o aplicativo foi implantado com *.wadcfgx* atualizado e o coletor configurado corretamente.

![][0]
  
>[AZURE.NOTE] Quando você faz atualizações no arquivo de configuração do Diagnóstico do Azure (.wadcfgx), recomendamos o envio das atualizações por push para todo o aplicativo e também a configuração usando a publicação do Visual Studio ou o script do Windows PowerShell.

## Exibição de dados do afunilamento

Como discutido anteriormente, há muitos casos de uso sobre ouvir e processar dados dos Hubs de Eventos.
  
Uma abordagem simples é criar um pequeno aplicativo de console de teste para ouvir o hub de eventos e imprimir a transmissão de saída. O código a seguir (que é explicado mais detalhadamente no artigo, [Introdução aos Hubs de Eventos](./event-hubs-csharp-ephcs-getstarted.md)) pode ser colocado em um aplicativo de console.

Observe que o aplicativo de console deve incluir o [pacote EventProcessor do Nuget](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).

Substitua os valores entre colchetes na função **Main** abaixo por valores de seus recursos.

	//Console application code for EventHub test client
	using System;
	using System.Collections.Generic;
	using System.Diagnostics;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using Microsoft.ServiceBus.Messaging;
	
	namespace EventHubListener
	{
	    class SimpleEventProcessor : IEventProcessor
	    {
	        Stopwatch checkpointStopWatch;
	
	        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
	        {
	            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
	            if (reason == CloseReason.Shutdown)
	            {
	                await context.CheckpointAsync();
	            }
	        }
	
	        Task IEventProcessor.OpenAsync(PartitionContext context)
	        {
	            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
	            this.checkpointStopWatch = new Stopwatch();
	            this.checkpointStopWatch.Start();
	            return Task.FromResult<object>(null);
	        }
	
	        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
	        {
	            foreach (EventData eventData in messages)
	            {
	                string data = Encoding.UTF8.GetString(eventData.GetBytes());
	
	                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
	                    context.Lease.PartitionId, data));
	
	                foreach (var x in eventData.Properties)
	                {
	                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
	                }
	            }
	
	            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
	            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
	            {
	                await context.CheckpointAsync();
	                this.checkpointStopWatch.Restart();
	            }
	        }
	    }
	
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            string eventHubConnectionString = "Endpoint= <Your Connection String>”
	            string eventHubName = "<EventHub Name>";
	            string storageAccountName = "<Storage Account Name>";
	            string storageAccountKey = "<Storage Account Key>”;
	            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
	
	            string eventProcessorHostName = Guid.NewGuid().ToString();
	            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
	            Console.WriteLine("Registering EventProcessor...");
	            var options = new EventProcessorOptions();
	            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
	            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
	
	            Console.WriteLine("Receiving. Press enter key to stop worker.");
	            Console.ReadLine();
	            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
	        }
	    }
	}

## Solucionar problemas do coletor dos Hubs de Eventos

- O hub de eventos não mostra a atividade de entrada ou saída de eventos conforme o esperado

	Verifique se o seu hub de eventos foi provisionado com êxito. Todas as informações de conexão na seção **PrivateConfig** de *.wadcfgx* devem corresponder aos valores de seu recurso, como visto no portal. Verifique se você tem uma política SAS definida ("SendRule", no exemplo) no portal e se a permissão de Envio foi concedida.

- Depois de executar uma atualização, o hub de eventos não mostra mais a atividade de entrada ou saída dos eventos

	Primeiro, verifique se as informações de configuração e do hub de evento mostram correspondência, de acordo com o exposto acima. Alguns problemas são causados pela redefinição de **PrivateConfig** em uma atualização de implantação. A solução recomendada é fazer todas as alterações em *.wadcfgx* no projeto e, depois, enviar uma atualização completa do aplicativo. Se isso não for possível, certifique-se de que a atualização do diagnóstico envie uma **PrivateConfig** completa, incluindo a chave SAS.

- Tentei o procedimento acima e o hub de eventos ainda não está funcionando

	Tente procurar na tabela do Armazenamento do Azure que contém logs e erros do próprio diagnóstico do Azure: **WADDiagnosticInfrastructureLogsTable**. Uma opção é usar uma ferramenta como o [Gerenciador de Armazenamento do Azure](http://www.storageexplorer.com) para se conectar a essa conta de armazenamento, exibir essa tabela e adicionar uma Consulta por TimeStamp nas últimas 24 horas. Você pode usar a ferramenta para exportar o CSV e abri-lo em um aplicativo como o Microsoft Excel, pois o Excel facilita a pesquisa por cadeias de caracteres de cartão de chamada como "EventHubs" para ver qual erro é relatado.

## Próximas etapas
• [Saiba mais sobre Hubs de Eventos](https://azure.microsoft.com/services/event-hubs/)

## Apêndice: Concluir o exemplo de arquivo de configuração do diagnóstico do Azure (.wadcfgx)
	<?xml version="1.0" encoding="utf-8"?>
	<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <WadCfg>
	      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
	        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
	        <Directories scheduledTransferPeriod="PT1M">
	          <IISLogs containerName="wad-iis-logfiles" />
	          <FailedRequestLogs containerName="wad-failedrequestlogs" />
	        </Directories>
	        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
	          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
	        </PerformanceCounters>
	        <WindowsEventLog scheduledTransferPeriod="PT1M">
	          <DataSource name="Application!*" />
	        </WindowsEventLog>
	        <CrashDumps>
	          <CrashDumpConfiguration processName="WaIISHost.exe" />
	          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
	          <CrashDumpConfiguration processName="w3wp.exe" />
	        </CrashDumps>
	        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
	      </DiagnosticMonitorConfiguration>
	      <SinksConfig>
	        <Sink name="HotPath">
	          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
	        </Sink>
	        <Sink name="applicationInsights">
	          <ApplicationInsights />
	          <Channels>
	            <Channel logLevel="Error" name="errors" />
	          </Channels>
	        </Sink>
	      </SinksConfig>
	    </WadCfg>
	    <StorageAccount />
	  </PublicConfig>
	  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <StorageAccount name="" key="" endpoint="" />
	    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
	  </PrivateConfig>
	  <IsEnabled>true</IsEnabled>
	</DiagnosticsConfiguration>

O *ServiceConfiguration.Cloud.cscfg* complementar para este exemplo se parece com o seguinte.

	<?xml version="1.0" encoding="utf-8"?>
	<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
	  <Role name="MyFixIt.WorkerRole">
	    <Instances count="1" />
	    <ConfigurationSettings>
	      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
	    </ConfigurationSettings>
	  </Role>
	</ServiceConfiguration>

<!-- Images. -->
[0]: ./media/event-hubs-streaming-azure-diags-data/dashboard.png

<!---HONumber=AcomDC_0511_2016-->