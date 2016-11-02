
<properties
    pageTitle="Fluxo de credenciais de cliente OAuth v2.0 do Azure AD | Microsoft Azure"
    description="Criando aplicativos Web usando a implementação do AD do Azure do protocolo de autenticação OAuth 2.0."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="msmbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="dastrock"/>


# <a name="v2.0-protocols---oauth-2.0-client-credentials-flow"></a>Protocolos de v2.0 – Fluxo de credenciais de cliente OAuth 2.0

A [concessão de credenciais do cliente OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.4), às vezes chamada de "OAuth de duas pernas", pode ser usada para acessar os recursos hospedados na Web usando a identidade de um aplicativo.  Ela costuma ser usada para interações entre servidores que devem ser executadas em segundo plano sem a presença imediata de um usuário final.  Esses tipos de aplicativo normalmente são referidos como **daemons** ou **contas de serviço**.

> [AZURE.NOTE]
    Nem todos os recursos e cenários do Azure Active Directory têm suporte no ponto de extremidade v2.0.  Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).

No tipo mais comum de "OAuth com três pernas", um aplicativo cliente tem permissão para acessar um recurso em nome de um usuário específico.  A permissão é **delegada** do usuário para o aplicativo, geralmente durante o processo de [consentimento](active-directory-v2-scopes.md) .  No entanto, no fluxo de credenciais do cliente, as permissões são concedidas **diretamente** ao próprio aplicativo.  Quando o aplicativo apresenta um token a um recurso, este impõe que o próprio aplicativo tem autorização para executar algumas ações, e não que algum usuário tem essa autorização.

## <a name="protocol-diagram"></a>Diagrama de protocolo
Todo o fluxo de credenciais de cliente é semelhante a este. Cada uma das etapas são descritas em detalhes abaixo.

![Fluxo de credenciais do cliente](../media/active-directory-v2-flows/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>Obter autorização direta 
Existem duas maneiras de um aplicativo receber autorização direta para acessar um recurso: por meio de uma lista de controle de acesso no recurso ou pela atribuição de permissão de aplicativo no Azure AD.  Há várias outras maneiras de um recurso escolher autorizar seus clientes, e cada servidor de recursos pode escolher o método que faz mais sentido para seu aplicativo.  Estes dois métodos são os mais comuns no Azure AD e são recomendados para clientes e recursos que desejam executar o fluxo de credenciais de cliente.

### <a name="access-control-lists"></a>Listas de controle de acesso
Um provedor de recursos pode impor uma verificação de autorização com base em uma lista de IDs de aplicativo que ele conhece e conceder determinado nível de acesso.  Quando o recurso recebe um token do ponto de extremidade v2.0, ele pode decodificar o token e extrair a ID do aplicativo do cliente das declarações `appid` e `iss`.  Ele pode comparar o aplicativo com alguma ACL (lista de controle de acesso) mantida por ele.  A granularidade e o método da lista de controle de acesso podem variar bastante de recurso para recurso.

Um caso de uso comum para essas ACLs é testar executores para um aplicativo Web ou API Web.  A API Web só pode conceder um subconjunto de suas permissões completas para seus vários clientes.  Mas para executar testes de ponta a ponta na API, um cliente de teste é criado para adquirir tokens do ponto de extremidade v2.0 e enviá-las para a API.  A API pode passar a ID do aplicativo cliente para a ACL a fim de ter acesso completo à funcionalidade da API.  Observe que se você tiver uma lista desse tipo em seu serviço, deverá não só validar a `appid` do chamador, mas também que o `iss` do token é confiável.

Esse tipo de autorização é comum para daemons e contas de serviço que precisam acessar dados pertencentes a usuários consumidores com contas pessoais da Microsoft.  Para dados pertencentes a organizações, é recomendável que você obtenha a autorização necessária usando permissões de aplicativo.

### <a name="application-permissions"></a>Permissões de aplicativo
Em vez de usar ACLs, as APIs podem expor um conjunto de **permissões de aplicativo** que podem ser concedidas a um aplicativo.  Uma permissão de aplicativo é concedida a um aplicativo por um administrador de uma organização e só pode ser usada para acessar os dados pertencentes a essa organização e a seus funcionários.  Por exemplo, o Microsoft Graph expõe várias permissões de aplicativo:

- Leitura de emails em todas as caixas de correio
- Leitura e gravação de emails em todas as caixas de correio
- Envio de emails como qualquer usuário
- Leitura de dados do diretório
- [muito mais](https://graph.microsoft.io)

Para obter essas permissões em seu aplicativo, execute as etapas a seguir.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Solicitar as permissões no portal de registro do aplicativo

- Navegue até o aplicativo em [apps.dev.microsoft.com](https://apps.dev.microsoft.com) ou [crie um aplicativo](active-directory-v2-app-registration.md), se ainda não fez isso.  Verifique se seu aplicativo criou pelo menos um Segredo do Aplicativo.
- Localize a seção **Permissões diretas do aplicativo** e adicione as permissões que seu aplicativo requer.
- **Salve** o registro do aplicativo

#### <a name="recommended:-sign-the-user-into-your-app"></a>Recomendado: conectar o usuário ao aplicativo

Normalmente, ao criar um aplicativo que usa permissões do aplicativo, ele precisará ter uma página/exibição que permita ao administrador aprovar as permissões do aplicativo.  Essa página pode ser parte do fluxo de entrada do aplicativo, parte das configurações do aplicativo ou um fluxo dedicado de "conexão".  Em muitos casos, faz sentido que o aplicativo somente mostre o modo de exibição "conectar" depois que o usuário entra com uma conta corporativa ou de estudante da Microsoft.

A conexão do usuário ao aplicativo permite que você identifique a organização à qual ele pertence antes de pedir a ele que aprove as permissões do aplicativo.  Embora não seja estritamente necessário, isso pode ajudá-lo a criar uma experiência mais intuitiva para os usuários empresariais.  Para conectar o usuário, siga nossos [tutoriais de protocolo v2.0](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Solicitar permissões de um administrador de diretório

Quando você estiver pronto para solicitar permissões de administrador da empresa, redirecione o usuário para o **ponto de extremidade de consentimento do administrador**v2.0.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro Tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| locatário | obrigatório | O locatário do diretório para o qual você deseja solicitar permissão.  Pode ser fornecido no formato de nome amigável ou GUID.  Se você não souber a qual locatário o usuário pertence e quiser deixá-lo entrar com qualquer locatário, use `common`. |
| client_id | obrigatório | A Id de Aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) atribuiu ao aplicativo. |
| redirect_uri | obrigatório | O redirect_uri onde você deseja que a resposta seja enviada para ser tratada pelo aplicativo.  Ele deve corresponder exatamente a um dos redirect_uris que você registrou no portal, com exceção de que ele deve ser codificado por URL e podem ter mais segmentos de caminho. |
| state | recomendável | Um valor incluído na solicitação também será retornado na resposta do token.  Pode ser uma cadeia de caracteres de qualquer conteúdo desejado.  O estado é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |

Neste ponto, o Azure AD impõe que somente um administrador de locatário pode entrar para concluir a solicitação.  O administrador deverá aprovar todas as permissões diretas do aplicativo que você solicitou para o aplicativo no portal de registro. 

##### <a name="successful-response"></a>Resposta bem-sucedida
Se o administrador aprovar as permissões para o seu aplicativo, a resposta bem-sucedida será:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| locatário | O locatário do diretório que concedeu as permissões solicitadas, no formato GUID. |
| state | Um valor incluído na solicitação também será retornado na resposta do token.  Pode ser uma cadeia de caracteres de qualquer conteúdo desejado.  O estado é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| admin_consent | Será definido como `True`. |


##### <a name="error-response"></a>Resposta de erro
Se o administrador não aprovar as permissões para o seu aplicativo, a resposta de falha será:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| error | Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro.  |

Depois de receber uma resposta bem-sucedida do ponto de extremidade de provisionamento do aplicativo, o aplicativo terá as permissões diretas solicitadas.  Agora, você pode solicitar um token para o recurso desejado.

## <a name="get-a-token"></a>Obter um token
Após adquirir a autorização necessária para seu aplicativo, você poderá adquirir tokens de acesso para as APIs.  Para obter um token usando a concessão de credenciais do cliente, envie uma solicitação POST para o ponto de extremidade v2.0 `/token` :

```
POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| client_id | obrigatório | A Id de Aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) atribuiu ao aplicativo. |
| scope | obrigatório | O valor transmitido ao parâmetro `scope` na solicitação deve ser o identificador de recurso (URI de ID do aplicativo) do recurso desejado, com o sufixo `.default`.  Portanto, para o exemplo do Microsoft Graph fornecido, o valor deve ser `https://graph.microsoft.com/.default`.  Esse valor informa ao ponto de extremidade v2.0 que em relação a todas as permissões diretas de aplicativo que você configurou para seu aplicativo, ele deverá emitir um token para as que pertencem ao recurso desejado. |
| client_secret | obrigatório | O Segredo do Aplicativo que você criou no portal de registro do aplicativo para seu aplicativo. |
| grant_type | obrigatório | Deve ser `client_credentials`. | 

#### <a name="successful-response"></a>Resposta bem-sucedida
Uma resposta bem-sucedida terá o formato:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| access_token | O token de acesso solicitado. O aplicativo pode usar esse token para se autenticar no recurso protegido, como uma API Web. |
| token_type | Indica o valor do tipo de token. O único tipo com suporte do Azure AD é o `Bearer`.  |
| expires_in | Por quanto tempo o token de acesso é válido (em segundos). |

#### <a name="error-response"></a>Resposta de erro
Uma resposta de falha terá o formato:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| error | Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação.  |
| error_codes | Uma lista de códigos de erro específicos do STS que pode ajudar no diagnóstico.  |
| timestamp | A hora na qual o erro ocorreu. |
| trace_id | Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico.  |
| correlation_id | Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico entre os componentes. |

## <a name="use-a-token"></a>Usar um token
Agora que você já adquiriu um token, pode usá-lo para fazer solicitações ao recurso.  Quando o token expira, basta repetir a solicitação para o ponto de extremidade `/token` a fim de adquirir um novo token de acesso.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro Tip: Try the below command out! (but replace the token with your own)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-sample"></a>Exemplo de código
Para ver um exemplo de aplicativo que implementa a concessão de client_credentials usando o ponto de extremidade de consentimento de administrador, confira nosso [exemplo de código de daemon para v2.0](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).


<!--HONumber=Oct16_HO2-->


