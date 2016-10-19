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
	ms.date="09/27/2016"
	ms.author="dastrock"/>

# Devo usar o ponto de extremidade da v2.0?

Ao criar aplicativos que se integram ao Azure Active Directory, você precisará decidir se os protocolos de autenticação e o ponto de extremidade v2.0 atendem às suas necessidades. O modelo de ponto de extremidade do Azure AD original ainda tem suporte completo e, em alguns aspectos, tem mais recursos do que o v2.0. No entanto, o ponto de extremidade da v2.0 [oferece benefícios consideráveis](active-directory-v2-compare.md) para desenvolvedores que podem convencê-lo a usar o novo modelo de programação.

No momento, nossa recomendação sobre o uso do ponto de extremidade v2.0 é a seguinte:

- Se você deseja dar suporte a contas pessoais da Microsoft em seu aplicativo, use o ponto de extremidade v2.0. Mas entenda as limitações listadas neste artigo, especialmente aquelas relativas às contas corporativas e de estudante.
- Se seu aplicativo requer apenas o suporte de contas corporativas e de estudante, você deve usar [os pontos de extremidade originais do Azure AD](active-directory-developers-guide.md).

No futuro, o ponto de extremidade v2.0 vai expandir e eliminar as restrições listadas aqui, para que você só precise usar o ponto de extremidade v2.0. Enquanto isso, este artigo destina-se a ajudá-lo a determinar se o ponto de extremidade da v 2.0 é adequado para você. Continuaremos a atualizar este artigo ao longo do tempo para refletir o estado atual do ponto de extremidade v2.0. Por isso, confira-o novamente para reavaliar seus requisitos em relação aos recursos de v2.0.

Se você tem um aplicativo existente com o Azure AD que não usa o ponto de extremidade da v2.0, não é necessário começar do zero. No futuro, forneceremos uma maneira de habilitar seus aplicativos existentes do Azure AD para uso com o ponto de extremidade v2.0.

## Restrições quanto a aplicativos
Os seguintes tipos de aplicativos atualmente não têm suporte no ponto de extremidade da v2.0. Para obter uma descrição dos tipos de aplicativos compatíveis, consulte [este artigo](active-directory-v2-flows.md).

##### APIs da Web autônomas
Com o ponto de extremidade da v2.0, você tem a capacidade de [criar uma API Web protegida usando OAuth 2.0](active-directory-v2-flows.md#web-apis). No entanto, essa API Web só poderá receber tokens de um aplicativo que compartilhe a mesma ID de aplicativo. Não há suporte para a criação de uma API Web que é acessada de um cliente com uma Id de Aplicativo diferente. Esse cliente não poderá solicitar nem obter permissões para sua API Web.

Para ver como criar uma API Web que aceita tokens de um cliente com a mesma Id de Aplicativo, confira os exemplos de API Web do ponto de extremidade v2.0 em [Introdução](active-directory-appmodel-v2-overview.md#getting-started).

##### Fluxo em nome de da API Web
Muitas arquiteturas incluem uma API Web que precisa chamar outra API Web downstream, ambas protegidas pelo ponto de extremidade v2.0. Esse cenário é comum em clientes nativos que têm um back-end de API Web que, por sua vez, chama um serviço online da Microsoft ou outra API Web personalizada que dá suporte ao Azure AD.

Esse cenário pode ter suporte usando a concessão Credencial de Portador Jwt do OAuth 2.0, também conhecido como fluxo Em Nome de. No entanto, o fluxo Em Nome de não tem suporte no ponto de extremidade da v2.0. Para ver como esse fluxo funciona no serviço do AD do Azure disponível ao público geral, confira o [exemplo de código Em Nome De no GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

## Restrições quanto a registros de aplicativos
No momento, todos os aplicativos que desejam integrar-se ao ponto de extremidade da v2.0 devem criar um novo registro de aplicativo em [apps.dev.microsoft.com](https://apps.dev.microsoft.com). Nenhum aplicativo existente da Conta da Microsoft ou do Azure AD será compatível com o ponto de extremidade da v2.0, nem o serão os aplicativos registrados em qualquer portal além do novo Portal de Registro de Aplicativo. Planejamos fornecer uma maneira de habilitar aplicativos existentes para serem usados como aplicativo v2.0. No momento, não existe um caminho de migração de um aplicativo para o ponto de extremidade v2.0.

Da mesma forma, os aplicativos registrados no novo Portal de Registro de Aplicativo não funcionarão em relação ao ponto de extremidade de autenticação original do Azure AD. Porém, você pode usar aplicativos criados no Portal de Registro de Aplicativo para serem integrados com êxito ao ponto de extremidade de autenticação da conta da Microsoft, `https://login.live.com`.

Além disso, os registros do aplicativo criados em [apps.dev.microsoft.com](https://apps.dev.microsoft.com) têm as seguintes limitações:

- A propriedade **homepage**, também conhecida como **URL de logon** não tem suporte. Sem uma home page, esses aplicativos não serão exibidos no painel MyApps do Office.
- No momento, apenas dois segredos do aplicativo são permitidos por Id do aplicativo.
- Um registro de aplicativo só pode ser exibido e gerenciado por uma única conta de desenvolvedor. Ele não pode ser compartilhado entre vários desenvolvedores.
- Há várias restrições em relação ao formato de redirect\_uri permitido. Confira a seção a seguir para obter mais detalhes.

## Restrições em URIs de redirecionamento
Aplicativos que são registrados no novo Portal de Registro de Aplicativo são atualmente restritos a um conjunto limitado de valores redirect\_uri. O redirect\_uri para serviços e aplicativos Web deve começar com o esquema `https`, e todos os valores de redirect\_uri devem compartilhar um único domínio DNS. Por exemplo, não é possível registrar um aplicativo Web que tenha redirect\_uris:

`https://login-east.contoso.com` `https://login-west.contoso.com`

O sistema de registro compara o nome DNS completo do redirect\_uri existente ao nome DNS do redirect\_uri que você está adicionando. A solicitação para adicionar o nome DNS falhará se alguma das condições abaixo for atendida:

- Se o nome DNS completo do redirect\_uri novo não coincidir com o nome DNS do redirect\_uri existente
- Se o nome DNS completo do redirect\_uri novo não for um subdomínio do redirect\_uri existente

Por exemplo, se o aplicativo tiver redirect\_uri:

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
- As APIs REST de Email, Calendário e Contatos do Outlook, que estão localizadas em https://outlook.office.com. Para saber como escrever um aplicativo que acessa essas APIs, consulte estes tutoriais de [Introdução ao Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).
- As APIs do Microsoft Graph. Para saber mais sobre o Microsoft Graph e todos os dados que estão disponíveis, acesse [https://graph.microsoft.io](https://graph.microsoft.io).

Nenhum outro serviço tem suporte no momento. Mais serviços Microsoft Online serão adicionados no futuro, bem como suporte para seus próprios serviços e APIs Web personalizados.

## Restrições quanto a bibliotecas e SDKs
O suporte de biblioteca para o ponto de extremidade v2.0 é bastante limitado no momento. Se deseja usar o ponto de extremidade da v2.0 em um aplicativo de produção, você tem as seguintes opções:

- Se está criando um aplicativo Web, você pode usar com segurança nosso middleware do servidor disponível para realizar a entrada e a validação de token. Isso inclui o middleware OWIN Open ID Connect para ASP.NET e o plug-in NodeJS Passport. Exemplos de código que usam nossos middlewares também estão disponíveis na seção [Introdução](active-directory-appmodel-v2-overview.md#getting-started).
- Para outras plataformas e aplicativos móveis e nativos, você também pode fazer a integração com o ponto de extremidade da v2.0 enviando e recebendo diretamente mensagens de protocolo no código do aplicativo. Os protocolos OAuth e OpenID Connect da v2.0 [foram explicitamente documentados](active-directory-v2-protocols.md) para ajudar você a executar essa integração.
- Finalmente, você pode usar bibliotecas de software livre do Open ID Connect e do OAuth para fazer a integração com o ponto de extremidade da v2.0. O protocolo da v2.0 deve ser compatível com muitas bibliotecas de protocolo de software livre sem grandes alterações. A disponibilidade dessas bibliotecas varia por linguagem e plataforma, e os sites do [Open ID Connect](http://openid.net/connect/) e do [OAuth 2.0](http://oauth.net/2/) mantêm uma lista de implementações populares. Confira [Bibliotecas de autenticação e v2.0 do Azure AD (Azure Active Directory)](active-directory-v2-libraries.md) para obter mais detalhes, a lista de bibliotecas de cliente de software livre e exemplos foram testados com o ponto de extremidade v2.0.

A Microsoft também lançou uma visualização inicial da [MSAL (biblioteca de autenticação da Microsoft)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) somente para .NET. Sinta-se à vontade para experimentar essa biblioteca em aplicativos de cliente e servidor .NET, mas como uma biblioteca de visualização, ela não terá suporte com a qualidade de GA.

## Restrições quanto a protocolos
O ponto de extremidade da v2.0 dá suporte apenas a Open ID Connect e OAuth 2.0. No entanto, nem todos os recursos e capacidades de cada protocolo foram incorporados ao ponto de extremidade v2.0, incluindo:

- O `end_session_endpoint` do OpenID Connect, que permite a um aplicativo encerrar a sessão do usuário em um ponto de extremidade v2.0.
- id\_tokens emitido pelo ponto de extremidade v2.0 contém somente um identificador de par para o usuário. Isso significa que dois aplicativos diferentes recebam IDs diferentes para o mesmo usuário. Observe que, ao consultar o ponto de extremidade `/me` do Microsoft Graph, você poderá obter uma ID correlacionável para o usuário que pode ser usada em todos os aplicativos.
- id\_tokens emitidos pelo ponto de extremidade v2.0 não contêm uma declaração `email` para o usuário no momento, mesmo se você adquire permissão do usuário para exibir seus emails.
- O ponto de extremidade de informações do usuário do OpenID Connect. No momento, o ponto de extremidade de informações do usuário não está implementado no ponto de extremidade v2.0. No entanto, todos os dados de perfil de usuário que você possivelmente receberia nesse ponto de extremidade estão disponíveis no ponto de extremidade `/me` do Microsoft Graph.
- Declarações de função e de grupo. Neste momento, o ponto de extremidade v2.0 não dá suporte à emissão de declarações de função ou de grupo em id\_tokens.

Para entender melhor o escopo da funcionalidade de protocolo com suporte no ponto de extremidade v2.0, leia nossa [Referência de protocolo do OpenID Connect e OAuth 2.0](active-directory-v2-protocols.md).

## Restrições de contas corporativas e de estudante
Há alguns recursos específicos para usuários de negócios/organizações da Microsoft que ainda não têm suporte do ponto de extremidade v2.0.

##### Acesso condicional com base em dispositivo, aplicativos móveis e nativos e o Microsoft Graph
O ponto de extremidade v 2.0 ainda não dá suporte à autenticação de dispositivo para aplicativos móveis e nativos, como aplicativos nativos em execução no iOS ou no Android. Isso pode impedir seu aplicativo nativo de chamar o Microsoft Graph para certas organizações. A autenticação de dispositivo é necessária quando um administrador define a política de acesso condicional com base em dispositivo para um aplicativo. Para o ponto de extremidade v2.0, o cenário mais provável para acesso condicional com base em dispositivo é um administrador definir uma política em um recurso no Microsoft Graph, como a API do Outlook. Se um administrador define essa política e seu aplicativo nativo solicita um token para o Microsoft Graph, a solicitação acaba falhando porque a autenticação de dispositivo ainda não tem suporte. No entanto, aplicativos Web que solicitam tokens para o Microsoft Graph têm suporte quando as políticas baseadas em dispositivo estão configuradas. No cenário de aplicativo Web, a autenticação de dispositivo é executada pelo navegador do usuário.

Como desenvolvedor, você provavelmente não tem controle sobre quando as políticas são definidas para recursos do Microsoft Graph, e nem mesmo fica ciente de quando isso acontece. Se você estiver criando um aplicativo para usuários corporativos ou estudantes, use [o ponto de extremidade original do Azure AD](active-directory-developers-guide.md) até que o ponto de extremidade v2.0 dê suporte a autenticação de dispositivo. Para saber mais sobre o acesso condicional com base em dispositivo, confira [este artigo](active-directory-conditional-access.md#device-based-conditional-access).

##### Autenticação integrada do Windows para locatários federados
Se você já usou a ADAL (e, portanto, o ponto de extremidade original do Azure AD) em aplicativos do Windows, pode ter se beneficiado do que é conhecido como concessão de asserção SAML. Essa concessão permite que os usuários de locatários do Azure AD federados autentiquem com suas instâncias do Active Directory local sem precisar inserir as credenciais. A concessão de asserção SAML não tem suporte atualmente no ponto de extremidade v2.0.

<!---HONumber=AcomDC_0928_2016-->