<properties
	pageTitle="Escopos, permissões e consentimento do Azure AD v2.0 | Microsoft Azure"
	description="Uma descrição da autorização no ponto de extremidade v2.0 do Azure AD, incluindo escopos, permissões e consentimento."
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
	ms.date="09/26/2016"
	ms.author="dastrock"/>

# Escopos, permissões e consentimento no ponto de extremidade v2.0

Os aplicativos que se integram ao AD do Azure seguem um modelo de autorização específico que permite aos usuários controlar como um aplicativo pode acessar os respectivos dados. A implementação v2.0 desse modelo de autorização foi atualizada, alterando a maneira como um aplicativo deve interagir com o Azure AD. Este tópico aborda os conceitos básicos deste modelo de autorização, incluindo escopos, permissões e consentimento.

> [AZURE.NOTE]
	Nem todos os recursos e cenários do Azure Active Directory têm suporte no ponto de extremidade v2.0. Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).

## Escopos e permissões

O Azure AD implementa o protocolo de autorização [OAuth 2.0](active-directory-v2-protocols.md), que é um método de permitir que um aplicativo de terceiros acesse recursos hospedados na Web em nome de um usuário. Qualquer recurso hospedado na Web que se integre ao AD do Azure terá um identificador de recurso, ou **URI de ID de Aplicativo**. Por exemplo, alguns dos recursos hospedados na Web da Microsoft incluem:

- A API de Email Unificado do Office 365: `https://outlook.office.com`
- A Graph API do AD do Azure: `https://graph.windows.net`
- O Microsoft Graph: `https://graph.microsoft.com`

Isso se aplica a todos os recursos de terceiros que se integraram ao AD do Azure. Qualquer um desses recursos também pode definir um conjunto de permissões que pode ser usado para dividir a funcionalidade desse recurso em partes menores. Por exemplo, o Microsoft Graph tem algumas permissões definidas:

- Ler o calendário de um usuário
- Escrever no calendário de um usuário
- Enviar email como um usuário
- [muito mais](https://graph.microsoft.io)

Ao definir essas permissões, o recurso pode ter um controle refinado sobre seus dados e como ele é exposto ao mundo externo. Um aplicativo de terceiros pode solicitar essas permissões de um usuário final e este deve aprovar as permissões para que o aplicativo possa agir em nome dele. Ao dividir a funcionalidade do recurso em conjuntos menores de permissão, os aplicativos de terceiros podem ser criados para solicitar apenas as permissões específicas que eles precisam para realizar suas tarefas. Isso também permite aos usuários finais saber exatamente como um aplicativo usará seus dados, de modo que eles se sentem mais seguros de que o aplicativo não está se comportando com más intenções.

No AD do Azure e no OAuth, essas permissões são conhecidas como **escopos**. Elas também podem ser mencionadas como **oAuth2Permissions**. Um escopo é representado no AD do Azure como um valor de cadeia de caracteres. Continuando com o exemplo do Microsoft Graph,o valor do escopo para cada permissão é:

- Ler o calendário de um usuário: `Calendar.Read`
- Escrever no calendário de um usuário: `Mail.ReadWrite`
- Enviar email como um usuário: `Mail.Send`

Um aplicativo pode solicitar essas permissões especificando os escopos em solicitações para o ponto de extremidade v2.0, conforme descrito abaixo.

## Escopos do OpenId Connect

A implementação v2.0 do OpenID Connect tem alguns escopos bem definidos que não se aplicam a nenhum recurso específico - `openid`, `email`, `profile` e `offline_access`.

#### OpenId

Se um aplicativo fizer conexão usando o [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow), ele deverá solicitar o escopo `openid`. O escopo `openid` aparecerá na tela de consentimento da conta corporativa como a permissão "Conectar você" e na tela de consentimento da conta pessoal da Microsoft como a permissão "Exibir seu perfil e se conectar a aplicativos e serviços usando sua conta da Microsoft". Essa permissão permite que um aplicativo receba um identificador exclusivo para o usuário na forma da declaração `sub`. Ela também permite ao aplicativo acesso ao ponto de extremidade de informações do usuário. O escopo `openid` também pode ser usado no ponto de extremidade v2.0 para adquirir id\_tokens, que podem ser usados para proteger chamadas HTTP entre diferentes componentes de um aplicativo.

#### Email

O escopo de `email` pode ser incluído junto com o escopo de `openid` e quaisquer outros. Ele concede ao aplicativo acesso ao endereço de email principal do usuário na forma da declaração `email`. A declaração `email` só será incluída nos tokens se um endereço de email estiver associado à conta de usuário, o que nem sempre é o caso. Se estiver usando o escopo de `email`, seu aplicativo deverá estar preparado para lidar com casos em que a declaração `email` não existe no token.

#### Perfil

O escopo de `profile` pode ser incluído junto com o escopo de `openid` e quaisquer outros. Ele permite que o aplicativo acesse uma grande quantidade de informações sobre o usuário. Isso inclui, mas sem limitação, o nome, sobrenome, nome de usuário preferido, ID de objeto e outras informações do usuário. Para obter uma lista completa das declarações de perfil disponíveis em id\_tokens para um determinado usuário, consulte a [referência do token v2.0](active-directory-v2-tokens.md).

#### Offline\_access

O [escopo de `offline_access`](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) permite ao aplicativo acessar recursos em nome do usuário por um período prolongado. Na tela de consentimento de conta corporativa, esse escopo aparecerá como a permissão "Acessar dados a qualquer momento". Na tela de consentimento de conta pessoal da Microsoft, ele aparecerá como a permissão "Acessar dados a qualquer momento". Quando um usuário aprovar o escopo `offline_access`, o aplicativo será habilitado para receber tokens de atualização do ponto de extremidade do token v2.0. Os tokens de atualização têm longa duração e permitem ao aplicativo adquirir novos tokens de acesso à medida que os antigos expiram.

Se o aplicativo não solicitar o escopo `offline_access`, ele não receberá refresh\_tokens. Isso significa que, quando você resgatar um authorization\_code no [fluxo de código de autorização do OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), você só receberá de volta um access\_token do ponto de extremidade `/token`. Esse access\_token permanecerá válido por um curto período de tempo (normalmente uma hora), mas acabará expirando. Nesse momento, seu aplicativo terá que redirecionar o usuário de volta ao ponto de extremidade `/authorize` para recuperar um novo authorization\_code. Durante esse redirecionamento, o usuário pode ou não precisar digitar suas credenciais novamente ou consentir de novo as permissões, dependendo do tipo do aplicativo.

Para obter mais informações sobre como obter e usar tokens de atualização, consulte a [referência do protocolo v2.0](active-directory-v2-protocols.md).


## Solicitando consentimento de usuário individual

Em uma solicitação de autorização do [OpenID Connect ou OAuth 2.0](active-directory-v2-protocols.md), um aplicativo pode solicitar as permissões que precisa usando o parâmetro de consulta `scope`. Por exemplo, quando um usuário entra em um aplicativo, o aplicativo pode enviar uma solicitação como a seguinte (com quebras de linhas para legibilidade):

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendar.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

O parâmetro `scope` é uma lista de escopos separados por espaço que o aplicativo está solicitando. Cada escopo individual é indicado acrescentando o valor de escopo para o identificador do recurso (URI de ID de Aplicativo). A solicitação acima indica que o aplicativo precisa de permissão para ler o calendário e enviar emails como o usuário.

Depois que o usuário insere suas credenciais, o ponto de extremidade v2.0 verifica se há um registro correspondente do **consentimento de usuário**. Se o usuário não tiver consentido nenhuma das permissões solicitadas no passado, o ponto de extremidade v2.0 pedirá que o usuário as conceda.

![Captura de tela de consentimento da conta corporativa](../media/active-directory-v2-flows/work_account_consent.png)

Quando o usuário aprovar a permissão, o consentimento será registrado para que o usuário não tenha que consentir novamente em conexões subsequentes.

## Solicitando consentimento para um locatário inteiro

Geralmente, quando uma organização adquire uma licença ou assinatura para um aplicativo, ela deseja provisioná-la totalmente para seus funcionários. Como parte desse processo, um administrador da empresa pode autorizar que o aplicativo atue em nome de todos os funcionários. Ao conceder autorização para um locatário inteiro, os funcionários da organização não verão a tela de consentimento para o aplicativo.

Para solicitar consentimento para todos os usuários em um locatário, seu aplicativo pode usar o **ponto de extremidade de consentimento de administrador** descrito abaixo.

## Escopos restritos ao administrador

Determinadas permissões de alto privilégio no ecossistema da Microsoft podem ser marcados como **restritas ao administrador**. Exemplos de tais escopos incluem:

- Leitura de dados do diretório de uma organização: `Directory.Read`
- Gravação de dados no diretório da organização: `Directory.ReadWrite`
- Leitura de grupos de segurança no diretório da organização: `Groups.Read.All`

Embora um usuário consumidor possa conceder acesso de aplicativo para esses dados, os usuários organizacionais são impedidos de conceder acesso ao mesmo conjunto de dados confidenciais da empresa. Se seu aplicativo solicita acesso a uma dessas permissões de um usuário organizacional, o usuário receberá uma mensagem de erro indicando que não está autorizado a consentir com as permissões de seu aplicativo.

Se seu aplicativo requer acesso a esses escopos restritos a administradores para as organizações, você deve solicitá-los diretamente de um administrador da empresa também usando o **ponto de extremidade de consentimento do administrador** descrito abaixo.

Quando um administrador concede essas permissões pelo ponto de extremidade de consentimento do administrador, a permissão é concedida para todos os usuários no locatário, conforme descrito acima.

## Usando o ponto de extremidade de consentimento do administrador

Seguindo essas etapas, seu aplicativo será capaz de obter permissões para todos os usuários em determinado locatário, incluindo escopos restritos ao administrador. Para ver um exemplo de código que implementa as etapas descritas abaixo, consulte o [exemplo de escopos restritos ao administrador](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

#### Solicitar as permissões no portal de registro do aplicativo

- Navegue até o aplicativo em [apps.dev.microsoft.com](https://apps.dev.microsoft.com) ou [crie um aplicativo](active-directory-v2-app-registration.md), se ainda não fez isso.
- Localize a seção **Permissões do Microsoft Graph** e adicione as permissões que seu aplicativo requer.
- **Salve** o registro do aplicativo

#### Recomendado: conectar o usuário ao aplicativo

Normalmente, ao criar um aplicativo que usa o ponto de extremidade de consentimento de aplicativo, ele precisará ter uma página/exibição que permita ao administrador aprovar as permissões do aplicativo. Essa página pode ser parte do fluxo de entrada do aplicativo, parte das configurações do aplicativo ou um fluxo dedicado de "conexão". Em muitos casos, faz sentido que o aplicativo somente mostre o modo de exibição "conectar" depois que o usuário entra com uma conta corporativa ou de estudante da Microsoft.

A conexão do usuário ao aplicativo permite que você identifique a organização à qual o administrador pertence antes de pedir a ele que aprove as permissões necessárias. Embora não seja estritamente necessário, isso pode ajudá-lo a criar uma experiência mais intuitiva para os usuários empresariais. Para conectar o usuário, siga nossos [tutoriais de protocolo v2.0](active-directory-v2-protocols.md).

#### Solicitar permissões de um administrador de diretório

Quando você estiver pronto para solicitar permissões de administrador da empresa, redirecione o usuário para o **ponto de extremidade de consentimento do administrador** v2.0.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissons
```

```
// Pro Tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| locatário | obrigatório | O locatário do diretório para o qual você deseja solicitar permissão. Pode ser fornecido no formato de nome amigável ou GUID. |
| client\_id | obrigatório | A Id de Aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) atribuiu ao aplicativo. |
| redirect\_uri | obrigatório | O redirect\_uri onde você deseja que a resposta seja enviada para ser tratada pelo aplicativo. Ele deve corresponder exatamente a um dos redirect\_uris que você registrou no portal. |
| state | recomendável | Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. O estado é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |

Neste ponto, o Azure AD impõe que somente um administrador de locatário pode entrar para concluir a solicitação. O administrador deverá aprovar todas as permissões que você solicitou para o aplicativo no portal de registro.

##### Resposta bem-sucedida
Se o administrador aprovar as permissões para o seu aplicativo, a resposta bem-sucedida será:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| locatário | O locatário do diretório que concedeu as permissões solicitadas, no formato GUID. |
| state | Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. O estado é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| admin\_consent | Será definido como `True`. |



##### Resposta de erro
Se o administrador não aprovar as permissões para o seu aplicativo, a resposta de falha será:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| error | Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error\_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro. |

Depois de receber uma resposta bem-sucedida do ponto de extremidade de consentimento do administrador, o aplicativo terá as permissões solicitadas por ele. Agora, você pode solicitar um token para o recurso desejado, conforme descrito abaixo.

## Usando permissões

Depois que o usuário consente permissões para o aplicativo, este pode adquirir tokens de acesso que representam a permissão do seu aplicativo para acessar um recurso em alguma capacidade. Um determinado token de acesso só pode ser usado para um único recurso, mas codificada dentro dele estará cada permissão que o aplicativo recebeu para esse recurso. Para adquirir um token de acesso, o aplicativo pode fazer uma solicitação ao ponto de extremidade do token v2.0.

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
	"scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
	"redirect_uri": "https://localhost/myapp",
	"client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

O token de acesso resultante pode ser usado em solicitações HTTP para o recurso – ele indicará com segurança para o recurso que o aplicativo tem permissão apropriada para executar uma determinada tarefa.

Para obter mais detalhes sobre o protocolo OAuth 2.0 e como adquirir tokens de acesso, consulte a [referência do protocolo do ponto de extremidade v2.0](active-directory-v2-protocols.md).

<!---HONumber=AcomDC_0928_2016-->