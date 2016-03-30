<properties 
	pageTitle="Configurar políticas de entrega de ativos usando o SDK do .NET" 
	description="Este tópico mostra como configurar políticas de entrega de ativos diferentes com o SDK do .NET dos Serviços de Mídia do Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako,Mingfeiy" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
  	ms.date="03/15/2016"
	ms.author="juliako"/>

#Configurar políticas de entrega de ativos usando o SDK do .NET
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

##Visão geral

Se você planeja entregar ativos criptografados, uma das etapas do fluxo de trabalho de fornecimento de conteúdo de Serviços de Mídia é configurar políticas de entrega de ativos. A política de entrega de ativos informa aos serviços de mídia como você deseja que o ativo seja entregue: em que protocolo de fluxo seu ativo deve ser dinamicamente empacotado (por exemplo, MPEG DASH, HLS, Smooth Streaming ou todos), se você deseja criptografar dinamicamente seu ativo ou não e como (criptografia de envelope ou comum).

Este tópico discute por que e como criar e configurar políticas de entrega de ativos.

>[AZURE.NOTE]Para poder usar o empacotamento dinâmico e a criptografia dinâmica, certifique-se de ter pelo menos uma unidade de escala (também conhecida como unidade de streaming). Para obter mais informações, consulte [Como dimensionar um serviço de mídia](media-services-manage-origins.md#scale_streaming_endpoints).
>
>Além disso, o ativo deve conter um conjunto de MP4s de taxa de bits adaptável ou arquivos de Smooth Streaming de taxa de bits adaptável.

Você pode aplicar políticas diferentes para o mesmo ativo. Por exemplo, você poderia aplicar criptografia PlayReady para criptografia de Envelope AES e Smooth Streaming para MPEG DASH e HLS. Todos os protocolos que não são definidos em uma política de entrega (por exemplo, você adicionar uma única política que só especifica HLS como o protocolo) será bloqueado a partir do streaming. A exceção a isso é se você não tiver nenhuma política de entrega de ativos definida em todos. Em seguida, todos os protocolos poderão ser criptografados.

Se você quiser entregar um ativo de armazenamento criptografado, configure a política de entrega do ativo. Antes que seu ativo possa ser transmitido, o servidor de streaming remove a criptografia de armazenamento e transmite o conteúdo usando a política de entrega especificada. Por exemplo, para entregar o ativo criptografado com chave de criptografia de envelope de AES (Criptografia Avançada Padrão), defina o tipo de política como **DynamicEnvelopeEncryption**. Para remover a criptografia de armazenamento e transmitir o ativo não criptografado, defina o tipo de política como **NoDynamicEncryption**. Seguem exemplos que mostram como configurar esses tipos de política.

Dependendo de como você configura a política de entrega de ativos, você será capaz de empacotar dinamicamente, criptografar dinamicamente e transmitir os seguintes protocolos de streaming: Smooth Streaming, HLS, MPEG DASH e fluxos HDS.

A lista a seguir mostra os formatos que você usa para transmitir Smooth, HLS, DASH e HDS.

Smooth Streaming:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf) 

HDS

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

Para obter instruções sobre como publicar um ativo e criar uma URL de streaming, consulte [Criar uma URL de streaming](media-services-deliver-streaming-content.md).

##Considerações

- Você não pode excluir um AssetDeliveryPolicy associado a um ativo enquanto um localizador OnDemand (streaming) existir para esse ativo. A recomendação é remover a política do ativo antes de excluir a política.
- Não é possível criar um localizador de streaming em um ativo criptografado para armazenamento quando nenhuma política de entrega de ativo estiver definida. Se o Ativo não estiver criptografado para armazenamento, o sistema permitirá que você crie um localizador e transmita o ativo sem uma política de entrega de ativos.
- Você pode ter várias políticas de entrega de ativos associadas a um único ativo, mas pode especificar apenas uma maneira de lidar com um determinado AssetDeliveryProtocol. Isso significa que se você tentar vincular duas políticas de entrega que especificam o protocolo AssetDeliveryProtocol.SmoothStreaming, o resultado será um erro, pois o sistema não sabe qual delas você desejará aplicar quando um cliente fizer uma solicitação do Smooth Streaming.  
- Se você tiver um ativo com um localizador de streaming existente, não será possível vincular uma nova política ao ativo (você pode desvincular uma política existente do ativo ou atualizar uma política de entrega associada ao ativo). Primeiramente, você precisa remover o localizador de streaming, ajustar as políticas e recriar o localizador de streaming. Você pode usar o mesmo locatorId quando recriar o localizador de streaming, mas certifique-se de que isso não causará problemas para os clientes, uma vez que o conteúdo pode ser armazenado em cache pela CDN de origem ou downstream.  


##Política de entrega de ativos clara 

O seguinte método **ConfigureClearAssetDeliveryPolicy** especifica para não aplicar criptografia dinâmica e entregar o fluxo em qualquer um dos seguintes protocolos: MPEG DASH, HLS e Smooth Streaming. Você talvez queira aplicar essa política para seus ativos de armazenamento criptografados.
  
Para obter informações sobre os valores que você pode especificar ao criar um AssetDeliveryPolicy, consulte a seção [Tipos usados ao definir AssetDeliveryPolicy](#types).

    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
            _context.AssetDeliveryPolicies.Create("Clear Policy",
            AssetDeliveryPolicyType.NoDynamicEncryption, 
            AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);

        asset.DeliveryPolicies.Add(policy);
    }

##Política de entrega de ativos DynamicCommonEncryption 


O método **CreateAssetDeliveryPolicy** a seguir cria o **AssetDeliveryPolicy**, que é configurado para aplicar a criptografia comum dinâmica (**DynamicCommonEncryption**) a um protocolo de streaming suave (outros protocolos de streaming serão bloqueados no streaming). O método utiliza dois parâmetros: **Ativo** (o ativo ao qual você deseja aplicar a política de entrega) e **IContentKey** (a chave de conteúdo do tipo **CommonEncryption**. Para obter mais informações, consulte: [Criando uma chave de conteúdo](media-services-dotnet-create-contentkey.md#common_contentkey)).

Para obter informações sobre os valores que você pode especificar ao criar um AssetDeliveryPolicy, consulte a seção [Tipos usados ao definir AssetDeliveryPolicy](#types).


    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.SmoothStreaming,
            assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " +
            assetDeliveryPolicy.AssetDeliveryPolicyType);
    }

Os Serviços de Mídia do Azure também permitem que você adicione criptografia Widevine. O exemplo a seguir demonstra o PlayReady e o Widevine sendo adicionados à política de fornecimento de ativos.


    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
    	// Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
        
        // Build the Widevine license service URL.
        Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
        UriBuilder uriBuilder = new UriBuilder(widevineUrl);
        uriBuilder.Query = String.Empty;
        widevineUrl = uriBuilder.Uri;

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }

>[AZURE.NOTE]Ao criptografar com Widevine, você só seria capaz de fornecer usando um DASH. Certifique-se de especificar DASH no protocolo de fornecimento de ativos.


##Política de entrega de ativos DynamicEnvelopeEncryption 

O método **CreateAssetDeliveryPolicy** a seguir cria o **AssetDeliveryPolicy** que é configurado para aplicar a criptografia de envelope dinâmico (**DynamicEnvelopeEncryption**) para protocolos HLS e DASH (outros protocolos serão bloqueados no streaming). O método utiliza dois parâmetros: **Ativo** (o ativo ao qual você deseja aplicar a política de entrega) e **IContentKey** (a chave de conteúdo do tipo **EnvelopeEncryption**. Para obter mais informações, consulte: [Criando uma chave de conteúdo](media-services-dotnet-create-contentkey.md#envelope_contentkey)).


Para obter informações sobre os valores que você pode especificar ao criar um AssetDeliveryPolicy, consulte a seção [Tipos usados ao definir AssetDeliveryPolicy](#types).

    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        
        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamice Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }


##<a id="types"></a>Tipos usados ao definir AssetDeliveryPolicy

###<a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol 

    /// <summary>
    /// Delivery protocol for an asset delivery policy.
    /// </summary>
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        /// <summary>
        /// Adobe HTTP Dynamic Streaming (HDS)
        /// </summary>
        Hds = 0x8,

        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

###<a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

    /// <summary>
    /// Policy type for dynamic encryption of assets.
    /// </summary>
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    /// <summary>
    /// Delivery method of the content key to the client.
    /// </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        /// </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        /// </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        /// </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }

###<a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

    /// <summary>
    /// Keys used to get specific configuration for an asset delivery policy.
    /// </summary>
    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,
        
        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }

##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0316_2016-->