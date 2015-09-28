<properties
	pageTitle="Escopos, permissões e consentimento do modelo de aplicativo v2.0 | Microsoft Azure"
	description="Uma descrição de autorização no modelo de aplicativo v2.0 do AD do Azure, incluindo escopos, permissões e consentimento."
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

# Visualização do modelo de aplicativo v2.0: escopos, permissões e consentimento

Os aplicativos que se integram ao AD do Azure seguem um modelo de autorização específico que permite aos usuários controlar como um aplicativo pode acessar os respectivos dados. No modelo de aplicativo v2.0, a implementação desse modelo de autorização foi atualizada, mudando como um aplicativo deve interagir com o AD do Azure. Este tópico aborda os conceitos básicos deste modelo de autorização, incluindo escopos, permissões e consentimento.

> [AZURE.NOTE]Essas informações se aplicam à visualização pública do modelo de aplicativo v2.0. Para obter instruções sobre como integrar-se ao serviço AD do Azure disponível ao público geral, consulte o [Guia do desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md).

## Escopos e permissões

O modelo de aplicativo v2.0 implementa o protocolo de autorização [OAuth 2.0](active-directory-v2-protocols.md), que é um método de permitir que um aplicativo de terceiro acesse recursos hospedados na Web em nome de um usuário. Qualquer recurso hospedado na Web que se integre ao AD do Azure terá um identificador de recurso, ou **URI de ID de Aplicativo**. Por exemplo, alguns dos recursos hospedados na Web da Microsoft incluem:

- A API de Email Unificado do Office 365: `https://outlook.office.com`
- A API do Gerenciador de Recursos do Azure: `https://management.azure.com`
- A Graph API do AD do Azure: `https://graph.windows.net`

Isso se aplica a todos os recursos de terceiros que se integraram ao AD do Azure. Qualquer um desses recursos também pode definir um conjunto de permissões que pode ser usado para dividir a funcionalidade desse recurso em partes menores. Por exemplo, a API de Email Unificado do Office 365 definiu essas permissões básicas:

- Ler a caixa de correio de um usuário
- Escrever para uma caixa de correio de um usuário
- Enviar email como um usuário

Ao definir essas permissões, o recurso pode ter um controle refinado sobre seus dados e como ele é exposto ao mundo externo. Um aplicativo de terceiros pode solicitar essas permissões de um usuário final e este deve aprovar as permissões para que o aplicativo possa agir em nome dele. Ao dividir a funcionalidade do recurso em conjuntos menores de permissão, os aplicativos de terceiros podem ser criados para solicitar apenas as permissões específicas que eles precisam para realizar suas tarefas. Isso também permite aos usuários finais saber exatamente como um aplicativo usará seus dados, de modo que eles se sentem mais seguros de que o aplicativo não está se comportando com más intenções.

No AD do Azure e no OAuth, essas permissões são conhecidas como **escopos**. Elas também podem ser mencionadas como **oAuth2Permissions**. Um escopo é representado no AD do Azure como um valor de cadeia de caracteres. Continuando com o exemplo da API de Email Unificado do Office 365, o valor do escopo para cada permissão é:

- Ler a caixa de correio de um usuário: `Mail.Read`
- Escrever para uma caixa de correio de um usuário: `Mail.ReadWrite`
- Enviar email como um usuário: `Mail.Send`

Um aplicativo pode solicitar essas permissões especificando os escopos em solicitações para o ponto de extremidade v2.0, conforme descrito abaixo.

## Consentimento

Em uma solicitação de autorização do [OpenID Connect ou OAuth 2.0](active-directory-v2-protocols.md), um aplicativo pode solicitar as permissões que precisa usando o parâmetro de consulta `scope`. Por exemplo, quando um usuário entra em um aplicativo, o aplicativo pode enviar uma solicitação como a seguinte (com quebras de linhas para legibilidade):

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Foutlook.office.com%2Fmail.read%20
https%3A%2F%2Fgraph.windows.net%2Fmail.send
&state=12345
```

O parâmetro `scope` é uma lista de escopos separados por espaço que o aplicativo está solicitando. Cada escopo individual é indicado acrescentando o valor de escopo para o identificador do recurso (URI de ID de Aplicativo). A solicitação acima indica que o aplicativo precisa de permissão para ler a caixa de correio do usuário e enviar emails como o usuário.

Depois que o usuário insere suas credenciais, o ponto de extremidade v2.0 verifica se há um registro correspondente do **consentimento de usuário**. Se o usuário não tiver consentido nenhuma das permissões solicitadas no passado, o ponto de extremidade v2.0 pedirá que o usuário as conceda.

![Captura de tela de consentimento da conta corporativa](../media/active-directory-v2-flows/work_account_consent.png)

Quando o usuário aprovar a permissão, o consentimento será registrado para que o usuário não tenha que consentir novamente em conexões subsequentes.

## Consentimento incremental

O aplicativo não precisa pedir cada permissão que precisa na conexão ou assinatura do usuário inicial. Como você pode especificar escopos por solicitação, o aplicativo pode executar "consentimento incremental" e escolher quando será a melhor hora de pedir consentimento ao usuário. Existem alguns motivos comuns pelos quais um aplicativo pode não pedir todas as permissões de uma vez:

- Os aplicativos que precisam de várias permissões. Se o aplicativo acessar muitos recursos diferentes e executar funcionalidade avançada em cada um, a lista de permissões que o aplicativo precisa pode se tornar longa muito rapidamente. Historicamente, listas longas de permissões têm desestimulado os usuários a assinar para receber um aplicativo e diminuído a adoção do usuário. Em vez de solicitar essas permissões ao mesmo tempo, seu aplicativo pode pedir um subconjunto na conexão inicial e, de modo incremental, pedir permissões adicionais conforme o usuário tenta usar recursos avançados.
- Aplicativos que crescem ao longo do tempo. Quase todos os aplicativos com um conjunto menor de funcionalidades e crescem com o tempo incorporam novos recursos. Com consentimento incremental, você pode fazer alterações com facilidade no aplicativo e solicitar sem percalços novas permissões do usuário. Você pode simplesmente atualizar seu código para enviar escopos adicionais em solicitações de autorização.
- Permissões somente de administrador. Alguns recursos definirão permissões que **apenas** o administrador de uma organização poderá consentir, ou aprovar. Por exemplo, a Graph API do AD do Azure define a permissão `Directory.Write`, que permite a um aplicativo criar, atualizar e excluir usuários e grupos, entre outras coisas. Você pode imaginar porque essa permissão pode exigir aprovação de um administrador altamente privilegiado. Ao usar o consentimento incremental, o aplicativo pode expor um conjunto básico de funcionalidades que qualquer usuário pode assinar sem a aprovação de um administrador. No entanto, quando ele tenta executar uma ação altamente privilegiada, você pode solicitar a permissão somente de administrador e exigir que um administrador assine para poder acessar essa parte do aplicativo.

## Usando permissões

Depois que o usuário consente permissões para o aplicativo, este pode adquirir tokens de acesso que representam a permissão do seu aplicativo para acessar um recurso em alguma capacidade. Um determinado token de acesso só pode ser usado para um único recurso, mas codificada dentro dele estará cada permissão que o aplicativo recebeu para esse recurso. Para adquirir um token de acesso, o aplicativo pode fazer uma solicitação ao ponto de extremidade do token v2.0.

```
POST common/v2.0/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "2d4d11a2-f814-46a7-890a-274a72a7309e",
	"scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
	"client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

O token de acesso resultante pode ser usado em solicitações HTTP para o recurso – ele indicará com segurança para o recurso que o aplicativo tem permissão apropriada para executar uma determinada tarefa.

Para obter mais detalhes sobre o protocolo OAuth 2.0 e como adquirir tokens de acesso, consulte a [referência ao protocolo do modelo de aplicativo v2.0](active-directory-v2-protocols.md).

## OpenId e Offline\_Access

O modelo de aplicativo v2.0 tem dois escopos bem definidos que não se aplicam a um recurso específico —`openid` e `offline_access`.

#### OpenId

Se um aplicativo fizer conexão usando o [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow), ele deverá solicitar o escopo `openid`. O escopo `openid` aparecerá na tela de consentimento da conta corporativa como a permissão "Conectar você" e na tela de consentimento da conta pessoal da Microsoft como a permissão "Exibir seu perfil e se conectar a aplicativos e serviços usando sua conta da Microsoft". Essa permissão permite a um aplicativo acessar o ponto de extremidade das informações do usuário do OpenID Connect e, portanto, exige aprovação do usuário. O escopo `openid` também pode ser usado no ponto de extremidade v2.0 para adquirir id\_tokens, que podem ser usados para proteger chamadas HTTP entre diferentes componentes de um aplicativo.

#### Offline\_Access

O escopo `offline_access` permite ao aplicativo acessar recursos em nome do usuário por um período estendido. Na tela de consentimento de conta corporativa, esse escopo aparecerá como a permissão "Acessar dados a qualquer momento". Na tela de consentimento de conta pessoal da Microsoft, ele aparecerá como a permissão "Acessar dados a qualquer momento". Quando um usuário aprovar o escopo `offline_access`, o aplicativo será habilitado para receber tokens de atualização do ponto de extremidade do token v2.0. Os tokens de atualização têm longa duração e permitem ao aplicativo adquirir novos tokens de acesso à medida que os antigos expiram.

Se o aplicativo não solicitar o escopo `offline_access`, ele não receberá refresh\_tokens. Isso significa que, quando você resgatar um authorization\_code no [fluxo do código de autorização do OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), você só receberá de volta um access\_token do ponto de extremidade `/token`. Esse access\_token permanecerá válido por um curto período de tempo (normalmente uma hora), mas acabará expirando. Nesse momento, seu aplicativo terá que redirecionar o usuário de volta ao ponto de extremidade `/authorize` para recuperar um novo authorization\_code. Durante esse redirecionamento, o usuário pode ou não precisar digitar suas credenciais novamente ou consentir de novo as permissões, dependendo do tipo do aplicativo.

Para obter mais informações sobre como obter e usar tokens de atualização, consulte a [referência ao protocolo do modelo de aplicativo v2.0](active-directory-v2-protocols.md).

<!---HONumber=Sept15_HO3-->