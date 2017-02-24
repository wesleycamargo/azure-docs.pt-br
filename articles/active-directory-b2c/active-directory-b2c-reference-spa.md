---
title: Azure Active Directory B2C | Microsoft Docs
description: "Como criar aplicativos de página única diretamente usando o fluxo implícito."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: a45cc74c-a37e-453f-b08b-af75855e0792
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: f2eb1f67c5687ac9bd8b94f6480617e6dc87ac9f
ms.openlocfilehash: ace35adc1cd2745ea11cb60429a1e7892d1f66d3


---
# <a name="azure-active-directory-b2c-sign-in-for-single-page-apps-using-oauth-20-implicit-flow"></a>Azure Active Directory B2C: conexão para aplicativos de página única usando o fluxo implícito do OAuth 2.0

> [!NOTE]
> Este recurso ainda está na versão prévia.
> 

Muitos aplicativos modernos têm um aplicativo de página única front-end que é escrito principalmente em JavaScript. Geralmente, ele é escrito usando uma estrutura como AngularJS, Ember.js ou Durandal.js. Aplicativos de página única e outros aplicativos JavaScript executados principalmente em um navegador enfrentam desafios interessantes quando o assunto é autenticação:

* As características de segurança desses aplicativos são consideravelmente diferentes dos aplicativos Web tradicionais baseados em servidor.
* Muitos servidores de autorização e provedores de identidade não dão suporte a solicitações CORS.
* Redirecionamentos para fora do aplicativo em páginas inteiras do navegador se tornam algo particularmente invasivo para a experiência do usuário.

Para oferecer suporte a esses aplicativos, o Azure AD B2C usa o fluxo implícito do OAuth 2.0.  O fluxo de concessão implícita de autorização do OAuth 2.0 é descrito na [seção 4.2 da especificação do OAuth 2.0](http://tools.ietf.org/html/rfc6749).  Nesse fluxo, o aplicativo recebe tokens diretamente do ponto de extremidade de autorização do Azure AD, sem qualquer troca entre servidores. Todo o manuseio de lógica de autenticação e de sessão ocorra inteiramente no cliente JavaScript, sem redirecionamentos adicionais de página.

O Azure AD B2C estende os fluxos implícitos padrão do OAuth 2.0 para fazer mais do que simples ações de autenticação e autorização. Ele apresenta o [**parâmetro de política**](active-directory-b2c-reference-policies.md), que permite usar o OAuth 2.0 para adicionar experiências de usuário ao seu aplicativo, como o gerenciamento de inscrição, de entrada e de perfis. Aqui, vamos mostrar como usar o fluxo implícito e o Azure AD para implementar cada uma dessas experiências em seus aplicativos de página única.  Examine também nossos exemplos de [Node. js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi) ou [.NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi) para começar.

As solicitações HTTP de exemplo abaixo usam nosso diretório B2C de exemplo, **fabrikamb2c.onmicrosoft.com**, bem como nosso aplicativo e políticas de exemplo. Fique à vontade para experimentar as solicitações usando esses valores ou os substituindo pelos seus próprios.
Saiba como [obter seu próprio diretório B2C, aplicativos e políticas](#use-your-own-b2c-tenant).

## <a name="protocol-diagram"></a>Diagrama de protocolo
O fluxo completo de entrada implícita é semelhante a este. Cada uma das etapas são descritas em detalhes abaixo.

![Raias do OpenId Connect](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Enviar solicitações de autenticação
Quando o aplicativo Web precisa autenticar o usuário e executar uma política, ele direciona o usuário para o ponto de extremidade `/authorize`. Essa é a parte interativa do fluxo, na qual o usuário realmente executa uma ação, dependendo da política, e recebe `id_token` de um ponto de extremidade do Azure AD.

Nessa solicitação, o cliente indica as permissões que precisa adquirir do usuário no parâmetro `scope` e a política a ser executada no parâmetro `p`. Três exemplos são fornecidos abaixo (com quebras de linha para facilitar a leitura), cada um com uma política diferente. Para ter uma ideia de como funciona cada solicitação, tente colar a solicitação em um navegador e executá-lo.

#### <a name="use-a-sign-in-policy"></a>Usar uma política de entrada
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>Usar uma política de inscrição
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Usar uma política de edição de perfil
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| client_id |Obrigatório |A ID do aplicativo que o [portal do Azure](https://portal.azure.com/) atribuiu a seu aplicativo. |
| response_type |obrigatório |Deve incluir `id_token` para conexão do OpenID Connect.  Também pode incluir o response_type `token`. O uso de `token` aqui permite que seu aplicativo receba imediatamente um token de acesso do ponto de extremidade de autorização sem precisar fazer uma segunda solicitação ao ponto de extremidade de autorização.  Se você usar o response_type de `token`, o parâmetro `scope` deverá conter um escopo indicando para quais recursos o token será emitido. |
| redirect_uri |Recomendadas |O redirect_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos redirect_uris que você registrou no portal, com exceção de que ele deve ser codificado por URL. |
| response_mode |recomendável |Especifica o método que deve ser usado para enviar o token resultante de volta ao aplicativo.  Deve ser `fragment` para o fluxo implícito. |
| scope |Obrigatório |Uma lista de escopos separados por espaços. Um valor de escopo único indica ao Azure AD que ambas as permissões estão sendo solicitadas. O escopo `openid` indica uma permissão para conectar o usuário e obter dados sobre ele na forma de **id_tokens** (falaremos mais sobre isso posteriormente neste artigo). O escopo `offline_access` é opcional para aplicativos Web. Indica que seu aplicativo precisará de um **refresh_token** para acessar os recursos a longo prazo. |
| state |Recomendadas |Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente que normalmente é usado para impedir ataques de solicitação intersite forjada. O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página em que ele estava. |
| nonce |Obrigatório |Um valor incluído na solicitação, gerado pelo aplicativo, que será incluído no id_token resultante como uma declaração. O aplicativo pode verificar esse valor para reduzir os ataques de reprodução de token. Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. |
| p |Obrigatório |A política que será executada. É o nome de uma política criada no locatário B2C. O valor do nome da política deve começar com "b2c\_1\_". Saiba mais sobre as políticas em [Estrutura de política extensível](active-directory-b2c-reference-policies.md). |
| prompt |Opcional |O tipo de interação do usuário que é necessário. O único valor válido no momento é 'login', que força o usuário a inserir suas credenciais na solicitação. O logon único não terá efeito. |

Nesse momento, o usuário será solicitado a concluir o fluxo de trabalho da política. Isso pode exigir que o usuário insira seu nome de usuário e senha, entre com uma identidade social, inscreva-se no diretório ou outras etapas, dependendo de como a política estiver definida.

Depois que o usuário concluir a política, o Azure AD retornará uma resposta ao seu aplicativo no evento do tipo `redirect_uri`, usando o método especificado no parâmetro `response_mode`. A resposta será exatamente a mesma para cada um dos casos acima, independentemente da política que foi executada.

#### <a name="successful-response"></a>Resposta bem-sucedida
Uma resposta bem-sucedida usando `response_mode=fragment` e `response_type=id_token+token` é semelhante ao seguinte, com quebras de linha para legibilidade:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| Parâmetro | Descrição |
| --- | --- |
| access_token |O token de acesso que o aplicativo solicitou.  O token de acesso não deve ser decodificado ou inspecionado de outra forma, ele pode ser tratado como uma cadeia de caracteres opaca. |
| token_type |O valor do tipo de token. O único tipo com suporte do Azure AD é Portador |
| expires_in |O período de tempo pelo qual o access_token é válido (em segundos). |
| scope |Os escopos para os quais o token é válido, que podem ser usados no caching de tokens para uso posterior. |
| id_token |O id_token que o aplicativo solicitou. Você pode usar o id_token para verificar a identidade do usuário e iniciar uma sessão com o usuário. Mais detalhes sobre id_tokens e respectivo conteúdo estão incluídos na [referência ao token do Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| state |Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro
As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| . | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de código de erro usada para classificar os tipos de erros que ocorrem. O código de erro pode ser usado para tratamento dos erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |
| state |Veja a descrição completa na tabela anterior. Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos.|

## <a name="validate-the-idtoken"></a>Validar o id_token
Apenas receber o id_token não é suficiente para autenticar o usuário -- você deve validar a assinatura do id_token e verificar as declarações no token de acordo com os requisitos do aplicativo. O Azure AD B2C usa [JWTs (Tokens Web JSON)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar tokens e verificar se eles são válidos.

Há muitas bibliotecas de software livre para validar JWTs dependendo do idioma de preferência. Considere explorar essas opções em vez de implementar a sua própria lógica de validação. As informações aqui serão úteis para descobrir como usar essas bibliotecas adequadamente.

O Azure AD B2C tem um ponto de extremidade de metadados do OpenID Connect, que permite a um aplicativo buscar informações sobre o Azure AD B2C no tempo de execução. Essas informações incluem pontos de extremidade, conteúdos de token e chaves de assinatura de token. Há um documento de metadados JSON para cada política no seu locatário de B2C. Por exemplo, o documento de metadados para a política `b2c_1_sign_in` no `fabrikamb2c.onmicrosoft.com` está localizado em:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Uma das propriedades desse documento de configuração é o `jwks_uri`, cujo valor para a mesma política será:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

A fim de determinar qual política foi usada na assinatura de um id_token (e onde buscar os metadados), você tem duas opções. Primeiro, o nome da política é incluído na declaração `acr` no id_token. Para obter informações sobre como analisar as declarações de um id_token, consulte a [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). A outra opção é codificar a política no valor do parâmetro `state` quando você emitir a solicitação e, em seguida, decodificá-lo para determinar qual política foi usada. Qualquer um dos métodos é perfeitamente válido.

Depois que tiver adquirido o documento de metadados do ponto de extremidade de metadados OpenID Connect, você poderá usar as chaves públicas RSA256 (localizadas nesse ponto de extremidade) para validar a assinatura do id_token. Pode haver várias chaves listadas nesse ponto de extremidade em qualquer ponto no tempo, cada uma identificada por um `kid`. O cabeçalho do id_token também contém uma declaração `kid`, que indica quais dessas chaves foi usada para assinar o id_token. Consulte a [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md) para obter mais informações, incluindo como [validar os tokens](active-directory-b2c-reference-tokens.md#token-validation).
<!--TODO: Improve the information on this-->

Após validar a assinatura do id_token, várias declarações também exigem verificação, por exemplo:

* Valide a declaração `nonce` para evitar ataques de reprodução de token. Seu valor deve ser o que você especificou na solicitação de conexão.
* Valide a declaração `aud` para garantir que o id_token foi emitido pelo seu aplicativo. Seu valor deve ser a ID do aplicativo do seu aplicativo.
* Valide as declarações `iat` e `exp` para garantir que o id_token não tenha expirado.

Também há várias outras validações que devem ser executadas. Elas são descritas detalhadamente na [Especificação de OpenID Connect Core](http://openid.net/specs/openid-connect-core-1_0.html).  Talvez você também queira validar declarações adicionais, dependendo do cenário. Algumas validações comuns incluem:

* A garantia de que o usuário/organização tenha feito inscrição para usar o aplicativo.
* A garantia de que o usuário tenha a autorização/os privilégios adequados.
* A garantia de que uma determinada intensidade de autenticação tenha ocorrido, como o Azure Multi-Factor Authentication.

Para obter mais informações sobre declarações em um id_token, consulte a [referência ao token do Azure AD B2C](active-directory-b2c-reference-tokens.md).

Depois de ter validado completamente o id_token, você poderá iniciar uma sessão com o usuário e usar as declarações no id_token para obter informações sobre o usuário no seu aplicativo. Essas informações podem ser usadas para exibição, registros, autorizações e outros.

## <a name="get-access-tokens"></a>Obter tokens de acesso
Se tudo o que o seu aplicativo Web precisar fazer for executar políticas, você poderá ignorar as próximas seções. Essas seções só serão aplicáveis aos aplicativos Web que precisarem fazer chamadas autenticadas para uma API Web e que também sejam protegidas pelo Azure AD B2C.

Agora que você autenticou o usuário em seu aplicativo de página única, pode obter tokens de acesso para chamar APIs da Web protegidas pelo Azure AD.  Mesmo se já tiver recebido um token usando o response_type `token`, você poderá usar esse método para adquirir tokens para recursos adicionais sem precisar redirecionar o usuário para entrar novamente.

No fluxo normal de aplicativo Web, você faria isso por meio de uma solicitação para o ponto de extremidade `/token`.  No entanto, o ponto de extremidade não oferecem suporte a solicitações CORS, portanto, fazer chamadas AJAX para obter e atualizar tokens está fora de cogitação.  Em vez disso, você pode usar o fluxo implícito em um iframe oculto para obter novos tokens para outras APIs da Web:

```
// Line breaks for legibility only

https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
&p=b2c_1_sign_in
```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| client_id |Obrigatório |A ID do aplicativo que o [portal do Azure](https://portal.azure.com/) atribuiu a seu aplicativo. |
| response_type |obrigatório |Deve incluir `id_token` para conexão do OpenID Connect.  Também pode incluir o response_type `token`. O uso de `token` aqui permitirá que seu aplicativo receba imediatamente um token de acesso do ponto de extremidade de autorização sem precisar fazer uma segunda solicitação ao ponto de extremidade de autorização.  Se você usar o response_type de `token`, o parâmetro `scope` deverá conter um escopo indicando para quais recursos o token será emitido. |
| redirect_uri |recomendável |O redirect_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo.  Ele deve corresponder exatamente a um dos redirect_uris que você registrou no portal, com exceção de que ele deve ser codificado por url. |
| scope |obrigatório |Uma lista de escopos separados por espaços.  Para obter tokens, inclua todos os escopos necessários para o recurso de seu interesse. |
| response_mode |recomendável |Especifica o método que deve ser usado para enviar o token resultante de volta ao aplicativo.  Pode ser `query`, `form_post` ou `fragment`. |
| state |recomendável |Um valor incluído na solicitação que retorna na resposta do token.  Pode ser uma cadeia de caracteres de qualquer conteúdo desejado.  Um valor exclusivo gerado aleatoriamente que normalmente é usado para impedir ataques de solicitação intersite forjada.  O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| nonce |obrigatório |Um valor incluído na solicitação, gerado pelo aplicativo, incluído no id_token resultante como uma declaração.  O aplicativo pode verificar esse valor para reduzir os ataques de reprodução de token.  Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que identifica a origem da solicitação. |
| prompt |obrigatório |Para atualizar e obter tokens em um iframe oculto, use o `prompt=none` para garantir que o iframe não pare de responder na página de entrada e retorne imediatamente. |
| login_hint |obrigatório |Para atualizar e obter tokens em um iframe oculto, você deve incluir o nome de usuário nessa dica a fim de distinguir entre várias sessões que o usuário pode ter em um determinado momento. Você pode extrair o nome de usuário de uma entrada anterior usando a declaração `preferred_username`. |
| domain_hint |obrigatório |Pode ser `consumers` ou `organizations`.  Para atualizar e obter tokens em um iframe oculto, você deve incluir o domain_hint na solicitação.  Extraia a declaração `tid` do id_token de uma entrada anterior para determinar qual valor usar.  Se o valor da declaração `tid` for `9188040d-6c67-4c5b-b112-36a304b66dad`, você deverá usar `domain_hint=consumers`.  Caso contrário, use `domain_hint=organizations`. |

Ao configurar o parâmetro `prompt=none`, essa solicitação terá êxito ou falhará imediatamente e retornará ao seu aplicativo.  Uma resposta bem-sucedida será enviada ao seu aplicativo no `redirect_uri` indicado, usando o método especificado no parâmetro `response_mode`.

#### <a name="successful-response"></a>Resposta bem-sucedida
Uma resposta bem-sucedida usando `response_mode=fragment` tem a seguinte aparência:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| . | Descrição |
| --- | --- |
| access_token |O token solicitado pelo aplicativo. |
| token_type |Sempre será `Bearer`. |
| state |Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |
| expires_in |Por quanto tempo o token de acesso é válido (em segundos). |
| scope |Os escopos para os quais o access_token é válido. |

#### <a name="error-response"></a>Resposta de erro
As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente.  No caso de `prompt=none`, um erro esperado será:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

Se você receber esse erro na solicitação do iframe, o usuário deverá entrar novamente de forma interativa para recuperar um novo token.  Você pode escolher lidar com isso da maneira que fizer mais sentido para seu aplicativo.

## <a name="refreshing-tokens"></a>Atualizando tokens
`id_token`s e `access_token`s expiram após um curto período. Portanto, seu aplicativo deve estar preparado para atualizar esses tokens periodicamente.  Para atualizar qualquer um desses tipos de tokens, execute a mesma solicitação de iframe oculto mencionada acima usando o parâmetro `prompt=none` para controlar o comportamento do Azure AD.  Para receber um novo `id_token`, use `response_type=id_token` e `scope=openid`, bem como um parâmetro `nonce`.

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de saída
Quando você quiser desconectar o usuário do aplicativo, redirecione o usuário ao Azure AD para sair. Se você não conseguir fazer isso, é provável que o usuário consiga se autenticar novamente no aplicativo sem ter que reinserir as credenciais. Isso ocorre porque eles terão uma sessão de logon único válida com o Azure AD.

Você pode simplesmente redirecionar o usuário para o `end_session_endpoint` listado no documento de metadados do OpenID Connect descrito na seção anterior, “Validar o id_token”:

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parâmetro | Obrigatório? | Descrição |
| --- | --- | --- |
| p |Obrigatório |A política usada para desconectar o usuário de seu aplicativo. |
| post_logout_redirect_uri |Recomendadas |A URL para a qual o usuário deve ser redirecionado após a saída com êxito. Se não estiver incluído, o usuário verá uma mensagem genérica do Azure AD B2C. |

> [!NOTE]
> Embora o direcionamento do usuário para o `end_session_endpoint` remova o estado de logon único do usuário com o Azure AD B2C, ele não desconecta a sessão do IDP (provedor de identidade) social do usuário. Se o usuário selecionar o mesmo IDP durante uma conexão posterior, ele será autenticados novamente sem precisar inserir suas credenciais. Se um usuário quiser sair do seu aplicativo B2C, isso não significa necessariamente que ele deseja desconectar totalmente sua conta do Facebook. No entanto, no caso de contas locais, a sessão do usuário será encerrada corretamente.
> 
> 

## <a name="use-your-own-b2c-tenant"></a>Usar seu próprio locatário B2C
Se você quiser experimentar essas solicitações por conta própria, primeiro deverá seguir estas três etapas e substituir os valores de exemplo acima pelos seus:

* [Criar um locatário de B2C](active-directory-b2c-get-started.md)e usar o nome do seu locatário nas solicitações.
* [Criar um aplicativo](active-directory-b2c-app-registration.md) para obter uma ID de aplicativo e um redirect_uri. Você deve incluir um **aplicativo Web/API Web** em seu aplicativo e, opcionalmente, criar um **segredo do aplicativo**.
* [Criar suas regras](active-directory-b2c-reference-policies.md) para obter os nomes de política.

## <a name="samples"></a>Exemplos

* [Criar um aplicativo de página única usando Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi)
* [Criar um aplicativo de página única usando .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi)




<!--HONumber=Feb17_HO2-->


