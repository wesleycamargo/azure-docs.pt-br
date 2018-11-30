---
title: Proteger aplicativos de página única usando o fluxo implícito do Azure AD v2.0 | Microsoft Docs
description: Criando aplicativos Web usando a implementação v2.0 do Azure AD do fluxo implícito para aplicativos de página única.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 878c2596a1d884e26a4b4a4ed4764cfd9ce6b39b
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52424093"
---
# <a name="v20-protocols---spas-using-the-implicit-flow"></a>Protocolos v2.0 - SPAs que usam o fluxo implícito

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Com o ponto de extremidade v2.0, você pode autenticar usuários em seus aplicativos de página única com contas pessoais e contas corporativas ou de estudante da Microsoft. Aplicativos de página única e outros aplicativos JavaScript executados principalmente em um navegador enfrentam desafios interessantes quando o assunto é autenticação:

* As características de segurança desses aplicativos são consideravelmente diferentes dos aplicativos Web tradicionais baseados em servidor.
* Muitos servidores de autorização e provedores de identidade não são compatíveis com solicitações CORS.
* Redirecionamentos para fora do aplicativo em páginas inteiras do navegador se tornam algo particularmente invasivo para a experiência do usuário.

Para esses aplicativos (AngularJS, Ember.js, React.js etc.), o Azure AD (Azure Active Directory) é compatível com o Fluxo de concessão implícita do OAuth 2.0. O fluxo está descrito na [Especificação do OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.2). O principal benefício é que ele permite que o aplicativo obtenha tokens do Azure AD sem executar uma troca de credenciais de servidor back-end. Isso permite que o aplicativo autentique o usuário, mantenha a sessão e obtenha tokens para outras APIs da Web, tudo dentro do código JavaScript do cliente. Há algumas considerações de segurança importantes que você deve levar em conta ao usar o fluxo implícito, especificamente sobre [cliente](https://tools.ietf.org/html/rfc6749#section-10.3) e [representação de usuário](https://tools.ietf.org/html/rfc6749#section-10.3).

Se você quer usar o fluxo implícito e o Azure AD para adicionar autenticação ao seu aplicativo JavaScript, recomendamos o uso da biblioteca JavaScript de software livre, a [msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js). 

No entanto, se você preferir não usar uma biblioteca em seu aplicativo de página única e enviar mensagens de protocolo por conta própria, siga as etapas gerais a seguir.

> [!NOTE]
> Nem todos os recursos e cenários do Azure Active Directory têm suporte no ponto de extremidade v2.0. Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagrama de protocolo

O diagrama a seguir mostra a aparência de todo fluxo de entrada implícita e as seções a seguir descrevem cada etapa em mais detalhes.

![Raias do OpenId Connect](./media/v2-oauth2-implicit-grant-flow/convergence_scenarios_implicit.png)

## <a name="send-the-sign-in-request"></a>Enviar a solicitação de conexão

Para autenticar inicialmente o usuário em seu aplicativo, você pode enviar uma solicitação de autorização [OpenID Connect](v2-protocols-oidc.md) e obter um `id_token` do ponto de extremidade v2.0.

> [!IMPORTANT]
> Para solicitar um token de ID com êxito, o registro do aplicativo no [portal de registro](https://apps.dev.microsoft.com) precisa ter **Permitir Fluxo Implícito** habilitado para o cliente Web. Se não estiver habilitado, um erro `unsupported_response` será retornado: **O valor fornecido para o parâmetro de entrada 'response_type' não é permitido para este cliente. O valor esperado é 'code'**

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Para testar a entrada usando o fluxo implícito, clique em <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> Depois de entrar, seu navegador deverá ser redirecionado para `https://localhost/myapp/` com um `id_token` na barra de endereços.
> 

| Parâmetro |  | DESCRIÇÃO |
| --- | --- | --- |
| `tenant` | obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| `client_id` | obrigatório |A Id de Aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) atribuiu ao aplicativo. |
| `response_type` | obrigatório |Deve incluir `id_token` para conexão do OpenID Connect. Também pode incluir o response_type `token`. O uso de `token` aqui permitirá que seu aplicativo receba imediatamente um token de acesso do ponto de extremidade de autorização sem precisar fazer uma segunda solicitação ao ponto de extremidade de autorização. Se você usar o response_type de `token`, o parâmetro `scope` deverá conter um escopo indicando para quais recursos o token será emitido. |
| `redirect_uri` | recomendável |O redirect_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos redirect_uris que você registrou no portal, com exceção de que ele deve ser codificado por url. |
| `scope` | obrigatório |Uma lista de escopos separados por espaços. Para o OpenID Connect, é necessário incluir o escopo `openid`, que é traduzido para a permissão "Fazer seu logon" na interface do usuário de consentimento. Como opção, convém incluir[ escopos](v2-permissions-and-consent.md) `email`ou `profile` para obter acesso a dados adicionais do usuário. Você também pode incluir outros escopos nessa solicitação a fim de solicitar o consentimento a vários recursos. |
| `response_mode` | opcional |Especifica o método que deve ser usado para enviar o token resultante de volta ao aplicativo. O padrão é consulta de um token de acesso, mas um fragmento caso a solicitação inclua um id_token. |
| `state` | recomendável |Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente normalmente é usado para [impedir ataques de solicitação intersite forjada](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| `nonce` | obrigatório |Um valor incluído na solicitação, gerado pelo aplicativo, que será incluído no id_token resultante como uma declaração. O aplicativo pode verificar esse valor para reduzir os ataques de reprodução de token. Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. Necessário somente quando um id_token é solicitado. |
| `prompt` | opcional |Indica o tipo de interação do usuário que é necessário. Os únicos valores válidos no momento são 'login', 'none', 'select_account' e 'consent'. `prompt=login` forçará o usuário a inserir suas credenciais na solicitação, negando o logon único. `prompt=none` é o oposto - ele garantirá que o usuário não seja apresentado a nenhum prompt interativo. Se a solicitação não puder ser concluída silenciosamente por meio de logon único, o ponto de extremidade v2.0 retornará um erro. `prompt=select_account` envia o usuário para um seletor de conta em que todas as contas lembradas na sessão serão exibidas. `prompt=consent` irá disparar a caixa de diálogo de consentimento do OAuth depois que o usuário iniciar a sessão, solicitando que ele conceda permissões ao aplicativo. |
| `login_hint`  |opcional |Pode ser usado para preencher previamente o campo de nome de usuário/endereço de email da página de entrada do usuário, se você souber o nome de usuário com antecedência. Geralmente, os aplicativos usarão esse parâmetro durante a reautenticação, após já terem extraído o nome de usuário de uma entrada anterior usando a declaração `preferred_username` .|
| `domain_hint` | opcional |Pode ser `consumers` ou `organizations`. Se for incluído, ele ignorará o processo de descoberta baseada em email que o usuário passa na página de entrada v 2.0, resultando em uma experiência de usuário um pouco mais simples. Geralmente, os aplicativos usarão esse parâmetro durante a reautenticação, extraindo a declaração `tid` do id_token. Se o `tid` é de valor de declaração `9188040d-6c67-4c5b-b112-36a304b66dad` (o Microsoft Account locatário do consumidor), você deve usar `domain_hint=consumers`. Caso contrário, você pode usar `domain_hint=organizations` durante a reautenticação. |

Nesse ponto, será solicitado que o usuário insira suas credenciais e conclua a autenticação. O ponto de extremidade v2.0 também garantirá que o usuário tenha consentido as permissões indicadas no parâmetro de consulta `scope` . Se o usuário não consentir a **nenhuma** dessas permissões, ele será solicitado a consentir às permissões necessárias. Para obter mais informações, consulte [permissões, consentimento e aplicativos multilocatários](v2-permissions-and-consent.md).

Depois que o usuário se autentica e dá consentimento, o ponto de extremidade v2.0 retorna uma resposta ao aplicativo no `redirect_uri` indicado usando o método especificado no parâmetro `response_mode`.

#### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida usando `response_mode=fragment` e `response_type=id_token+token` é semelhante ao seguinte (com quebras de linha para legibilidade):

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fmail.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parâmetro | DESCRIÇÃO |
| --- | --- |
| `access_token` |Incluído se `response_type` incluir `token`. O token de acesso solicitado pelo aplicativo, nesse caso para o Microsoft Graph. O token de acesso não deve ser decodificado ou inspecionado de outra forma, ele deve ser tratado como uma cadeia de caracteres opaca. |
| `token_type` |Incluído se `response_type` incluir `token`. Sempre será `Bearer`. |
| `expires_in`|Incluído se `response_type` incluir `token`. Indica o número de segundos durante os quais o token é válido, para fins de caching. |
| `scope` |Incluído se `response_type` incluir `token`. Indica os escopos para os quais access_token será válido. Pode não incluir todos os escopos solicitados, se eles não eram aplicáveis ao usuário (no caso de escopos somente do AAD sendo solicitados quando uma conta pessoal é usada para fazer logon). |
| `id_token` | Um JWT (Token Web JSON) assinado. O aplicativo pode decodificar os segmentos desse token para solicitar informações sobre o usuário que fez login. O aplicativo pode armazenar em cache os valores e exibi-los, mas não deve depender deles para qualquer autorização ou limites de segurança. Para obter mais informações sobre id_tokens, veja a [`id_token reference`](id-tokens.md). <br> **Observação:** somente fornecido se o escopo `openid` for solicitado. |
| `state` |Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | DESCRIÇÃO |
| --- | --- |
| `error` |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| `error_description` |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

## <a name="validate-the-idtoken"></a>Validar o id_token

Apenas receber o id_token não é suficiente para autenticar o usuário; você deve também validar a assinatura do id_token e verificar as declarações no token com base nos requisitos do aplicativo. O ponto de extremidade v2.0 usa [JWTs (Tokens Web JSON)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar tokens e verificar se eles são válidos.

Você pode escolher validar o `id_token` no código do cliente, mas uma prática comum é enviar o `id_token` para um servidor de back-end e executar a validação nele. Após a validação da assinatura do id_token, será necessário verificar algumas declarações: Consulte a [referência do `id_token`](id-tokens.md) para obter mais informações, incluindo [validação de tokens](id-tokens.md#validating-an-idtoken) e [informações importantes sobre substituição de chave de assinatura](active-directory-signing-key-rollover.md). Há, pelo menos, uma disponível para a maioria das linguagens e plataformas.

Talvez você também queira validar declarações adicionais, dependendo do cenário. Algumas validações comuns incluem:

* Garantir que o usuário/organização tenha assinado para usar o aplicativo.
* Garantir que o usuário tenha autorização/privilégios adequados.
* Garantir que uma determinada intensidade de autenticação tenha ocorrido, como autenticação multifator.

Depois de ter validado completamente o id_token, você poderá iniciar uma sessão com o usuário e usar declarações no id_token para obter informações sobre o usuário no seu aplicativo. Essas informações podem ser usadas para exibição, registros, personalização etc.

## <a name="get-access-tokens"></a>Obter tokens de acesso

Agora que você autenticou o usuário em seu aplicativo de página única, pode obter tokens de acesso para chamar APIs Web protegidas pelo Azure AD, como o [Microsoft Graph](https://developer.microsoft.com/graph). Mesmo se já tiver recebido um token usando o response_type `token`, você poderá usar esse método para adquirir tokens para recursos adicionais sem precisar redirecionar o usuário para entrar novamente.

No fluxo normal de OpenID Connect/OAuth, você faria isso por meio de uma solicitação para o ponto de extremidade `/token` do v2.0. No entanto, o ponto de extremidade v 2.0 não suporta solicitações CORS, portanto, fazer chamadas AJAX para obter e atualizar tokens está fora de cogitação. Em vez disso, você pode usar o fluxo implícito em um iframe oculto para obter novos tokens para outras APIs da Web: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

Para obter detalhes sobre os parâmetros de consulta na URL, consulte [enviar a solicitação de entrada](#send-the-sign-in-request).

> [!TIP]
> Tente copiar e colar a solicitação abaixo em uma guia do navegador! (Não se esqueça de substituir os valores `domain_hint` e `login_hint` pelos valores corretos para o seu usuário)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&domain_hint=consumers-or-organizations&login_hint=your-username`
>

Graças ao parâmetro `prompt=none` , essa solicitação terá êxito ou falhará imediatamente e retornará ao seu aplicativo. Uma resposta bem-sucedida será enviada ao seu aplicativo no `redirect_uri` indicado, usando o método especificado no parâmetro `response_mode`.

#### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida usando `response_mode=fragment` tem a seguinte aparência:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parâmetro | DESCRIÇÃO |
| --- | --- |
| `access_token` |Incluído se `response_type` incluir `token`. O token de acesso solicitado pelo aplicativo, nesse caso para o Microsoft Graph. O token de acesso não deve ser decodificado ou inspecionado de outra forma, ele deve ser tratado como uma cadeia de caracteres opaca. |
| `token_type` | Sempre será `Bearer`. |
| `expires_in` | Indica o número de segundos durante os quais o token é válido, para fins de caching. |
| `scope` | Indica os escopos para os quais access_token será válido. Pode não incluir todos os escopos solicitados, se eles não eram aplicáveis ao usuário (no caso de escopos somente do AAD sendo solicitados quando uma conta pessoal é usada para fazer logon). |
| `id_token` | Um JWT (Token Web JSON) assinado. Incluído se `response_type` incluir `id_token`. O aplicativo pode decodificar os segmentos desse token para solicitar informações sobre o usuário que fez login. O aplicativo pode armazenar em cache os valores e exibi-los, mas não deve depender deles para qualquer autorização ou limites de segurança. Para obter mais informações sobre id_tokens, veja a [referência `id_token`](id-tokens.md). <br> **Observação:** somente fornecido se o escopo `openid` for solicitado. |
| `state` |Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |


#### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente. No caso de `prompt=none`, um erro esperado será:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parâmetro | DESCRIÇÃO |
| --- | --- |
| `error` |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| `error_description` |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

Se você receber esse erro na solicitação do iframe, o usuário deverá entrar novamente de forma interativa para recuperar um novo token. Você pode escolher lidar com isso da maneira que fizer mais sentido para seu aplicativo.

## <a name="validating-access-tokens"></a>Validar tokens de acesso

Após receber um access_token, certifique-se de validar a assinatura do token e as declarações a seguir. Também é possível escolher validar declarações adicionais com base no seu cenário. 

* Declaração de**audiência** para garantir que o token tenha sido destinado ao aplicativo
* A declaração **emissor**, para verificar se o token foi de fato emitido para o aplicativo pelo ponto de extremidade v2.0
* Declarações de **tempo de expiração** e **não antes** para verificar se o token não expirou

Para obter mais informações sobre as declarações presentes no token de acesso, veja a [referência de token de acesso](access-tokens.md)

## <a name="refreshing-tokens"></a>Atualizando tokens

A concessão implícita não fornece tokens de atualização. `id_token`s e `access_token`s expirarão após um curto período. Portanto, seu aplicativo deve estar preparado para atualizar esses tokens periodicamente. Para atualizar qualquer um desses tipos de tokens, você pode executar a mesma solicitação de iframe oculto mencionada acima usando o parâmetro `prompt=none` para controlar o comportamento do Azure AD. Se você quiser receber um novo `id_token`, use `response_type=id_token` e `scope=openid`, bem como um parâmetro `nonce`.

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de desconexão

O `end_session_endpoint` do OpenIdConnect permite que o aplicativo envie uma solicitação ao ponto de extremidade v2.0 para encerrar uma sessão do usuário e limpar os cookies definidos pelo ponto de extremidade v2.0. Para desconectar por completo um usuário de um aplicativo Web, seu aplicativo deve encerrar sua própria sessão com o usuário (normalmente, limpando o cache de token ou removendo cookies) e, depois, redirecionar o navegador para:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parâmetro |  | DESCRIÇÃO |
| --- | --- | --- |
| `tenant` |obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | recomendável | A URL para a qual o usuário deve retornar após a conclusão do logoff. Esse valor deve corresponder a um dos URIs de redirecionamento registrados no aplicativo. Se ele não estiver incluído, o usuário verá uma mensagem genérica do ponto de extremidade v2.0. |

## <a name="next-steps"></a>Próximas etapas

* Percorra os [exemplos de MSAL JS](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) para começar a codificação.
