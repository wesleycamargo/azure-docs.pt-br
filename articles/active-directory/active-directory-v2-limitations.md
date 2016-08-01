<properties
	pageTitle="Limitações e restrições do ponto de extremidade da v2.0 | Microsoft Azure"
	description="Uma lista de limitações e restrições com o ponto de extremidade da v2.0 do Azure AD."
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
	ms.date="03/18/2016"
	ms.author="dastrock"/>

# Devo usar o ponto de extremidade da v2.0?

Ao criar aplicativos que se integram ao Active Directory do Azure, você precisará decidir se os protocolos de autenticação e o ponto de extremidade da v2.0 atendem às suas necessidades. O modelo de aplicativo do Azure AD original ainda tem suporte completo e, em alguns aspectos, tem mais recursos do que a v2.0. No entanto, o ponto de extremidade da v2.0 [oferece benefícios consideráveis](active-directory-v2-compare.md) para desenvolvedores que podem convencê-lo a usar o novo modelo de programação. Ao longo do tempo, a v2.0 será expandida para abranger todos os recursos do Azure AD, para que você só precise usar o ponto de extremidade da v2.0.

No momento, há dois recursos principais que você pode obter com o ponto de extremidade da v2.0:

- Conectar usuários com contas pessoais e de trabalho.
- Chamar a [API do Outlook convergida](https://dev.outlook.com).

Esses dois recursos podem ser implementados em aplicativos Web, móveis e para PC. Se esses recursos relativamente limitados fazem sentido para seu aplicativo, é recomendável usar o ponto de extremidade da v2.0. Se seu aplicativo requer recursos adicionais de serviços da Microsoft, recomendamos que você continue a usar os pontos de extremidade comprovados do Azure AD e a conta da Microsoft. Futuramente, os pontos de extremidade da v2.0 substituirão o Azure AD e a conta da Microsoft, e ajudaremos todos os desenvolvedores a passar para o ponto de extremidade da v2.0.

Enquanto isso, este artigo destina-se a ajudá-lo a determinar se o ponto de extremidade da v 2.0 é adequado para você. Continuaremos a atualizar este artigo ao longo do tempo para refletir o estado atual do ponto de extremidade da v2.0. Por isso, confira-o novamente para reavaliar seus requisitos em relação aos recursos da v2.0.

Se você tem um aplicativo existente com o Azure AD que não usa o ponto de extremidade da v2.0, não é necessário começar do zero. No futuro, forneceremos uma maneira de habilitar seus aplicativos existentes do Azure AD para uso com o ponto de extremidade da v2.0.

## Restrições quanto a aplicativos
Os seguintes tipos de aplicativos atualmente não têm suporte no ponto de extremidade da v2.0. Para obter uma descrição dos tipos de aplicativos compatíveis, consulte [este artigo](active-directory-v2-flows.md).

##### APIs da Web autônomas
Com o ponto de extremidade da v2.0, você tem a capacidade de [criar uma API Web protegida usando OAuth 2.0](active-directory-v2-flows.md#web-apis). No entanto, essa API Web só poderá receber tokens de um aplicativo que compartilhe a mesma ID de aplicativo. Não há suporte para a criação de uma API Web que é acessada de um cliente com uma Id de Aplicativo diferente. Esse cliente não poderá solicitar nem obter permissões para sua API Web.

Para ver como criar uma API Web que aceita tokens de um cliente com a mesma Id de Aplicativo, confira os exemplos de API Web do ponto de extremidade da v 2.0 na seção [Introdução](active-directory-appmodel-v2-overview.md#getting-started).

##### Aplicativos do lado do servidor/daemons
Os aplicativos que contêm processos de longa duração ou que operem sem a presença de um usuário também precisam de uma maneira de acessar recursos protegidos, como APIs da Web. Esses aplicativos podem se autenticar e obter tokens usando a identidade do aplicativo (em vez de a identidade delegada de um usuário) usando o fluxo de credenciais do cliente OAuth 2.0.

Esse fluxo atualmente não tem suporte no ponto de extremidade da v2.0. Isso significa que os aplicativos só podem obter tokens após a ocorrência de um fluxo de entrada de usuário interativo. O fluxo de credenciais de cliente será adicionado em breve. Se quiser ver o fluxo de credenciais de cliente usando o ponto de extremidade original do Azure AD, confira o [exemplo de Daemon no GitHub](https://github.com/AzureADSamples/Daemon-DotNet).

##### Fluxo em nome de da API Web
Muitas arquiteturas incluem uma API Web que precisa chamar outra API Web downstream, ambas protegidas pelo ponto de extremidade da v2.0. Esse cenário é comum em clientes nativos que têm um back-end de API Web que, por sua vez, chama um serviço Microsoft Online ou outra API Web personalizada que dá suporte ao Azure AD.

Esse cenário pode ter suporte usando a concessão Credencial de Portador Jwt do OAuth 2.0, também conhecido como fluxo Em Nome de. No entanto, o fluxo Em Nome de não tem suporte no ponto de extremidade da v2.0. Para ver como esse fluxo funciona no serviço do AD do Azure disponível ao público geral, confira o [exemplo de código Em Nome De no GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

## Restrições quanto a registros de aplicativos
No momento, todos os aplicativos que desejam integrar-se ao ponto de extremidade da v2.0 devem criar um novo registro de aplicativo em [apps.dev.microsoft.com](https://apps.dev.microsoft.com). Nenhum aplicativo existente da Conta da Microsoft ou do Azure AD será compatível com o ponto de extremidade da v2.0, nem o serão os aplicativos registrados em qualquer portal além do novo Portal de Registro de Aplicativo. Planejamos fornecer uma maneira de habilitar aplicativos existentes para uso como um aplicativo da v2.0, mas, no momento, não há um caminho de migração para um aplicativo para o ponto de extremidade da v2.0.

Da mesma forma, os aplicativos registrados no novo Portal de Registro de Aplicativo não funcionarão em relação ao ponto de extremidade de autenticação original do Azure AD. Porém, você pode usar aplicativos criados no Portal de Registro de Aplicativo para serem integrados com êxito ao ponto de extremidade de autenticação da conta da Microsoft, `https://login.live.com`.

Aplicativos que são registrados no novo Portal de Registro de Aplicativo são atualmente restritos a um conjunto limitado de valores redirect\_uri. O redirect\_uri para serviços e aplicativos Web deve começar com o esquema ou `https`, enquanto o redirect\_uri para todas as outras plataformas deve usar o valor embutido em código de `urn:ietf:oauth:2.0:oob`.

## Restrições em URIs de redirecionamento
Para aplicativos Web, todos os valores de redirect\_uri devem compartilhar um único domínio DNS. Por exemplo, não é possível registrar um aplicativo Web que tenha redirect\_uris:

`https://login-east.contoso.com` `https://login-west.contoso.com`

O sistema de registro compara o nome DNS completo do redirect\_uri existente ao nome DNS do redirect\_uri que você está adicionando. Se o nome DNS completo do redirect\_uri novo não corresponder exatamente ao nome DNS do redirect\_uri existente, ou se o nome completo do DNS do redirect\_uri novo não for um subdomínio do redirect\_uri existente, a solicitação para adicionar falhará. Por exemplo, se o aplicativo tiver redirect\_uri:

`https://login.contoso.com`

Então será possível adicionar:

`https://login.contoso.com/new`

que corresponda exatamente ao nome DNS ou:

`https://new.login.contoso.com`

que é um subdomínio DNS de login.contoso.com. Se você quiser ter um aplicativo com login-east.contoso.com e login-west.contoso.com como redirect\_uris, deverá adicionar o seguinte redirect\_uris na ordem:

`https://contoso.com` `https://login-east.contoso.com` `https://login-west.contoso.com`

Os dois últimos podem ser adicionados porque eles são subdomínios do primeiro redirect\_uri, contoso.com. Essa limitação será removida em uma versão futura.

Para saber como registrar um aplicativo no novo Portal de Registro de Aplicativo, consulte [este artigo](active-directory-v2-app-registration.md).

## Restrições quanto a serviços e APIs
Atualmente, o ponto de extremidade da v2.0 dá suporte à entrada para qualquer aplicativo registrado no novo Portal de Registro de Aplicativo, desde que ele se enquadre na lista de [fluxos de autenticação com suporte](active-directory-v2-flows.md). No entanto, esses aplicativos só poderão adquirir tokens de acesso do OAuth 2.0 para um conjunto muito limitado de recursos. O ponto de extremidade v2.0 emitirá somente access\_tokens para:

- O aplicativo que solicitou o token. Um aplicativo pode adquirir um access\_token para si mesmo, se o aplicativo lógico é constituído de várias camadas ou componentes diferentes. Para ver esse cenário em ação, confira nossos tutoriais de [Introdução](active-directory-appmodel-v2-overview.md#getting-started).
- As APIs REST do Outlook Mail, Calendar e Contacts, que estão localizadas em https://outlook.office.com. Para saber como escrever um aplicativo que acessa essas APIs, consulte estes tutoriais de [Introdução ao Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).
- As APIs do Microsoft Graph. Para saber mais sobre o Microsoft Graph e todos os dados que estão disponíveis, acesse [https://graph.microsoft.io](https://graph.microsoft.io).

Nenhum outro serviço tem suporte no momento. Mais serviços Microsoft Online serão adicionados no futuro, bem como suporte para seus próprios serviços e APIs Web personalizados.

## Restrições quanto a bibliotecas e SDKs
Para ajudá-lo a experimentar itens, fornecemos uma versão experimental da Biblioteca de Autenticação do Active Directory que é compatível com o ponto de extremidade da v2.0. No entanto, essa versão do ADAL está em um estado de visualização. Ela não tem suporte e será alterada significativamente nos próximos meses. Há exemplos de código que usam a ADAL para .NET, iOS, Android e Javascript disponíveis na seção [Introdução](active-directory-appmodel-v2-overview.md#getting-started), se você deseja colocar um aplicativo em execução rapidamente com o ponto de extremidade da v2.0.

Se deseja usar o ponto de extremidade da v2.0 em um aplicativo de produção, você tem as seguintes opções:

- Se está criando um aplicativo Web, você pode usar com segurança nosso middleware do servidor disponível para realizar a entrada e a validação de token. Isso inclui o middleware OWIN Open ID Connect para ASP.NET e o plug-in NodeJS Passport. Exemplos de código que usam esses middlewares também estão disponíveis na seção [Introdução](active-directory-appmodel-v2-overview.md#getting-started).
- Para outras plataformas e aplicativos móveis e nativos, você também pode fazer a integração com o ponto de extremidade da v2.0 enviando e recebendo diretamente mensagens de protocolo no código do aplicativo. Os protocolos OAuth e OpenID Connect da v2.0 [foram explicitamente documentados](active-directory-v2-protocols.md) para ajudar você a executar essa integração.
- Finalmente, você pode usar bibliotecas de software livre do Open ID Connect e do OAuth para fazer a integração com o ponto de extremidade da v2.0. O protocolo da v2.0 deve ser compatível com muitas bibliotecas de protocolo de software livre sem grandes alterações. A disponibilidade dessas bibliotecas varia por linguagem e plataforma, e os sites do [Open ID Connect](http://openid.net/connect/) e do [OAuth 2.0](http://oauth.net/2/) mantêm uma lista de implementações populares. Abaixo estão listados os exemplos e as bibliotecas de clientes de software livre que foram testados com o ponto de extremidade da v2.0.

  - [Servidor de Identidade Java WSO2](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server)
  - [Java Gluu Federation](https://github.com/GluuFederation/oxAuth)
  - [Node.Js passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect)
  - [Cliente Básico do OpenID Connect PHP](https://github.com/jumbojett/OpenID-Connect-PHP)
  - [Cliente OAuth2 do iOS](https://github.com/nxtbgthng/OAuth2Client)
  - [Cliente OAuth2 do Android](https://github.com/wuman/android-oauth-client)
  - [Cliente OpenID Connect do Android](https://github.com/kalemontes/OIDCAndroidLib)

## Restrições quanto a protocolos
O ponto de extremidade da v2.0 dá suporte apenas a Open ID Connect e OAuth 2.0. No entanto, nem todos os recursos e capacidades de cada protocolo foram incorporados ao ponto de extremidade da v2.0. Alguns exemplos incluem:

- O `end_sesssion_endpoint` do OpenID Connect
- A concessão de credenciais de cliente do OAuth 2.0

Para entender melhor o escopo da funcionalidade de protocolo com suporte no ponto de extremidade v2.0, leia nossa [Referência de protocolo do OpenID Connect e OAuth 2.0](active-directory-v2-protocols.md).

## Recursos de desenvolvedor avançados do Azure AD
Há um conjunto de recursos de desenvolvedor disponíveis no serviço do Active Directory do Azure que ainda não têm suporte no ponto de extremidade da v2.0, incluindo:

- Declarações de grupo para usuários do Azure AD
- Funções de aplicativo e declarações de função

<!---HONumber=AcomDC_0720_2016-->