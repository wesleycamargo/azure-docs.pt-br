<properties pageTitle="Telemetria dos Serviços de Mídia do Azure com o .NET | Microsoft Azure" 
	description="Este artigo mostra como usar a telemetria dos Serviços de Mídia do Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/27/2016"   
	ms.author="juliako"/>

# Telemetria dos Serviços de Mídia do Azure com o .NET
 
## Visão geral

A telemetria/monitoramento dos Serviços de Mídia permite que os clientes de Serviços de Mídia acessem dados de métricas para seus serviços. A versão atual dá suporte a dados de telemetria para entidades "Channel" e "StreamingEndpoint". Você pode configurar a telemetria com granularidade no nível de componente. Há dois níveis de detalhe, "Normal" e "Detalhado". A versão atual dá suporte somente a "Normal".

A telemetria é gravada em uma tabela de armazenamento em uma conta de Armazenamento do Azure fornecida pelo cliente (a conta de armazenamento deve ser anexada à conta dos Serviços de Mídia). O sistema de telemetria criará uma tabela separada para cada novo dia com base em 00:00 UTC. Como um exemplo, "TelemetryMetrics20160321", em que "20160321" é a data da tabela criada. Para cada dia, haverá uma tabela separada.

Observe que o sistema de telemetria não gerencia a retenção de dados. Você pode remover os dados de telemetria antigos excluindo as tabelas de armazenamento.

É possível consumir os dados de telemetria das seguintes maneiras:

- Leia os dados diretamente no Armazenamento de Tabelas do Azure (por exemplo, usando o SDK de Armazenamento). Para obter a descrição das tabelas de armazenamento de telemetria, confira **Consumindo informações de telemetria** [neste](https://msdn.microsoft.com/library/mt742089.aspx) tópico.

Ou

- Use o suporte do SDK para .NET dos Serviços de Mídia para leitura de dados de armazenamento. Este tópico mostra como habilitar a telemetria para a conta AMS especificada e como consultar as métricas usando o SDK para .NET dos Serviços de Mídia do Azure.

## Configurando a telemetria para uma conta dos Serviços de Mídia

As etapas abaixo são necessárias para habilitar a telemetria:

- Obter as credenciais da conta de armazenamento anexada à conta dos Serviços de Mídia.
- Criar um Ponto de Extremidade de Notificação com **EndPointType** definido como **AzureTable** e endPontAddress apontando para a tabela de armazenamento.

	    INotificationEndPoint notificationEndPoint = 
	                  _context.NotificationEndPoints.Create("monitoring", 
	                  NotificationEndPointType.AzureTable,
	                  "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/");

- Criar definições de configuração de monitoramento para os serviços que você deseja monitorar. Não é permitida mais de uma definição de configuração de monitoramento.
  
        IMonitoringConfiguration monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
            new List<ComponentMonitoringSetting>()
            {
                new ComponentMonitoringSetting(MonitoringComponent.Channel, MonitoringLevel.Normal),
                new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)
            });

## Consumo de informações de telemetria

A telemetria é gravada em uma Tabela de Armazenamento do Azure na conta de armazenamento especificada durante a configuração da telemetria para a conta de Serviços de Mídia. O sistema de telemetria criará uma tabela separada para cada novo dia com base em 00:00 UTC. Como um exemplo, "TelemetryMetrics20160321", em que "20160321" é a data da tabela criada. Para cada dia, haverá uma tabela separada.

Você pode consultar as tabelas de telemetria em busca das informações de métricas a seguir. O exemplo, mostrado mais adiante neste tópico, demonstra como usar o SDK para .NET dos Serviços de Mídia para consultar métricas.

### Log StreamingEndpoint

Você pode consultar as métricas de StreamingEndPoint a seguir.

Propriedade|Descrição|Valor de exemplo
---|---|---
**PartitionKey**|Obtém a chave da partição do registro.|60b71b0f6a0e4d869eb0645c16d708e1\_6efed125eef44fb5b61916edc80e6e23
**RowKey**|Obtém a chave da linha do registro.|00959\_00000
**AccountId**|Obtém a ID da conta de Serviços de Mídia.|6efed125-eef4-4fb5-b619-16edc80e6e23
**StreamingEndpointId**|Obtém a ID do ponto de extremidade de streaming dos Serviços de Mídia.|d17ec9e4-a5d4-033d-0c36-def70229f06f
**ObservedTime**|Obtém a hora observada da métrica.|20/01/16 23:44:01
**HostName**|Obtém o nome de host do ponto de extremidade de streaming.|builddemoserver.origin.mediaservices.windows.net
**StatusCode**|Obtém o código de status.|200
**ResultCode**|Obtém o código do resultado.|S\_OK
**RequestCount**|Obtém a contagem de solicitações.|3
**BytesSent**|Obtém os bytes enviados.|2987358
**ServerLatency**|Obtém a latência do servidor (incluindo armazenamento).|129
**EndToEndLatency**|Obtém a hora da solicitação de ponta a ponta.|250


### Pulsação do canal ao vivo

Você pode consultar as seguintes métricas de canal ao vivo.

Propriedade|Descrição|Valor de exemplo
---|---|---
**PartitionKey**|Obtém a chave da partição do registro.|60b71b0f6a0e4d869eb0645c16d708e1\_0625cc45918e4f98acfc9a33e8066628
**RowKey**|Obtém a chave da linha do registro.|13872\_00005
**AccountId**|Obtém a ID da conta de Serviços de Mídia.|6efed125-eef4-4fb5-b619-16edc80e6e23
**ChannelId**|Obtém a ID do canal dos Serviços de Mídia.|
**ObservedTime**|Obtém a hora observada da métrica.|21/01/2016 20:08:49
**CustomAttributes**|Obtém os atributos personalizados.|
**TrackType**|Obtém o tipo de controle.|video
**TrackName**|Obtém o nome da faixa.|video
**Bitrate**|Obtém a taxa de bits.|785000
**IncomingBitrate**|Obtém a taxa de bits de entrada.|784548
**OverlapCount**|Obtém a contagem de sobreposição.|0
**DiscontinuityCount**|Obtém a contagem de descontinuidade.|0
**LastTimestamp**|Obtém o último carimbo de data/hora.|1800488800
 
## Exemplo  
	
O exemplo a seguir mostra habilitar a telemetria para a conta AMS especificada e como consultar as métricas usando o SDK para .NET dos Serviços de Mídia do Azure.

	using System;
	using System.Collections.Generic;
	using System.Configuration;
	using System.Linq;
	using Microsoft.WindowsAzure.MediaServices.Client;
	
	namespace AMSMetrics
	{
	    class Program
	    {
	        // Read values from the App.config file.
	        private static readonly string _mediaServicesAccountName =
	            ConfigurationManager.AppSettings["MediaServicesAccountName"];
	        private static readonly string _mediaServicesAccountKey =
	            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	        private static readonly string _mediaServicesAccountID =
	            ConfigurationManager.AppSettings["MediaServicesAccountID"];
	        private static readonly string _streamingEndpointID =
	            ConfigurationManager.AppSettings["StreamingEndpointID"];
	        private static readonly string _mediaServicesStorageAccountName =
	            ConfigurationManager.AppSettings["StorageAccountName"];
	        private static readonly string _mediaServicesStorageAccountKey =
	            ConfigurationManager.AppSettings["StorageAccountKey"];
	
	        // Field for service context.
	        private static CloudMediaContext _context = null;
	        private static MediaServicesCredentials _cachedCredentials = null;
	
	        static void Main(string[] args)
	        {
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName,
	                            _mediaServicesAccountKey);
	            // Used the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	

	
	            var monitoringConfigurations = _context.MonitoringConfigurations;
	            IMonitoringConfiguration monitoringConfiguration = null;
	
	            // No more than one monitoring configuration settings is allowed.
	            if (monitoringConfigurations.ToArray().Length != 0)
	            {
	                monitoringConfiguration = _context.MonitoringConfigurations.FirstOrDefault();
	            }
	            else
	            {
		            INotificationEndPoint notificationEndPoint = 
		                          _context.NotificationEndPoints.Create("monitoring", 
								  NotificationEndPointType.AzureTable, GetTableEndPoint());

	                monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
	                    new List<ComponentMonitoringSetting>()
	                    {
	                        new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)
	                    });
	            }
	
	            //Print metrics for a Streaming Endpoint.
	            PrintStreamingEndpointMetrics();
	
	            Console.ReadLine();
	        }
	
	        private static string GetTableEndPoint()
	        {
	            return "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/";
	        }
	
	        private static void PrintStreamingEndpointMetrics()
	        {
	            var end = DateTime.UtcNow;
	            var start = DateTime.UtcNow.AddHours(-5);
	
	            // Get some streaming endpoint metrics.
	            var res = _context.StreamingEndPointRequestLogs.GetStreamingEndPointMetrics(
	                    GetTableEndPoint(),
	                    _mediaServicesStorageAccountKey,
	                    _mediaServicesAccountID,
	                    _streamingEndpointID,
	                    start,
	                    end);
	
	
	            Console.Title = "Streaming endpoint metrics:";
	
	            foreach (var log in res)
	            {
	                Console.WriteLine("AccountId: {0}", log.AccountId);
	                Console.WriteLine("BytesSent: {0}", log.BytesSent);
	                Console.WriteLine("EndToEndLatency: {0}", log.EndToEndLatency);
	                Console.WriteLine("HostName: {0}", log.HostName);
	                Console.WriteLine("ObservedTime: {0}", log.ObservedTime);
	                Console.WriteLine("PartitionKey: {0}", log.PartitionKey);
	                Console.WriteLine("RequestCount: {0}", log.RequestCount);
	                Console.WriteLine("ResultCode: {0}", log.ResultCode);
	                Console.WriteLine("RowKey: {0}", log.RowKey);
	                Console.WriteLine("ServerLatency: {0}", log.ServerLatency);
	                Console.WriteLine("StatusCode: {0}", log.StatusCode);
	                Console.WriteLine("StreamingEndpointId: {0}", log.StreamingEndpointId);
	                Console.WriteLine();
	            }
	
	            Console.WriteLine();
	        }

	        private static void PrintChannelMetrics()
	        {
	            var end = DateTime.UtcNow;
	            var start = DateTime.UtcNow.AddHours(-5);
	
	            // Get some channel metrics.
	            var channelMetrics = _context.ChannelMetrics.GetChannelMetrics(
	                GetTableEndPoint(),
	                _mediaServicesStorageAccountKey,
	                _mediaServicesAccountName,
	                _context.Channels.FirstOrDefault().Id,
	                start,
	                end);
	
	            // Print the channel metrics.
	            Console.WriteLine("Channel metrics:");
	
	            foreach (var channelHeartbeat in channelMetrics.OrderBy(x => x.ObservedTime))
	            {
	                Console.WriteLine(
	                    "    Observed time: {0}, Last timestamp: {1}, Incoming bitrate: {2}",
	                    channelHeartbeat.ObservedTime,
	                    channelHeartbeat.LastTimestamp,
	                    channelHeartbeat.IncomingBitrate);
	            }
	
	            Console.WriteLine();
	        }
	    }
	}


##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Próxima etapa
 
Veja os roteiros de aprendizagem dos Serviços de Mídia do Azure para ajudá-lo a saber mais sobre os excelentes recursos oferecidos pelo AMS.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

<!---HONumber=AcomDC_0803_2016-->