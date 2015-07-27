<properties 
   pageTitle="Tipos de declaração e token com suporte"
   description="Um guia para compreender e avaliar as declarações em tokens SAML 2.0 e JSON Web Tokens (JWT) emitidos pelo AAD (Active Directory do Azure)"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   services="active-directory" 
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/01/2015"
   ms.author="mbaldwin"/>

# Tipos de declaração e token com suporte

Este tópico foi criado para ajudá-lo a compreender e avaliar as declarações em tokens SAML 2.0 e JSON Web Tokens (JWT) que o Active Directory do Azure (AD do Azure) emite.

O tópico começa com uma descrição de cada declaração de token e mostra um exemplo da declaração em um token SAML e um token JWT, conforme apropriado. Declarações que estão no status de visualização são listadas separadamente. Ele termina com tokens de exemplo para que você possa ver as declarações no contexto.

O Azure adiciona declarações aos tokens ao longo do tempo para habilitar novos cenários. Normalmente, introduzimos essas declarações no status de visualização e, em seguida, convertemo-nas para suporte completo após um período de teste. Para se prepararem para alterações nas declarações, aplicativos que aceitam tokens do AD do Azure devem ignorar declarações de token não familiares de modo que as novas declarações não interrompam o aplicativo. Os aplicativos que usam declarações que estão no status de visualização não devem depender dessas declarações, e não devem gerar exceções se a declaração não aparecer no token. Se seu aplicativo precisa de declarações que não estão disponíveis nos tokens SAML ou JWT que o AD do Azure emite, use a seção “Contribuições da comunidade” na parte inferior desta página para sugerir e discutir novos tipos de declaração.

## Referência de declarações de token

Esta seção lista e descreve as declarações em tokens retornados pelo AD do Azure. Ele inclui as versões SAML e JWT da declaração e uma descrição da declaração e do seu uso. As declarações são listadas em ordem alfabética.

### ID do aplicativo

A declaração de ID do aplicativo identifica o aplicativo que está usando o token para acessar um recurso. O aplicativo pode agir como ele próprio ou em nome de um usuário. A ID do aplicativo normalmente representa um objeto de aplicativo, mas também pode representar um objeto de entidade de serviço no AD do Azure.

O AD do Azure não dá suporte a uma declaração de ID do aplicativo em um token SAML.

Em um token JWT, a ID do aplicativo aparece em uma declaração appidacr.

    "appid":"15CB020F-3984-482A-864D-1D92265E8268"

### Público-alvo
O público-alvo de um token é o destinatário pretendido desse token. O aplicativo que recebe o token deve verificar se o valor de público-alvo está correto e rejeitar quaisquer tokens destinados a um público-alvo diferente.

O valor de público-alvo é uma cadeia de caracteres – normalmente, o endereço básico do recurso que está sendo acessado, como "https://contoso.com". Em tokens do AD do Azure, o público-alvo é o URI de ID do aplicativo daquele aplicativo que solicitou o token. Quando o aplicativo (que é o público-alvo) tiver mais de um URI de ID do aplicativo, o URI de ID do aplicativo na declaração Público-alvo do token corresponde ao URI de ID do aplicativo na solicitação de token. Em um token SAML, a declaração Público-alvo é definida no elemento Audience do elemento AudienceRestriction.

    <AudienceRestriction>
    <Audience>https://contoso.com</Audience>
    </AudienceRestriction>

Em um token JWT, o público-alvo é exibido em uma declaração aud.

    "aud":"https://contoso.com"

### Referência de classe de contexto de autenticação de aplicativo

A declaração de referência de classe de contexto de autenticação do aplicativo indica como o cliente foi autenticado. Para um cliente público, o valor é 0. Se a ID do cliente e o segredo do cliente são usados, o valor é 1.

Em um token JWT, o valor de referência de classe de contexto de autenticação aparece em uma declaração appidacr (valor ACR específico do aplicativo).

    "appidacr": "0"

### Referência de classe de contexto de autenticação
A declaração de referência de classe de contexto de autenticação indica como o assunto foi autenticado, em vez do cliente na declaração de referência de classe de contexto de autenticação do aplicativo. Um valor "0" indica que a autenticação do usuário final não atendeu aos requisitos da ISO/IEC 29115.

- Em um token JWT, a declaração de referência de classe de contexto de autenticação aparece na declaração acr (valor ACR específico do usuário).

    "acr": "0"

### Instante da autenticação

A declaração Instante da Autenticação registra a data e a hora em que a autenticação ocorreu.

Em um token SAML, o instante de autenticação aparece no atributo AuthnInstant do elemento AuthnStatement. Ele representa uma data e hora em formato de horário UTC (Z).

    <AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">

O AD do Azure não tem uma declaração equivalente em tokens JWT.

### Método de autenticação

A declaração Método de autenticação informa como o assunto do token foi autenticado. Neste exemplo, o provedor de identidade usou uma senha para autenticar o usuário. http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password

Em um token SAML, o valor do método de autenticação é exibido no elemento AuthnContextClassRef.

    <AuthnContextClassRef>http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password</AuthnContextClassRef>

Em um token JWT, o valor do método de autenticação aparece dentro da declaração amr.

    “amr”: ["pwd"]

###Nome

A declaração Nome ou “nome fornecido” fornece o primeiro nome ou nome “fornecido” do usuário, conforme definido no objeto de usuário do AD do Azure.

Um token SAML, o nome (ou "nome fornecido") aparece em uma declaração no elemento givenname do atributo SAML.

    <Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>
    <AttributeValue>Frank<AttributeValue>

Em um token JWT, o nome aparece na declaração nome_fornecido.

    "given_name": "Frank"

### Grupos

A declaração Grupos fornece IDs de objetos que representam as associações de grupo do assunto. Esses valores são exclusivos (consulte a ID de objeto) e podem ser usados com segurança para gerenciar o acesso, como a imposição da autorização para acessar um recurso. Os grupos incluídos na declaração grupos são configurados por aplicativo, por meio da propriedade "groupMembershipClaims" do manifesto do aplicativo. Um valor nulo exclui todos os grupos; já um valor "SecurityGroup" inclui somente os membros do grupo de segurança do Active Directory, enquanto um valor "All" inclui tanto grupos de segurança quanto listas de distribuição do Office 365. Em qualquer configuração, a declaração grupos representa associações de grupo transitivas do assunto.

Em um token SAML, a declaração grupos aparece no atributo grupos.

    <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
    <AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>

Em um token JWT, a declaração grupos aparece na declaração grupos.

    “groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]

### Provedor de identidade

A declaração Provedor de Identidade registra o provedor de identidade que autenticou o assunto do token. Esse valor é idêntico ao valor da declaração Emissor, a menos que a conta de usuário esteja em um locatário diferente que o emissor.

Em um token SAML, o provedor de identidade aparece em uma declaração no elemento identityprovider do atributo SAML.

    <Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>
    <AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>

Em um token JWT, o provedor de identidade aparece em uma declaração idp.

    "idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”

### IssuedAt

A declaração IssuedAt armazena a hora em que o token foi emitido. Ela geralmente é usada para mensurar o quanto o token é recente. Em um token SAML, o valor de IssuedAt aparece na asserção IssueInstant.

    <Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">

Em um token JWT, o valor de IssuedAt aparece na declaração iat. O valor é expresso em número de segundos desde 1970-01-010:0:0Z em UTC (Tempo Universal Coordenado).

    "iat": 1390234181

### Emissor

A declaração do emissor identifica o STS (serviço de token de segurança) que constrói e retorna o token e o locatário de diretório do AD do Azure. Nos tokens que o AD do Azure retorna, o emissor é sts.windows.net. A GUID no valor de declaração do emissor é a ID do locatário do diretório do AD do Azure. A ID do locatário é um identificador imutável e confiável do diretório.

Em um token SAML, a declaração Emissor aparece em um elemento Issuer.

    <Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>

Em um token JWT, o Emissor é exibido em uma declaração iss.

    "iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”

### Sobrenome

A declaração Sobrenome fornece o último nome, sobrenome ou sobrenome da família do usuário conforme definido no objeto de usuário do AD do Azure. Em um token SAML, o último nome aparece em uma declaração no elemento surname do atributo SAML.

    <Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>
    <AttributeValue>Miller<AttributeValue>

Em um token JWT, o último nome aparece na declaração family_name.

    "family_name": "Miller"

### Nome

A declaração Nome fornece um valor legível humano que identifica o assunto do token. Não há garantia de que esse valor seja exclusivo dentro de um locatário e ele é projetado para ser usado apenas para fins de exibição. Em um token SAML, o nome aparece no atributo Name.

    <Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>
    <AttributeValue>frankm@contoso.com<AttributeValue>

Em uma declaração JWT, o nome aparece na declaração unique_name.

    "unique_name": "frankm@contoso.com"

### ID de objeto

A declaração ID de objeto contém um identificador único de um objeto no AD do Azure. Esse valor é imutável e não pode ser reatribuído ou reutilizado, portanto, você pode usá-lo para realizar verificações de autorização com segurança, como por exemplo quando o token é usado para acessar um recurso. Use a ID de objeto para identificar um objeto em consultas ao AD do Azure. Em um token SAML, a ID do objeto aparece no atributo objectidentifier.

    <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
    <AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>

Em um token JWT, a ID de objeto aparece em uma declaração oid.

    "oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"

### Funções

A declaração Funções fornece cadeias de caracteres amigáveis, que representam as atribuições de função de aplicativo do assunto no AD do Azure, podendo também ser usada para impor o controle de acesso baseado em função. As funções de aplicativo são definidas segundo o aplicativo, por meio da propriedade "appRoles" do manifesto do aplicativo. A propriedade "value" de cada função de aplicativo é o valor que aparece na declaração de funções. As funções incluídas na declaração de funções representam todas as funções de aplicativo que a entidade recebeu direta e indiretamente por meio da associação a um grupo. Em um token SAML, a declaração de funções é exibida no atributo roles.

    <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">
    <AttributeValue>Admin</AttributeValue>

Em um token JWT, a declaração de funções é exibida na declaração funções.

    “roles”: ["Admin", … ]

### Escopo

O escopo do token indica as permissões de representação concedidas ao aplicativo cliente. A permissão padrão é user_impersonation. O proprietário do recurso protegido pode registrar valores adicionais no AD do Azure.

Em um token JWT, o escopo do token é especificado em uma declaração scp.

    "scp": "user_impersonation"

### Assunto

O assunto do token é o tema principal sobre o qual o token declara informações, como o usuário de um aplicativo. Esse valor é imutável e não pode ser reatribuído ou reutilizado, então ele pode ser usado para executar verificações de autorização com segurança, como por exemplo quando o token é usado para acessar um recurso. Como o assunto está sempre presente nos tokens emitidos pelo AD do Azure, é recomendável usar esse valor em um sistema de autorização de uso geral.

Em um token SAML, o assunto do token é especificado no elemento NameID do elemento Subject. O NameID é um identificador exclusivo, não reutilizável do assunto, que pode ser um usuário, um aplicativo ou um serviço.

SubjectConfirmation não é uma declaração. Ele descreve como o assunto do token é verificado. "Bearer" indica que o assunto é confirmado pela sua posse do token.

    <Subject>
    <NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>
    <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
    </Subject>

Em um token JWT, o assunto aparece em uma declaração sub.

    "sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"

### ID do locatário
A ID do locatário é um identificador imutável, não reutilizável que identifica o locatário do diretório que emitiu o token. Você pode usar esse valor para acessar os recursos de diretório específicos de um determinado locatário em um aplicativo multilocatário. Por exemplo, você pode usar esse valor para identificar o locatário em uma chamada à Graph API.

Em um token SAML, a ID do locatário aparece em uma declaração no elemento tenantid do atributo SAML.

    <Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>
    <AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>

Em um token JWT, a ID do locatário aparece em uma declaração tid.

    "tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"

### Tempo de vida do token
O Tempo de Vida do Token define o intervalo de tempo pelo qual um token é válido. O serviço que valida o token deve verificar se a data atual está dentro do tempo de tempo de vida do token. Caso contrário, ele deverá rejeitar o token. O serviço pode fornecer uma margem de cinco minutos além do intervalo de tempo de vida do token para levar em conta as diferenças na hora do relógio ("distorção de tempo") entre o AD do Azure e o serviço.

Em um token SAML, a declaração de tempo de vida do token é definida no elemento Conditions usando os atributos NotBefore e NotOnOrAfter.

    <Conditions
    NotBefore="2013-03-18T21:32:51.261Z"
    NotOnOrAfter="2013-03-18T22:32:51.261Z"
    >

Em um token JWT, o tempo de vida do Token é definida pelas solicitações nbf (não antes de) e exp (tempo de expiração). O valor dessas declarações é expresso em número de segundos desde 1970-01-010:0:0Z em UTC (Tempo Universal Coordenado). Para obter mais informações, consulte RFC 3339.

    "nbf":1363289634,
    "exp":1363293234

### Nome UPN
A declaração de Nome UPN armazena o nome de usuário da entidade de usuário.

Em um token JWT, o nome UPN é exibido em uma declaração upn.

    "upn": frankm@contoso.com

### Versão
A declaração Versão armazena o número de versão do token. Em um token JWT, o nome UPN é exibido em uma declaração ver.

    "ver": "1.0"

## Tokens de exemplo

Esta seção exibe exemplos de tokens SAML e JWT retornados pelo AD do Azure. Esses exemplos permitem que você veja as declarações no contexto. Token SAML

Este é um exemplo de um token SAML típico.

	<?xml version="1.0" encoding="UTF-8"?>
	<t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
	  <t:Lifetime>
		<wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
		<wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
	  </t:Lifetime>
	  <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
		<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
		  <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
		</EndpointReference>
	  </wsp:AppliesTo>
	  <t:RequestedSecurityToken>
		<Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
		  <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
		  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
			<ds:SignedInfo>
			  <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
			  <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
			  <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
				<ds:Transforms>
				  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
				  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
				</ds:Transforms>
				<ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
				<ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
			  </ds:Reference>
			</ds:SignedInfo>
			<ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
			<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
			  <X509Data>
				<X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
			  </X509Data>
			</KeyInfo>
		  </ds:Signature>
		  <Subject>
			<NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
			<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
		  </Subject>
		  <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
			<AudienceRestriction>
			  <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
			</AudienceRestriction>
		  </Conditions>
		  <AttributeStatement>
			<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
			  <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
			  <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
			  <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
			  <AttributeValue>Admin</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
			  <AttributeValue>Sample</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
			  <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
			  <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
			  <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
			  <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
			  <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
			  <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
			  <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
			  <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
			  <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
			  <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
			  <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
			  <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
			  <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
			  <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
			</Attribute>
		  </AttributeStatement>
		  <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
			<AuthnContext>
			  <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
			</AuthnContext>
		  </AuthnStatement>
		</Assertion>
	  </t:RequestedSecurityToken>
	  <t:RequestedAttachedReference>
		<SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
		  <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
		</SecurityTokenReference>
	  </t:RequestedAttachedReference>
	  <t:RequestedUnattachedReference>
		<SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
		  <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
		</SecurityTokenReference>
	  </t:RequestedUnattachedReference>
	  <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
	  <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
	  <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
	</t:RequestSecurityTokenResponse>

### Token JWT - representação de usuário

Este é um exemplo de um típico JWT (JSON Web token) usado em um fluxo de Web de representação do usuário. Além de declarações, o token inclui um número de versão em **ver**; inclui também **appidacr**, a referência de classe de contexto de autenticação que indica como o cliente foi autenticado. Para um cliente público, o valor é 0. Se uma ID de cliente ou um segredo do cliente tiver sido usado, o valor será 1.

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

##Consulte também

[Protocolos de autenticação do Active Directory do Azure](https://msdn.microsoft.com/library/azure/dn151124.aspx)
 

<!---HONumber=July15_HO3-->