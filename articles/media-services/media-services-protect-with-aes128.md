<properties
	pageTitle="Utilizando criptografia dinâmica AES-128 e serviço de entrega de chaves"
	description="Os Serviços de Mídia do Microsoft Azure permitem que você entregue o conteúdo criptografado com chaves criptografia AES de 128 bits. Os Serviços de Mídia também fornecem o serviço de distribuição de chaves, que distribui chaves de criptografia para usuários autorizados. Este tópico mostra como criptografar dinamicamente com o AES-128 e usar o serviço de distribuição de chaves."
	services="media-services"
	documentationCenter=""
	authors="Juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
 	ms.date="04/07/2016" 
	ms.author="juliako"/>

#Utilizando criptografia dinâmica AES-128 e serviço de entrega de chaves

> [AZURE.SELECTOR]
- [.NET](media-services-protect-with-aes128.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##Visão geral

Os Serviços de Mídia do Microsoft Azure permitem distribuir conteúdo HLS (Http Live Streaming) e Smooth Streams criptografados com criptografia AES (padrão avançado) (usando chaves de criptografia de 128 bits). Os Serviços de Mídia também fornecem o serviço de distribuição de chaves, que distribui chaves de criptografia para usuários autorizados. Se você desejar que os Serviços de Mídia criptografem um ativo, você precisa associar uma chave de criptografia ao ativo e também configurar políticas de autorização para a chave. Quando um fluxo é solicitado por um player, os Serviços de Mídia usam a chave especificada para criptografar dinamicamente o conteúdo usando a criptografia AES. Para descriptografar o fluxo, o player solicitará a chave do serviço de distribuição de chaves. Para decidir se o usuário está autorizado para obter a chave ou não, o serviço avalia as políticas de autorização que você especificou para a chave.

Os serviços de mídia oferecem suporte a várias maneiras de autenticar os usuários que fazem solicitações de chave. A política de autorização de chave de conteúdo pode ter uma ou mais restrições de autorização: aberta ou restrição de token. A política restrita do token deve ser acompanhada por um token emitido por um Secure Token Service (STS). Os Serviços de Mídia dão suporte a tokens no formato SWT ([Tokens Web Simples](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) e no formato JWT ([Token Web JSON](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)). Para saber mais, consulte [Configure a política de autorização de chave de conteúdo](media-services-protect-with-aes128.md#configure_key_auth_policy).

Para tirar proveito da criptografia dinâmica, você precisa ter um ativo que contenha um conjunto de arquivos MP4 com múltiplas taxas de bits ou arquivos de origem de Smooth Streaming com múltiplas taxas de bits. Você também precisa configurar a política de entrega para o ativo (descrita mais adiante neste tópico). Em seguida, com base no formato especificado na URL de streaming, o servidor de streaming sob demanda garantirá que você receba o fluxo no protocolo escolhido por você. Como resultado você só precisa armazenar e pagar pelos arquivos em um único formato de armazenamento, e os Serviços de Mídia vão criar e fornecer a resposta apropriada com base nas solicitações de um cliente.

Este tópico poderá ser útil para desenvolvedores que trabalham em aplicativos que entregam mídia protegida. O tópico mostra como configurar o serviço de distribuição de chaves com políticas de autorização para que somente clientes autorizados possam receber as chaves de criptografia. Ele também mostra como usar criptografia dinâmica.

>[AZURE.NOTE]Para começar a usar criptografia dinâmica, é necessário primeiro obter pelo menos uma unidade de escala (também conhecida como unidade de streaming). Para obter mais informações, consulte [Como dimensionar um serviço de mídia](media-services-manage-origins.md#scale_streaming_endpoints).

##Criptografia dinâmica AES-128 e fluxo de trabalho de serviço de distribuição de chaves

A seguir estão as etapas gerais que você precisará executar ao criptografar seus ativos com o AES, usando o serviço de distribuição de chaves dos Serviços de Mídia e também usando criptografia dinâmica.

1. [Criar um ativo e carregar arquivos no ativo](media-services-protect-with-aes128.md#create_asset). 
1. [Codificar o ativo contendo o arquivo para o conjunto de MP4 de taxa de bits adaptável](media-services-protect-with-aes128.md#encode_asset).
1. [Criar uma chave de conteúdo e associá-la ao ativo codificado](media-services-protect-with-aes128.md#create_contentkey). Nos Serviços de Mídia, a chave de conteúdo contém a chave de criptografia do ativo.
1. [Configurar a política de autorização da chave de conteúdo](media-services-protect-with-aes128.md#configure_key_auth_policy). A política de autorização de chave de conteúdo deve ser configurada por você e atendida pelo cliente para que a chave de conteúdo seja entregue ao cliente. 
1. [Configure a política de entrega para um ativo](media-services-protect-with-aes128.md#configure_asset_delivery_policy). A configuração de política de entrega inclui: URL de aquisição de chave e VI (vetor de inicialização) (o AES 128 requer que o mesmo VI seja fornecido ao criptografar e descriptografar), protocolo de entrega (por exemplo, MPEG DASH, HLS, HDS, Smooth Streaming ou todos), o tipo de criptografia dinâmica (por exemplo, envelope ou nenhuma criptografia dinâmica). 

	Você poderia aplicar uma política diferente a cada protocolo no mesmo ativo. Por exemplo, você poderia aplicar criptografia PlayReady a Smooth/DASH e aplicar Envelope de AES a HLS. Todos os protocolos que não são definidos em uma política de entrega (por exemplo, você adicionar uma única política que só especifica HLS como o protocolo) será bloqueado a partir do streaming. A exceção a isso é se você não tiver nenhuma política de entrega de ativos definida em todos. Em seguida, todos os protocolos poderão ser criptografados.

1. [Criar um localizador OnDemand](media-services-protect-with-aes128.md#create_locator) para obter uma URL de streaming.

O tópico também mostra [como um aplicativo cliente pode solicitar uma chave do serviço de distribuição de chaves](media-services-protect-with-aes128.md#client_request).

Você encontrará um [exemplo](media-services-protect-with-aes128.md#example) de .NET completo no final do tópico.

A imagem a seguir demonstra o fluxo de trabalho descrito acima. Aqui, o token é usado para autenticação.

![Proteger com o AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

O restante deste tópico fornece explicações detalhadas, exemplos de código e links para tópicos que mostram como realizar as tarefas descritas acima.

##Limitações atuais

Se você adicionar ou atualizar a política de fornecimento do ativo, você deve excluir um localizador existente (se houver) e criar um novo localizador.

##<a id="create_asset"></a>Criar um ativo e carregar arquivos no ativo

Para gerenciar, codificar e transmitir seus vídeos, você deve primeiro carregar o conteúdo nos Serviços de Mídia do Microsoft Azure. Depois de carregado, seu conteúdo é armazenado com segurança na nuvem para processamento adicional e transmissão.

Para obter informações detalhadas, consulte [Carregar arquivos em uma conta dos Serviços de Mídia](media-services-dotnet-upload-files.md).

##<a id="encode_asset"></a>Codificar o ativo contendo o arquivo para o conjunto de MP4 de taxa de bits adaptável

Com a criptografia dinâmica, tudo o que você precisa fazer é criar um ativo que contenha um conjunto de arquivos MP4 com múltiplas taxas de bits ou arquivos de origem de Smooth Streaming com múltiplas taxas de bits. Em seguida, com base no formato especificado na solicitação de fragmento ou manifesto, o servidor de Streaming OnDemand garantirá que você receba o fluxo no protocolo escolhido por você. Como resultado você só precisa armazenar e pagar pelos arquivos em um único formato de armazenamento, e os Serviços de Mídia vão criar e fornecer a resposta apropriada com base nas solicitações de um cliente. Para saber mais, consulte o tópico [Visão geral sobre o Empacotamento dinâmico](media-services-dynamic-packaging-overview.md).

Para obter instruções sobre como codificar, consulte [Como codificar um ativo usando o Codificador de mídia padrão](media-services-dotnet-encode-with-media-encoder-standard.md).

##<a id="create_contentkey"></a>Criar uma chave de conteúdo e associá-la ao ativo codificado

Nos Serviços de Mídia, a chave de conteúdo contém a chave com a qual você deseja criptografar um ativo.

Para obter informações detalhadas, consulte [Criar chave de conteúdo](media-services-dotnet-create-contentkey.md).

##<a id="configure_key_auth_policy"></a>Configurar a política de autorização da chave de conteúdo

Os serviços de mídia oferecem suporte a várias maneiras de autenticar os usuários que fazem solicitações de chave. A política de autorização de chave de conteúdo deve ser configurada por você e atendida pelo cliente (player) para que a chave seja entregue ao cliente. A política de autorização de chave de conteúdo pode ter uma ou mais restrições de autorização: aberta, restrição de token ou restrição de IP.

Para obter informações detalhadas, consulte [Configurar política de autorização de chave de conteúdo](media-services-dotnet-configure-content-key-auth-policy.md).

##<a id="configure_asset_delivery_policy"></a>Configurar política de entrega de ativos 

Configure a política de entrega para seu ativo. Algumas coisas incluídas na configuração de política de entrega de ativos:

- A URL de aquisição de chave. 
- O VI (vetor de inicialização) a ser usado para a criptografia de envelope. O AES 128 requer que o mesmo VI seja fornecido tanto ao criptografar quanto ao descriptografar. 
- O protocolo de entrega de ativos (por exemplo, MPEG DASH, HLS, HDS, Smooth Streaming ou todos).
- O tipo de criptografia dinâmica (por exemplo, envelope de AES) ou ausência de criptografia dinâmica. 

Para obter informações detalhadas, consulte [Configurar política de entrega de ativos](media-services-rest-configure-asset-delivery-policy.md).

##<a id="create_locator"></a>Criar um localizador de streaming OnDemand para obter uma URL de streaming

Você precisará fornecer ao seu usuário a URL para Smooth Streaming, DASH ou HLS.

>[AZURE.NOTE]Se você adicionar ou atualizar a política de fornecimento do ativo, você deve excluir um localizador existente (se houver) e criar um novo localizador.

Para obter instruções sobre como publicar um ativo e criar uma URL de streaming, consulte [Criar uma URL de streaming](media-services-deliver-streaming-content.md).

##Obter um token de teste

Obtenha um token de teste com base na restrição de token que foi usada para a política de autorização da chave.

	// Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
	// back into a TokenRestrictionTemplate class instance.
	TokenRestrictionTemplate tokenTemplate = 
	    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
	
	// Generate a test token based on the data in the given TokenRestrictionTemplate.
	//The GenerateTestToken method returns the token without the word “Bearer” in front
	//so you have to add it in front of the token string. 
	string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
	Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

É possível usar o [AMS Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) para testar seu fluxo.

##<a id="client_request"></a>Como seu cliente pode solicitar uma chave do serviço de distribuição de chaves?

Na etapa anterior, você construiu a URL que aponta para um arquivo de manifesto. O cliente precisa extrair as informações necessárias dos arquivos de manifesto de streaming para que possa fazer uma solicitação para o serviço de distribuição de chaves.

###Arquivos de manifesto

O cliente precisa extrair o valor da URL (que também contém a kID, ID da chave de conteúdo) do arquivo de manifesto. O cliente tentará obter a chave de criptografia por meio do serviço de distribuição de chaves. O cliente também precisa extrair o valor do IV e usá-lo para descriptografar o fluxo. O trecho a seguir mostra o elemento <Protection> do manifesto de Smooth Streaming.

	<Protection>
	  <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
	    <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
	      <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
	      <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
	      <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
	                        keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
	                                        kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
	    </ContentProtection>
	  </ProtectionHeader>
	</Protection>

No caso de HLS, o manifesto raiz é dividido em arquivos de segmento.

Por exemplo, o manifesto raiz é: http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl) e contém uma lista de nomes de arquivos de segmento.
	
	. . . 
	#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
	QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
	#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
	QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
	…

Se você abrir um dos arquivos de segmento no editor de texto (por exemplo, http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl)), ele deve conter #EXT-X-KEY, que indica que o arquivo está criptografado.
	
	#EXTM3U
	#EXT-X-VERSION:4
	#EXT-X-ALLOW-CACHE:NO
	#EXT-X-MEDIA-SEQUENCE:0
	#EXT-X-TARGETDURATION:9
	#EXT-X-KEY:METHOD=AES-128,
	URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
	     kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
	        IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
	#EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
	#EXTINF:8.425708,no-desc
	Fragments(video=0,format=m3u8-aapl)
	#EXT-X-ENDLIST
	
###Solicitar a chave ao serviço de distribuição de chaves

O código a seguir mostra como enviar uma solicitação ao serviço de distribuição de chaves dos Serviços de Mídia usando um Uri de distribuição de chaves (que foi extraído do manifesto) e um token (Este tópico não trata de como obter Tokens Web Simples de um Serviço de Token Seguro).

	private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
	{
	    HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);
	            
	    request.Method = "POST";
	    request.ContentType = "text/xml";
	    if (!string.IsNullOrEmpty(token))
	    {
	        request.Headers[AuthorizationHeader] = token;
	    }
	    request.ContentLength = 0;
	
	    var response = request.GetResponse();
	 
	    var stream = response.GetResponseStream();
	    if (stream == null)
	    {
	        throw new NullReferenceException("Response stream is null");
	    }
	
	    var buffer = new byte[256];
	    var length = 0;
	    while (stream.CanRead && length <= buffer.Length)
	    {
	        var nexByte = stream.ReadByte();
	        if (nexByte == -1)
	        {
	            break;
	        }
	        buffer[length] = (byte)nexByte;
	        length++;
	    }
	    response.Close();
	
	    // AES keys must be exactly 16 bytes (128 bits).
	    var key = new byte[length];
	    Array.Copy(buffer, key, length);
	    return key;
	}
	
##<a id="example"></a>Exemplo

1. Crie um novo projeto de console.
1. Use o NuGet para instalar e adicionar extensões do SDK do .NET dos Serviços de Mídia do Azure. Instalar esse pacote também instala os SDK do .NET dos Serviços de Mídia e adiciona todas as outras dependências necessárias.
2. Adicione o arquivo de configuração que contém o nome da conta e as informações de chave:

	
		<?xml version="1.0" encoding="utf-8"?>
		<configuration>
		    <startup> 
		        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		    </startup>
			  <appSettings>
			
			    <add key="MediaServicesAccountName" value="AccountName"/>
			    <add key="MediaServicesAccountKey" value="AccountKey"/>
			
			    <add key="Issuer" value="http://testacs.com"/>
			    <add key="Audience" value="urn:test"/>
			  </appSettings>
		</configuration>

1. Substitua o código no seu arquivo Program.cs pelo código mostrado nesta seção.
	
	Certifique-se de atualizar as variáveis para que indiquem as pastas onde estão localizados os arquivos de entrada.
			
		
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
		using System.Threading;
		using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
		using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
		using System.Web;
		using System.Globalization;
		
		namespace AESDynamicEncryptionAndKeyDeliverySvc
		{
		    class Program
		    {
		        // Read values from the App.config file.
		        private static readonly string _mediaServicesAccountName =
		            ConfigurationManager.AppSettings["MediaServicesAccountName"];
		        private static readonly string _mediaServicesAccountKey =
		            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		
		        // A Uri describing the issuer of the token.  
		        // Must match the value in the token for the token to be considered valid.
		        private static readonly Uri _sampleIssuer =
		            new Uri(ConfigurationManager.AppSettings["Issuer"]);
		        // The Audience or Scope of the token.  
		        // Must match the value in the token for the token to be considered valid.
		        private static readonly Uri _sampleAudience =
		            new Uri(ConfigurationManager.AppSettings["Audience"]);
		
		        // Field for service context.
		        private static CloudMediaContext _context = null;
		        private static MediaServicesCredentials _cachedCredentials = null;
		
		        private static readonly string _mediaFiles =
		            Path.GetFullPath(@"../..\Media");
		
		        private static readonly string _singleMP4File =
		            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
		
		
		        static void Main(string[] args)
		        {
		            // Create and cache the Media Services credentials in a static class variable.
		            _cachedCredentials = new MediaServicesCredentials(
		                            _mediaServicesAccountName,
		                            _mediaServicesAccountKey);
		            // Used the chached credentials to create CloudMediaContext.
		            _context = new CloudMediaContext(_cachedCredentials);
		
		            bool tokenRestriction = false;
		            string tokenTemplateString = null;
		
		            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
		            Console.WriteLine("Uploaded asset: {0}", asset.Id);
		
		            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
		            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
		
		            IContentKey key = CreateEnvelopeTypeContentKey(encodedAsset);
		            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
		            Console.WriteLine();
		
		            if (tokenRestriction)
		                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
		            else
		                AddOpenAuthorizationPolicy(key);
		
		            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
		            Console.WriteLine();
		
		            CreateAssetDeliveryPolicy(encodedAsset, key);
		            Console.WriteLine("Created asset delivery policy. \n");
		            Console.WriteLine();
		
		            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
		            {
		                // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
		                // back into a TokenRestrictionTemplate class instance.
		                TokenRestrictionTemplate tokenTemplate =
		                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
		
		                // Generate a test token based on the data in the given TokenRestrictionTemplate.
		                // Note, you need to pass the key id Guid because we specified 
		                // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
		                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
		
		                //The GenerateTestToken method returns the token without the word “Bearer” in front
		                //so you have to add it in front of the token string. 
		                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
		                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
		                Console.WriteLine();
		            }
		
		            // You can use the bit.ly/aesplayer Flash player to test the URL 
		            // (with open authorization policy). 
		            // Paste the URL and click the Update button to play the video. 
		            //
		            string URL = GetStreamingOriginLocator(encodedAsset);
		            Console.WriteLine("Smooth Streaming Url: {0}/manifest", URL);
		            Console.WriteLine();
		            Console.WriteLine("HLS Url: {0}/manifest(format=m3u8-aapl)", URL);
		            Console.WriteLine();
		
		            Console.ReadLine();
		        }
		
		        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
		        {
		            if (!File.Exists(singleFilePath))
		            {
		                Console.WriteLine("File does not exist.");
		                return null;
		            }
		
		            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
		            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.StorageEncrypted);
		
		            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));
		
		            Console.WriteLine("Created assetFile {0}", assetFile.Name);
		
		            var policy = _context.AccessPolicies.Create(
		                                    assetName,
		                                    TimeSpan.FromDays(30),
		                                    AccessPermissions.Write | AccessPermissions.List);
		
		            var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);
		
		            Console.WriteLine("Upload {0}", assetFile.Name);
		
		            assetFile.Upload(singleFilePath);
		            Console.WriteLine("Done uploading {0}", assetFile.Name);
		
		            locator.Delete();
		            policy.Delete();
		
		            return inputAsset;
		        }
		
		        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
		        {
		            // Declare a new job.
		            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
		            // Get a media processor reference, and pass to it the name of the 
		            // processor to use for the specific task.
		            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
		
		            // Create a task with the encoding details, using a string preset.
		            // In this case "H264 Multiple Bitrate 720p" preset is used.
		            ITask task = job.Tasks.AddNew("My encoding task",
		                processor,
		                "H264 Multiple Bitrate 720p",
		                TaskOptions.None);
		
		            // Specify the input asset to be encoded.
		            task.InputAssets.Add(asset);
		            // Add an output asset to contain the results of the job. 
		            // This output is specified as AssetCreationOptions.None, which 
		            // means the output asset is not encrypted. 
		            task.OutputAssets.AddNew("Output asset",
		                AssetCreationOptions.None);
		
		            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
		            job.Submit();
		            job.GetExecutionProgressTask(CancellationToken.None).Wait();
		
		            return job.OutputMediaAssets[0];
		        }
		
		        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
		        {
		            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
		            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
		
		            if (processor == null)
		                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
		
		            return processor;
		        }
		
		        static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
		        {
		            // Create envelope encryption content key
		            Guid keyId = Guid.NewGuid();
		            byte[] contentKey = GetRandomBuffer(16);
		
		            IContentKey key = _context.ContentKeys.Create(
		                                    keyId,
		                                    contentKey,
		                                    "ContentKey",
		                                    ContentKeyType.EnvelopeEncryption);
		
		            // Associate the key with the asset.
		            asset.ContentKeys.Add(key);
		
		            return key;
		        }
		
		        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
		        {
		            // Create ContentKeyAuthorizationPolicy with Open restrictions 
		            // and create authorization policy             
		            IContentKeyAuthorizationPolicy policy = _context.
		                                    ContentKeyAuthorizationPolicies.
		                                    CreateAsync("Open Authorization Policy").Result;
		
		            List<ContentKeyAuthorizationPolicyRestriction> restrictions =
		                new List<ContentKeyAuthorizationPolicyRestriction>();
		
		            ContentKeyAuthorizationPolicyRestriction restriction =
		                new ContentKeyAuthorizationPolicyRestriction
		                {
		                    Name = "HLS Open Authorization Policy",
		                    KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
		                    Requirements = null // no requirements needed for HLS
		                        };
		
		            restrictions.Add(restriction);
		
		            IContentKeyAuthorizationPolicyOption policyOption =
		                _context.ContentKeyAuthorizationPolicyOptions.Create(
		                "policy",
		                ContentKeyDeliveryType.BaselineHttp,
		                restrictions,
		                "");
		
		            policy.Options.Add(policyOption);
		
		            // Add ContentKeyAutorizationPolicy to ContentKey
		            contentKey.AuthorizationPolicyId = policy.Id;
		            IContentKey updatedKey = contentKey.UpdateAsync().Result;
		            Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
		        }
		
		        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
		        {
		            string tokenTemplateString = GenerateTokenRequirements();
		
		            IContentKeyAuthorizationPolicy policy = _context.
		                                    ContentKeyAuthorizationPolicies.
		                                    CreateAsync("HLS token restricted authorization policy").Result;
		
		            List<ContentKeyAuthorizationPolicyRestriction> restrictions =
		                    new List<ContentKeyAuthorizationPolicyRestriction>();
		
		            ContentKeyAuthorizationPolicyRestriction restriction =
		                    new ContentKeyAuthorizationPolicyRestriction
		                    {
		                        Name = "Token Authorization Policy",
		                        KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
		                        Requirements = tokenTemplateString
		                    };
		
		            restrictions.Add(restriction);
		
		            //You could have multiple options 
		            IContentKeyAuthorizationPolicyOption policyOption =
		                _context.ContentKeyAuthorizationPolicyOptions.Create(
		                    "Token option for HLS",
		                    ContentKeyDeliveryType.BaselineHttp,
		                    restrictions,
		                    null  // no key delivery data is needed for HLS
		                    );
		
		            policy.Options.Add(policyOption);
		
		            // Add ContentKeyAutorizationPolicy to ContentKey
		            contentKey.AuthorizationPolicyId = policy.Id;
		            IContentKey updatedKey = contentKey.UpdateAsync().Result;
		            Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
		
		            return tokenTemplateString;
		        }
		
		        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
		        {
		            Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
		
		            string envelopeEncryptionIV = Convert.ToBase64String(GetRandomBuffer(16));
		
		            // The following policy configuration specifies: 
		            //   key url that will have KID=<Guid> appended to the envelope and
		            //   the Initialization Vector (IV) to use for the envelope encryption.
		            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
		                new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
		            {
		                        {AssetDeliveryPolicyConfigurationKey.EnvelopeKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
		                        {AssetDeliveryPolicyConfigurationKey.EnvelopeEncryptionIVAsBase64, envelopeEncryptionIV}
		            };
		
		            IAssetDeliveryPolicy assetDeliveryPolicy =
		                _context.AssetDeliveryPolicies.Create(
		                            "AssetDeliveryPolicy",
		                            AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
		                            AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS,
		                            assetDeliveryPolicyConfiguration);
		
		            // Add AssetDelivery Policy to the asset
		            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
		            Console.WriteLine();
		            Console.WriteLine("Adding Asset Delivery Policy: " +
		                assetDeliveryPolicy.AssetDeliveryPolicyType);
		        }
		
		        static public string GetStreamingOriginLocator(IAsset asset)
		        {
		
		            // Get a reference to the streaming manifest file from the  
		            // collection of files in the asset. 
		
		            var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
		                                        EndsWith(".ism")).
		                                        FirstOrDefault();
		
		            // Create a 30-day readonly access policy. 
                	// You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
		            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
		                TimeSpan.FromDays(30),
		                AccessPermissions.Read);
		
		            // Create a locator to the streaming content on an origin. 
		            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
		                policy,
		                DateTime.UtcNow.AddMinutes(-5));
		
		            // Create a URL to the manifest file. 
		            return originLocator.Path + assetFile.Name;
		        }
		
		        static private string GenerateTokenRequirements()
		        {
		            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
		
		            template.PrimaryVerificationKey = new SymmetricVerificationKey();
		            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
		            template.Audience = _sampleAudience.ToString();
		            template.Issuer = _sampleIssuer.ToString();
		
		            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
		
		            return TokenRestrictionTemplateSerializer.Serialize(template);
		        }
		
		        static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
		        {
		            Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
		                ((IJob)sender).Name,
		                e.CurrentState,
		                DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
		        }
		
		        static private byte[] GetRandomBuffer(int size)
		        {
		            byte[] randomBytes = new byte[size];
		            using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
		            {
		                rng.GetBytes(randomBytes);
		            }
		
		            return randomBytes;
		        }
		    }
		}


##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0413_2016-->