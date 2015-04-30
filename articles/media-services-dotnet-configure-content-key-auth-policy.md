<properties 
	pageTitle="Criptografia dinâmica:  Configurar política de autorização de chave de conteúdo usando o .NET" 
	description="Saiba como configurar uma política de autorização para uma chave de conteúdo." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="juliako"/>



# Criptografia dinâmica: Configurar a Política de Autorização de Chave de Conteúdo 
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)] 

Este artigo faz parte das séries de [Vídeo de serviços de mídia no fluxo de trabalho sob demanda](media-services-video-on-demand-workflow.md) e [Fluxo de trabalho da transmissão ao vivo dos serviços de mídia](media-services-live-streaming-workflow.md) . 

## Visão geral

Os serviços de mídia do Microsoft Azure permitem distribuir o conteúdo criptografado (dinamicamente) com criptografia AES (padrão avançado) (usando chaves de criptografia de 128 bits) e PlayReady DRM. Os serviços de mídia também fornecem um serviço de distribuição de chaves e licenças do PlayReady aos clientes autorizados. 

No momento, você pode criptografar o seguintes formatos de streaming: HLS, MPEG DASH e Smooth Streaming. Você não pode criptografar o formato de streaming HDSou fazer o download progressivo.

Se você deseja que os serviços de mídia criptografem um ativo, você precisa associar uma chave de criptografia (**CommonEncryption** ou **EnvelopeEncryption**) com o ativo (conforme descrito [aqui](media-services-dotnet-create-contentkey.md)) e também configurar políticas de autorização para a chave (conforme descrito neste artigo). 

Quando um fluxo é solicitado por um player, os serviços de mídia usam a chave especificada para criptografar dinamicamente o conteúdo usando a criptografia AES ou PlayReady. Para descriptografar o fluxo, o player solicitará a chave do serviço de distribuição de chaves. Para decidir se o usuário está autorizado para obter a chave ou não, o serviço avalia as políticas de autorização que você especificou para a chave.

Os serviços de mídia oferecem suporte a várias maneiras de autenticar os usuários que fazem solicitações de chave. A política de autorização de chave de conteúdo pode ter uma ou mais restrições de autorização: **abra**, restrição do **token** ou restrição de **IP**. A política restrita do token deve ser acompanhada por um token emitido por um Secure Token Service (STS). Os Serviços de Mídia dão suporte a tokens no formato **SWT** ([Simple Web Tokens](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) e no formato **JWT](** (JSON Web Token).  

Os serviços de mídia não fornecem Secure Token Services. Você pode criar um STS personalizado ou usar o Microsoft Azure ACS para emitir tokens. O STS deve ser configurado para criar um token assinado com a chave especificada e declarações de emissão que você especificou na configuração de restrição do token (conforme descrito neste artigo). O serviço de distribuição de chaves dos serviços de mídia retornará a chave de criptografia para o cliente se o token for válido e as declarações no token corresponderem aos configurados para a chave de conteúdo.

Para obter mais informações, consulte 

[Autenticação do token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrar o aplicativo do MVC OWIN dos serviços de mídia do Azure com base no aplicativo com Active Directory do Azure e restringir o fornecimento da chave de conteúdo com base em declarações JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Usar o ACS do Azure para emitir tokens](http://mingfeiy.com/acs-with-key-services).

### Algumas considerações se aplicam:

- Para poder usar o empacotamento dinâmico e a criptografia dinâmica, certifique-se de ter pelo menos uma unidade de streaming reservada. Para obter mais informações, consulte [Como dimensionar um serviço de mídia](media-services-manage-origins.md#scale_streaming_endpoints). 
- O ativo deve conter um conjunto de MP4s de taxa de bits adaptável ou arquivos de Smooth Streaming de taxa de bits adaptável. Para obter mais informações, consulte [Codificar um ativo](media-services-encode-asset.md).  
- Carregar e codificar seus ativos usando a opção **AssetCreationOptions.StorageEncrypted**.
- Se você planeja ter várias chaves de conteúdo que exigem a mesma configuração de política, é altamente recomendável criar uma política de autorização única e reutilizá-la com várias chaves de conteúdo.
- O serviço de entrega de chave armazena em cache ContentKeyAuthorizationPolicy e seus objetos relacionados (opções e restrições da política) por 15 minutos.  Se você criar um ContentKeyAuthorizationPolicy e optar por usar uma restrição "Token", testá-lo e, em seguida, atualizar a política de restrição "Aberta", levará aproximadamente 15 minutos antes da política alternar para a versão "Aberta" da política.
- Se você adicionar ou atualizar a política de fornecimento do ativo, você deve excluir um localizador existente (se houver) e criar um novo localizador.


## Criptografia dinâmica AES-128 

### Restrição aberta

A restrição aberta significa que o sistema fornecerá a chave para qualquer pessoa que fizer uma solicitação de chave. Essa restrição pode ser útil para fins de teste.

O exemplo a seguir cria uma política de autorização aberta e o adiciona à chave de conteúdo.
	
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


### Restrição de token

Esta seção descreve como criar uma política de autorização de chave de conteúdo e associá-la com a chave de conteúdo. A política de autorização descreve quais requisitos de autorização devem ser atendidos para determinar se o usuário está autorizado a receber a chave (por exemplo, a lista de "chave de verificação" contém a chave que o token foi assinado).

Para configurar a opção de restrição de token, você precisa usar um XML para descrever os requisitos da autorização do token. O XML de configuração de restrição de token deve estar de acordo com o esquema XML a seguir.

#### <a id="schema"></a>Esquema de restrição de token
	
	<?xml version="1.0" encoding="utf-8"?>
	<xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
	  <xs:complexType name="TokenClaim">
	    <xs:sequence>
	      <xs:element name="ClaimType" nillable="true" type="xs:string" />
	      <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
	  <xs:complexType name="TokenRestrictionTemplate">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
	      <xs:element name="Audience" nillable="true" type="xs:anyURI" />
	      <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
	      <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	      <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
	  <xs:complexType name="ArrayOfTokenVerificationKey">
	    <xs:sequence>
	      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
	  <xs:complexType name="TokenVerificationKey">
	    <xs:sequence />
	  </xs:complexType>
	  <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	  <xs:complexType name="ArrayOfTokenClaim">
	    <xs:sequence>
	      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
	  <xs:complexType name="SymmetricVerificationKey">
	    <xs:complexContent mixed="false">
	      <xs:extension base="tns:TokenVerificationKey">
	        <xs:sequence>
	          <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
	        </xs:sequence>
	      </xs:extension>
	    </xs:complexContent>
	  </xs:complexType>
	  <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
	</xs:schema>

Ao configurar a política restrita do **token**, você deve especificar os parâmetros da** chave de verificação** primária, **emissor** e **audiência**. A **chave de verificação primária **contém a chave que o token foi assinado, o **emissor** é o serviço de token seguro que emite o token. A **audiência** (às vezes chamada de **escopo**) descreve a intenção do token ou o recurso que o token autoriza o acesso. O serviço de distribuição de chaves dos serviços de mídia valida que esses valores no token correspondem aos valores no modelo. 

Ao usar o **SDK dos serviços de mídia para .NET**, você pode usar a classe **TokenRestrictionTemplate** para gerar o token de restrição.
O exemplo a seguir cria uma política de autorização com uma restrição de token. Neste exemplo, o cliente precisa apresentar um token que contém: chave de assinatura (VerificationKey), um emissor de token e declarações necessárias.
	
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
	
	static private string GenerateTokenRequirements()
	{
	    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
	
	    template.PrimaryVerificationKey = new SymmetricVerificationKey();
	    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
	    template.Audience = _sampleAudience;
	    template.Issuer = _sampleIssuer;
	
	    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
	
	    return TokenRestrictionTemplateSerializer.Serialize(template);
	}

#### <a id="test"></a>Token de teste

Para obter um token de teste com base na restrição de token que foi usada para a política de autorização da chave, faça o seguinte:
	
	// Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
	// back into a TokenRestrictionTemplate class instance.
	TokenRestrictionTemplate tokenTemplate =
	    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
	
	// Generate a test token based on the the data in the given TokenRestrictionTemplate.
	// Note, you need to pass the key id Guid because we specified 
	// TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
	Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
	
	//The GenerateTestToken method returns the token without the word "Bearer" in front
	//so you have to add it in front of the token string. 
	string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
	Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
	Console.WriteLine();


## Criptografia dinâmica do PlayReady 

Os serviços de mídia permitem que você configure os direitos e restrições que você deseja para que o tempo de execução do PlayReady DRM imponha quando um usuário está tentando reproduzir conteúdo protegido. 

Ao proteger o conteúdo com PlayReady, uma das coisas que você precisa especificar na sua política de autorização é uma cadeia de caracteres XML que define o [modelo de licença do PlayReady](https://msdn.microsoft.com/library/azure/dn783459.aspx). No SDK dos serviços de mídia para .NET, as classes **PlayReadyLicenseResponseTemplate** e **PlayReadyLicenseTemplate** ajudarão você a definir o modelo de licença do PlayReady. 

### Restrição aberta
	
A restrição aberta significa que o sistema fornecerá a chave para qualquer pessoa que fizer uma solicitação de chave. Essa restrição pode ser útil para fins de teste.

O exemplo a seguir cria uma política de autorização aberta e o adiciona à chave de conteúdo.

	static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
	{
	
	    // Create ContentKeyAuthorizationPolicy with Open restrictions 
	    // and create authorization policy          
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
	    {
	        new ContentKeyAuthorizationPolicyRestriction 
	        { 
	            Name = "Open", 
	            KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
	            Requirements = null
	        }
	    };
	
	    // Configure PlayReady license template.
	    string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
	
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create("",
	            ContentKeyDeliveryType.PlayReadyLicense,
	                restrictions, newLicenseTemplate);
	
	    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
	                ContentKeyAuthorizationPolicies.
	                CreateAsync("Deliver Common Content Key with no restrictions").
	                Result;
	
	
	    contentKeyAuthorizationPolicy.Options.Add(policyOption);
	
	    // Associate the content key authorization policy with the content key.
	    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
	    contentKey = contentKey.UpdateAsync().Result;
	}

### Restrição de token

Para configurar a opção de restrição de token, você precisa usar um XML para descrever os requisitos da autorização do token. A configuração XML de restrição de token deve estar em conformidade com o esquema XML mostrado [nesta](#schema) seção .
	
	public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
	{
	    string tokenTemplateString = GenerateTokenRequirements();
	
	    IContentKeyAuthorizationPolicy policy = _context.
	                            ContentKeyAuthorizationPolicies.
	                            CreateAsync("HLS token restricted authorization policy").Result;
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
	    {
	        new ContentKeyAuthorizationPolicyRestriction 
	        { 
	            Name = "Token Authorization Policy", 
	            KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
	            Requirements = tokenTemplateString, 
	        }
	    };
	
	    // Configure PlayReady license template.
	    string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
	
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
	            ContentKeyDeliveryType.PlayReadyLicense,
	                restrictions, newLicenseTemplate);
	
	    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
	                ContentKeyAuthorizationPolicies.
	                CreateAsync("Deliver Common Content Key with no restrictions").
	                Result;
	            
	    policy.Options.Add(policyOption);
	
	    // Add ContentKeyAutorizationPolicy to ContentKey
	    contentKeyAuthorizationPolicy.Options.Add(policyOption);
	
	    // Associate the content key authorization policy with the content key
	    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
	    contentKey = contentKey.UpdateAsync().Result;
	
	    return tokenTemplateString;
	}
	
	static private string GenerateTokenRequirements()
	{
	
	    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
	
	    template.PrimaryVerificationKey = new SymmetricVerificationKey();
	    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
	    template.Audience = _sampleAudience;
	    template.Issuer = _sampleIssuer;
	
	
	    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
	
	    return TokenRestrictionTemplateSerializer.Serialize(template);
	} 
	
	static private string ConfigurePlayReadyLicenseTemplate()
	{
	    // The following code configures PlayReady License Template using .NET classes
	    // and returns the XML string.
	             
	    PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();
	    PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
	
	    responseTemplate.LicenseTemplates.Add(licenseTemplate);
	
	    return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
	}

Para obter um token de teste com base na restrição de token que foi usada para a política de autorização da chave, consulte [isso](#test) . 

## <a id="types"></a>Tipos usados ao definir ContentKeyAuthorizationPolicy

### <a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    public enum ContentKeyDeliveryType
    {
        None = 0,
        PlayReadyLicense = 1,
        BaselineHttp = 2,
    }

### <a id="TokenType"></a>TokenType

    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }



## Próximas etapas
Agora que você configurou a política de autorização da chave de conteúdo, vá para o tópico [Como configurar a política de entrega de ativos](media-services-dotnet-configure-asset-delivery-policy.md) .

<!--HONumber=52-->