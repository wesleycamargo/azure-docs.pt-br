<properties
	pageTitle="Protocolos do modelo de aplicativo v2.0 | Microsoft Azure"
	description="Os protocolos com suporte da visualização pública do modelo de aplicativo v2.0 do AD do Azure."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# Visualização do modelo de aplicativo v2.0: protocolos — OAuth 2.0 e OpenID Connect

O modelo de aplicativo v2.0 fornece identidade como um serviço para seus aplicativos oferecendo suporte a protocolos padrão do setor, OpenID Connect e OAuth 2.0. Embora o serviço esteja em conformidade padrão, pode haver diferenças sutis entre quaisquer duas implementações desses protocolos. As informações aqui serão úteis se você optar por escrever seu código diretamente enviando e tratando solicitações HTTP, em vez de usar uma das nossas bibliotecas de software livre. <!-- TODO: Need link to libraries above -->

> [AZURE.NOTE]Essas informações se aplicam à visualização pública do modelo de aplicativo v2.0. Para obter instruções sobre como integrar-se ao serviço AD do Azure disponível ao público geral, consulte o [Guia do desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md).

## Tokens
A implementação do modelo de aplicativo v2.0 do OAuth 2.0 e OpenID Connect faz amplo uso de tokens de portador, incluindo os representados como JWTs. Um token de portador é um token de segurança leve que concede ao "portador" acesso a um recurso protegido. Nesse sentido, o "portador" é qualquer parte que possa apresentar o token. Embora uma parte deva primeiro se autenticar no Azure AD para receber o token de portador, se as medidas necessárias não forem tomadas para proteger o token durante a transmissão e o armazenamento, ele pode ser interceptado e usado por uma parte não planejada. Embora alguns tokens de segurança tenham um mecanismo interno para impedir que partes não autorizadas os utilizem, tokens de portador não possuem esse mecanismo e devem ser transportados em um canal seguro, como segurança da camada de transporte (HTTPS). Se um token de portador for transmitido livremente, um ataque por parte de intermediários pode ser usado por uma parte mal-intencionada para adquirir o token e usá-lo para um acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança se aplicam ao armazenar ou manter em cache tokens de portador para uso posterior. Sempre se certifique de que seu aplicativo transmita e armazene tokens de portador de maneira segura. Para obter mais considerações de segurança sobre tokens de portador, consulte [RFC 6750 seção 5](http://tools.ietf.org/html/rfc6750).

Mais detalhes sobre os diferentes tipos de token usados no modelo de aplicativo v2.0 estão disponíveis na [referência ao token do modelo de aplicativo v2.0](active-directory-v2-tokens.md).

## Noções básicas
Cada aplicativo que usa o modelo de aplicativo v2.0 precisará ser registrado em [apps.dev.microsoft.com](https://apps.dev.microsoft.com). O processo de registro de aplicativo vai coletar e atribuir alguns valores para seu aplicativo:

- Uma **Id de aplicativo** que identifica exclusivamente o aplicativo
- Um **URI de Redirecionamento** ou **Identificador de Pacote** que pode ser usado para direcionar as respostas de volta ao aplicativo
- Alguns outros valores específicos de cenário. Para obter mais detalhes, saiba como [registrar um aplicativo](active-directory-v2-app-registration.md).

Depois de registrado, o aplicativo se comunica com o AD do Azure enviando solicitações ao ponto de extremidade v2.0:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Em quase todos os fluxos do OAuth e OpenID Connect, há quatro partes envolvidas na troca:

![Funções do OAuth 2.0](../media/active-directory-v2-flows/protocols_roles.png)

- O **Servidor de Autorização** é o ponto de extremidade v2.0. Ele é responsável por garantir a identidade do usuário, conceder e revogar o acesso a recursos e emitir tokens. Ele também é conhecido como o provedor de identidade; ele trata com segurança tudo que estiver relacionado às informações do usuário, seu acesso e as relações de confiança entre as partes de um fluxo.
- O **Proprietário do Recurso** normalmente é o usuário final. Ele é a parte que possui os dados e tem o poder de permitir que terceiros acessem esses dados, ou recurso.
- O **Cliente OAuth** é o seu aplicativo, identificado pela respectiva Id de Aplicativo. Geralmente é a parte com a qual usuário final interage e solicita tokens do servidor de autorização. O cliente deve receber permissão do proprietário do recurso para acessar o recurso.
- O **Servidor de Recurso** é onde o recurso ou os dados residem. Ele confia no Servidor de Autorização para autenticar e autorizar o Cliente OAuth com segurança, além de usar access\_tokens de portador para garantir que o acesso a um recurso possa ser concedido.

## Fluxo do código de autorização do OAuth2
O fluxo do código de autorização do OAuth 2.0 é descrito na [seção 4.1 da especificação do OAuth 2.0](http://tools.ietf.org/html/rfc6749). Ele é usado para realizar a autenticação e a autorização na maioria dos tipos de aplicativo, incluindo [aplicativos web](active-directory-v2-flows.md#web-apps) e [aplicativos instalados nativamente](active-directory-v2-flows.md#mobile-and-native-apps). Ele permite que aplicativos adquiram access\_tokens com segurança, que podem ser usados para acessar recursos que são protegidos usando o modelo de aplicativo v2.0.

Veja abaixo o fluxo inteiro de um aplicativo nativo; cada solicitação é detalhada nas seções abaixo: ![Fluxo do código de autenticação do OAuth](../media/active-directory-v2-flows/convergence_scenarios_native.png)

#### Solicitar um código de autorização
O fluxo do código de autorização começa com o cliente direcionando o usuário para o ponto de extremidade `/authorize`. Nessa solicitação, o cliente indica as permissões que precisa adquirir do usuário:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=											   // See table below for scope parameter details.
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
&state=12345						 				 // Any value provided by your app
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | obrigatório | A Id de Aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) atribuiu ao aplicativo. |
| response\_type | obrigatório | Deve incluir `code` para o fluxo do código de autorização. |
| redirect\_uri | obrigatório | O redirect\_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos redirect\_uris que você registrou no portal, com exceção de que ele deve ser codificado por url. |
| scope | obrigatório | Uma lista de escopos separados por espaços. Um único valor de escopo indica para o ponto de extremidade v2.0 o recurso e as permissões para esse recurso que está sendo solicitado. Os escopos têm a forma de `<app identifier URI>/<scope value>`. No exemplo acima, o identificador do aplicativo para Graph API do AD do Azure, `https://graph.windows.net`, e duas permissões são solicitadas: `directory.read` e `directory.write`. Para obter uma explicação mais detalhada de escopos, consulte c. |
| response\_mode | recomendável | Especifica o método que deve ser usado para enviar o authorization\_code resultante de volta ao aplicativo. Pode ser um de 'query', 'form\_post' ou 'fragment'.
| state | recomendável | Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente que normalmente é usado para impedir ataques de solicitação intersite forjada. O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| prompt | opcional | Indica o tipo de interação do usuário que é necessário. O único valor válido no momento é 'login', que força o usuário a inserir suas credenciais na solicitação. O logon único não terá efeito. |

Nesse ponto, será solicitado que o usuário insira suas credenciais e conclua a autenticação. O ponto de extremidade v2.0 também garantirá que o usuário tenha consentido as permissões indicadas no parâmetro de consulta `scope`. Se o usuário não tiver consentido nenhuma dessas permissões, ele será solicitado a consentir as permissões necessárias. Os detalhes dos aplicativos quanto a [permissões, consentimento e multilocatário são fornecidos aqui](active-directory-v2-scopes.md).

Depois que o usuário se autentica e dá consentimento, o ponto de extremidade v2.0 retorna uma resposta ao aplicativo no `redirect_uri` indicado usando o método especificado no parâmetro `response_mode`.

Uma resposta bem-sucedida usando `response_mode=query` se parece com esta:

```
GET https://localhost/myapp/?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq... 	// the authorization_code, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  							// the value provided in the request
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| código | O authorization\_code que o aplicativo solicitou. O aplicativo pode usar o código de autorização para solicitar um token de acesso para o recurso de destino. Authorization\_codes têm uma duração bastante curta, geralmente, expirando depois de aproximadamente 10 minutos. |
| session\_state | Um valor exclusivo que identifica a sessão de usuário atual. Esse valor é um GUID, mas deve ser tratado como um valor opaco que é transmitido sem verificação. |
| state | Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| error | Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error\_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

#### Solicitar um token de acesso
Agora que você já adquiriu um authorization\_code e recebeu permissão do usuário, é possível resgatar o `code` de um `access_token` para o recurso desejado enviando uma solicitação `POST` ao ponto de extremidade `/token`:

```
POST common/v2.0/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "2d4d11a2-f814-46a7-890a-274a72a7309e",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
	"client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------------- |
| client\_id | obrigatório | A Id de Aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) atribuiu ao aplicativo. |
| grant\_type | obrigatório | Deve ser `authorization_code` para o fluxo do código de autorização. |
| scope | obrigatório | Uma lista de escopos separados por espaços. Os escopos solicitados nessa ramificação devem ser equivalentes aos escopos solicitados na primeira ramificação, ou um subconjunto desses escopos. Se os escopos especificados nessa solicitação incluírem vários servidores de recursos, o ponto de extremidade v2.0 retornará um token para o recurso especificado no primeiro escopo. Para obter uma explicação mais detalhada de escopos, consulte [permissões, consentimento e escopos](active-directory-v2-scopes.md). |
| código | obrigatório | O authorization\_code que você adquiriu na primeira ramificação do fluxo. |
| client\_secret | obrigatório para aplicativos Web | O segredo do aplicativo que você criou no portal de registro do aplicativo para seu aplicativo. Ele não deve ser usado em um aplicativo nativo, pois client\_secrets não podem ser armazenados de modo confiável em dispositivos. Ele é obrigatório para aplicativos Web e APIs Web, que têm a capacidade de armazenar o client\_secret com segurança no servidor. |

Uma resposta de token bem-sucedida se parecerá com esta:

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": "3600",
	"expires_on": "1388444763",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```
| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| access\_token | O token de acesso solicitado. O aplicativo pode usar esse token para se autenticar no recurso protegido, como uma API Web. |
| token\_type | Indica o valor do tipo de token. O único tipo que oferece suporte ao AD do Azure é Portador |
| expires\_in | Por quanto tempo o token de acesso é válido (em segundos). |
| expires\_on | A hora de expiração do token de acesso. A data é representada como o número de segundos da época. |
| scope | Os escopos para os quais o access\_token é válido. |
| refresh\_token | Um token de atualização do OAuth 2.0. O aplicativo pode usar esse token para adquirir tokens de acesso adicionais depois que o token de acesso atual expira. Os Refresh\_tokens têm longa duração e podem ser usados para reter acesso a recursos por períodos estendidos. Para obter mais detalhes, consulte a [referência ao token v2.0](active-directory-v2-tokens.md). |
| id\_token | Um JWT (Token Web JSON) não assinado. O aplicativo pode decodificar com base64Url os segmentos desse token para solicitar informações sobre o usuário que se conectou. O aplicativo pode armazenar em cache os valores e exibi-los, mas não deve depender deles para qualquer autorização ou limites de segurança. Para obter mais informações sobre id\_tokens, consulte a [referência ao token do modelo de aplicativo v2.0](active-directory-v2-tokens.md). |

As respostas de erro serão parecidas com esta:

```
{
	"error": "access_denied",
	"error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| error | Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error\_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

#### Usar o token de acesso
Agora que você já adquiriu com êxito um `access_token`, você pode usar o token em solicitações para APIs Web incluindo-o no cabeçalho `Authorization`:

```
GET /contoso.onmicrosoft.com/users
Host: https://graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

#### Atualizar o token de acesso
Os access\_tokens têm curta duração e você deve atualizá-los depois que eles expiram para continuar acessando recursos. Faça isso enviando outra solicitação `POST` ao ponto de extremidade `/authorize`, dessa vez fornecendo `refresh_token` em vez de `code`:

```
POST common/v2.0/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "refresh_token",
	"client_id": "2d4d11a2-f814-46a7-890a-274a72a7309e",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | -------- |
| client\_id | obrigatório | A Id de Aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) atribuiu ao aplicativo. |
| grant\_type | obrigatório | Deve ser `refresh_token` para essa ramificação do código de autorização. |
| scope | obrigatório | Uma lista de escopos separados por espaços. Os escopos solicitados nessa ramificação devem ser equivalentes aos escopos solicitados na ramificação de solicitação authorization\_code original, ou um subconjunto desses escopos. Se os escopos especificados nessa solicitação incluírem vários servidores de recursos, o ponto de extremidade v2.0 retornará um token para o recurso especificado no primeiro escopo. Para obter uma explicação mais detalhada de escopos, consulte [permissões, consentimento e escopos](active-directory-v2-scopes.md). |
| refresh\_token | obrigatório | O refresh\_token que você adquiriu na segunda ramificação do fluxo. |
| client\_secret | obrigatório para aplicativos Web | O segredo do aplicativo que você criou no portal de registro do aplicativo para seu aplicativo. Ele não deve ser usado em um aplicativo nativo, pois client\_secrets não podem ser armazenados de modo confiável em dispositivos. Ele é obrigatório para aplicativos Web e APIs Web, que têm a capacidade de armazenar o client\_secret com segurança no servidor. |

Uma resposta de token bem-sucedida se parecerá com esta:

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": "3600",
	"expires_on": "1388444763",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```
| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| access\_token | O token de acesso solicitado. O aplicativo pode usar esse token para se autenticar no recurso protegido, como uma API Web. |
| token\_type | Indica o valor do tipo de token. O único tipo que oferece suporte ao AD do Azure é Portador |
| expires\_in | Por quanto tempo o token de acesso é válido (em segundos). |
| expires\_on | A hora de expiração do token de acesso. A data é representada como o número de segundos da época. |
| scope | Os escopos para os quais o access\_token é válido. |
| refresh\_token | Um novo token de atualização OAuth 2.0. Você deve substituir o token de atualização antigo por esse token de atualização recém-adquirido para garantir que seus tokens de atualização permaneçam válidos pelo máximo tempo possível. |
| id\_token | Um JWT (Token Web JSON) não assinado. O aplicativo pode decodificar com base64Url os segmentos desse token para solicitar informações sobre o usuário que se conectou. O aplicativo pode armazenar em cache os valores e exibi-los, mas não deve depender deles para qualquer autorização ou limites de segurança. Para obter mais informações sobre id\_tokens, consulte a [referência ao token do modelo de aplicativo v2.0](active-directory-v2-tokens.md). |

As respostas de erro serão parecidas com esta:

```
{
	"error": "access_denied",
	"error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| error | Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error\_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |






## Fluxo de conexão do OpenID Connect
O [OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) estende o protocolo de *autorização* do OAuth 2.0 para uso como um protocolo de *autenticação*, o que permite executar o logon único usando o OAuth. Ele apresenta o conceito de um `id_token`, que é um token de segurança que permite ao cliente verificar a identidade do usuário e obter informações básicas de perfil sobre o usuário.

O OpenID Connect para o modelo de aplicativo v2.0 é a maneira recomendada de implementar a conexão para um [aplicativo Web](active-directory-v2-flows.md#web-apps). O fluxo de conexão mais básico contém as seguintes etapas:

![Raias do OpenId Connect](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

#### Enviar a solicitação de conexão
Quando o aplicativo Web precisa autenticar o usuário, ele pode direcionar o usuário para o ponto de extremidade `/authorize`. Essa solicitação é semelhante à primeira ramificação do [Fluxo do código de autorização do OAuth 2.0](#oauth2-authorization-code-flow), com algumas distinções importantes: - A solicitação deve incluir o escopo `openid` no parâmetro `scope`. - O parâmetro `response_type` deve incluir `id_token` - A solicitação deve incluir o parâmetro `nonce`

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=openid										 // Translates to the 'Read your profile' permission
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| client\_id | obrigatório | A Id de Aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) atribuiu ao aplicativo. |
| response\_type | obrigatório | Deve incluir `id_token` para conexão do OpenID Connect. Ele também pode incluir outros response\_types, conforme descrito na seção [OpenID Connect com fluxo de código do OAuth](#OpenID-Connect-with-OAuth-Code-Flow). |
| redirect\_uri | obrigatório | O redirect\_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos redirect\_uris que você registrou no portal, com exceção de que ele deve ser codificado por url. |
| scope | obrigatório | Uma lista de escopos separados por espaços. Para o OpenID Connect, ele deve incluir o escopo `openid`, que é traduzido para a permissão "Conectar e ler seu perfil" na interface de usuário de consentimento. Você também pode incluir outros escopos nessa solicitação de consentimento, conforme descrito na seção [OpenID Connect com fluxo de código do OAuth](#OpenID-Connect-with-OAuth-Code-Flow). |
| nonce | obrigatório | Um valor incluído na solicitação, gerado pelo aplicativo, que será incluído no id\_token resultante como uma declaração. O aplicativo pode verificar esse valor para reduzir os ataques de reprodução de token. Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. |
| response\_mode | recomendável | Especifica o método que deve ser usado para enviar o authorization\_code resultante de volta ao aplicativo. Pode ser um de 'query', 'form\_post' ou 'fragment'.  
| state | recomendável | Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente que normalmente é usado para impedir ataques de solicitação intersite forjada. O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| prompt | opcional | Indica o tipo de interação do usuário que é necessário. O único valor válido no momento é 'login', que força o usuário a inserir suas credenciais na solicitação. O logon único não terá efeito. |

Nesse ponto, será solicitado que o usuário insira suas credenciais e conclua a autenticação. O ponto de extremidade v2.0 também garantirá que o usuário tenha consentido as permissões indicadas no parâmetro de consulta `scope`. Se o usuário não tiver consentido nenhuma dessas permissões, ele será solicitado a consentir as permissões necessárias. Os detalhes dos aplicativos quanto a [permissões, consentimento e multilocatário são fornecidos aqui](active-directory-v2-scopes.md).

Depois que o usuário se autentica e dá consentimento, o ponto de extremidade v2.0 retorna uma resposta ao aplicativo no `redirect_uri` indicado usando o método especificado no parâmetro `response_mode`.

Uma resposta bem-sucedida usando `response_mode=query` se parece com esta:

```
GET https://localhost/myapp/?
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the id_token, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request

```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| id\_token | O id\_token que o aplicativo solicitou. Você pode usar o id\_token para verificar a identidade do usuário e iniciar uma sessão com o usuário. Mais detalhes sobre id\_tokens e respectivo conteúdo estão incluídos na [referência ao token do ponto de extremidade v2.0](active-directory-v2-tokens.md). |
| session\_state | Um valor exclusivo que identifica a sessão de usuário atual. Esse valor é um GUID, mas deve ser tratado como um valor opaco que é transmitido sem verificação. |
| state | Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| error | Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error\_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

#### Validar o id\_token
Apenas receber o id\_token não é suficiente para autenticar o usuário; você deve validar a assinatura do id\_token e verificar as declarações no token de acordo com os requisitos do aplicativo. O ponto de extremidade v2.0 usa [JWTs (Tokens Web JSON)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar tokens e verificar se eles são válidos.

O modelo de aplicativo v2.0 tem um ponto de extremidade de metadados do OpenID Connect, que permite a um aplicativo buscar informações sobre o modelo de aplicativo v2.0 no tempo de execução. Essas informações incluem pontos de extremidade, conteúdos de token e chaves de assinatura de token. O ponto de extremidade de metadados contém um documento JSON localizado em:

`https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`

Uma das propriedades desse documento de configuração é o `jwks_uri`, cujo valor para o modelo de aplicativo v2.0 será:

`https://login.microsoftonline.com/common/discovery/v2.0/keys`.

Você pode usar as chaves públicas RSA256 localizadas nesse ponto de extremidade para validar a assinatura do id\_token. Há várias chaves listadas nesse ponto de extremidade em qualquer ponto no tempo, cada uma identificada por um `kid`. O cabeçalho do id\_token também contém uma declaração `kid`, que indica quais dessas chaves foi usada para assinar o id\_token.

Consulte a [referência ao token do modelo de aplicativo v2.0](active-directory-v2-tokens.md) para obter mais informações, incluindo [Validando tokens](active-directory-v2-tokens.md#validating-tokens) e [Informações importantes sobre substituição de chave de assinatura](active-directory-v2-tokens.md#validating-tokens). <!--TODO: Improve the information on this-->

Depois que tiver validado a assinatura do id\_token, haverá algumas declarações que você precisará verificar:

- Você deve validar a declaração `nonce` para evitar ataques de reprodução de token. Seu valor deve ser o que você especificou na solicitação de conexão.
- Você deve validar a declaração `aud` para garantir que o id\_token foi emitido pelo seu aplicativo. Seu valor deve ser o `client_id` do seu aplicativo.
- Você deve validar as declarações `iat` e `exp` para garantir que o id\_token não expirou.

Talvez você também queira validar declarações adicionais, dependendo do cenário. Algumas validações comuns incluem:

- Garantir que o usuário/organização tenha assinado para usar o aplicativo.
- Garantir que o usuário tenha autorização/privilégios adequados.
- Garantir que uma determinada intensidade de autenticação tenha ocorrido, como autenticação multifator.

Para obter mais informações sobre declarações em um id\_token, consulte a [referência ao token do modelo de aplicativo v2.0](active-directory-v2-tokens.md).

Depois de ter validado completamente o id\_token, você poderá iniciar uma sessão com o usuário e usar declarações no id\_token para obter informações sobre o usuário no seu aplicativo. Essas informações podem ser usadas para exibição, registros, autorizações, etc.

#### Enviar uma solicitação de desconexão

Atualmente, o `end_session_endpoint` do OpenIdConnect não tem suporte na visualização do modelo de aplicativo v2.0. Isso significa que o aplicativo não pode enviar uma solicitação ao ponto de extremidade v2.0 para encerrar uma sessão do usuário e limpar os cookies definidos pelo ponto de extremidade v2.0. Para desconectar um usuário, o aplicativo pode simplesmente encerrar sua própria sessão com o usuário e deixar a sessão do usuário com o ponto de extremidade v2.0 intacta. Na próxima vez que o usuário tentar se conectar, ele verá uma página "escolher conta", com as suas contas ativamente conectadas listadas. Nessa página, o usuário pode optar por se desconectar de qualquer conta, encerrando a sessão com o ponto de extremidade v2.0.

<!--

When you wish to sign the user out of the  app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->

## OpenID Connect com fluxo de código do OAuth
Muitos aplicativos Web precisam conectar o usuário e acessar um serviço Web em nome desse usuário usando o OAuth. Esse cenário combina as duas seções acima — ele usa o OpenID Connect para autenticação de usuário enquanto adquire simultaneamente um authorization\_code que pode ser usado para obter access\_tokens usando o Fluxo do código de autorização do OAuth:

![Raias do OpenId Connect](../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

Esse fluxo difere apenas ligeiramente das seções acima, em como você envia a solicitação de conexão.

#### Enviar a solicitação de conexão
Quando o aplicativo Web precisa autenticar o usuário e obter as permissões necessárias para acessar recursos, ele pode direcionar o usuário para o ponto de extremidade `/authorize`. Nesse caso, o aplicativo deve solicitar um `id_token` e um`code` na resposta:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=openid%20										 // Include both 'openid' and scopes your app needs  
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | ----------------- |
| client\_id | obrigatório | A Id de Aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) atribuiu ao aplicativo. |
| response\_type | obrigatório | Deve incluir `id_token` e `code` para esse cenário. |
| redirect\_uri | obrigatório | O redirect\_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos redirect\_uris que você registrou no portal, com exceção de que ele deve ser codificado por url. |
| scope | obrigatório | Uma lista de escopos separados por espaços. Para o OpenID Connect, ele deve incluir o escopo `openid`, que é traduzido para a permissão "Conectar e ler seu perfil" na interface de usuário de consentimento. Você também deve incluir escopos para recursos aos quais o aplicativo exige acesso. Para obter uma explicação mais detalhada de escopos, consulte [permissões, consentimento e escopos](active-directory-v2-scopes.md). |
| nonce | obrigatório | Um valor incluído na solicitação, gerado pelo aplicativo, que será incluído no id\_token resultante como uma declaração. O aplicativo pode verificar esse valor para reduzir os ataques de reprodução de token. Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. |
| response\_mode | recomendável | Especifica o método que deve ser usado para enviar o authorization\_code resultante de volta ao aplicativo. Pode ser um de 'query', 'form\_post' ou 'fragment'.  
| state | recomendável | Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente que normalmente é usado para impedir ataques de solicitação intersite forjada. O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| prompt | opcional | Indica o tipo de interação do usuário que é necessário. O único valor válido no momento é 'login', que força o usuário a inserir suas credenciais na solicitação. O logon único não terá efeito. |

Nesse ponto, será solicitado que o usuário insira suas credenciais e conclua a autenticação. O ponto de extremidade v2.0 também garantirá que o usuário tenha consentido as permissões indicadas no parâmetro de consulta `scope`. Se o usuário não tiver consentido nenhuma dessas permissões, ele será solicitado a consentir as permissões necessárias. Os detalhes dos aplicativos quanto a [permissões, consentimento e multilocatário são fornecidos aqui](active-directory-v2-scopes.md).

Depois que o usuário se autentica e dá consentimento, o ponto de extremidade v2.0 retorna uma resposta ao aplicativo no `redirect_uri` indicado usando o método especificado no parâmetro `response_mode`.

Uma resposta bem-sucedida usando `response_mode=query` se parece com esta:

```
GET https://localhost/myapp/?
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the id_token, truncated
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq... 	// the authorization_code, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request

```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| id\_token | O id\_token que o aplicativo solicitou. Você pode usar o id\_token para verificar a identidade do usuário e iniciar uma sessão com o usuário. Mais detalhes sobre id\_tokens e respectivo conteúdo estão incluídos na [referência ao token do modelo de aplicativo v2.0](active-directory-v2-tokens.md). |
| código | O authorization\_code que o aplicativo solicitou. O aplicativo pode usar o código de autorização para solicitar um token de acesso para o recurso de destino. Authorization\_codes têm uma duração bastante curta, geralmente, expirando depois de aproximadamente 10 minutos. |
| session\_state | Um valor exclusivo que identifica a sessão de usuário atual. Esse valor é um GUID, mas deve ser tratado como um valor opaco que é transmitido sem verificação. |
| state | Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| error | Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error\_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

#### Validar o id\_token
Esse processo é exatamente igual, conforme descrito acima no [Fluxo de conexão do OpenID Connect](#OpenID-Connect-Sign-In-Flow).

#### Enviar uma solicitação de desconexão
Esse processo é exatamente igual, conforme descrito acima no [Fluxo de conexão do OpenID Connect](#OpenID-Connect-Sign-In-Flow).

#### Solicitar um token de acesso
Esse processo é exatamente igual, conforme descrito no [Fluxo do código de autorização do OAuth 2.0](#oauth2-authorization-code-flow).

#### Usar o token de acesso
Esse processo é exatamente igual, conforme descrito no [Fluxo do código de autorização do OAuth 2.0](#oauth2-authorization-code-flow).

#### Atualizar o token de acesso
Esse processo é exatamente igual, conforme descrito no [Fluxo do código de autorização do OAuth 2.0](#oauth2-authorization-code-flow).





## Fluxo de concessão de credenciais do Cliente OAuth2
A concessão de credenciais do Cliente OAuth 2.0 é descrita na [Especificação do OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.4). É útil para processos de longa execução e outros cenários de servidor, onde o aplicativo pode se autenticar em um recurso usando a própria "Identidade de Aplicativo", em vez de uma identidade delegada do usuário.

Atualmente, esse fluxo não tem suporte da visualização do modelo de aplicativo v2.0. Para ver como ele funciona no serviço AD do Azure disponível para o público em geral, consulte [este exemplo de código do AD do Azure](https://github/com/AzureADSamples/Daemon-DotNet).

## Fluxo Implícito do OAuth2
O Fluxo Implícito do OAuth 2.0 é descrito na [Especificação do OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.2). Ele é usado frequentemente para aplicativos javascript/de única página que são executados em um navegador.

Atualmente, esse fluxo não tem suporte da visualização do modelo de aplicativo v2.0. Para ver como ele funciona no serviço AD do Azure disponível para o público em geral, consulte [este exemplo de código do AD do Azure](active-directory-devquickstarts-angular.md).

## Concessão de credenciais de senha ao proprietário do recurso OAuth2
A concessão de credenciais de senha ao proprietário do recurso OAuth 2.0 é descrita na [Especificação do OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.3). Ela permite que um aplicativo adquira access\_tokens fornecendo o nome de usuário e a senha de um usuário em uma solicitação de token.

Atualmente, esse fluxo não tem suporte da visualização do modelo de aplicativo v2.0. Para ver como ela funciona no serviço AD do Azure disponível para o público em geral, consulte [este exemplo de código do AD do Azure](https://github.com/AzureADSamples/NativeClient-Headless-DotNet).

## OAuth2 em Nome do Fluxo
O OAuth em Nome do Fluxo, ou concessão de credenciais portadoras do JWT, é descrito neste [Rascunho de concessão de extensão do OAuth 2.0](https://tools.ietf.org/html/draft-ietf-oauth-jwt-bearer-12). Ele permite que uma API Web que recebe um access\_token use esse access\_token como uma credencial para adquirir access\_tokens para outros recursos. Isso permite que uma API Web chame com segurança outra API Web de downstream.

Atualmente, esse fluxo não tem suporte da visualização do modelo de aplicativo v2.0. Para ver como ele funciona no serviço AD do Azure disponível para o público em geral, consulte [este exemplo de código do AD do Azure](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

<!---HONumber=Sept15_HO4-->