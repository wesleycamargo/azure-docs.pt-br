<properties
	pageTitle="O ponto de extremidade de v2.0 do Azure AD | Microsoft Azure"
	description="Uma comparação entre o Azure AD original e os pontos de extremidade v2.0."
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
	ms.date="09/16/2016"
	ms.author="dastrock"/>

# O que há de diferente no ponto de extremidade v2.0?

Se você estiver familiarizado com o Azure Active Directory ou se tiver integrado aplicativos com o Azure AD anteriormente, poderá encontrar algumas diferenças inesperadas ponto de extremidade v2.0. Este documento chama a atenção para essas diferenças para sua compreensão.

> [AZURE.NOTE]
	Nem todos os recursos e cenários do Azure Active Directory têm suporte no ponto de extremidade v2.0. Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).


## Contas da Microsoft e contas do Azure AD
O ponto de extremidade v2.0 permite aos desenvolvedores criar aplicativos que aceitam entrada de Contas da Microsoft e de Contas do Azure AD usando um único ponto de extremidade. Isso oferece a você a capacidade de escrever seu aplicativo completamente independente de conta; ele não precisa saber o tipo de conta com a qual o usuário entra. Obviamente, é *possível* deixar seu aplicativo com reconhecimento do tipo de conta que está sendo usada em uma sessão específica, mas isso não é necessário.

Por exemplo, se seu aplicativo chamar o [Microsoft Graph](https://graph.microsoft.io), alguns dados e funcionalidades adicionais estarão disponíveis para usuários corporativos, como os sites do SharePoint ou dados do Diretório. Porém, para várias ações, como [Ler o email de um usuário](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message), o código pode ser escrito exatamente da mesma forma para as Contas da Microsoft e do Azure AD.

Integrar seu aplicativo com as contas da Microsoft e do AD do Azure agora é um processo simples. É possível usar um único conjunto de pontos de extremidade, uma única biblioteca e um único registro de aplicativo para acessar os mundos empresarial e do consumidor. Para saber mais sobre o ponto de extremidade v2.0, confira [a visão geral](active-directory-appmodel-v2-overview.md).


## Novo portal de registro de aplicativos
o ponto de extremidade v2.0 só pode ser registrado em um novo local: [apps.dev.microsoft.com](https://apps.dev.microsoft.com). Esse é o portal em que é possível obter uma Id de aplicativo, personalizar a aparência da página de entrada do aplicativo e muito mais. Tudo que você precisa para acessar o portal é uma conta da plataforma Microsoft, seja ela pessoal ou corporativa/de estudante.

Continuaremos a adicionar cada vez mais recursos a este Portal de Registro de Aplicativos com o passar do tempo. A intenção é que esse portal seja o novo local no qual você possa ir para gerenciar tudo sobre seus aplicativos da Microsoft.


## Uma ID do aplicativo para todas as plataformas
No serviço do Azure Active Directory original, você pode ter registrado vários aplicativos diferentes para um único projeto. Você foi forçado a usar registros de aplicativo separados para seus clientes nativos e aplicativos Web:

![Interface do usuário do registro de aplicativo antigo](../media/active-directory-v2-flows/old_app_registration.PNG)

Por exemplo, se você criou um site da Web e um aplicativo iOS, era preciso registrá-los separadamente usando duas IDs de Aplicativo diferentes. Se você tiver um site e uma API Web de back-end, você pode ter registrado cada um deles como um aplicativo separado no AD do Azure. Se você tiver um aplicativo iOS e um aplicativo Android, você também pode ter registrado dois aplicativos diferentes.

<!-- You may have even registered different apps for each of your build environments - one for dev, one for test, and one for production. -->

Agora, tudo o que você precisa é de um registro único aplicativo e uma Id de aplicativo única para cada um dos seus projetos. É possível adicionar várias "plataformas" a cada projeto e fornecer os dados apropriados para cada plataforma que você adicionar. Claro, você pode criar quantos aplicativos desejar dependendo dos seus requisitos, mas, para a maioria dos casos, apenas uma Id de aplicativo deve ser necessária.

<!-- You can also label a particular platform as "production-ready" when it is ready to be published to the outside world, and use that same Application Id safely in your development environments. -->

Nosso objetivo é que isso resulte em uma experiência de desenvolvimento e gerenciamento de aplicativos mais simplificada e crie uma visão mais consolidada de um projeto simples no qual você estiver trabalhando.


## Escopos, não recursos
No serviço do Azure AD original, um aplicativo pode se comportar como um **recurso** ou um destinatário de tokens. Um recurso pode definir um número de **escopos** ou **oAuth2Permissions** que ele entende, permitindo que os aplicativos cliente solicitem tokens a esse recurso para um determinado conjunto de escopos. Considere a Graph API do AD do Azure como um exemplo de um recurso:

- Identificador de recurso, ou `AppID URI`: `https://graph.windows.net/`
- Escopos, ou `OAuth2Permissions`: `Directory.Read`, `Directory.Write`, etc.

Tudo isso se aplica para o ponto de extremidade v2.0. Um aplicativo ainda pode se comportar como recurso, definir escopos e ser identificado por um URI. Aplicativos cliente ainda podem solicitar acesso a esses escopos. No entanto, a maneira na qual um cliente solicita essas permissões foi alterada. No passado, uma solicitação de autorização de OAuth 2.0 ao AD do Azure teria esta aparência:

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

onde o parâmetro **resource** indicava o recurso para o qual o aplicativo cliente estava solicitando a autorização. O AD do Azure computava as permissões exigidas pelo aplicativo com base na configuração estática no Portal do Azure e emitia os tokens de acordo. Agora, a mesma solicitação de autorização de OAuth 2.0 se parece com o seguinte:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

onde o parâmetro **scope** indica quais recursos e permissões para o qual o aplicativo está solicitando a autorização. O recurso desejado ainda está muito presente na solicitação; ele simplesmente está englobado em cada um dos valores do parâmetro de escopo. Usar o parâmetro de escopo dessa maneira permite que o ponto de extremidade v2.0 seja mais compatível com a especificação OAuth 2.0 e esteja alinhado mais estreitamente com práticas comuns do setor. Ele também permite que os aplicativos realizem o [consentimento incremental](#incremental-and-dynamic-consent), que é descrito na próxima seção.

## Consentimento incremental e dinâmico
Aplicativos registrados no serviço do AD do Azure disponível ao público geral necessários para especificar as permissões necessárias do OAuth 2.0 no Portal do Azure, no momento da criação do aplicativo:

![Interface do usuário do registro de permissões](../media/active-directory-v2-flows/app_reg_permissions.PNG)

As permissões que um aplicativo precisava foram configuradas **estaticamente**. Embora isso tenha permitido que a configuração do aplicativo exista no Portal do Azure e tenha mantido o código agradável e simples, isso também apresenta alguns problemas para os desenvolvedores:

- No momento da criação do aplicativo, o aplicativo tinha que conhecer todas as permissões que pudesse precisar. Adicionar permissões ao longo do tempo era um processo difícil.
- Um aplicativo tinha que conhecer todos os recursos que fosse acessar antes do tempo. Era difícil criar aplicativos que pudessem acessar um número arbitrário de recursos.
- Um aplicativo tinha que solicitar todas as permissões que nunca seriam necessárias após a primeira entrada do usuário. Em alguns casos, isso resultava em uma lista muito longa de permissões, o que desencorajava os usuários finais a aprovarem o acesso do aplicativo na entrada inicial.

Com o ponto de extremidade v2.0, é possível especificar **dinamicamente** as permissões que o aplicativo precisa, no tempo de execução, durante o uso normal do aplicativo. Para fazer isso, é possível especificar os escopos que o aplicativo precisa em qualquer ponto no tempo incluindo-os no parâmetro `scope` de uma solicitação de autorização:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

O item acima solicita permissão para o aplicativo ler dados do diretório de um usuário do AD do Azure, bem como gravar dados no diretório dele. Se o usuário aceitou essas permissões no passado para esse aplicativo específico, eles simplesmente digitarão suas credenciais e entrarão no aplicativo. Se o usuário não aceitou alguma dessas permissões, o ponto de extremidade v2.0 solicitará ao usuário consentimento para essas permissões. Para obter mais informações, você pode ler sobre [permissões, autorização e escopos](active-directory-v2-scopes.md).

Permitir que um aplicativo solicite permissões dinamicamente por meio do parâmetro `scope` dá controle total a você sobre a experiência do usuário. Se desejar, você pode optar por antecipar a experiência de consentimento e pedir todas as permissões em uma solicitação de autorização inicial. Ou, se seu aplicativo precisar de um grande número de permissões, você pode optar por reunir essas permissões do usuário de forma incremental, à medida que ele tentar usar determinados recursos do seu aplicativo ao longo do tempo.

## Escopos conhecidos

#### Acesso offline
o ponto de extremidade v2.0 pode exigir o uso de uma nova permissão conhecida para aplicativos - o escopo de `offline_access`. Todos os aplicativos terão que solicitar essa permissão se precisarem acessar recursos em nome de um usuário por um longo período de tempo, mesmo quando o usuário pode não estiver usando o aplicativo de maneira ativa. O escopo `offline_access` será exibido para o usuário em caixas de diálogo de consentimento como "Acesso a seus dados offline", com as quais o usuário deverá concordar. Solicitar a permissão `offline_access` permitirá que seu aplicativo Web receba refresh\_tokens do OAuth 2.0 a partir do ponto de extremidade v2.0. Refresh\_tokens são duradouros e podem ser trocados por novos access\_tokens do OAuth 2.0 por longos períodos de acesso.

Se seu aplicativo não solicitar o escopo `offline_access`, ele não receberá refresh\_tokens. Isso significa que, quando você resgatar um authorization\_code no [fluxo de código de autorização do OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), você só receberá de volta um access\_token do ponto de extremidade `/token`. Esse access\_token permanecerá válido por um curto período de tempo (normalmente uma hora), mas acabará expirando. Nesse momento, seu aplicativo terá que redirecionar o usuário de volta ao ponto de extremidade `/authorize` para recuperar um novo authorization\_code. Durante esse redirecionamento, o usuário pode ou não precisar digitar suas credenciais novamente ou consentir de novo as permissões, dependendo do tipo do aplicativo.

Para saber mais sobre OAuth 2.0, refresh\_tokens e access\_tokens, consulte a [referência de protocolo da v2.0](active-directory-v2-protocols.md).

#### OpenID, perfil e email

No serviço Active Directory do Azure original, o fluxo de conexão mais básico do OpenID Connect forneceria uma grande quantidade de informações sobre o usuário do id\_token resultante. As declarações em um id\_token podem incluir o nome do usuário, nome de usuário preferido, endereço de email, ID do objeto e muito mais.

Agora estamos restringindo as informações que o escopo de `openid` permite que seu aplicativo acesse. O escopo de “openid” apenas permitirá que seu aplicativo faça logon do usuário e receba um identificador específico do aplicativo para o usuário. Se você quiser obter informações de identificação pessoal (PII) sobre o usuário em seu aplicativo, seu aplicativo precisará solicitar permissões adicionais do usuário. Estamos apresentando dois novos escopos, os escopos de `email` e `profile`, que permitem que você faça isso.

O escopo de `email` é muito simples. Ele permite que seu aplicativo acesse o endereço de email principal do usuário por meio da declaração `email` no id\_token. O escopo de `profile` permite que seu aplicativo acesse todas as outras informações básicas sobre o usuário: nome, nome de usuário preferido, ID do objeto, etc.

Isso permite que você codifique seu aplicativo com uma divulgação mínima. Você só pode solicitar ao usuário o conjunto de informações que seu aplicativo precisa para fazer seu trabalho. Para obter mais informações sobre os escopos, consulte [a referência de escopo da v2.0](active-directory-v2-scopes.md).

## Declarações de token

As declarações em tokens emitidos pelo ponto de extremidade v2.0 não serão idênticas aos tokens emitidos pelos pontos de extremidade do AD do Azure disponíveis ao público geral; os aplicativos que migrarem para o novo serviço não deverão presumir que uma declaração específica existirá em id\_tokens ou access\_tokens. Os tokens emitidos pelo ponto de extremidade v2.0 são compatíveis com as especificações OAuth 2.0 e OpenID Connect, mas podem seguir semânticas diferentes do serviço do AD do Azure disponível ao público geral.

Para saber mais sobre as declarações específicas emitidas em tokens da v2.0, consulte a [referência de token v2.0](active-directory-v2-tokens.md).

## Limitações
Há algumas restrições que merecem atenção ao usar o ponto v2.0. Consulte o [documento de limitações da v2.0](active-directory-v2-limitations.md) para ver se alguma dessas restrições se aplica ao seu cenário específico.

<!---HONumber=AcomDC_0921_2016-->