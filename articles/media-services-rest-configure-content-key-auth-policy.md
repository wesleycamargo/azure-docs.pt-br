<properties 
	pageTitle="Configurar política de autorização de chave de conteúdo usando REST" 
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



#Criptografia dinâmica: Configurar a Política de Autorização de Chave de Conteúdo 
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)] 

Este artigo faz parte das séries do [fluxo de trabalho de vídeo por demanda dos Serviços de Mídia](../media-services-video-on-demand-workflow) e [fluxo de trabalho de transmissão ao vivo dos serviços de mídia](../media-services-live-streaming-workflow) . 

##Visão geral

Os serviços de mídia do Microsoft Azure permitem distribuir o conteúdo criptografado (dinamicamente) com criptografia AES (padrão avançado) (usando chaves de criptografia de 128 bits) e PlayReady DRM. Os serviços de mídia também fornecem um serviço de distribuição de chaves e licenças do PlayReady aos clientes autorizados. 

Se você deseja que os serviços de mídia criptografem um ativo, você precisa associar uma chave de criptografia (**CommonEncryption** ou **EnvelopeEncryption**) com o ativo (conforme descrito [aqui](../media-services-rest-create-contentkey/)) e também configurar políticas de autorização para a chave (conforme descrito neste artigo). 

No momento, você pode criptografar o seguintes formatos de streaming: HLS, MPEG DASH e Smooth Streaming. Você não pode criptografar o formato de streaming HDSou fazer o download progressivo.

Quando um fluxo é solicitado por um player, os serviços de mídia usam a chave especificada para criptografar dinamicamente o conteúdo usando a criptografia AES ou PlayReady. Para descriptografar o fluxo, o player solicitará a chave do serviço de distribuição de chaves. Para decidir se o usuário está autorizado para obter a chave ou não, o serviço avalia as políticas de autorização que você especificou para a chave.

Os serviços de mídia oferecem suporte a várias maneiras de autenticar os usuários que fazem solicitações de chave. A política de autorização de chave de conteúdo pode ter uma ou mais restrições de autorização: **abra**, restrição do **token** ou restrição de **IP**. A política restrita do token deve ser acompanhada por um token emitido por um Secure Token Service (STS). Os Serviços de Mídia dão suporte a tokens no formato **Simple Web Tokens** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) e no formato **JSON Web Token** (JWT).  

Os serviços de mídia não fornecem Secure Token Services. Você pode criar um STS personalizado ou usar o Microsoft Azure ACS para emitir tokens. O STS deve ser configurado para criar um token assinado com a chave especificada e declarações de emissão que você especificou na configuração de restrição do token (conforme descrito neste artigo). O serviço de distribuição de chaves dos serviços de mídia retornará a chave de criptografia para o cliente se o token for válido e as declarações no token corresponderem aos configurados para a chave de conteúdo.

Para obter mais informações, consulte 

[Autenticação do token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrar o aplicativo do MVC OWIN dos serviços de mídia do Azure com base no aplicativo com Active Directory do Azure e restringir o fornecimento da chave de conteúdo com base em declarações JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Usar o ACS do Azure para emitir tokens](http://mingfeiy.com/acs-with-key-services).

###Algumas considerações se aplicam:

- Para poder usar o empacotamento dinâmico e a criptografia dinâmica, certifique-se de ter pelo menos uma unidade de escala (também conhecida como unidade de streaming). Para obter mais informações, consulte [Como dimensionar um serviço de mídia](../media-services-manage-origins#scale_streaming_endpoints). 
- O ativo deve conter um conjunto de MP4s de múltiplas taxas de bits ou arquivos de Smooth Streaming de múltiplas taxas de bits. Para obter mais informações, consulte [Codificar um ativo](../media-services-encode-asset/).  
- Carregar e codificar seus ativos usando a opção **AssetCreationOptions.StorageEncrypted**.
- Se você planeja ter várias chaves de conteúdo que exigem a mesma configuração de política, é altamente recomendável criar uma política de autorização única e reutilizá-la com várias chaves de conteúdo.
- O serviço de entrega de chave armazena em cache ContentKeyAuthorizationPolicy e seus objetos relacionados (opções e restrições da política) por 15 minutos.  Se você criar um ContentKeyAuthorizationPolicy e optar por usar uma restrição "Token", testá-lo e, em seguida, atualizar a política de restrição "Aberta", levará aproximadamente 15 minutos antes da política alternar para a versão "Aberta" da política.
- Se você adicionar ou atualizar a política de fornecimento do ativo, você deve excluir um localizador existente (se houver) e criar um novo localizador.


##Criptografia dinâmica AES-128 

>[AZURE.NOTE] Ao trabalhar com a API REST dos serviços de mídia, as seguintes considerações se aplicam:
>
>Ao acessar entidades nos serviços de mídia, você deve definir valores e campos de cabeçalho específicos nas suas solicitações HTTP. Para obter mais informações, consulte [Instalação para desenvolvimento de API REST dos Serviços de Mídia](../media-services-rest-how-to-use).

>Depois de se conectar com êxito a https://media.windows.net, você receberá um redirecionamento 301 especificando outro URI dos serviços de mídia. Você deve fazer chamadas subsequentes para o novo URI conforme descrito em [Conectar aos serviços de mídia usando a API REST](../media-services-rest-connect_programmatically/). 


###Restrição aberta

A restrição aberta significa que o sistema fornecerá a chave para qualquer pessoa que fizer uma solicitação de chave. Essa restrição pode ser útil para fins de teste.

O exemplo a seguir cria uma política de autorização aberta e o adiciona à chave de conteúdo.

####<a id="ContentKeyAuthorizationPolicies"></a>Criar ContentKeyAuthorizationPolicies

Solicitação:
		
	POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423578086&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=lZlyQ2%2bvH73qtJsb42%2fH3xF7r7EvQFR3UXyezuDENFU%3d
	x-ms-version: 2.8
	UserAgent: Azure Media Services .NET SDK v3.1.0.1
	x-ms-client-request-id: d732dbfa-54fc-474c-99d6-9b46a006f389
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 36
	
	{"Name":"Open Authorization Policy"}
	
Resposta:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 211
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3Adb4593da-f4d1-4cc5-a92a-d20eacbabee4')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: d732dbfa-54fc-474c-99d6-9b46a006f389
	request-id: aabfa731-e884-4bf3-8314-492b04747ac4
	x-ms-request-id: aabfa731-e884-4bf3-8314-492b04747ac4
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Tue, 10 Feb 2015 08:25:56 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicies/@Element","Id":"nb:ckpid:UUID:db4593da-f4d1-4cc5-a92a-d20eacbabee4","Name":"Open Authorization Policy"}

####<a id="ContentKeyAuthorizationPolicyOptions"></a>Criar ContentKeyAuthorizationPolicyOptions
	
Solicitação:

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 3.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423580006&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Ref3EsonGF7fUKCwGwGgiMnZitzIzsDOvvMTeVrVVPg%3d
	x-ms-version: 2.8
	UserAgent: Azure Media Services .NET SDK v3.1.0.1
	x-ms-client-request-id: d225e357-e60e-4f42-add8-9d93aba1409a
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 168
	
	{"Name":"policy","KeyDeliveryType":2,"KeyDeliveryConfiguration":"","Restrictions":[{"Name":"HLS Open Authorization Policy","KeyRestrictionType":0,"Requirements":null}]}

Resposta:	
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 349
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A57829b17-1101-4797-919b-f816f4a007b7')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: d225e357-e60e-4f42-add8-9d93aba1409a
	request-id: 81bcad37-295b-431f-972f-b23f2e4172c9
	x-ms-request-id: 81bcad37-295b-431f-972f-b23f2e4172c9
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Tue, 10 Feb 2015 08:56:40 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:57829b17-1101-4797-919b-f816f4a007b7","Name":"policy","KeyDeliveryType":2,"KeyDeliveryConfiguration":"","Restrictions":[{"Name":"HLS Open Authorization Policy","KeyRestrictionType":0,"Requirements":null}]}

####<a id="LinkContentKeyAuthorizationPoliciesWithOptions"></a>Link ContentKeyAuthorizationPolicies com opções

Solicitação:
	
	POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3A0baa438b-8ac2-4c40-a53c-4d4722b78715')/$links/Options HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Content-Type: application/json
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423580006&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Ref3EsonGF7fUKCwGwGgiMnZitzIzsDOvvMTeVrVVPg%3d
	x-ms-version: 2.8
	UserAgent: Azure Media Services .NET SDK v3.1.0.1
	x-ms-client-request-id: 9847f705-f2ca-4e95-a478-8f823dbbaa29
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 154
	
	{"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A57829b17-1101-4797-919b-f816f4a007b7')"}

Resposta:

	HTTP/1.1 204 No Content

####<a id="AddAuthorizationPolicyToKey"></a>Adicionar política de autorização para a chave de conteúdo

Solicitação:

	PUT https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A2e6d36a7-a17c-4e9a-830d-eca23ad1a6f9') HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423581565&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=JiNSG3w6r2C0nIyfKvTZj1uPJGjuitD%2b0sbfZ%2b2JDZI%3d
	x-ms-version: 2.8
	UserAgent: Azure Media Services .NET SDK v3.1.0.1
	x-ms-client-request-id: e613efff-cb6a-41b4-984a-f4f8fb6e76a4
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 78
	
	{"AuthorizationPolicyId":"nb:ckpid:UUID:c06cebb8-c4f0-4d1a-ba00-3273fb2bc3ad"}

Resposta:

	HTTP/1.1 204 No Content

###Restrição de token

Esta seção descreve como criar uma política de autorização de chave de conteúdo e associá-la com a chave de conteúdo. A política de autorização descreve quais requisitos de autorização devem ser atendidos para determinar se o usuário está autorizado a receber a chave (por exemplo, a lista de "chave de verificação" contém a chave que o token foi assinado).

Para configurar a opção de restrição de token, você precisa usar um XML para descrever os requisitos da autorização do token. O XML de configuração de restrição de token deve estar de acordo com o esquema XML a seguir.

####<a id="schema"></a>Esquema de restrição de token
	
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

Ao configurar a política restrita do **token**, você deve especificar os parâmetros da **chave de verificação** primária, **emissor** e **audiência**. A **chave de verificação primária **contém a chave que o token foi assinado, o **emissor** é o serviço de token seguro que emite o token. A **audiência** (às vezes chamada de **escopo**) descreve a intenção do token ou o recurso que o token autoriza o acesso. O serviço de distribuição de chaves dos serviços de mídia valida que esses valores no token correspondem aos valores no modelo. 

O exemplo a seguir cria uma política de autorização com uma restrição de token. Neste exemplo, o cliente precisa apresentar um token que contém: chave de assinatura (VerificationKey), um emissor de token e declarações necessárias.
	
###Criar ContentKeyAuthorizationPolicies

Criar "Diretiva de restrição Token" como mostrado[aqui](#ContentKeyAuthorizationPolicies).


###Criar ContentKeyAuthorizationPolicyOptions

Solicitação:
	
	POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 3.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423580720&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=5LsNu%2b0D4eD3UOP3BviTLDkUjaErdUx0ekJ8402xidQ%3d
	x-ms-version: 2.8
	UserAgent: Azure Media Services .NET SDK v3.1.0.1
	x-ms-client-request-id: 2643d836-bfe7-438e-9ba2-bc6ff28e4a53
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 1079
	
	{"Name":"Token option for HLS","KeyDeliveryType":2,"KeyDeliveryConfiguration":null,"Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>BklyAFiPTQsuJNKriQJBZHYaKM2CkCTDQX2bw9sMYuvEC9sjW0W7GUIBygQL/+POEeUqCYPnmEU2g0o1GW2Oqg==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testacs.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>E5BUHiN4vBdzUzdP0IWaHFMMU3D1uRZgF16TOhSfwwHGSw+Kbf0XqsHzEIYk11M372viB9vbiacsdcQksA0ftw==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}

Resposta:	
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 1260
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3Ae1ef6145-46e8-4ee6-9756-b1cf96328c23')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 2643d836-bfe7-438e-9ba2-bc6ff28e4a53
	request-id: 2310b716-aeaa-421e-913e-3ce2f6f685ca
	x-ms-request-id: 2310b716-aeaa-421e-913e-3ce2f6f685ca
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Tue, 10 Feb 2015 09:10:37 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:e1ef6145-46e8-4ee6-9756-b1cf96328c23","Name":"Token option for HLS","KeyDeliveryType":2,"KeyDeliveryConfiguration":null,"Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>BklyAFiPTQsuJNKriQJBZHYaKM2CkCTDQX2bw9sMYuvEC9sjW0W7GUIBygQL/+POEeUqCYPnmEU2g0o1GW2Oqg==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testacs.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>E5BUHiN4vBdzUzdP0IWaHFMMU3D1uRZgF16TOhSfwwHGSw+Kbf0XqsHzEIYk11M372viB9vbiacsdcQksA0ftw==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}
	
####Link ContentKeyAuthorizationPolicies com opções

Link ContentKeyAuthorizationPolicies com opções como mostrado [aqui](#ContentKeyAuthorizationPolicies).

####Adicionar política de autorização para a chave de conteúdo

Adicionar AuthorizationPolicy para o ContentKey, como mostrado [aqui](#AddAuthorizationPolicyToKey).


##Criptografia dinâmica do PlayReady 

Os serviços de mídia permitem que você configure os direitos e restrições que você deseja para que o tempo de execução do PlayReady DRM imponha quando um usuário está tentando reproduzir conteúdo protegido. 

Ao proteger o conteúdo com PlayReady, uma das coisas que você precisa especificar na sua política de autorização é uma cadeia de caracteres XML que define o [modelo de licença do PlayReady](https://msdn.microsoft.com/library/azure/dn783459.aspx). 

###Restrição aberta
	
A restrição aberta significa que o sistema fornecerá a chave para qualquer pessoa que fizer uma solicitação de chave. Essa restrição pode ser útil para fins de teste.

O exemplo a seguir cria uma política de autorização aberta e o adiciona à chave de conteúdo.
	
####<a id="ContentKeyAuthorizationPolicies2"></a>Criar ContentKeyAuthorizationPolicies

Solicitação:

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423581565&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=JiNSG3w6r2C0nIyfKvTZj1uPJGjuitD%2b0sbfZ%2b2JDZI%3d
	x-ms-version: 2.8
	UserAgent: Azure Media Services .NET SDK v3.1.0.1
	x-ms-client-request-id: 9e7fa407-f84e-43aa-8f05-9790b46e279b
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 58
	
	{"Name":"Deliver Common Content Key"}
	
Resposta:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 233
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3Acc3c64a8-e2fc-4e09-bf60-ac954251a387')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 9e7fa407-f84e-43aa-8f05-9790b46e279b
	request-id: b3d33c1b-a9cb-4120-ac0c-18f64846c147
	x-ms-request-id: b3d33c1b-a9cb-4120-ac0c-18f64846c147
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Tue, 10 Feb 2015 09:26:00 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicies/@Element","Id":"nb:ckpid:UUID:cc3c64a8-e2fc-4e09-bf60-ac954251a387","Name":"Deliver Common Content Key"}
	

#### Criar ContentKeyAuthorizationPolicyOptions

Solicitação:
	
	POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 3.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423581565&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=JiNSG3w6r2C0nIyfKvTZj1uPJGjuitD%2b0sbfZ%2b2JDZI%3d
	x-ms-version: 2.8
	UserAgent: Azure Media Services .NET SDK v3.1.0.1
	x-ms-client-request-id: f160ad25-b457-4bc6-8197-315604c5e585
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 593
	
	{"Name":"","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Open","KeyRestrictionType":0,"Requirements":null}]}
	
Resposta:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 774
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A1052308c-4df7-4fdb-8d21-4d2141fc2be0')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: f160ad25-b457-4bc6-8197-315604c5e585
	request-id: 563f5a42-50a4-4c4a-add8-a833f8364231
	x-ms-request-id: 563f5a42-50a4-4c4a-add8-a833f8364231
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Tue, 10 Feb 2015 09:23:24 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:1052308c-4df7-4fdb-8d21-4d2141fc2be0","Name":"","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Open","KeyRestrictionType":0,"Requirements":null}]}

####Link ContentKeyAuthorizationPolicies com opções

Link ContentKeyAuthorizationPolicies com opções como mostrado [aqui](#ContentKeyAuthorizationPolicies).

####Adicionar política de autorização para a chave de conteúdo

Adicionar AuthorizationPolicy para o ContentKey, como mostrado [aqui](#AddAuthorizationPolicyToKey).


###Restrição de token

Para configurar a opção de restrição de token, você precisa usar um XML para descrever os requisitos da autorização do token. A configuração XML de restrição de token deve estar em conformidade com o esquema XML mostrado [nesta] seção(#schema) .
	
####Criar ContentKeyAuthorizationPolicies
	
Criar ContentKeyAuthorizationPolicies como mostrado[aqui](#ContentKeyAuthorizationPolicies2).

####Criar ContentKeyAuthorizationPolicyOptions
	
Solicitação:

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 3.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423583561&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=5eZnkOsSv%2fLLEKmS%2bWObBlsNYyee8BQlp%2bUYbjugcJg%3d
	x-ms-version: 2.8
	UserAgent: Azure Media Services .NET SDK v3.1.0.1
	x-ms-client-request-id: ab079b0e-2ba9-4cf1-b549-a97bfa6cd2d3
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 1525
	
	{"Name":"Token option","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>w52OyHVqXT8aaupGxuJ3NGt8M6opHDOtx132p4r6q4hLI6ffnLusgEGie1kedUewVoIe1tqDkVE6xsIV7O91KA==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testacs.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>dYwLKIEMBljLeY9VM7vWdlhps31Fbt0XXhqP5VyjQa33bJXleBtkzQ6dF5AtwI9gDcdM2dV2TvYNhCilBKjMCg==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}

Resposta:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 1706
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3Ae42bbeae-de42-4077-90e9-a844f297ef70')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: ab079b0e-2ba9-4cf1-b549-a97bfa6cd2d3
	request-id: ccf8a4ba-731e-4124-8192-079592c251cc
	x-ms-request-id: ccf8a4ba-731e-4124-8192-079592c251cc
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Tue, 10 Feb 2015 09:58:47 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:e42bbeae-de42-4077-90e9-a844f297ef70","Name":"Token option","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>w52OyHVqXT8aaupGxuJ3NGt8M6opHDOtx132p4r6q4hLI6ffnLusgEGie1kedUewVoIe1tqDkVE6xsIV7O91KA==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testacs.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>dYwLKIEMBljLeY9VM7vWdlhps31Fbt0XXhqP5VyjQa33bJXleBtkzQ6dF5AtwI9gDcdM2dV2TvYNhCilBKjMCg==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}

####Link ContentKeyAuthorizationPolicies com opções

Link ContentKeyAuthorizationPolicies com opções como mostrado [aqui](#ContentKeyAuthorizationPolicies).

####Adicionar política de autorização para a chave de conteúdo

Adicionar AuthorizationPolicy para o ContentKey, como mostrado [aqui](#AddAuthorizationPolicyToKey).


##<a id="types"></a>Tipos usados ao definir ContentKeyAuthorizationPolicy

###<a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    public enum ContentKeyDeliveryType
    {
        None = 0,
        PlayReadyLicense = 1,
        BaselineHttp = 2,
    }


##Próximas etapas
Agora que você configurou a política de autorização da chave de conteúdo, vá para o tópico [Como configurar a política de entrega de ativos](../media-services-rest-configure-asset-delivery-policy/) .


<!--HONumber=47-->
