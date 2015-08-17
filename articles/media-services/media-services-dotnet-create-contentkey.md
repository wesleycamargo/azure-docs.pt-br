<properties 
	pageTitle="Criar ContentKeys com .NET" 
	description="Saiba como criar chaves de conteúdo que fornecem acesso seguro aos ativos." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/15/2015" 
	ms.author="juliako"/>


#Criar ContentKeys com .NET

Este artigo faz parte das séries de [Vídeo de serviços de mídia no fluxo de trabalho sob demanda](media-services-video-on-demand-workflow.md) e [Fluxo de trabalho da transmissão ao vivo dos serviços de mídia](media-services-live-streaming-workflow.md).

Os serviços de mídia permitem que você crie ativos e forneça ativos criptografados. O **ContentKey** fornece acesso seguro aos seus **ativo**s.

Ao criar um novo ativo (por exemplo, antes de [carregar arquivos](media-services-dotnet-upload-files.md)), você pode especificar as seguintes opções de criptografia: **StorageEncrypted**, **CommonEncryptionProtected** ou **EnvelopeEncryptionProtected**.

Quando você fornece ativos para seus clientes, você pode [configurar para que os ativos sejam criptografados](media-services-dotnet-configure-asset-delivery-policy.md) dinamicamente com uma das duas criptografias seguintes: **DynamicEnvelopeEncryption** ou **DynamicCommonEncryption**.

Os ativos criptografados precisam ser associados ao **ContentKey**s. Este artigo descreve como criar uma chave de conteúdo.

>[AZURE.NOTE]Ao criar um novo ativo **StorageEncrypted** usando o SDK do .NET dos Serviços de Mídia, o **ContentKey** é automaticamente criado e vinculado ao ativo.

##ContentKeyType

Um dos valores que você deve definir ao criar um conteúdo da chave é o tipo de chave de conteúdo. Escolha um dos seguintes valores.

    /// <summary>
    /// Specifies the type of a content key.
    /// </summary>
    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for encrypting encoding configuration data that may contain sensitive preset information. 
        /// </summary>
        ConfigurationEncryption = 2,
    }

##<a id="envelope_contentkey"></a>Criar um tipo de envelope de ContentKey

O trecho de código a seguir cria uma chave de conteúdo do tipo de criptografia de envelope. Em seguida, associa a chave com o ativo especificado.

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

        asset.ContentKeys.Add(key);

        return key;
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

chamada

	IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);



##<a id="common_contentkey"></a>Criar um tipo comum de ContentKey    

O trecho de código a seguir cria uma chave de conteúdo do tipo de criptografia comum. Em seguida, associa a chave com o ativo especificado.

    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.CommonEncryption);

        // Associate the key with the asset.
        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int length)
    {
        var returnValue = new byte[length];

        using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
        {
            rng.GetBytes(returnValue);
        }

        return returnValue;
    }
chamada

	IContentKey key = CreateCommonTypeContentKey(encryptedsset); 

<!---HONumber=August15_HO6-->