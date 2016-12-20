---
title: "Limitações e restrições de ponto de extremidade v2.0 do Azure Active Directory | Microsoft Docs"
description: "Uma lista de limitações e restrições para o ponto de extremidade v2.0 do Azure AD."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: a99289c0-e6ce-410c-94f6-c279387b4f66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 8fabc37ad8c7a99a09083422f740e5352e403438


---
# <a name="should-i-use-the-v20-endpoint"></a>Devo usar o ponto de extremidade da v2.0?
Ao criar aplicativos que se integram ao Azure AD (Azure Active Directory), você precisará decidir se os protocolos de autenticação e o ponto de extremidade v2.0 atendem às suas necessidades. O modelo de ponto de extremidade do Azure AD original ainda tem suporte completo e, em alguns aspectos, tem mais recursos do que o v2.0. No entanto, o ponto de extremidade v2.0 [apresenta vantagens consideráveis](active-directory-v2-compare.md) para desenvolvedores. Os benefícios da v2.0 podem convencer o usuário a usar o novo modelo de programação.

Aqui está nossa recomendação para usar o ponto de extremidade v2.0 agora:

* Se você deseja dar suporte a contas pessoais da Microsoft em seu aplicativo, use o ponto de extremidade v2.0. Antes de fazer isso, certifique-se de que você compreende as limitações que discutimos neste artigo, especialmente aquelas que se aplicam a contas corporativas e de estudante.
* Se seu aplicativo precisa oferecer suporte somente a contas corporativas e de estudantes, use [os pontos de extremidade do Azure AD originais](active-directory-developers-guide.md).

No futuro, o ponto de extremidade v2.0 vai expandir e eliminar as restrições listadas aqui, para que você só precise usar o ponto de extremidade v2.0. Enquanto isso, este artigo destina-se a ajudá-lo a determinar se o ponto de extremidade v2.0 é adequado para você. Continuaremos a atualizar este artigo para refletir o estado atual do ponto de extremidade v2.0. Verifique novamente para reavaliar seus requisitos em relação aos recursos da v2.0.

Se você tiver um aplicativo do Azure AD existente que não use o ponto de extremidade v2.0, não será necessário começar do zero. No futuro, forneceremos uma maneira de usar seus aplicativos existentes do Azure AD com o ponto de extremidade v2.0.

## <a name="restrictions-on-app-types"></a>Restrições de tipos de aplicativo
No momento, os seguintes tipos de aplicativos atualmente não têm suporte do ponto de extremidade v2.0. Para obter uma descrição dos tipos de aplicativos com suporte, veja [Tipos de aplicativo para o ponto de extremidade v2.0 do Azure Active Directory](active-directory-v2-flows.md).

### <a name="standalone-web-apis"></a>APIs da Web autônomas
Você pode usar o ponto de extremidade v2.0 para [criar uma API Web segura com OAuth 2.0](active-directory-v2-flows.md#web-apis). No entanto, essa API Web só pode receber tokens de um aplicativo que tenha a mesma ID do aplicativo. Você não pode acessar uma API Web de um cliente com uma ID de aplicativo diferente. O cliente não poderá solicitar nem obter permissões para sua API Web.

Para ver como criar uma API Web que aceita tokens de um cliente com a mesma ID de Aplicativo, confira os exemplos de API Web do ponto de extremidade da v 2.0 na seção [Introdução](active-directory-appmodel-v2-overview.md#getting-started).

### <a name="web-api-on-behalf-of-flow"></a>Fluxo em nome de da API Web
Muitas arquiteturas incluem uma API Web que precisa chamar outra API Web downstream, ambas protegidas pelo ponto de extremidade v2.0. Esse cenário é comum em clientes nativos que têm um back-end de API Web que, por sua vez, chama uma instância do Microsoft Online Services ou outra API Web personalizada que dá suporte ao Azure AD.

Você pode criar esse cenário usando a concessão de credenciais de portador OAuth 2.0Token Web JSON (JWT), também conhecida como o fluxo em nome de. No entanto, o fluxo em nome de não tem suporte do ponto de extremidade v2.0 no momento. Para ver como esse fluxo funciona no serviço do Azure AD disponível ao público geral, confira o [exemplo de código em nome de no GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

## <a name="restrictions-on-app-registrations"></a>Restrições quanto a registros de aplicativos
Atualmente, para cada aplicativo que deseja integrar ao ponto de extremidade v2.0, você deve criar um registro de aplicativo no novo [Portal de Registro de Aplicativo da Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). O aplicativos do Azure AD ou de conta da Microsoft existentes não são compatíveis com o ponto de extremidade v2.0. Os aplicativos registrados em qualquer portal que não seja o Portal de Registro de Aplicativo não são compatíveis com o ponto de extremidade v2.0. No futuro, planejamos fornecer uma maneira de usar um aplicativo existente como um aplicativo v2.0. Atualmente, no entanto, há um caminho de migração para que um aplicativo existente funcione com o ponto de extremidade v2.0.

Os aplicativos registrados no Portal de Registro de Aplicativo não funcionarão com o ponto de extremidade de autenticação original do Azure AD. Porém, você pode usar aplicativos criados no Portal de Registro de Aplicativo para serem integrados com êxito ao ponto de extremidade de autenticação da conta da Microsoft, `https://login.live.com`.

Além disso, os registros do aplicativo que você cria no [Portal de Registro de Aplicativo](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) têm as seguintes condições:

* A propriedade **homepage**, também conhecida como *URL de entrada*, não tem suporte. Sem uma home page, esses aplicativos não aparecerão no painel MyApps do Office.
* No momento, apenas dois segredos do aplicativo são permitidos por ID do aplicativo.
* Um registro de aplicativo só pode ser exibido e gerenciado por uma única conta de desenvolvedor. Ele não pode ser compartilhado entre vários desenvolvedores.
* Há várias restrições em relação ao formato de URI de redirecionamento permitido. Para saber mais sobre URIs de redirecionamento, veja a próxima seção.

## <a name="restrictions-on-redirect-uris"></a>Restrições de URIs de redirecionamento
No momento, os aplicativos registrados no novo Portal de Registro de Aplicativo são atualmente restritos a um conjunto limitado de valores de URI de redirecionamento. O URI de redirecionamento para serviços e aplicativos Web deve começar com o esquema `https`, e todos os valores de URI de redirecionamento devem compartilhar um único domínio DNS. Por exemplo, você não pode registrar um aplicativo Web que tenha um destes URIs direto:

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

O sistema de registro compara o nome DNS completo do URI de redirecionamento existente ao nome DNS do URI de redirecionamento que você está adicionando. A solicitação para adicionar o nome DNS falhará se alguma das condições abaixo for verdadeira:  

* Se o nome DNS completo do URI de redirecionamento novo não coincidir com o nome DNS do URI de redirecionamento existente.
* Se o nome DNS completo do URI de redirecionamento novo não for um subdomínio do URI de redirecionamento existente.

Por exemplo, se o aplicativo tiver este URI de redirecionamento:

`https://login.contoso.com`

Você pode adicionar a ele, desta forma:

`https://login.contoso.com/new`

Nesse caso, os nome DNS corresponde exatamente. Ou você pode fazer isto:

`https://new.login.contoso.com`

Nesse caso, você está se referindo a um subdomínio DNS logon.contoso.com. Se você quiser ter um aplicativo com login-east.contoso.com e login-west.contoso.com como URIs de redirecionamento, deverá adicionar o seguinte URIs de redirecionamento nesta ordem:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Os dois últimos podem ser adicionados porque eles são subdomínios do primeiro URI de redirecionamento, contoso.com. Essa limitação será removida em uma versão futura.

Para saber como registrar um aplicativo no Portal de Registro de Aplicativo, veja [Como registrar um aplicativo no ponto de extremidade v2.0](active-directory-v2-app-registration.md).

## <a name="restrictions-on-services-and-apis"></a>Restrições de serviços e APIs
Atualmente, o ponto de extremidade v2.0 dá suporte à entrada para qualquer aplicativo registrado no Portal de Registro de Aplicativo e que se enquadre na lista de [fluxos de autenticação com suporte](active-directory-v2-flows.md). No entanto, esses aplicativos podem adquirir tokens de acesso do OAuth 2.0 para um conjunto muito limitado de recursos. O ponto de extremidade v2.0 só emite tokens de acesso para:

* O aplicativo que solicitou o token. Um aplicativo pode adquirir um token de acesso para si mesmo, se o aplicativo lógico é constituído de várias camadas ou componentes diferentes. Para ver esse cenário em ação, confira nossos tutoriais de [Introdução](active-directory-appmodel-v2-overview.md#getting-started) .
* As APIs REST de Email, Calendário e Contatos do Outlook, que estão localizadas em https://outlook.office.com. Para saber como escrever um aplicativo que acessa essas APIs, veja os tutoriais de [Introdução ao Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).
* APIs do Microsoft Graph. Você pode saber mais sobre o [Microsoft Graph](https://graph.microsoft.io) e os dados que estão disponíveis para você.

Nenhum outro serviço tem suporte no momento. Mais Microsoft Online Services serão adicionados no futuro, bem como suporte para seus próprios serviços e APIs Web personalizados.

## <a name="restrictions-on-libraries-and-sdks"></a>Restrição de bibliotecas e SDKs
Atualmente, o suporte de biblioteca para o ponto de extremidade v2.0 é limitado. Se deseja usar o ponto de extremidade v2.0 em um aplicativo de produção, você tem estas opções:

* Se estiver criando um aplicativo Web, você poderá usar com segurança nosso middleware do servidor disponível da Microsoft para realizar a entrada e a validação de token. Isso inclui o middleware OWIN Open ID Connect para ASP.NET e o plug-in Passport do Node.js. Para obter exemplos de código que usam o middleware da Microsoft, veja a nossa seção [Introdução](active-directory-appmodel-v2-overview.md#getting-started).
* Para outras plataformas e aplicativos móveis e nativos, você pode fazer a integração com o ponto de extremidade v2.0 enviando e recebendo diretamente mensagens de protocolo no código do aplicativo. Os protocolos OAuth e OpenID Connect v2.0 [foram explicitamente documentados](active-directory-v2-protocols.md) para ajudar você a executar essa integração.
* Por fim, você pode usar bibliotecas de software livre do Open ID Connect e do OAuth para fazer a integração com o ponto de extremidade v2.0. O protocolo v2.0 deve ser compatível com muitas bibliotecas de protocolo de software livre sem grandes alterações. A disponibilidade desses tipos de bibliotecas varia por idioma e plataforma. Os sites do [Open ID Connect](http://openid.net/connect/) e do [OAuth 2.0](http://oauth.net/2/) mantêm uma lista das implementações populares. Para saber mais, confira [Bibliotecas de autenticação e v2.0 do Azure Active Directory](active-directory-v2-libraries.md) e para obter a lista de bibliotecas de cliente de software livre e exemplos testados com o ponto de extremidade v2.0.

A Microsoft lançou uma visualização inicial da [MSAL (Biblioteca de Autenticação da Microsoft)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) somente para .NET. Sinta-se à vontade para experimentar essa biblioteca em aplicativos de cliente e servidor .NET, mas como uma biblioteca de visualização, ela não terá suporte com a qualidade de GA (disponibilidade geral).

## <a name="restrictions-on-protocols"></a>Restrições quanto a protocolos
O ponto de extremidade v2.0 dá suporte apenas a Open ID Connect e a OAuth 2.0. No entanto, nem todos os recursos e capacidades de cada protocolo foram incorporados ao ponto de extremidade da v2.0.

No momento, essas funcionalidades e recursos típicos do protocolo *não estão disponíveis* no ponto de extremidade v2.0:

* O parâmetro `end_session_endpoint` do OpenID Connect, que permite que um aplicativo encerre a sessão do usuário, não está disponível com o ponto de extremidade v2.0.
* Os tokens de ID emitidos pelo ponto de extremidade v2.0 contêm somente um identificador de par para o usuário. Isso significa que dois aplicativos diferentes recebem IDs diferentes para o mesmo usuário. Observe que, ao consultar o ponto de extremidade `/me` do Microsoft Graph, você poderá obter uma ID correlacionável para o usuário que poderá ser usada entre aplicativos.
* Os tokens de ID emitidos pelo ponto de extremidade v2.0 não contêm uma declaração `email` para o usuário, mesmo se você adquire permissão do usuário para exibir seus emails.
* O ponto de extremidade UserInfo do OpenID Connect não está implementado no ponto de extremidade v2.0. No entanto, todos os dados de perfil de usuário que você possivelmente receberia nesse ponto de extremidade estão disponíveis no ponto de extremidade `/me` do Microsoft Graph.
* O ponto de extremidade v2.0 não dá suporte à emissão de declarações de função ou de grupo em tokens de ID.

Para entender melhor o escopo da funcionalidade de protocolo com suporte no ponto de extremidade v2.0, leia nossa [referência de protocolo do OpenID Connect e OAuth 2.0](active-directory-v2-protocols.md).

## <a name="restrictions-for-work-and-school-accounts"></a>Restrições de contas corporativas e de estudante
Alguns recursos específicos para os usuários empresariais da Microsoft ainda não têm suporte do ponto de extremidade v2.0. Para saber mais, leia as seções a seguir.

### <a name="device-based-conditional-access-native-and-mobile-apps-and-microsoft-graph"></a>Acesso condicional com base em dispositivo, aplicativos móveis e nativos e o Microsoft Graph
O ponto de extremidade v 2.0 ainda não dá suporte à autenticação de dispositivo para aplicativos móveis e nativos, como aplicativos nativos em execução no iOS ou no Android. Para algumas organizações, isso poderá impedir que seu aplicativo nativo chame o Microsoft Graph. A autenticação de dispositivo é necessária quando um administrador define a política de acesso condicional com base em dispositivo para um aplicativo. Para o ponto de extremidade v2.0, o cenário mais provável para acesso condicional com base em dispositivo é se um administrador tiver de definir uma política em um recurso no Microsoft Graph, como a API do Outlook. Se um administrador define essa política e seu aplicativo nativo solicita um token para o Microsoft Graph, a solicitação acaba falhando porque a autenticação de dispositivo ainda não tem suporte. No entanto, aplicativos Web que solicitam tokens para o Microsoft Graph têm suporte quando as políticas baseadas em dispositivo estão configuradas. No cenário de aplicativo Web, a autenticação de dispositivo é executada pelo navegador do usuário.

Como desenvolvedor, você provavelmente não tem controle sobre quando as políticas são definidas para recursos do Microsoft Graph. Talvez você ainda não esteja ciente disso quando acontecer. Se você estiver criando um aplicativo para usuários corporativos ou estudantes, use [o ponto de extremidade original do Azure AD](active-directory-developers-guide.md) até que o ponto de extremidade v2.0 dê suporte a autenticação de dispositivo. Você pode saber mais sobre [acesso condicional com base no dispositivo no Azure AD](active-directory-conditional-access.md#device-based-conditional-access).

### <a name="windows-integrated-authentication-for-federated-tenants"></a>Autenticação integrada do Windows para locatários federados
Se você tiver usado o Active Directory Authentication Library (ADAL) (com o ponto de extremidade original do Azure AD) em aplicativos do Windows, poderá se beneficiar com o que é conhecido como SAML (Security Assertion Markup Language). Com essa concessão, os usuários de locatários do Azure AD federados se autenticam com suas instâncias do Active Directory local sem inserir as credenciais. Atualmente, a concessão de asserção SAML não tem suporte no ponto de extremidade v2.0.




<!--HONumber=Nov16_HO3-->


