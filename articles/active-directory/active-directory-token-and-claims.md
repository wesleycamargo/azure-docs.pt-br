 <properties
   pageTitle="Referência de token do Azure AD | Microsoft Azure"
   description="Um guia para compreender e avaliar as declarações em tokens SAML 2.0 e JSON Web Tokens (JWT) emitidos pelo AAD (Active Directory do Azure)"
   documentationCenter="na"
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
   ms.date="07/19/2016"
   ms.author="mbaldwin"/>

# Referência de token do Azure AD

O Azure Active Directory (Azure AD) emite vários tipos de tokens de segurança no processamento de cada fluxo de autenticação. Este documento descreve o formato, as características de segurança e o conteúdo de cada tipo de token.

## Tipos de tokens

O Azure AD dá suporte ao [protocolo de autorização OAuth 2.0](active-directory-protocols-oauth-code.md), que usa access\_tokens e refresh\_tokens. Ele também oferece suporte à autenticação e conexão por meio do [OpenID Connect](active-directory-protocols-openid-connect-code.md), que introduz um terceiro tipo de token, o id\_token. Cada um desses tokens é representado como um "token de portador".

Um token de portador é um token de segurança leve que concede ao "portador" acesso a um recurso protegido. Nesse sentido, o "portador" é qualquer parte que possa apresentar o token. Embora uma parte deva primeiro se autenticar no Azure AD para receber o token de portador, se as medidas necessárias não forem tomadas para proteger o token durante a transmissão e o armazenamento, ele pode ser interceptado e usado por uma parte não planejada. Embora alguns tokens de segurança tenham um mecanismo interno para impedir que partes não autorizadas os utilizem, tokens de portador não possuem esse mecanismo e devem ser transportados em um canal seguro, como segurança da camada de transporte (HTTPS). Se um token de portador for transmitido livremente, um ataque por parte de intermediários pode ser usado por uma parte mal-intencionada para adquirir o token e usá-lo para um acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança se aplicam ao armazenar ou manter em cache tokens de portador para uso posterior. Sempre se certifique de que seu aplicativo transmita e armazene tokens de portador de maneira segura. Para obter mais considerações de segurança sobre tokens de portador, consulte [RFC 6750 seção 5](http://tools.ietf.org/html/rfc6750).

Muitos dos tokens emitidos pelo Azure AD são implementados como Tokens Web JSON, ou JWTs. Um JWT é um meio compacto e protegido por URL de transferir informações entre duas partes. As informações contidas em JWTs são conhecidas como "declarações" ou asserções de informações sobre o portador e o assunto do token. As declarações em JWTs são objetos JSON codificados e serializados para transmissão. Uma vez que os JWTs emitidos pelo Azure AD são assinados, mas não criptografados, você pode inspecionar com facilidade o conteúdo de um JWT para fins de depuração. Há várias ferramentas disponíveis para isso, como [jwt.calebb.net](http://jwt.calebb.net). Para obter mais informações sobre JWTs, você pode consultar a [Especificação do JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## Id\_tokens

Id\_tokens são uma forma de token de segurança de conexão que seu aplicativo recebe ao executar a autenticação usando o [OpenID Connect](active-directory-protocols-openid-connect-code.md). Eles são representados como [JWTs](#types-of-tokens) e contêm declarações que você pode usar para conectar o usuário ao aplicativo. Você pode usar as declarações em um id\_token como julgar conveniente — geralmente elas são usadas para exibir informações de conta ou tomar decisões de controle de acesso em um aplicativo.

Atualmente, Id\_tokens são assinados, mas não criptografados. Quando seu aplicativo recebe um id\_token, ele deve [validar a assinatura](#validating-tokens) para comprovar a autenticidade do token e validar algumas declarações no token para comprovar sua validade. As declarações validadas por um aplicativo variam de acordo com os requisitos do cenário, mas há algumas [validações comuns de declaração](#validating-tokens) que seu aplicativo deve executar em cada cenário.

Os detalhes completos sobre as declarações nos id\_tokens são fornecidos abaixo, bem como o exemplo de id\_token. Observe que as declarações em id\_tokens não são retornadas em uma ordem específica. Além disso, novas declarações podem ser introduzidas nos id\_tokens a qualquer momento — o aplicativo não deve ser interrompido conforme novas declarações são introduzidas. A lista abaixo inclui as declarações que o aplicativo pode interpretar confiavelmente no momento em que este documento foi escrito. Se necessário, mais detalhes podem ser encontrados na [especificação do OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).

#### Exemplo de Id\_Token

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [AZURE.TIP] Para praticar, tente inspecionar as declarações no exemplo de id\_token colando-o em [calebb.net](http://jwt.calebb.net).

#### Declarações em Id\_Tokens

| Declaração JWT | Nome | Descrição |
|-----------|------|-------------|
| `appid`| ID do aplicativo | Identifica o aplicativo que está usando o token para acessar um recurso. O aplicativo pode agir como ele próprio ou em nome de um usuário. A ID do aplicativo normalmente representa um objeto de aplicativo, mas também pode representar um objeto de entidade de serviço no AD do Azure. <br><br> **Valor de exemplo de JWT**: <br> `"appid":"15CB020F-3984-482A-864D-1D92265E8268"` |
| `aud`| Público-alvo | O destinatário pretendido do token. O aplicativo que recebe o token deve verificar se o valor de público-alvo está correto e rejeitar quaisquer tokens destinados a um público-alvo diferente. <br><br> **Valor de exemplo de SAML**: <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **Valor de exemplo de JWT**: <br> `"aud":"https://contoso.com"` |
| `appidacr`| Referência de classe de contexto de autenticação de aplicativo | Indica como o cliente foi autenticado. Para um cliente público, o valor é 0. Se a ID do cliente e o segredo do cliente são usados, o valor é 1. <br><br> **Valor de exemplo de JWT**: <br> `"appidacr": "0"`|
| `acr`| Referência de classe de contexto de autenticação | Indica como o assunto foi autenticado, em oposição ao cliente na declaração de Referência de Classe de Contexto de Autenticação do Aplicativo. Um valor "0" indica que a autenticação do usuário final não atendeu aos requisitos da ISO/IEC 29115. <br><br> **Valor de exemplo de JWT**: <br> `"acr": "0"`|
| | Instante da autenticação | Registra a data e o horário em que ocorreu a autenticação. <br><br> **Valor de exemplo de SAML**: <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` |
| `amr`| Método de autenticação | Identifica como o assunto do token foi autenticado. <br><br> **Valor de exemplo de SAML**: <br> `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **Valor de exemplo de JWT**: `“amr”: ["pwd"]` |
| `given_name`| Nome | Fornece o nome ou nome “determinado” do usuário, como definido no objeto de usuário do Azure AD. <br><br> **Valor de exemplo de SAML**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>`<br><br> **Valor de exemplo de JWT**: <br> `"given_name": "Frank"` |
| `groups`| Grupos | Fornece IDs de objetos que representam as associações de grupo do assunto. Esses valores são exclusivos (consulte a ID de objeto) e podem ser usados com segurança para gerenciar o acesso, como a imposição da autorização para acessar um recurso. Os grupos incluídos na declaração grupos são configurados por aplicativo, por meio da propriedade "groupMembershipClaims" do manifesto do aplicativo. Um valor nulo exclui todos os grupos; já um valor "SecurityGroup" inclui somente os membros do grupo de segurança do Active Directory, enquanto um valor "All" inclui tanto grupos de segurança quanto listas de distribuição do Office 365. <br><br> **Valor de exemplo de SAML**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>`<br><br> **Valor de exemplo de JWT**: <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
| `idp` | Provedor de identidade | Registra o provedor de identidade que autenticou a entidade do token. Esse valor é idêntico ao valor da declaração Emissor, a menos que a conta de usuário esteja em um locatário diferente que o emissor. <br><br> **Valor de exemplo de SAML**: <br> `<Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>`<br><br> **Valor de exemplo de JWT**: <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` | IssuedAt | Armazena a hora em que o token foi emitido. Ela geralmente é usada para mensurar o quanto o token é recente. <br><br> **Valor de exemplo de SAML**: <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **Valor de exemplo de JWT**: <br> `"iat": 1390234181` |
| `iss` | Emissor | Identifica o STS (serviço de token de segurança) que constrói e retorna o token. Nos tokens que o AD do Azure retorna, o emissor é sts.windows.net. A GUID no valor de declaração do emissor é a ID do locatário do diretório do AD do Azure. A ID do locatário é um identificador imutável e confiável do diretório. <br><br> **Valor de exemplo de SAML**: <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **Valor de exemplo de JWT**: <br> `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` | Sobrenome | Fornece o último nome, o sobrenome ou o nome da família do usuário conforme definido no objeto de usuário do Azure AD. <br><br> **Valor de exemplo de SAML**: <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>`<br><br> **Valor de exemplo de JWT**: <br> `"family_name": "Miller"` |
| `unique_name`| Nome | Fornece um valor legível que identifica a entidade do token. Não há garantia de que esse valor seja exclusivo dentro de um locatário e ele é projetado para ser usado apenas para fins de exibição. <br><br> **Valor de exemplo de SAML**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>`<br><br> **Valor de exemplo de JWT**: <br> `"unique_name": "frankm@contoso.com"` |
| `oid` | ID de objeto | Contém um identificador único de um objeto no Azure AD. Esse valor é imutável e não pode ser reatribuído nem reutilizado. Use a ID de objeto para identificar um objeto em consultas ao AD do Azure. <br><br> **Valor de exemplo de SAML**: <br> `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>`<br><br> **Valor de exemplo de JWT**: <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` | Funções | Representa todas as funções de aplicativo que a entidade recebeu direta e indiretamente por meio da associação a um grupo e pode ser usado para impor o controle de acesso baseado em função. As funções de aplicativo são definidas segundo o aplicativo, por meio da propriedade `appRoles` do manifesto do aplicativo. A propriedade `value` de cada função de aplicativo é o valor que aparece na declaração de funções. <br><br> **Valor de exemplo de SAML**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **Valor de exemplo de JWT**: <br> `“roles”: ["Admin", … ]` |
| `scp` | Escopo | Indica as permissões de representação concedidas ao aplicativo cliente. A permissão padrão é `user_impersonation`. O proprietário do recurso protegido pode registrar valores adicionais no AD do Azure. <br><br> **Valor de exemplo de JWT**: <br> `"scp": "user_impersonation"`|
| `sub` |Subject| Identifica o item mais importante sobre o qual o token declara informações, como o usuário de um aplicativo. Esse valor é imutável e não pode ser reatribuído ou reutilizado, então ele pode ser usado para executar verificações de autorização com segurança. Como o assunto está sempre presente nos tokens emitidos pelo Azure AD, é recomendável usar esse valor em um sistema de autorização de uso geral. <br> `SubjectConfirmation` não é uma declaração. Ele descreve como o assunto do token é verificado. `Bearer` indica que o assunto é confirmado pela posse do token. <br><br> **Valor de exemplo de SAML**: <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **Valor de exemplo de JWT**: <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"`|
| `tid` | ID do locatário | Um identificador imutável, não reutilizável que identifica o locatário do diretório que emitiu o token. Você pode usar esse valor para acessar os recursos de diretório específicos de um determinado locatário em um aplicativo multilocatário. Por exemplo, você pode usar esse valor para identificar o locatário em uma chamada à Graph API. <br><br> **Valor de exemplo de SAML**: <br> `<Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **Valor de exemplo de JWT**: <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"`|
| `nbf`, `exp`|Tempo de vida do token | Define o intervalo de tempo no qual um token é válido. O serviço que valida o token deve verificar se a data atual está dentro do tempo de vida do token; se não estiver, ele deverá rejeitar o token. O serviço pode fornecer uma margem de cinco minutos além do intervalo de tempo de vida do token para levar em conta as diferenças na hora do relógio ("distorção de tempo") entre o AD do Azure e o serviço. <br><br> **Valor de exemplo de SAML**: <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **Valor de exemplo de JWT**: <br> `"nbf":1363289634, "exp":1363293234` |
| `upn`| Nome UPN | Armazena o nome de usuário da entidade de usuário.<br><br> **Valor de exemplo de JWT**: <br> `"upn": frankm@contoso.com`|
| `ver`| Versão | Armazena o número de versão do token. <br><br> **Valor de exemplo de JWT**: <br> `"ver": "1.0"`|

## Tokens de acesso

Os tokens de acesso só podem ser consumidos pelos Serviços Microsoft neste momento. Os aplicativos não precisam executar nenhuma validação ou inspeção de tokens de acesso para qualquer um dos cenários que atualmente têm suporte. É possível tratar tokens de acesso como totalmente opacos; eles são apenas cadeias de caracteres que seu aplicativo pode transferir para a Microsoft em solicitações HTTP.

Quando você solicita um token de acesso, o Azure AD também retorna alguns metadados sobre o token de acesso para consumo do aplicativo. Essas informações incluem a data de expiração do token de acesso e os escopos para os quais ele é válido. Isso permite ao aplicativo realizar caching, de modo inteligente, dos tokens de acesso sem precisar analisar abertamente o token de acesso em si.

## Tokens de atualização

Os tokens de atualização são tokens de segurança que o aplicativo pode usar para adquirir novos tokens de acesso em um fluxo do OAuth 2.0. Ele permite ao aplicativo obter acesso a longo prazo a recursos em nome de um usuário sem a necessidade de interação do usuário.

Os tokens de atualização têm vários recursos. Isso quer dizer que um token de atualização recebido durante uma solicitação de token para um recurso pode ser resgatado para tokens de acesso para um recurso totalmente diferente. Para fazer isso, defina o parâmetro `resource` na solicitação para o recurso de destino.

Os tokens de atualização são completamente opacos para seu aplicativo. Eles têm longa duração, mas o aplicativo não deve ser escrito para esperar que um token de atualização dure por qualquer período de tempo. Os tokens de atualização podem ser invalidados a qualquer momento por vários motivos. A única maneira de o aplicativo saber se um token de atualização é válido, é tentando resgatá-lo fazendo uma solicitação de token ao ponto de extremidade do Azure AD.

Ao resgatar um token de atualização para um novo token de acesso, você receberá um novo token de atualização na resposta de token. É preciso salvar o token de atualização recentemente emitido, substituindo o que você usou na solicitação. Isso garantirá que seus tokens de atualização permanecem válidos pelo máximo tempo possível.

## Validando tokens

Neste momento, a única validação de token que seus aplicativos cliente devem precisar executar é a validação de id-tokens. Para validar um id\_token, o aplicativo deve validar a assinatura do id\_token e as declarações contidas nele.

Fornecemos bibliotecas e códigos de exemplo que mostram como tratar com facilidade a validação do token. As informações abaixo são fornecidas simplesmente para aqueles que desejam entender o processo subjacente. Também há várias bibliotecas de software livre de terceiros disponíveis para validação de JWT; há pelo menos uma opção para quase todos os idiomas e plataformas. Para saber mais sobre bibliotecas de autenticação e exemplos de código do Azure AD, veja [bibliotecas de autenticação do Azure AD](active-directory-authentication-libraries.md).

#### Validação da assinatura

Um JWT contém três segmentos, que são separados pelo caractere `.`. O primeiro segmento é conhecido como o **cabeçalho**, o segundo como o **corpo** e o terceiro como a **assinatura**. O segmento de assinatura pode ser usado para validar a autenticidade do id\_token, de modo que seu aplicativo possa confiar nele.

Os Id\_Tokens são assinados usando algoritmos de criptografia assimétrica padrões do setor, como RSA 256. O cabeçalho do id\_token contém informações sobre o método de criptografia e a chave usados para assinar o token:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "kriMPdmBvx68skT8-mPAB3BseeA"
}
```

A declaração `alg` indica o algoritmo que foi usado para assinar o token, enquanto a declaração `x5t` indica a chave pública privada que foi usada para assinar o token.

Em qualquer ponto no tempo, o AD do Azure pode assinar um id\_token usando qualquer um de um determinado conjunto de pares de chaves públicas-privadas. O AD do Azure gira o possível conjunto de chaves em intervalos periódicos, de modo que o aplicativo deve ser escrito para tratar essas mudanças de chave automaticamente. Uma frequência razoável para verificar se há atualizações para as chaves públicas usadas pelo Azure AD é a cada 24 horas.

#### Validação das declarações

Quando o aplicativo recebe um id\_token na conexão do usuário, ele também deve fazer algumas verificações nas declarações no id\_token. Elas incluem, mas sem limitação:

  - A declaração **Público-alvo**: para verificar se o id\_token foi destinado a ser dado ao aplicativo.
  - As declarações **Não Antes De** e **Data de Expiração** - para verificar se o id\_token não expirou.
  - A declaração **Emissor** - para verificar se o token foi de fato emitido para o aplicativo pelo AD do Azure.
  - O **Nonce**: como uma redução do ataque de reprodução do token.
  - e mais...

Para obter uma lista completa das validações de declaração que seu aplicativo deve executar, consulte a [especificação do OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Os detalhes dos valores esperados para essas declarações estão incluídos acima na [seção id\_token](#id-tokens).

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

Este é um exemplo de um típico JWT (token Web JSON) usado em um fluxo de concessão de código de autorização. Além de declarações, o token inclui um número de versão em **ver**; inclui também **appidacr**, a referência de classe de contexto de autenticação que indica como o cliente foi autenticado. Para um cliente público, o valor é 0. Se uma ID de cliente ou um segredo do cliente tiver sido usado, o valor será 1.

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

<!---HONumber=AcomDC_0727_2016-->