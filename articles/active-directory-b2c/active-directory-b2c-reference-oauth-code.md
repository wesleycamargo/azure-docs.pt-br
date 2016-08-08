<properties
	pageTitle="Azure Active Directory B2C | Microsoft Azure"
	description="Criação de aplicativos Web usando a implementação do Azure Active Directory do protocolo de autenticação OpenID Connect."
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
	ms.date="07/22/2016"
	ms.author="dastrock"/>

# Azure Active Directory B2C: fluxo de código de autorização OAuth 2.0

A concessão de código de autorização OAuth 2.0 pode ser usada em aplicativos instalados em um dispositivo para obter acesso a recursos protegidos, como APIs Web. Usando a implementação do Azure Active Directory B2C (Azure AD) do OAuth 2.0, você pode adicionar tarefas de inscrição, entrada e outras de gerenciamento de identidade aos seus aplicativos móveis e para área de trabalho. Este guia é independente da linguagem. Ele descreve como enviar e receber mensagens HTTP sem usar qualquer uma das nossas bibliotecas de software livre.

<!-- TODO: Need link to libraries -->

O fluxo do código de autorização do OAuth 2.0 é descrito na [seção 4.1 da especificação do OAuth 2.0](http://tools.ietf.org/html/rfc6749). Você pode usá-lo para realizar a autenticação e a autorização na maioria dos tipos de aplicativo, incluindo [aplicativos Web](active-directory-b2c-apps.md#web-apps) e [aplicativos instalados nativamente](active-directory-b2c-apps.md#mobile-and-native-apps). Ele permite que os aplicativos adquiram **access\_tokens** com segurança, que podem ser usados para acessar recursos protegidos por um [servidor de autorização](active-directory-b2c-reference-protocols.md#the-basics).

Este guia se concentrará em um tipo específico de fluxo de código de autorização OAuth 2.0 -- **clientes públicos**. Um cliente público é qualquer aplicativo cliente que não é confiável para manter a integridade de uma senha secreta com segurança. Isso inclui aplicativos móveis, aplicativos para área de trabalho e praticamente qualquer aplicativo executado em um dispositivo que precise obter access\_tokens. Se você quiser adicionar o gerenciamento de identidades a um aplicativo Web usando o Azure AD B2C, deverá usar o [OpenID Connect](active-directory-b2c-reference-oidc.md) em vez do OAuth 2.0.

O Azure AD B2C estende os fluxos padrão do OAuth 2.0 para fazer mais do que simples ações de autenticação e autorização. Ele apresenta o [**parâmetro de política**](active-directory-b2c-reference-policies.md), que permite usar o OAuth 2.0 para adicionar experiências de usuário ao seu aplicativo, como o gerenciamento de inscrição, de entrada e de perfis. Aqui, mostraremos como usar o OAuth 2.0 e as políticas para implementar cada uma dessas experiências em seus aplicativos nativos. Também mostraremos como obter access\_tokens para acessar APIs Web.

As solicitações HTTP de exemplo abaixo usam nosso diretório B2C de exemplo, **fabrikamb2c.onmicrosoft.com**, bem como nosso aplicativo e políticas de exemplo. Fique à vontade para experimentar as solicitações usando esses valores ou os substituindo pelos seus próprios. Saiba como [obter seu próprio diretório B2C, aplicativos e políticas](#use-your-own-b2c-directory).

## 1\. Obter um código de autorização
O fluxo do código de autorização começa com o cliente direcionando o usuário para o ponto de extremidade `/authorize`. Essa é a parte interativa do fluxo, em que o usuário realmente irá executar uma ação. Nessa solicitação, o cliente indica as permissões que precisa adquirir do usuário no parâmetro `scope` e a política a ser executada no parâmetro `p`. Três exemplos são fornecidos abaixo (com quebras de linha para facilitar a leitura), cada um com uma política diferente.

#### Usar uma política de entrada

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

#### Usar uma política de inscrição

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

#### Usar uma política de edição de perfil

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Parâmetro | Obrigatório? | Descrição |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | Obrigatório | A ID do aplicativo que o [portal do Azure](https://portal.azure.com) atribuiu a seu aplicativo. |
| response\_type | Obrigatório | O tipo de resposta, que deve incluir `code` para o fluxo do código de autorização. |
| redirect\_uri | Obrigatório | O redirect\_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos redirect\_uris que você registrou no portal, com exceção de que ele deve ser codificado por URL. |
| scope | Obrigatório | Uma lista de escopos separados por espaços. Um valor de escopo único indica ao Azure AD que ambas as permissões estão sendo solicitadas. O uso da ID do cliente como o escopo indica que seu aplicativo precisa de um **token de acesso** que possa ser usado em relação a seu próprio serviço ou API Web, representado pela mesma ID de cliente. O escopo `offline_access` indica que seu aplicativo precisará de um **refresh\_token** para acessar os recursos a longo prazo. Você também pode usar o escopo `openid` para solicitar um **id\_token** do Azure AD B2C. |
| response\_mode | Recomendadas | O método que deve ser usado para enviar o authorization\_code resultante de volta ao aplicativo. Pode ser uma 'query', uma 'form\_post' ou um 'fragment'.
| state | Recomendadas | Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente que normalmente é usado para impedir ataques de solicitação intersite forjada. O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página em que ele estava ou a política em execução. |
| p | Obrigatório | A política que será executada. É o nome de uma política criada no diretório do B2C. O valor do nome da política deve começar com "b2c\_1\_". Saiba mais sobre as políticas em [Estrutura de política extensível](active-directory-b2c-reference-policies.md). |
| prompt | Opcional | O tipo de interação do usuário que é necessário. O único valor válido no momento é 'login', que força o usuário a inserir suas credenciais na solicitação. O logon único não terá efeito. |

Nesse momento, o usuário será solicitado a concluir o fluxo de trabalho da política. Isso pode exigir que o usuário insira seu nome de usuário e senha, entre com uma identidade social, inscreva-se no diretório ou outras etapas, dependendo de como a política estiver definida.

Depois que o usuário concluir a política, o Azure AD retornará uma resposta ao seu aplicativo no evento do tipo `redirect_uri`, usando o método especificado no parâmetro `response_mode`. A resposta será exatamente a mesma para cada um dos casos acima, independentemente da política que foi executada.

Uma resposta bem-sucedida que usa `response_mode=query` tem a seguinte aparência:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| código | O authorization\_code que o aplicativo solicitou. O aplicativo pode usar o código de autorização para solicitar um access\_token para um recurso de destino. Os authorization\_codes têm uma vida muito curta. Normalmente, eles expiram depois de cerca de 10 minutos. |
| state | Veja a descrição completa na tabela anterior. Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| error | Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar os tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error\_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |
| state | Confira a descrição completa da primeira tabela nesta seção. Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |


## 2\. Obter um token
Agora que você adquiriu um authorization\_code, poderá resgatar `code` para obter um token para o recurso desejado enviando uma solicitação `POST` para o ponto de extremidade `/token`. No Azure AD B2C, o único recurso para o qual você pode solicitar um token é a API Web de back-end do seu aplicativo. A convenção usada para solicitar um token para si mesmo é usar a ID do cliente do aplicativo como o escopo:

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parâmetro | Obrigatório? | Descrição |
| ----------------------- | ------------------------------- | --------------------- |
| p | Obrigatório | A política que foi usada para adquirir o código de autorização. Você não poderá usar uma política diferente nessa solicitação. Observe que esse parâmetro é adicionado à *cadeia de caracteres de consulta* e não ao corpo do POST. |
| client\_id | Obrigatório | A ID do aplicativo que o [portal do Azure](https://portal.azure.com) atribuiu a seu aplicativo. |
| grant\_type | Obrigatório | O tipo de concessão, que deve ser `authorization_code` para o fluxo do código de autorização. |
| scope | Recomendado | Uma lista de escopos separados por espaços. Um valor de escopo único indica ao Azure AD que ambas as permissões estão sendo solicitadas. O uso da ID do cliente como o escopo indica que seu aplicativo precisa de um **token de acesso** que possa ser usado em relação a seu próprio serviço ou API Web, representado pela mesma ID de cliente. O escopo `offline_access` indica que seu aplicativo precisará de um **refresh\_token** para acessar os recursos a longo prazo. Você também pode usar o escopo `openid` para solicitar um **id\_token** do Azure AD B2C. |
| código | Obrigatório | O authorization\_code que você adquiriu na primeira ramificação do fluxo. |
| redirect\_uri | Obrigatório | O redirect\_uri do aplicativo em que você recebeu o authorization\_code. |

Uma resposta de token bem-sucedida se parecerá com esta:

```
{
	"not_before": "1442340812",
	"token_type": "Bearer",
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
	"expires_in": "3600",
	"refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| not\_before | A hora em que o token é considerado válido, nos valores de hora da época. |
| token\_type | O valor do tipo de token. O único tipo com suporte do Azure AD é Portador |
| access\_token | O token JWT assinado que você solicitou. |
| scope | Os escopos para os quais o token for válido, que podem ser usados no caching de tokens para uso posterior. |
| expires\_in | O período de tempo pelo qual o token é válido (em segundos). |
| refresh\_token | O refresh\_token do OAuth 2.0. O aplicativo pode usar esse token para adquirir tokens de acesso adicionais depois que o atual expirar. Os refresh\_tokens têm longa duração e podem ser usados para reter acesso a recursos por períodos estendidos. Para obter mais detalhes, consulte a [referência ao token B2C](active-directory-b2c-reference-tokens.md). |

As respostas de erro serão parecidas com esta:

```
{
	"error": "access_denied",
	"error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| error | Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar os tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error\_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

## 3\. Usar o token
Agora que você já adquiriu com êxito um `access_token`, pode usar o token em solicitações para as suas APIs Web de back-end incluindo-o no cabeçalho `Authorization`:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## 4\. Atualizar o token
Os tokens de Id e acesso têm vida curta. Você deve atualizá-los depois que eles expirarem para poder continuar a acessar os recursos. Faça isso enviando outra solicitação `POST` ao ponto de extremidade `/token`. Dessa vez, forneça o `refresh_token` em vez do `code`:

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parâmetro | Obrigatório? | Descrição |
| ----------------------- | ------------------------------- | -------- |
| p | Obrigatório | A política usada para adquirir o refresh\_token original. Você não poderá usar uma política diferente nessa solicitação. Observe que esse parâmetro é adicionado à *cadeia de caracteres de consulta* e não ao corpo do POST. |
| client\_id | Recomendadas | A ID do aplicativo que o [portal do Azure](https://portal.azure.com) atribuiu a seu aplicativo. |
| grant\_type | Obrigatório | O tipo de concessão, que deve ser `refresh_token` para esse segmento do fluxo do código de autorização. |
| scope | Recomendadas | Uma lista de escopos separados por espaços. Um valor de escopo único indica ao Azure AD que ambas as permissões estão sendo solicitadas. O uso da ID do cliente como o escopo indica que seu aplicativo precisa de um **token de acesso** que possa ser usado em relação a seu próprio serviço ou API Web, representado pela mesma ID de cliente. O escopo `offline_access` indica que seu aplicativo precisará de um **refresh\_token** para acessar os recursos a longo prazo. Você também pode usar o escopo `openid` para solicitar um **id\_token** do Azure AD B2C. |
| redirect\_uri | Opcional | O redirect\_uri do aplicativo em que você recebeu o authorization\_code. |
| refresh\_token | Obrigatório | O refresh\_token original que você adquiriu na segunda ramificação do fluxo. |

Uma resposta de token bem-sucedida se parecerá com esta:

```
{
	"not_before": "1442340812",
	"token_type": "Bearer",
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
	"expires_in": "3600",
	"refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| not\_before | A hora em que o token é considerado válido, nos valores de hora da época. |
| token\_type | O valor do tipo de token. O único tipo com suporte do Azure AD é Portador |
| access\_token | O token JWT assinado que você solicitou. |
| scope | Os escopos para os quais o token for válido, que podem ser usados no caching de tokens para uso posterior. |
| expires\_in | O período de tempo pelo qual o token é válido (em segundos). |
| refresh\_token | O refresh\_token do OAuth 2.0. O aplicativo pode usar esse token para adquirir tokens de acesso adicionais depois que o atual expirar. Os refresh\_tokens têm longa duração e podem ser usados para reter acesso a recursos por períodos estendidos. Para obter mais detalhes, consulte a [referência ao token B2C](active-directory-b2c-reference-tokens.md). |

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

## Use seu próprio diretório B2C

Se você quiser experimentar essas solicitações por conta própria, primeiro deverá seguir estas três etapas e substituir os valores de exemplo acima pelos seus:

- [Criar um diretório B2C](active-directory-b2c-get-started.md) e usar o nome do seu diretório nas solicitações.
- [Criar um aplicativo](active-directory-b2c-app-registration.md) para obter uma ID de aplicativo e um redirect\_uri. Você deve incluir uma **cliente nativo** em seu aplicativo.
- [Criar suas regras](active-directory-b2c-reference-policies.md) para obter os nomes de política.

<!---HONumber=AcomDC_0727_2016-->