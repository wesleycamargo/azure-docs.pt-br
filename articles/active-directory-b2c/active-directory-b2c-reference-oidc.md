<properties
	pageTitle="Visualização do Azure AD B2C | Microsoft Azure"
	description="Criando aplicativos Web usando a implementação do Azure AD do protocolo de autenticação OpenID Connect."
	services="active-directory-b2c"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Visualização de Azure AD B2C: conexão na Web com OpenID Connect

O OpenID Connect é um protocolo de autenticação criado com base em OAuth 2.0 que pode ser usado para conectar com segurança os usuários em aplicativos Web. Usando a implementação do Azure AD B2C do OpenID Connect, você pode terceirizar a inscrição, a entrada e outras experiências de gerenciamento de identidade nos seus aplicativos Web para o Azure AD. Este guia mostrará como fazer isso de maneira independente do idioma, descrevendo como enviar e receber mensagens HTTP sem usar qualquer uma das nossas bibliotecas de software livre.

<!-- TODO: Need link to libraries -->

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

O [OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) estende o protocolo de *autorização* do OAuth 2.0 para uso como um protocolo de *autenticação*, o que permite executar o logon único usando o OAuth. Ele apresenta o conceito de um `id_token`, que é um token de segurança que permite ao cliente verificar a identidade do usuário e obter informações básicas de perfil sobre o usuário. Como ele estende o OAuth 2.0, também permite que aplicativos adquiram **access\_tokens** com segurança, os quais podem ser usados para acessar os recursos protegidos por um [servidor de autorização](active-directory-b2c-reference-protocols.md#the-basics). O OpenID Connect é a nossa recomendação se você estiver criando um aplicativo Web que fica hospedado em um servidor e é acessado por meio de um navegador. Se você deseja adicionar o gerenciamento de identidades para seus aplicativos móveis ou para área de trabalho usando o Azure AD B2C, você deve usar [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) em vez de OpenID Connect.

O Azure AD B2C estende o protocolo padrão OpenID Connect para fazer mais do que uma simples ação de autenticação e autorização. Ele apresenta o [**parâmetro de política**](active-directory-b2c-reference-poliices.md), que permite usar o OpenID Connect para adicionar experiências de usuário ao seu aplicativo, como inscrição, entrada e gerenciamento de perfil. Aqui, mostraremos como usar OpenID Connect e políticas para implementar cada uma dessas experiências em seus aplicativos nativos e obter access\_tokens para acessar APIs Web.

As solicitações HTTP de exemplo abaixo usam nosso diretório B2C de exemplo, **fabrikamb2c.onmicrosoft.com**, bem como nosso aplicativo ****https://aadb2cplayground.azurewebsites.net** e políticas de exemplo. Você é livre para experimentar as solicitações sozinho usando esses valores ou os substituindo pelos seus próprios. Saiba como [obter seu próprio diretório B2C, aplicativos e políticas](#use-your-own-b2c-directory).

## Enviar solicitações de autenticação
Quando o aplicativo Web precisa autenticar o usuário e executar uma política, ele pode direcionar o usuário para o ponto de extremidade `/authorize`. Essa é a parte interativa do fluxo, em que o usuário realmente irá executar uma ação, dependendo da política. Nessa solicitação, o cliente indica as permissões que precisa adquirir do usuário no parâmetro `scope` e a política para executar no parâmetro `p`. Três exemplos são fornecidos abaixo (com quebras de linha para facilitar a leitura), cada um com uma política diferente. Para ter uma ideia de como funciona cada solicitação, tente colar a solicitação em um navegador e executá-lo.

#### Usar uma política de entrada

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### Usar uma política de inscrição

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### Usar uma política de edição de perfil

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | obrigatório | A ID do aplicativo que o [portal do Azure](https://portal.azure.com/) atribuiu a seu aplicativo. |
| response\_type | obrigatório | Deve incluir `id_token` para o OpenID Connect. Se seu aplicativo Web também precisar de tokens para chamar uma API Web, você poderá usar `code+id_token`, como fizemos aqui. |
| redirect\_uri | obrigatório | O redirect\_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos redirect\_uris que você registrou no portal, com exceção de que ele deve ser codificado por url. |
| scope | obrigatório | Uma lista de escopos separados por espaços. Um valor de escopo único indica ao Azure AD que ambas as permissões de controle estão sendo solicitadas. O escopo `openid` indica uma permissão para conectar o usuário e obter dados sobre ele na forma de **id\_tokens** (mais sobre isso a seguir). O escopo `offline_access` é opcional para aplicativos Web. Indica que seu aplicativo precisará de um **refresh\_token** para acessar os recursos a longo prazo. |
| response\_mode | recomendável | Especifica o método que deve ser usado para enviar o authorization\_code resultante de volta ao aplicativo. Pode ser um de 'query', 'form\_post' ou 'fragment'. |
| state | recomendável | Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente que normalmente é usado para impedir ataques de solicitação intersite forjada. O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página em que ele estava. |
| nonce | obrigatório | Um valor incluído na solicitação, gerado pelo aplicativo, que será incluído no id\_token resultante como uma declaração. O aplicativo pode verificar esse valor para reduzir os ataques de reprodução de token. Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. |
| p | obrigatório | Indica a política a ser executada. É o nome de uma política criada no seu diretório B2C cujo valor deve começar com "b2c\_1\_". Saiba mais sobre políticas [aqui](active-directory-b2c-reference-policies.md). |
| prompt | opcional | Indica o tipo de interação do usuário que é necessário. O único valor válido no momento é 'login', que força o usuário a inserir suas credenciais na solicitação. O logon único não terá efeito. |

Nesse momento, o usuário será solicitado a concluir o fluxo de trabalho da política. Isso pode exigir que o usuário insira seu nome de usuário e senha, entre com uma identidade social, inscreva-se no diretório ou outras etapas, dependendo de como a política estiver definida. Quando o usuário conclui a política, o Azure AD retornará uma resposta ao seu aplicativo no evento do tipo `redirect_uri`, usando o método especificado no parâmetro `response_mode`. A resposta será exatamente a mesma para cada um dos casos acima, independentemente da política que foi executada.

Uma resposta bem-sucedida usando `response_mode=query` se parece com esta:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| id\_token | O id\_token que o aplicativo solicitou. Você pode usar o id\_token para verificar a identidade do usuário e iniciar uma sessão com o usuário. Mais detalhes sobre id\_tokens e respectivo conteúdo estão incluídos na [referência ao token do Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| código | O authorization\_code que o aplicativo solicitou, se você tiver usado `response_type=code+id_token`. O aplicativo pode usar o código de autorização ao solicitar um token de acesso para o recurso de destino. Authorization\_codes têm uma duração bastante curta, geralmente, expirando depois de aproximadamente 10 minutos. |
| state | Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| error | Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error\_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |
| state | Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |


## Validar o id\_token
Apenas receber o id\_token não é suficiente para autenticar o usuário; você deve validar a assinatura do id\_token e verificar as declarações no token de acordo com os requisitos do aplicativo. O Azure AD B2C usa [JWTs (Tokens Web JSON)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar tokens e verificar se eles são válidos. Há muitas bibliotecas de software livre para validar JWTs dependendo do idioma de preferência. Recomendamos que você explore essas opções em vez de implementar a sua própria lógica de validação. As informações aqui serão úteis para descobrir como usar essas bibliotecas adequadamente.

O Azure AD B2C tem um ponto de extremidade de metadados do OpenID Connect, que permite a um aplicativo buscar informações sobre o Azure AD B2C no tempo de execução. Essas informações incluem pontos de extremidade, conteúdos de token e chaves de assinatura de token. Há um documento de metadados JSON para cada política no seu diretório B2C. Por exemplo, o documento de metadados para a política `b2c_1_sign_in` no `fabrikamb2c.onmicrosoft.com` está localizado em:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Uma das propriedades desse documento de configuração é o `jwks_uri`, cujo valor para a mesma política será:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

A fim de determinar qual política foi usada na assinatura de um id\_token (e onde buscar os metadados), você tem duas opções. Primeiro, o nome da política é incluído na declaração `acr` no id\_token. Para obter informações sobre como analisar as declarações de um id\_token, consulte a [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). A outra opção é codificar a política no valor do parâmetro `state` quando você emitir a solicitação e decodificá-la para determinar qual política foi usada. Qualquer um dos métodos é perfeitamente válido.

Depois que tiver adquirido o documento de metadados do ponto de extremidade de metadados OpenID Connect, você pode usar as chaves públicas RSA256 localizadas nesse ponto de extremidade para validar a assinatura do id\_token. Pode haver várias chaves listadas nesse ponto de extremidade em qualquer ponto no tempo, cada uma identificada por um `kid`. O cabeçalho do id\_token também contém uma declaração `kid`, que indica quais dessas chaves foi usada para assinar o id\_token. Consulte a [referência ao token do Azure AD B2C](active-directory-b2c-reference-tokens.md) para obter mais informações, incluindo [Validando tokens](active-directory-b2c-reference-tokens.md#validating-tokens) e [Informações importantes sobre substituição de chave de assinatura](active-directory-b2c-reference-tokens.md#validating-tokens). <!--TODO: Improve the information on this-->

Depois que tiver validado a assinatura do id\_token, haverá algumas declarações que você precisará verificar:

- Você deve validar a declaração `nonce` para evitar ataques de reprodução de token. Seu valor deve ser o que você especificou na solicitação de conexão.
- Você deve validar a declaração `aud` para garantir que o id\_token foi emitido pelo seu aplicativo. Seu valor deve ser a ID de aplicativo do seu aplicativo.
- Você deve validar as declarações `iat` e `exp` para garantir que o id\_token não expirou.

Talvez você também queira validar declarações adicionais, dependendo do cenário. Algumas validações comuns incluem:

- Garantir que o usuário/organização tenha assinado para usar o aplicativo.
- Garantir que o usuário tenha autorização/privilégios adequados.
- Garantir que uma determinada intensidade de autenticação tenha ocorrido, como autenticação multifator.

Para obter mais informações sobre declarações em um id\_token, consulte a [referência ao token do Azure AD B2C](active-directory-b2c-reference-tokens.md).

Depois de ter validado completamente o id\_token, você poderá iniciar uma sessão com o usuário e usar declarações no id\_token para obter informações sobre o usuário no seu aplicativo. Essas informações podem ser usadas para exibição, registros, autorizações e outros.

## Obter um token
Se tudo o que o seu aplicativo Web precisar fazer for executar políticas, você poderá ignorar as próximas seções. Essas seções são aplicáveis somente aos aplicativos Web que precisam fazer chamadas autenticadas para uma API Web que também seja protegida pelo Azure AD B2C.

Você pode resgatar o authorization\_code adquirido (usando `response_type=code+id_token`) para um token do recurso desejado enviando uma solicitação `POST` ao ponto de extremidade `/token` ponto de extremidade. Na visualização do Azure AD B2C, o único recurso para o qual você pode solicitar um token é a API Web de back-end do seu aplicativo. A convenção usada para solicitar um token para você mesmo deve usar o escopo `openid`:

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
	"scope": "openid offline_access",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob",
	"client_secret": "<your-application-secret>"
}
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------------- |
| p | obrigatório | A política que foi usada para adquirir o código de autorização. Você não poderá usar uma política diferente nessa solicitação. **Observe que esse parâmetro é adicionado à cadeia de caracteres de consulta**, e não ao corpo do POST. |
| client\_id | obrigatório | A ID do aplicativo que o [portal do Azure](https://portal.azure.com/) atribuiu a seu aplicativo. |
| grant\_type | obrigatório | Deve ser `authorization_code` para o fluxo do código de autorização. |
| scope | obrigatório | Uma lista de escopos separados por espaços. Um valor de escopo único indica ao Azure AD que ambas as permissões de controle estão sendo solicitadas. O escopo `openid` indica uma permissão para conectar o usuário e obter dados sobre ele na forma de **id\_tokens**. Ele pode ser usado para obter tokens para a própria API Web de back-end do seu aplicativo, representado pela mesma Id de aplicativo do cliente. O escopo `offline_access` indica que seu aplicativo precisará de um **refresh\_token** para acessar os recursos a longo prazo. |
| código | obrigatório | O authorization\_code que você adquiriu na primeira ramificação do fluxo. |
| redirect\_uri | obrigatório | O redirect\_uri do aplicativo em que você recebeu o authorization\_code. |
| client\_secret | obrigatório | O segredo do aplicativo gerado no [portal do Azure](https://portal.azure.com/). Esse segredo do aplicativo é um artefato de segurança importante e deve ser armazenado com segurança em seu servidor. Você também deve ter cuidado para circular esse segredo do cliente em intervalos periódicos. |

Uma resposta de token bem-sucedida se parecerá com esta:

```
{
	"not_before": "1442340812",
	"token_type": "Bearer",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"scope": "openid offline_access",
	"id_token_expires_in": "3600",
	"profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI3NzU1MjdmZi05YTM3LTQzMDctOGIzZC1jY...",
	"refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
	"refresh_token_expires_in": "1209600"
}
```
| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| not\_before | A hora em que o token é considerado válido, nos valores de hora da época. |
| token\_type | Indica o valor do tipo de token. O único tipo com suporte do Azure AD é Portador |
| id\_token | O token JWT assinado que você solicitou. |
| scope | Os escopos para os quais o token for válido, que podem ser usados no caching de tokens para uso posterior. |
| id\_token\_expires\_in | Por quanto tempo o token de acesso é válido (em segundos). |
| profile\_info | Cadeia de caracteres JSON codificado em base 64 que pode conter informações úteis sobre o usuário para exibição em seu aplicativo nativo. O conteúdo exato dependerá das declarações de aplicativo configuradas na sua política |
| refresh\_token | Um token de atualização do OAuth 2.0. O aplicativo pode usar esse token para adquirir tokens de acesso adicionais depois que o atual expirar. Os Refresh\_tokens têm longa duração e podem ser usados para reter acesso a recursos por períodos estendidos. Para obter mais detalhes, consulte a [referência ao token B2C](active-directory-b2c-reference-tokens.md). Observe que você deve ter usado o escopo `offline_access` tanto na autorização quanto nas solicitações de token para receber um token de atualização. |
| refresh\_token\_expires\_in | O tempo máximo em que token de atualização pode ser válido (em segundos). O token de atualização, no entanto, pode se tornar inválido a qualquer momento. |

> [AZURE.NOTE]
	Se nesse ponto você está pensando: "Onde está o access\_token?", considere o seguinte: Quando você solicitar o escopo `openid`, o Azure AD emitirá um JWT `id_token` na resposta. Embora esse `id_token` não seja tecnicamente um access\_token OAuth 2.0, ele pode ser usado como tal quando se comunicar com o serviço de back-end do aplicativo, representado pelo mesmo client\_id do cliente. O `id_token` ainda é um token de portador JWT assinado que pode ser enviado a um recurso em um cabeçalho de autorização HTTP e usado para autenticar solicitações. A diferença é que um `id_token` não tem um mecanismo para definir o escopo do acesso que um aplicativo cliente possa exigir. No entanto, quando o aplicativo cliente é o único cliente capaz de se comunicar com o serviço de back-end (como é o caso com a visualização atual do Azure AD B2C), não há a necessidade desse mecanismo de escopo. Quando a visualização do Azure AD B2C adicionar a capacidade dos clientes de se comunicar com recursos adicionais internos e de terceiros, os access\_tokens serão introduzidos. No entanto, o uso de `id_tokens` para se comunicar com o back-end de serviço do aplicativo ainda será o padrão recomendado. Para saber mais sobre os tipos de aplicativos que você pode criar com a visualização do Azure AD B2C, consulte [este artigo](active-directory-b2c-apps.md).

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

## Usar o token
Agora que você já adquiriu com êxito um `id_token`, pode usar o token em solicitações para as suas APIs Web de back-end incluindo-o no cabeçalho `Authorization`:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## Atualizar o token
Os access\_tokens têm curta duração e você deve atualizá-los depois que eles expirarem para continuar acessando os recursos. Faça isso enviando outra solicitação `POST` ao ponto de extremidade `/token`, dessa vez fornecendo `refresh_token` em vez de `code`:

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "refresh_token",
	"client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
	"scope": "openid offline_access",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob",
	"client_secret": "<your-application-secret>"
}
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | -------- |
| p | obrigatório | A política que foi usada para adquirir o token de atualização original. Você não poderá usar uma política diferente nessa solicitação. **Observe que esse parâmetro é adicionado à cadeia de caracteres de consulta**, e não ao corpo do POST. |
| client\_id | obrigatório | A ID do aplicativo que o [portal do Azure](https://portal.azure.com/) atribuiu a seu aplicativo. |
| grant\_type | obrigatório | Deve ser `refresh_token` para essa ramificação do código de autorização. |
| scope | obrigatório | Uma lista de escopos separados por espaços. Um valor de escopo único indica ao Azure AD que ambas as permissões de controle estão sendo solicitadas. O escopo `openid` indica uma permissão para conectar o usuário e obter dados sobre ele na forma de **id\_tokens**. Ele pode ser usado para obter tokens para a própria API Web de back-end do seu aplicativo, representado pela mesma Id de aplicativo do cliente. O escopo `offline_access` indica que seu aplicativo precisará de um **refresh\_token** para acessar os recursos a longo prazo. |
| redirect\_uri | obrigatório | O redirect\_uri do aplicativo em que você recebeu o authorization\_code. |
| refresh\_token | obrigatório | O refresh\_token original que você adquiriu na segunda ramificação do fluxo. Observe que você deve ter usado o escopo `offline_access` tanto na autorização quanto nas solicitações de token para receber um token de atualização. |
| client\_secret | obrigatório | O segredo do aplicativo gerado no [portal do Azure](https://portal.azure.com/). Esse segredo do aplicativo é um artefato de segurança importante e deve ser armazenado com segurança em seu servidor. Você também deve ter cuidado para circular esse segredo do cliente em intervalos periódicos. |

Uma resposta de token bem-sucedida se parecerá com esta:

```
{
	"not_before": "1442340812",
	"token_type": "Bearer",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"scope": "openid offline_access",
	"id_token_expires_in": "3600",
	"profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI3NzU1MjdmZi05YTM3LTQzMDctOGIzZC1jY...",
	"refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
	"refresh_token_expires_in": "1209600"
}
```
| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| not\_before | A hora em que o token é considerado válido, nos valores de hora da época. |
| token\_type | Indica o valor do tipo de token. O único tipo com suporte do Azure AD é Portador |
| id\_token | O token JWT assinado que você solicitou. |
| scope | Os escopos para os quais o token for válido, que podem ser usados no caching de tokens para uso posterior. |
| id\_token\_expires\_in | Por quanto tempo o token de acesso é válido (em segundos). |
| profile\_info | Cadeia de caracteres JSON codificado em base 64 que pode conter informações úteis sobre o usuário para exibição em seu aplicativo nativo. O conteúdo exato dependerá das declarações de aplicativo configuradas na sua política |
| refresh\_token | Um token de atualização do OAuth 2.0. O aplicativo pode usar esse token para adquirir tokens de acesso adicionais depois que o atual expirar. Os Refresh\_tokens têm longa duração e podem ser usados para reter acesso a recursos por períodos estendidos. Para obter mais detalhes, consulte a [referência ao token B2C](active-directory-b2c-reference-tokens.md). |
| refresh\_token\_expires\_in | O tempo máximo em que token de atualização pode ser válido (em segundos). O token de atualização, no entanto, pode se tornar inválido a qualquer momento. |

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


<!--

Here is the entire flow for a native  app; each request is detailed in the sections below:

![OAuth Auth Code Flow](./media/active-directory-b2c-reference-oauth-code/convergence_scenarios_native.png)

-->

## Enviar uma solicitação de desconexão

Quando você deseja conectar o usuário do aplicativo, não é suficiente limpar os cookies do aplicativo e encerrar a sessão do usuário. Você também deve redirecionar o usuário ao Azure AD para a desconexão. Se você não conseguir fazer isso, o usuário poderá se autenticar novamente no seu aplicativo sem inserir as credenciais novamente, pois continuará em uma sessão de logon único válida com o Azure AD.

Você pode simplesmente redirecionar o usuário para o `end_session_endpoint` listado no documento de metadados OpenID Connect mesmo descrito [acima](#validate-the-id-token):

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | ------------ |
| p | obrigatório | A política que o usuário usou mais recentemente para entrar em seu aplicativo. |
| post\_logout\_redirect\_uri | recomendável | A URL a qual o usuário deve ser redirecionado após o logout com êxito. Se não estiver incluído, o usuário verá uma mensagem genérica do Azure AD B2C. |

> [AZURE.NOTE]
	Embora o direcionamento do usuário para o `end_session_endpoint` remova o estado de usuários em logon único com o AD do Azure, ele não desconectará o usuário efetivamente. Em vez disso, o usuário selecionará o IDP no qual deseja entrar e será novamente autenticado sem inserir suas credenciais. No caso de IDPs de redes sociais, esse é o comportamento esperado. Se um usuário deseja sair do seu diretório B2C, isso não significa necessariamente que deseja sair inteiramente da sua conta do Facebook. No entanto, no caso de contas locais, deve ser possível encerrar a sessão do usuário corretamente. É uma [limitação](active-directory-b2c-limitations.md) conhecida da visualização do Azure AD que o logout de conta local não funciona corretamente. Uma solução alternativa e imediata é enviar o parâmetro `&prompt=login` em cada solicitação de autenticação, que terá a aparência do comportamento desejado, mas interromperá o logon único entre aplicativos no seu diretório B2C.

## Use seu próprio diretório B2C

Se você quiser experimentar essas solicitações por conta própria, primeiro deve seguir estas três etapas e substituir os valores de exemplo acima com os seus:

- [Criar um diretório B2C](active-directory-b2c-get-started.md) e usar o nome do seu diretório nas solicitações.
- [Criar um aplicativo](active-directory-b2c-app-registration.md) para obter uma Id de aplicativo e um redirect\_uri. Você deve incluir um **aplicativo Web/API Web** em seu aplicativo e, opcionalmente, criar um **segredo do aplicativo**.
- [Criar suas regras](active-directory-b2c-reference-policies.md) para obter os nomes de política.

<!--

TODO

OpenID Connect for the v2.0 app model is the recommended way to implement sign-in for a [web  app](active-directory-v2-flows.md#web-apps).  The most basic sign-in flow contains the following steps:

image goes here

-->

<!---HONumber=AcomDC_0128_2016-->