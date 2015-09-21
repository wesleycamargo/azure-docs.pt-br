<properties 
	pageTitle="Use o SDK do .NET para criar canais que realizam codificação ao vivo por meio de um fluxo com taxa de bits única para fluxo com múltiplas taxas de bits (Visualização)" 
	description="Este tutorial orienta você pelas etapas de criação de um Canal que recebe um fluxo ao vivo de taxa de bits única e o codifica em fluxo de múltiplas taxas de bits." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="09/07/2015"
	ms.author="juliako"/>


#Use o SDK do .NET para criar canais que realizam codificação ao vivo de um fluxo com taxa de bits única para fluxo múltiplas taxas de bits (Visualização)

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [REST API](https://msdn.microsoft.com/library/azure/dn783458.aspx)

##Visão geral

Este tutorial orienta você pelas etapas de criação de um **Canal** que recebe um fluxo ao vivo de taxa de bits única e o codifica em fluxo de múltiplas taxas de bits.

>[AZURE.NOTE]Para obter mais informações conceituais relacionadas aos canais habilitados para codificação ao vivo, consulte [Trabalhando com canais que executam codificação ao vivo de um fluxo de taxa de bits única para fluxo de múltiplas taxas de bits](media-services-manage-live-encoder-enabled-channels.md).

>[AZURE.NOTE]Você deve usar o Media Services .NET SDK na versão 3.2.0.0 ou mais recente.

##Cenário comum de streaming ao vivo

As etapas a seguir descrevem as tarefas envolvidas na criação de aplicativos comuns de streaming ao vivo.

1. Conecte uma câmera de vídeo a um computador. Inicie e configure um codificador ao vivo local que possa produzir um fluxo de taxa de bits única em um dos seguintes protocolos: RTMP, Smooth Streaming ou RTP (MPEG-TS). Para obter mais informações, consulte [Suporte RTMP dos Serviços de Mídia do Azure e Codificadores ao Vivo](http://go.microsoft.com/fwlink/?LinkId=532824).
	
	Essa etapa também pode ser realizada após a criação do canal.

1. Crie e inicie um Canal.

1. Recupere a URL de ingestão do canal.

	A URL de ingestão é usada pelo codificador ao vivo para enviar o fluxo para o canal.
1. Recupere a URL de visualização do canal. 

	Use essa URL para verificar se o canal está recebendo corretamente o fluxo ao vivo.

2. Crie um ativo.
3. Se você quiser que o ativo seja criptografado dinamicamente durante a reprodução, faça o seguinte: 	
	
	1. 	Crie uma chave de conteúdo. 
	1. 	Configure a política de autorização da chave de conteúdo.
1. Configure a política de entrega de ativos (usada pelo empacotamento dinâmico e criptografia dinâmica).
3. Crie um programa e especifique o uso do ativo que você criou.
1. Publique o ativo associado ao programa criando um localizador OnDemand.  

	Certifique-se de ter pelo menos uma unidade reservada para streaming no ponto de extremidade de streaming por meio do qual você deseja transmitir o conteúdo.
1. Inicie o programa quando estiver pronto para iniciar o streaming e o arquivamento.
2. Opcionalmente, o codificador ao vivo pode ser sinalizado para iniciar um anúncio. O anúncio é inserido no fluxo de saída.
1. Interrompa o programa sempre que você deseja parar o streaming e o arquivamento do evento.
1. Exclua o programa (e, opcionalmente, exclua o ativo).   

##Neste tópico

Este tópico mostra como executar operações diferentes em canais e programas usando o SDK dos Serviços de Mídia para .NET. Como muitas operações são demoradas, são usadas APIs do .NET que gerenciam operações de execução longa.

O tópico mostra como fazer o seguinte:

1. Crie e inicie um canal. São usadas APIs de execução longa.
1. Obtenha o ponto de extremidade de ingestão (entrada) dos canais. Esse ponto de extremidade deve ser fornecido ao codificador que pode enviar um fluxo ao vivo de taxa de bits única.
1. Obtenha o ponto de extremidade de visualização. Esse ponto de extremidade é usado para visualizar o fluxo. 
1. Crie um ativo que será usado para armazenar seu conteúdo. As políticas de entrega de ativos devem ser configuradas, assim, como mostrado neste exemplo.
1. Crie um programa e especifique o uso do ativo que você criou anteriormente. Inicie o programa. São usadas APIs de execução longa.
1. Crie um localizador para o ativo, de modo que o conteúdo seja publicado e possa ser transmitido para seus clientes.
1. Mostrar e ocultar slates. Iniciar e parar anúncios. São usadas APIs de execução longa.
1. Limpe seu canal e todos os recursos associados.

>[AZURE.NOTE]Enquanto esse recurso estiver no modo de Visualização, o máximo recomendado durante um evento ao vivo é 8 horas.
>
##Pré-requisitos
Os itens a seguir são necessários para concluir o tutorial.

- Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](azure.microsoft.com).
- Uma conta dos Serviços de Mídia. Para criar uma conta de Serviços de Mídia, consulte [Criar Conta](media-services-create-account.md).
- Visual Studio 2010 SP1 ou posterior.
- Uma webcam e um codificador que possa enviar um fluxo ao vivo de taxa de bits única.

##Configurar para o desenvolvimento com o SDK dos Serviços de Mídia para .NET
 
1. No Visual Studio, crie um aplicativo de console.
1. Adicione o SDK dos Serviços de Mídia para .NET a seu aplicativo de console usando o pacote do NuGet dos Serviços de Mídia.

##Conectar-se aos Serviços de Mídia
Como prática recomendada, você deve usar um arquivo app.config para armazenar o nome e a chave de conta dos Serviços de Mídia.

>[AZURE.NOTE]Para localizar os valores de Nome e Chave, vá para o Portal do Azure, selecione sua conta de serviço de mídia e clique no ícone "GERENCIAR CHAVES" na parte inferior da janela do portal. Clicando no ícone ao lado de cada caixa de texto, o valor é copiado para a área de transferência do sistema.
 
Adicione a seção appSettings ao arquivo app.config e defina os valores do nome e da chave de sua conta dos Serviços de Mídia.


	<?xml version="1.0"?>
	<configuration>
	  <appSettings>
	      <add key="MediaServicesAccountName" value="YouMediaServicesAccountName" />
	      <add key="MediaServicesAccountKey" value="YouMediaServicesAccountKey" />
	  </appSettings>
	</configuration>
	 
	

##Exemplo de código

	using System;
	using System.Collections.Generic;
	using System.Configuration;
	using System.IO;
	using System.Linq;
	using System.Net;
	using System.Security.Cryptography;
	using System.Text;
	using System.Threading.Tasks;
	using Microsoft.WindowsAzure.MediaServices.Client;
	using Newtonsoft.Json.Linq;
	using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
	
	namespace ConsoleApplication1
	{
	    class Program
	    {
	        private const string ChannelName = "channel001";
	        private const string AssetlName = "asset001";
	        private const string ProgramlName = "program001";
	
	        // Read values from the App.config file.
	        private static readonly string _mediaServicesAccountName =
	            ConfigurationManager.AppSettings["MediaServicesAccountName"];
	        private static readonly string _mediaServicesAccountKey =
	            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	
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
	
	            IChannel channel = CreateAndStartChannel();
	
	            // The channel's input endpoint:
	            string ingestUrl = channel.Input.Endpoints.FirstOrDefault().Url.ToString();
	
	            Console.WriteLine("Intest URL: {0}", ingestUrl);
	
	
	            // Use the previewEndpoint to preview and verify 
	            // that the input from the encoder is actually reaching the Channel. 
	            string previewEndpoint = channel.Preview.Endpoints.FirstOrDefault().Url.ToString();
	
	            Console.WriteLine("Preview URL: {0}", previewEndpoint);
	
	            // Get a thumbnail preview of a live feed.
	            // When Live Encoding is enabled, you can now get a preview of the live feed as it reaches the Channel. 
	            // This can be a valuable tool to check whether your live feed is actually reaching the Channel. 
	
	            string thumbnailUri = new UriBuilder
	            {
	                Scheme = Uri.UriSchemeHttps,
	                Host = channel.Preview.Endpoints.FirstOrDefault().Url.Host,
	                Path = "thumbnails/input.jpg"
	            }.Uri.ToString();
	
	            Console.WriteLine("Thumbain URL: {0}", thumbnailUri);
	
	            // Once you previewed your stream and verified that it is flowing into your Channel, 
	            // you can create an event by creating an Asset, Program, and Streaming Locator. 
	            IAsset asset = CreateAndConfigureAsset();
	
	            IProgram program = CreateAndStartProgram(channel, asset);
	
	            ILocator locator = CreateLocatorForAsset(program.Asset, program.ArchiveWindowLength);
	
	            // You can use slates and ads only if the channel type is Standard.  
	            StartStopAdsSlates(channel);
	
	            // Once you are done streaming, clean up your resources.
	            Cleanup(channel);
	
	        }
	
	        public static IChannel CreateAndStartChannel()
	        {
	            ChannelCreationOptions options = new ChannelCreationOptions
	            {
	                EncodingType = ChannelEncodingType.Standard,
	                Name = ChannelName,
	                Input = CreateChannelInput(),
	                Preview = CreateChannelPreview(),
	                Encoding = CreateChannelEncoding()
	            };
	
	            Log("Creating channel");
	            IOperation channelCreateOperation = _context.Channels.SendCreateOperation(options);
	            string channelId = TrackOperation(channelCreateOperation, "Channel create");
	
	            IChannel channel = _context.Channels.Where(c => c.Id == channelId).FirstOrDefault();
	
	            Log("Starting channel");
	            var channelStartOperation = channel.SendStartOperation();
	            TrackOperation(channelStartOperation, "Channel start");
	
	            return channel;
	        }
	
	        /// <summary>
	        /// Create channel input, used in channel creation options. 
	        /// </summary>
	        /// <returns></returns>
	        private static ChannelInput CreateChannelInput()
	        {
	            return new ChannelInput
	            {
	                StreamingProtocol = StreamingProtocol.RTPMPEG2TS,
	                AccessControl = new ChannelAccessControl
	                {
	                    IPAllowList = new List<IPRange>
	                    {
	                        new IPRange
	                        {
	                            Name = "TestChannelInput001",
	                            Address = IPAddress.Parse("0.0.0.0"),
	                            SubnetPrefixLength = 0
	                        }
	                    }
	                }
	            };
	        }
	
	        /// <summary>
	        /// Create channel preview, used in channel creation options. 
	        /// </summary>
	        /// <returns></returns>
	        private static ChannelPreview CreateChannelPreview()
	        {
	            return new ChannelPreview
	            {
	                AccessControl = new ChannelAccessControl
	                {
	                    IPAllowList = new List<IPRange>
	                    {
	                        new IPRange
	                        {
	                            Name = "TestChannelPreview001",
	                            Address = IPAddress.Parse("0.0.0.0"),
	                            SubnetPrefixLength = 0
	                        }
	                    }
	                }
	            };
	        }
	
	        /// <summary>
	        /// Create channel encoding, used in channel creation options. 
	        /// </summary>
	        /// <returns></returns>
	        private static ChannelEncoding CreateChannelEncoding()
	        {
	            return new ChannelEncoding
	            {
	                SystemPreset = "Default720p",
	                IgnoreCea708ClosedCaptions = false,
	                AdMarkerSource = AdMarkerSource.Api,
	                // You can only set audio if streaming protocol is set to StreamingProtocol.RTPMPEG2TS.
	                AudioStreams = new List<AudioStream> { new AudioStream { Index = 103, Language = "eng" } }.AsReadOnly()
	            };
	        }
	
	        /// <summary>
	        /// Create an asset and configure asset delivery policies.
	        /// </summary>
	        /// <returns></returns>
	        public static IAsset CreateAndConfigureAsset()
	        {
	            IAsset asset = _context.Assets.Create(AssetlName, AssetCreationOptions.None);
	
	            IAssetDeliveryPolicy policy =
	                _context.AssetDeliveryPolicies.Create("Clear Policy",
	                AssetDeliveryPolicyType.NoDynamicEncryption,
	                AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
	
	            asset.DeliveryPolicies.Add(policy);
	
	            return asset;
	        }
	
	        /// <summary>
	        /// Create a Program on the Channel. You can have multiple Programs that overlap or are sequential;
	        /// however each Program must have a unique name within your Media Services account.
	        /// </summary>
	        /// <param name="channel"></param>
	        /// <param name="asset"></param>
	        /// <returns></returns>
	        public static IProgram CreateAndStartProgram(IChannel channel, IAsset asset)
	        {
	            IProgram program = channel.Programs.Create(ProgramlName, TimeSpan.FromHours(3), asset.Id);
	            Log("Program created", program.Id);
	
	            Log("Starting program");
	            var programStartOperation = program.SendStartOperation();
	            TrackOperation(programStartOperation, "Program start");
	
	            return program;
	        }
	
	        /// <summary>
	        /// Create locators in order to be able to publish and stream the video.
	        /// </summary>
	        /// <param name="asset"></param>
	        /// <param name="ArchiveWindowLength"></param>
	        /// <returns></returns>
	        public static ILocator CreateLocatorForAsset(IAsset asset, TimeSpan ArchiveWindowLength)
	        {
	            var locator = _context.Locators.CreateLocator
	                (
	                    LocatorType.OnDemandOrigin,
	                    asset,
	                    _context.AccessPolicies.Create
	                        (
	                            "Live Stream Policy",
	                            ArchiveWindowLength,
	                            AccessPermissions.Read
	                        )
	                );
	
	            return locator;
	        }
	
	        /// <summary>
	        /// Perform operations on slates.
	        /// </summary>
	        /// <param name="channel"></param>
	        public static void StartStopAdsSlates(IChannel channel)
	        {
	            Log("Creating asset");
	            var slateAsset = _context.Assets.Create("Slate test asset " + DateTime.Now.ToString("yyyy-MM-dd HH-mm"), AssetCreationOptions.None);
	            Log("Slate asset created", slateAsset.Id);
	
	            Log("Uploading file");
	            var assetFile = slateAsset.AssetFiles.Create("SlateTest.jpg");
	            assetFile.Upload("SlateTest.jpg");
	            assetFile.IsPrimary = true;
	            assetFile.Update();
	
	            Log("Showing slate");
	            var showSlateOpeartion = channel.SendShowSlateOperation(TimeSpan.FromMinutes(1), slateAsset.Id);
	            TrackOperation(showSlateOpeartion, "Show slate");
	
	            Log("Hiding slate");
	            var hideSlateOperation = channel.SendHideSlateOperation();
	            TrackOperation(hideSlateOperation, "Hide slate");
	
	            Log("Starting ad");
	            var startAdOperation = channel.SendStartAdvertisementOperation(TimeSpan.FromMinutes(1), 0, false);
	            TrackOperation(startAdOperation, "Start ad");
	
	            Log("Ending ad");
	            var endAdOperation = channel.SendEndAdvertisementOperation();
	            TrackOperation(endAdOperation, "End ad");
	
	            Log("Deleting slate asset");
	            slateAsset.Delete();
	        }
	
	        /// <summary>
	        /// Clean up resources associated with the channel.
	        /// </summary>
	        /// <param name="channel"></param>
	        public static void Cleanup(IChannel channel)
	        {
	            IAsset asset;
	            if (channel != null)
	            {
	                foreach (var program in channel.Programs)
	                {
	                    asset = _context.Assets.Where(se => se.Id == program.AssetId)
	                                            .FirstOrDefault();
	
	                    Log("Stopping program");
	                    var programStopOperation = program.SendStopOperation();
	                    TrackOperation(programStopOperation, "Program stop");
	
	                    program.Delete();
	
	                    if (asset != null)
	                    {
	                        Log("Deleting locators");
	                        foreach (var l in asset.Locators)
	                            l.Delete();
	
	                        Log("Deleting asset");
	                        asset.Delete();
	                    }
	                }
	
	                Log("Stopping channel");
	                var channelStopOperation = channel.SendStopOperation();
	                TrackOperation(channelStopOperation, "Channel stop");
	
	                Log("Deleting channel");
	                var channelDeleteOperation = channel.SendDeleteOperation();
	                TrackOperation(channelDeleteOperation, "Channel delete");
	            }
	        }
	
	
	        /// <summary>
	        /// Track long running operations.
	        /// </summary>
	        /// <param name="operation"></param>
	        /// <param name="description"></param>
	        /// <returns></returns>
	        public static string TrackOperation(IOperation operation, string description)
	        {
	            string entityId = null;
	            bool isCompleted = false;
	
	            Log("starting to track ", null, operation.Id);
	            while (isCompleted == false)
	            {
	                operation = _context.Operations.GetOperation(operation.Id);
	                isCompleted = IsCompleted(operation, out entityId);
	                System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
	            }
	            // If we got here, the operation succeeded.
	            Log(description + " in completed", operation.TargetEntityId, operation.Id);
	
	            return entityId;
	        }
	
	        /// <summary> 
	        /// Checks if the operation has been completed. 
	        /// If the operation succeeded, the created entity Id is returned in the out parameter.
	        /// </summary> 
	        /// <param name="operationId">The operation Id.</param> 
	        /// <param name="channel">
	        /// If the operation succeeded, 
	        /// the entity Id associated with the sucessful operation is returned in the out parameter.</param>
	        /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
	        private static bool IsCompleted(IOperation operation, out string entityId)
	        {
	
	            bool completed = false;
	
	            entityId = null;
	
	            switch (operation.State)
	            {
	                case OperationState.Failed:
	                    // Handle the failure. 
	                    // For example, throw an exception. 
	                    // Use the following information in the exception: operationId, operation.ErrorMessage.
	                    Log("operation failed", operation.TargetEntityId, operation.Id);
	                    break;
	                case OperationState.Succeeded:
	                    completed = true;
	                    entityId = operation.TargetEntityId;
	                    break;
	                case OperationState.InProgress:
	                    completed = false;
	                    Log("operation in progress", operation.TargetEntityId, operation.Id);
	                    break;
	            }
	            return completed;
	        }
	
	
	        private static void Log(string action, string entityId = null, string operationId = null)
	        {
	            Console.WriteLine(
	                "{0,-21}{1,-51}{2,-51}{3,-51}",
	                DateTime.Now.ToString("yyyy'-'MM'-'dd HH':'mm':'ss"),
	                action,
	                entityId ?? string.Empty,
	                operationId ?? string.Empty);
	        }
	    }
	}
	



##Roteiros de aprendizagem dos Serviços de Mídia

Você pode exibir os roteiros de aprendizagem do AMS aqui:

- [Fluxo de trabalho do streaming ao vivo do AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Fluxo de trabalho do streaming sob demanda do AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

 

<!---HONumber=Sept15_HO2-->