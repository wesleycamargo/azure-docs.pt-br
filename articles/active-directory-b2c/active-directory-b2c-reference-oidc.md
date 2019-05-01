---
title: Entrada na Web com OpenID Connect - Azure Active Directory B2C | Microsoft Docs
description: Crie aplicativos web usando o protocolo de autenticação OpenID Connect no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 4137360fadab0206c6569b58d6a9a0519ce74450
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703944"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Entrada na Web com o OpenID Connect no Azure Active Directory B2C

O OpenID Connect é um protocolo de autenticação criado com base em OAuth 2.0 que pode ser usado para conectar com segurança os usuários em aplicativos Web. Ao usar a implementação do Azure AD B2C (Azure Active Directory B2C) do OpenID Connect, você pode terceirizar a inscrição, a entrada e outras experiências de gerenciamento de identidade em seus aplicativos Web para o Azure AD (Azure Active Directory). Este guia mostra como fazer isso de maneira independente da linguagem. Ele descreve como enviar e receber mensagens HTTP sem usar qualquer uma das nossas bibliotecas de software livre.

O [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) estende o protocolo de *autorização* do OAuth 2.0 a ser usado como um protocolo de *autenticação*. Esse protocolo de autenticação permite que você executar o logon único. Ele apresenta o conceito de um *token de ID*, que permite que o cliente verificar a identidade do usuário e obter informações de perfil básico sobre o usuário.

Como ele estende o OAuth 2.0, ele também permite que aplicativos adquiram com segurança *tokens de acesso*. Você pode usar tokens de acesso para acessar os recursos protegidos por um [servidor de autorização](active-directory-b2c-reference-protocols.md). OpenID Connect é recomendado se você estiver criando um aplicativo web hospedado em um servidor e acessados por meio de um navegador. Se você deseja adicionar o gerenciamento de identidade para seu celular ou aplicativos da área de trabalho usando o Azure AD B2C, você deve usar [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) em vez de OpenID Connect. Para obter mais informações sobre tokens, consulte o [visão geral dos tokens no Azure Active Directory B2C](active-directory-b2c-reference-tokens.md)

O Azure AD B2C estende o protocolo padrão OpenID Connect para fazer mais do que uma simples ação de autenticação e autorização. Ele apresenta o [parâmetro de fluxo de usuário](active-directory-b2c-reference-policies.md), que permite que você use o OpenID Connect para adicionar usuário experiências ao seu aplicativo, como inscrição, entrada e gerenciamento de perfil.

## <a name="send-authentication-requests"></a>Enviar solicitações de autenticação

Quando seu aplicativo web precisa autenticar o usuário e executar um fluxo de usuário, ele pode direcionar o usuário para o `/authorize` ponto de extremidade. O usuário entra em ação dependendo do fluxo de usuário.

Essa solicitação, o cliente indica as permissões que ele precisa adquirir do usuário na `scope` parâmetro e o fluxo de usuário seja executado `p` parâmetro. Três exemplos são fornecidos nas seções a seguir (com quebras de linha para legibilidade), cada um usando um fluxo de usuário diferente. Para ter uma ideia de como funciona cada solicitação, tente colar a solicitação em um navegador e executá-lo. Você pode substituir `fabrikamb2c` com o nome do seu locatário, caso você tenha um e criou um fluxo de usuário.

#### <a name="use-a-sign-in-user-flow"></a>Usar um fluxo de usuário de entrada
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-user-flow"></a>Usar um fluxo de usuário de inscrição
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-user-flow"></a>Usar um fluxo de usuário do perfil de edição
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parâmetro | Obrigatório | DESCRIÇÃO |
| --------- | -------- | ----------- |
| client_id | Sim | A ID de aplicativo do [portal do Azure](https://portal.azure.com/) atribuído ao seu aplicativo. |
| response_type | Sim | Deve incluir um token de ID para o OpenID Connect. Se seu aplicativo web também precisa de tokens para chamar uma API da web, você pode usar `code+id_token`. |
| redirect_uri | Não  | O `redirect_uri` parâmetro do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo seu aplicativo. Ele deve corresponder exatamente a um do `redirect_uri` parâmetros que você registrou no portal do Azure, exceto que ele deve ser codificado por URL. |
| scope | Sim | Uma lista de escopos separados por espaços. O escopo `openid` indica uma permissão para entrar no usuário e obter dados sobre ele na forma de tokens de ID. O `offline_access` escopo é opcional para aplicativos da web. Ele indica que o seu aplicativo precisará uma *token de atualização* para acessar recursos estendido. |
| response_mode | Não  | O método que é usado para enviar o código de autorização resultante de volta para seu aplicativo. Ele pode ser `query`, `form_post` ou `fragment`.  O modo de resposta `form_post` é recomendado para maior segurança. |
| state | Não  | Um valor incluído na solicitação que também é retornada na resposta de token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente que normalmente é usado para impedir ataques de solicitação intersite forjada. O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página em que estava. |
| nonce | Sim | Um valor incluído na solicitação (gerado pelo aplicativo) que está incluída no token de ID resultante como uma declaração. O aplicativo pode, em seguida, verifique se esse valor para reduzir os ataques de reprodução de token. Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. |
| p | Sim | O fluxo de usuário que é executado. É o nome de um fluxo de usuário que é criado em seu locatário do Azure AD B2C. O nome do fluxo de usuário deve começar com `b2c\_1\_`. |
| prompt | Não  | O tipo de interação do usuário que é necessária. O único valor válido no momento é `login`, que força o usuário a inserir suas credenciais nessa solicitação. |

Neste ponto, o usuário é solicitado a concluir o fluxo de trabalho. O usuário talvez precise inserir seu nome de usuário e senha, entrar com uma identidade social ou inscrever-se para o diretório. Pode haver qualquer outro número de etapas, dependendo de como o fluxo de usuário é definido.

Depois que o usuário concluir o fluxo de usuário, uma resposta é retornada ao seu aplicativo em indicado `redirect_uri` parâmetro, usando o método que é especificado no `response_mode` parâmetro. A resposta é a mesma para cada um dos casos anteriores, independentemente do fluxo de usuário.

Uma resposta bem-sucedida usando `response_mode=fragment` se parece com esta:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | DESCRIÇÃO |
| --------- | ----------- |
| id_token | O token de ID que o aplicativo solicitou. Você pode usar o token de ID para verificar a identidade do usuário e iniciar uma sessão com o usuário. |
| código | O código de autorização que o aplicativo solicitou, se você usou `response_type=code+id_token`. O aplicativo pode usar o código de autorização para solicitar um token de acesso para um recurso de destino. Normalmente, os códigos de autorização expiram após cerca de 10 minutos. |
| state | Se um parâmetro `state` estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se o `state` valores na solicitação e resposta são idênticos. |

As respostas de erro também podem ser enviadas para o `redirect_uri` parâmetro para que o aplicativo possa tratá-las adequadamente:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | DESCRIÇÃO |
| --------- | ----------- |
| error | Um código que pode ser usado para classificar os tipos de erros que ocorrem. |
| error_description | Uma mensagem de erro específico que pode ajudar a identificar a causa raiz de um erro de autenticação. |
| state | Se um parâmetro `state` estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se o `state` valores na solicitação e resposta são idênticos. |

## <a name="validate-the-id-token"></a>Validar o token de ID

Apenas o recebimento de um tokend de ID não é suficiente para autenticar o usuário. Validar a assinatura do token de ID e verificar as declarações no token de acordo com os requisitos do aplicativo. O Azure AD B2C usa [JWTs (Tokens Web JSON)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar tokens e verificar se eles são válidos. Há muitas bibliotecas de software livre para validar JWTs dependendo do idioma de preferência. Recomendamos que você explore essas opções em vez de implementar a sua própria lógica de validação. 

B2C do AD do Azure tem uma empresa de metadados OpenID Connect, que permite que um aplicativo obter informações sobre o Azure AD B2C em tempo de execução. Essas informações incluem pontos de extremidade, conteúdos de token e chaves de assinatura de token. Há um documento de metadados JSON para cada fluxo de usuário no locatário B2C. Por exemplo, o documento de metadados para o fluxo de usuário `b2c_1_sign_in` em `fabrikamb2c.onmicrosoft.com` está localizado em:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Uma das propriedades desse documento de configuração é a `jwks_uri`, cujo valor para o mesmo fluxo de usuário seria:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Para determinar qual fluxo de usuário foi usado na assinatura de uma ID de token (e de onde obter os metadados), você tem duas opções. Primeiro, o nome do fluxo de usuário é incluído na declaração `acr` no token de ID. A outra opção é codificar o fluxo de usuário no valor do parâmetro `state` quando você emitir a solicitação e, em seguida, decodificá-lo para determinar qual fluxo de usuário foi usado. Ambos os métodos são válidos.

Depois que tiver adquirido o documento de metadados do ponto de extremidade de metadados OpenID Connect, você pode usar as chaves públicas RSA 256 para validar a assinatura do token de ID. Pode haver várias chaves listadas nesse ponto de extremidade, cada uma identificada por um `kid` de declaração. O cabeçalho do token de ID também contém uma declaração `kid`, que indica quais dessas chaves foi usada para assinar o token de ID.

Depois de validar a assinatura do token de ID, há várias declarações que você precisa verificar. Por exemplo:

- Valide a declaração `nonce` para evitar ataques de reprodução de token. Seu valor deve ser o que você especificou na solicitação de conexão.
- Validar o `aud` para garantir que o token de ID foi emitido para seu aplicativo. Seu valor deve ser a ID do aplicativo do seu aplicativo.
- Validar a `iat` e `exp` de declarações para certificar-se de que o token de ID não expirou.

Também há várias outras validações que devem ser realizadas. As validações são descritas detalhadamente na [especificação do OpenID Connect Core](https://openid.net/specs/openid-connect-core-1_0.html). Talvez você também queira validar declarações adicionais, dependendo do cenário. Algumas validações comuns incluem:

- Garantir que o usuário/organização tenha feito inscrição para o aplicativo.
- A garantia de que o usuário tenha a autorização/os privilégios adequados.
- A garantia de que uma determinada intensidade de autenticação tenha ocorrido, como a Autenticação Multifator do Azure.

Depois de validar o token de ID, você poderá iniciar uma sessão com o usuário. Você pode usar as declarações no token de ID para obter informações sobre o usuário em seu aplicativo. Os usos para essas informações incluem exibição, registros e autorização.

## <a name="get-a-token"></a>Obter um token

Se você precisar que seu aplicativo web para executar apenas fluxos de usuário, você pode ignorar as próximas seções. Essas seções são aplicáveis somente a web, aplicativos que precisam fazer chamadas autenticadas para uma API da web e também sejam protegidos pelo Azure AD B2C.

Você pode resgatar o código de autorização adquirido (usando `response_type=code+id_token`) para um token do recurso desejado enviando uma solicitação `POST` ao ponto de extremidade `/token`. Atualmente, o único recurso que você pode solicitar um token é sua API de web de back-end do aplicativo. A convenção para solicitar um token para si mesmo é usar a ID do aplicativo cliente como o escopo:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parâmetro | Obrigatório | DESCRIÇÃO |
| --------- | -------- | ----------- |
| p | Sim | O fluxo de usuário que foi usado para adquirir o código de autorização. Você não pode usar um fluxo de usuário diferente nessa solicitação. Adicione este parâmetro para a cadeia de caracteres de consulta, não ao corpo do POST. |
| client_id | Sim | A ID de aplicativo do [portal do Azure](https://portal.azure.com/) atribuído ao seu aplicativo. |
| grant_type | Sim | O tipo de concessão, que deve ser `authorization_code` para o fluxo do código de autorização. |
| scope | Não  | Uma lista de escopos separados por espaços. O escopo `openid` indica uma permissão para conectar o usuário e obter dados sobre ele na forma de parâmetros de id_token. Ele pode ser usado para obter tokens para seu web de back-end do aplicativo API, que é representado pela mesma ID de aplicativo do cliente. O `offline_access` escopo indica que seu aplicativo precisa de um token de atualização para acessar recursos estendido. |
| código | Sim | O código de autorização que você adquiriu no início do fluxo de usuário. |
| redirect_uri | Sim | O parâmetro `redirect_uri` do aplicativo em que você recebeu o código de autorização. |
| client_secret | Sim | O segredo do aplicativo que foi gerado na [portal do Azure](https://portal.azure.com/). O segredo do aplicativo é um artefato de segurança importante. Você deve armazená-lo com segurança no servidor. Altere esse segredo do cliente em intervalos periódicos. |

Uma resposta de token bem-sucedida tem a seguinte aparência:

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
| Parâmetro | DESCRIÇÃO |
| --------- | ----------- |
| not_before | A hora em que o token é considerado válido, nos valores de hora da época. |
| token_type | O valor do tipo de token. `Bearer` é o único tipo com suporte. |
| access_token | O token JWT assinado que você solicitou. |
| scope | Os escopos para os quais o token é válido. |
| expires_in | O período de tempo pelo qual o token de acesso é válido (em segundos). |
| refresh_token | Um token de atualização do OAuth 2.0. O aplicativo pode usar esse token para adquirir tokens adicionais depois que o token atual expira. Atualizar tokens podem ser usados para manter o acesso aos recursos por longos períodos de tempo. O escopo `offline_access` deve ter sido usado na autorização e solicitações de token para receber um token de atualização. |

As respostas de erro se parecem com:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | DESCRIÇÃO |
| --------- | ----------- |
| error | Um código que pode ser usado para classificar os tipos de erros que ocorrem. |
| error_description | Uma mensagem que pode ajudar a identificar a causa raiz de um erro de autenticação. |

## <a name="use-the-token"></a>Usar o token

Agora que adquiriu um token de acesso com êxito, você poderá usá-lo em solicitações às suas APIs Web de back-end incluindo-o no cabeçalho `Authorization`:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Atualizar o token

Tokens de ID expiram em um curto período de tempo. Os tokens de atualização depois que eles expirarem para poder continuar a acessar os recursos. Você pode atualizar um token enviando outra `POST` a solicitação para o `/token` ponto de extremidade. Desta vez, forneça o parâmetro `refresh_token` em vez do parâmetro `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parâmetro | Obrigatório | DESCRIÇÃO |
| --------- | -------- | ----------- |
| p | Sim | O fluxo de usuário que foi usado para adquirir o token de atualização original. Você não pode usar um fluxo de usuário diferente nessa solicitação. Adicione este parâmetro para a cadeia de caracteres de consulta, não ao corpo do POST. |
| client_id | Sim | A ID de aplicativo do [portal do Azure](https://portal.azure.com/) atribuído ao seu aplicativo. |
| grant_type | Sim | O tipo de concessão, que deve ser um token de atualização para essa parte do fluxo de código de autorização. |
| scope | Não  | Uma lista de escopos separados por espaços. O escopo `openid` indica uma permissão para entrar no usuário e obter dados sobre ele na forma de tokens de ID. Ele pode ser usado para enviar os tokens para seu web de back-end do aplicativo API, que é representado pela mesma ID de aplicativo do cliente. O `offline_access` escopo indica que seu aplicativo precisa de um token de atualização para acessar recursos estendido. |
| redirect_uri | Não  | O parâmetro `redirect_uri` do aplicativo em que você recebeu o código de autorização. |
| refresh_token | Sim | O token de atualização original que foi adquirido na segunda parte do fluxo. O `offline_access` escopo deve ser usado na autorização e solicitações de token para receber um token de atualização. |
| client_secret | Sim | O segredo do aplicativo que foi gerado na [portal do Azure](https://portal.azure.com/). O segredo do aplicativo é um artefato de segurança importante. Você deve armazená-lo com segurança no servidor. Altere esse segredo do cliente em intervalos periódicos. |

Uma resposta de token bem-sucedida tem a seguinte aparência:

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
| Parâmetro | DESCRIÇÃO |
| --------- | ----------- |
| not_before | A hora em que o token é considerado válido, nos valores de hora da época. |
| token_type | O valor do tipo de token. `Bearer` é o único tipo com suporte. |
| access_token | O token JWT assinado que foi solicitado. |
| scope | O escopo para o qual o token é válido. |
| expires_in | O período de tempo pelo qual o token de acesso é válido (em segundos). |
| refresh_token | Um token de atualização do OAuth 2.0. O aplicativo pode usar esse token para adquirir tokens adicionais depois que o token atual expira. Atualizar tokens podem ser usados para manter o acesso aos recursos por longos períodos de tempo. |

As respostas de erro se parecem com:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | DESCRIÇÃO |
| --------- | ----------- |
| error | Um código que pode ser usado para classificar os tipos de erros que ocorrem. |
| error_description | Uma mensagem que pode ajudar a identificar a causa raiz de um erro de autenticação. |

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de saída

Quando você deseja desconectar o usuário do aplicativo, ele não é suficiente para limpar os cookies do aplicativo ou encerrar a sessão do usuário. Redirecione o usuário para o Azure AD B2C para fazer logoff. Se você não conseguir fazer isso, o usuário pode ser capaz de autenticar novamente para seu aplicativo sem inserir as credenciais novamente.

Você pode simplesmente redirecionar o usuário para o `end_session` ponto de extremidade que está listado no documento de metadados OpenID Connect descrito anteriormente:

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parâmetro | Obrigatório | DESCRIÇÃO |
| --------- | -------- | ----------- |
| p | Sim | O fluxo de usuário que você quer usar para desconectar o usuário do aplicativo. |
| post_logout_redirect_uri | Não  | A URL à qual o usuário deve ser redirecionado após uma saída bem-sucedida. Se não for incluído, Azure AD B2C mostrará ao usuário uma mensagem genérica. |

Direcionando o usuário para o `end_session` ponto de extremidade limpa alguns do estado do usuário único logon com o Azure AD B2C, mas ele não desconecta o usuário de sua sessão do IDP (provedor) de identidade social. Se o usuário selecionar o mesmo IDP durante uma conexão subsequente, eles são autenticados novamente sem inserir as credenciais. Se um usuário quiser sair do aplicativo, isso não significa necessariamente que desejam desconectar sua conta do Facebook. No entanto, se as contas locais forem usadas, a sessão do usuário termina corretamente.

