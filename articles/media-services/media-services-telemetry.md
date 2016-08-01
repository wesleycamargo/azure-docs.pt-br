<properties pageTitle="Telemetria dos Serviços de Mídia do Azure com o .NET | Microsoft Azure" 
	description="Este artigo mostra como usar a telemetria dos Serviços de Mídia do Azure." 
	services="" 
	documentationCenter=""
	authors="juliako" />

# Telemetria dos Serviços de Mídia do Azure com o .NET

## Visão geral

A telemetria/monitoramento dos Serviços de Mídia permite que os clientes de Serviços de Mídia acessem dados de métricas para seus serviços. A versão atual dá suporte a dados de telemetria para entidades "Channel" e "StreamingEndpoint". Você pode configurar a telemetria com granularidade no nível de componente. Há dois níveis de detalhe, "Normal" e "Detalhado". A versão atual dá suporte somente a "Normal".

A telemetria é gravada em uma conta de armazenamento do Azure fornecida pelo cliente (uma conta de armazenamento deve ser anexada à conta dos Serviços de Mídia). A telemetria é gravada em uma Tabela de Armazenamento do Azure na conta de armazenamento especificada. O sistema de telemetria criará uma tabela separada para cada novo dia com base em 00:00 UTC. Como um exemplo, "TelemetryMetrics20160321", em que "20160321" é a data da tabela criada. Para cada dia, haverá uma tabela separada.

O sistema de telemetria não fornece a retenção de dados ou exclui registros antigos automaticamente. Por esse motivo, você precisa gerenciar e excluir registros antigos. Ter tabelas separadas para cada dia facilita a exclusão de registros antigos. Você pode apenas excluir tabelas antigas.

Este tópico mostra como habilitar a telemetria para os serviços AMS especificados e como consultar as métricas usando o .NET.

## Configurando a telemetria para uma conta dos Serviços de Mídia

As etapas abaixo são necessárias para habilitar a telemetria:

- Obter as credenciais da conta de armazenamento anexada à conta dos Serviços de Mídia.
- Criar um ponto de extremidade de notificação com **EndPointType** definido como **AzureTable** e endPontAddress apontando para a tabela de armazenamento.

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


## Log StreamingEndpoint

###Métricas disponíveis

Você pode consultar as métricas de StreamingEndPoint a seguir.

- **PartitionKey** obtém a chave da partição do registro.
- **RowKey** obtém a chave da linha do registro.
- **AccountId** obtém a ID da conta de Serviços de Mídia.
- **AccountId** obtém a ID do ponto de extremidade de streaming dos Serviços de Mídia.
- **ObservedTime** obtém a hora observada da métrica.
- **HostName** obtém o nome de host do ponto de extremidade de streaming.
- **StatusCode** obtém o código de status.
- **ResultCode** obtém o código de resultado.
- **RequestCount** obtém o número de solicitações.
- **BytesSent** obtém os bytes enviados.
- **ServerLatency** obtém a latência do servidor.
- **EndToEndLatency** obtém a hora da solicitação de ponta a ponta.

###Exemplo de resultado de consulta de ponto de extremidade de streaming

![Consulta de ponto de extremidade de streaming](media/media-services-telemetry/media-services-telemetry01.png)


## Pulsação do canal ao vivo

###Métricas disponíveis

Você pode consultar as seguintes métricas de canal ao vivo.

- **PartitionKey** obtém a chave da partição do registro.
- **RowKey** obtém a chave da linha do registro.
- **AccountId** obtém a ID da conta de Serviços de Mídia.
- **ChannelId** obtém a ID do canal dos Serviços de Mídia.
- **ObservedTime** obtém a hora observada da métrica.
- **CustomAttributes** obtém os atributos personalizados.
- **TrackType** obtém o tipo de controle.
- **TrackName** obtém o nome da faixa.
- **Taxa de bits** obtém a taxa de bits.
- **IncomingBitrate** obtém a taxa de bits de entrada.
- **OverlapCount** obtém a contagem de sobreposição.
- **DiscontinuityCount** obtém a contagem de descontinuidade.
- **LastTimestamp** obtém o último carimbo de data/hora.
 
###Exemplo de resultado da consulta de canal ao vivo

![Consulta de ponto de extremidade de streaming](media/media-services-telemetry/media-services-telemetry01.png)

## Exemplo de métricas de StreamingEndpoint
		
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
	
	            INotificationEndPoint notificationEndPoint = 
	                          _context.NotificationEndPoints.Create("monitoring", NotificationEndPointType.AzureTable, GetTableEndPoint());
	
	            var monitoringConfigurations = _context.MonitoringConfigurations;
	            IMonitoringConfiguration monitoringConfiguration = null;
	
	            // No more than one monitoring configuration settings is allowed.
	            if (monitoringConfigurations.ToArray().Length != 0)
	            {
	                monitoringConfiguration = _context.MonitoringConfigurations.FirstOrDefault();
	            }
	            else
	            {
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

<!---HONumber=AcomDC_0720_2016-->