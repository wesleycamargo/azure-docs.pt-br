---
title: Comparando o ponto de extremidade do Azure AD v2.0 com o ponto de extremidade v1.0 | Microsoft Docs
description: Conheça as diferenças entre o endpoint do Azure AD v2.0 e o endpoint v1.0
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5060da46-b091-4e25-9fa8-af4ae4359b6c
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: andret
ms.reviewer: hirsin, andret
ms.custom: aaddev
ms.openlocfilehash: b75b31ddfc77be5ed651e7b8484e41a4ae73d8d8
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47406525"
---
# <a name="comparing-the-azure-ad-v20-endpoint-with-the-v10-endpoint"></a>Comparando o ponto de extremidade Azure AD v2.0 com o ponto de extremidade v1.0

Ao desenvolver um novo aplicativo, é importante conhecer as diferenças entre os pontos de extremidade v1.0 e v2.0. Abaixo estão as principais diferenças, bem como algumas limitações existentes para o terminal v2.0.

> [!NOTE]
> Nem todos os recursos e cenários do Azure AD (Azure Active Directory) têm suporte no ponto de extremidade v2.0. Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](#limitations).

## <a name="who-can-sign-in"></a>Quem pode entrar

![Quem pode entrar com endpoints v1.0 e v2.0](media/azure-ad-endpoint-comparison/who-can-sign-in.png)

* O ponto de extremidade v1.0 permite que apenas contas do trabalho e da escola entrem no seu aplicativo (Azure AD)

* O ponto de extremidade v2.0 permite que contas corporativas e de estudante do Azure AD e contas pessoais (MSA) (hotmail.com, outlook.com, msn.com) se conectem.

* Os pontos de extremidade v1.0 e v2.0 também aceitam logins de *[usuários convidados](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* de um diretório do Azure AD para aplicativos configurados como *[locatário único](single-and-multi-tenant-apps.md)* ou para *aplicativos de vários locatários* configurados para apontar para o terminal específico do inquilino (`https://login.microsoftonline.com/{TenantId_or_Name}`).

O ponto de extremidade v2.0 permite que você crie aplicativos que aceitam login de contas pessoais e de trabalho e escola, permitindo que você escreva seu aplicativo completamente independente de sua conta. Por exemplo, se seu aplicativo chamar o [Microsoft Graph](https://graph.microsoft.io), alguns recursos e dados adicionais estarão disponíveis para contas de trabalho, como seus sites do SharePoint ou dados do Diretório. Mas, para muitas ações, como [a leitura de um e-mail de um usuário](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message), o mesmo código pode acessar o e-mail para contas pessoais e de trabalho e escola.

Para o ponto de extremidade v2.0, você pode usar uma única biblioteca (MSAL) para obter acesso aos mundos do consumidor, educacional e empresarial.

 O ponto de extremidade do Azure AD v1.0 aceita logins apenas de contas do trabalho e da escola.

## <a name="incremental-and-dynamic-consent"></a>Consentimento incremental e dinâmico

Os aplicativos que usam o endpoint do Azure AD v1.0 precisam especificar suas permissões necessárias do OAuth 2.0 antecipadamente, por exemplo:

![Interface do usuário do registro de permissões](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

As permissões definidas diretamente no registro do aplicativo são **static**. Embora as permissões estáticas do aplicativo definidas no portal do Azure mantivessem o código simples e agradável, ele pode apresentar alguns problemas para os desenvolvedores:

* O aplicativo precisa conhecer todas as permissões necessárias no momento da criação do aplicativo. Adicionar permissões ao longo do tempo era um processo difícil.

* O aplicativo precisa conhecer todos os recursos que jamais acessaria antes do tempo. Era difícil criar aplicativos que pudessem acessar um número arbitrário de recursos.

* O aplicativo precisa solicitar todas as permissões que precisaria no primeiro login do usuário. Em alguns casos, isso resultava em uma lista longa de permissões, o que desencorajava os usuários finais a aprovarem o acesso do aplicativo na entrada inicial.

Com o ponto de extremidade v2.0, você pode ignorar as permissões definidas estaticamente definidas nas informações de registro do aplicativo no portal do Azure e especificar as permissões que seu aplicativo precisa **dinamicamente** durante a execução, durante o uso regular do aplicativo, independentemente de permissões definidas estaticamente nas informações de registro do aplicativo.

Para fazer isso, você pode especificar os escopos que seu aplicativo precisa em um determinado ponto do tempo do aplicativo, incluindo os novos escopos no parâmetro `scope` ao solicitar um token de acesso, sem a necessidade de pré-defini-los nas informações de registro do aplicativo.

Se o usuário ainda não tiver consentido em novos escopos adicionados à solicitação, ele será solicitado a consentir apenas com as novas permissões. Para obter mais informações, você pode ler sobre [permissões, autorização e escopos](v2-permissions-and-consent.md).

Permitir que um aplicativo solicite permissões dinamicamente por meio do parâmetro `scope` fornece aos desenvolvedores controle total sobre a experiência do usuário. Se desejar, você também pode optar por carregar sua experiência de consentimento e solicitar todas as permissões em uma solicitação inicial de autorização. Ou, se seu aplicativo precisar de um grande número de permissões, você pode optar por reunir essas permissões do usuário de forma incremental, à medida que ele tentar usar determinados recursos do seu aplicativo ao longo do tempo.

Observe que o consentimento do administrador em nome de uma organização ainda usa as permissões estáticas registradas para o aplicativo, portanto, é recomendável que você defina essas permissões para aplicativos usando o ponto de extremidade v2.0, caso precise de um administrador para dar consentimento em nome de todo organização. Isso reduz os ciclos exigidos pelo administrador da organização para configurar o aplicativo

## <a name="scopes-not-resources"></a>Escopos, não recursos

Para aplicativos que usam o endpoint v1.0, um aplicativo pode se comportar como um **recurso** ou um destinatário de tokens. Um recurso pode definir um número de **escopos** ou **oAuth2Permissions** que ele entende, permitindo que os aplicativos cliente solicitem tokens a esse recurso para um determinado conjunto de escopos. Considere a API do Graph do AD do Azure como um exemplo de um recurso:

* Identificador de recurso, ou `AppID URI`: `https://graph.windows.net/`

* Escopos, ou `OAuth2Permissions`: `Directory.Read`, `Directory.Write`e assim por diante.

Tudo isso é válido para o ponto de extremidade v2.0. Um aplicativo ainda pode se comportar como recurso, definir escopos e ser identificado por um URI. Aplicativos cliente ainda podem solicitar acesso a esses escopos. No entanto, a maneira em que um cliente solicita essas permissões foi alterada. Para o ponto de extremidade v1.0, uma solicitação de autorização do OAuth 2.0 para o Azure AD pode ter parecido com:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

onde o parâmetro **resource** indicava o recurso para o qual o aplicativo cliente estava solicitando a autorização. O Microsoft Azure Active Directory computava as permissões exigidas pelo aplicativo com base na configuração estática no Portal do Azure e emitia os tokens de acordo. Para aplicativos que usam o terminal v2.0, a mesma solicitação de autorização do OAuth 2.0 é semelhante a:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

Em que o parâmetro **escopo** indica para qual recurso e permissões o aplicativo está solicitando autorização. O recurso desejado ainda está presente na solicitação; ele simplesmente está englobado em cada um dos valores do parâmetro de escopo. Usar o parâmetro de escopo dessa maneira permite que o ponto de extremidade v2.0 seja mais compatível com a especificação OAuth 2.0 e esteja alinhado mais estreitamente com práticas comuns do setor. Ele também permite que os aplicativos realizem [consentimento incremental](#incremental-and-dynamic-consent), que é descrito anteriormente.

## <a name="well-known-scopes"></a>Escopos conhecidos

### <a name="offline-access"></a>Acesso offline

Os aplicativos que usam o ponto de extremidade v2.0 podem exigir o uso de uma nova permissão conhecida para aplicativos – o escopo `offline_access`. Todos os aplicativos terão que solicitar essa permissão se precisarem acessar recursos em nome de um usuário por um longo período de tempo, mesmo quando o usuário pode não estiver usando o aplicativo de maneira ativa. O escopo `offline_access` aparecerá para o usuário em diálogos de consentimento como **Acesse seus dados a qualquer momento**, com os quais o usuário deve concordar. Solicitar a permissão `offline_access` permitirá que seu aplicativo Web receba refresh_tokens do OAuth 2.0 a partir do ponto de extremidade v2.0. Os tokens de atualização são de longa duração e podem ser trocados por novos tokens de acesso do OAuth 2.0 por longos períodos de acesso.

Se o seu aplicativo não solicitar o escopo `offline_access`, ele não receberá tokens de atualização. Isso significa que, quando você resgatar um código de autorização no fluxo do código de autorização do OAuth 2.0, receberá apenas um token de acesso do ponto de extremidade `/token`. Esse token de acesso permanecerá válido por um curto período de tempo (geralmente uma hora), mas acabará expirando. Nesse momento, seu aplicativo precisará redirecionar o usuário de volta ao ponto de extremidade `/authorize` para recuperar um novo código de autorização. Durante esse redirecionamento, o usuário pode ou não precisar digitar suas credenciais novamente ou consentir de novo as permissões, dependendo do tipo do aplicativo.

Para saber mais sobre o OAuth 2.0, `refresh_tokens` e `access_tokens`, confira a referência do protocolo [ v2.0 ](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, perfil e email

Historicamente, o fluxo de entrada mais básico do OpenID Connect com o Azure AD forneceria muitas informações sobre o usuário no *id_token* resultante. As reivindicações em um *id_token* podem incluir o nome do usuário, nome de usuário preferido, endereço de e-mail, ID do objeto e muito mais.

As informações que o escopo de `openid` permite que seu aplicativo acesse agora estão restritas. O escopo de `openid` apenas permitirá que seu aplicativo faça logon do usuário e receba um identificador específico do aplicativo para o usuário. Se você quiser obter dados pessoais sobre o usuário em seu aplicativo, seu aplicativo precisará solicitar permissões adicionais do usuário. Dois novos escopos – escopos `email` e `profile` – permitirão que você solicite permissões adicionais.

O escopo de `email` permite que seu aplicativo acesse o endereço de email principal do usuário por meio da declaração `email` no id_token. O escopo `profile` permite que seu aplicativo acesse todas as outras informações básicas sobre o usuário – seu nome, nome de usuário preferido, ID do objeto etc.

Isso permite que você codifique seu aplicativo com uma divulgação mínima. Você só pode solicitar ao usuário o conjunto de informações de que seu aplicativo precisa para fazer seu trabalho. Para obter mais informações sobre os escopos, consulte a [referência de escopo da v2.0](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Declarações de token

As declarações em tokens emitidos pelo ponto de extremidade v 2.0 não serão idênticas aos tokens emitidos pelos pontos de extremidade do Microsoft Azure Active Directory disponível geralmente. Aplicativos migrando para o novo serviço de aplicativos não devem presumir que exista uma declaração específica em id_tokens ou access_tokens. Mais detalhes sobre os diferentes tipos de tokens usados no endpoint v2.0 estão disponíveis na referência [do token de acesso](access-tokens.md) e na [`id_token` referência](id-tokens.md)

## <a name="limitations"></a>Limitações

Existem algumas restrições a serem observadas ao usar a v2.0.

Ao criar aplicativos que se integram à plataforma de identidade da Microsoft, você precisa decidir se os protocolos de ponto de extremidade e de autenticação v2.0 atendem às suas necessidades. O terminal e a plataforma v1.0 ainda são totalmente suportados e, em alguns aspectos, têm mais recursos que a v2.0. No entanto, v2.0 [introduz benefícios significativos](azure-ad-endpoint-comparison.md) para desenvolvedores.

No momento, esta é uma recomendação simplificada para desenvolvedores:

* Se você precisar dar suporte a contas pessoais da Microsoft em seu aplicativo, use a versão 2.0. Mas antes fazer isso, tenha certeza de que entende as limitações discutidas neste artigo.

* Se o seu aplicativo precisar apenas suportar contas de trabalho e escola da Microsoft, não use a v2.0. Em vez disso, consulte a [v1.0 guia](azure-ad-developers-guide.md).

O ponto de extremidade v2.0 evoluirá e eliminará as restrições listadas aqui, para que você precise usar apenas o ponto de extremidade v2.0. Enquanto isso, use este artigo para determinar se o ponto de extremidade v2.0 é adequado para você. Continuaremos a atualizar este artigo para refletir o estado atual do ponto de extremidade v2.0. Verifique novamente para reavaliar seus requisitos em relação aos recursos da v2.0.

### <a name="restrictions-on-app-types"></a>Restrições de tipos de aplicativo

No momento, os seguintes tipos de aplicativos atualmente não têm suporte do ponto de extremidade v2.0. Para obter uma descrição dos tipos de aplicativos compatíveis, consulte [Tipos de aplicativo na v2.0](v2-app-types.md).

#### <a name="standalone-web-apis"></a>APIs da Web autônomas

Você pode usar o ponto de extremidade v2.0 para [criar uma API Web segura com OAuth 2.0](v2-app-types.md#web-apis). No entanto, essa API Web só pode receber tokens de um aplicativo que tenha a mesma ID do aplicativo. Você não pode acessar uma API Web de um cliente com uma ID de aplicativo diferente. O cliente não poderá solicitar nem obter permissões para sua API Web.

Para ver como criar uma API da Web que aceite tokens de um cliente que tenha o mesmo ID do Aplicativo, consulte as amostras da API da Web do terminal v2.0 na seção [v2.0 getting started](v2-overview.md#getting-started).

### <a name="restrictions-on-app-registrations"></a>Restrições quanto a registros de aplicativos

Atualmente, para cada aplicativo que deseja integrar ao ponto de extremidade v2.0, você deve criar um registro de aplicativo no novo [Portal de Registro de Aplicativo da Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). O aplicativos do Azure AD ou de conta da Microsoft existentes não são compatíveis com o ponto de extremidade v2.0. Os aplicativos registrados em qualquer portal que não seja o Portal de Registro de Aplicativo não são compatíveis com o ponto de extremidade v2.0.

Além disso, os registros do aplicativo que você cria no [Portal de Registro de Aplicativo](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) têm as seguintes condições:

* Apenas dois segredos do aplicativo são permitidos por ID do Aplicativo.

* Um registro de aplicativo registrado por um usuário com uma conta pessoal da Microsoft pode ser exibido e gerenciado somente por uma única conta de desenvolvedor. Ele não pode ser compartilhado entre vários desenvolvedores. Se você desejar compartilhar o registro do aplicativo entre vários desenvolvedores, poderá criar o aplicativo entrando no portal de registro com uma conta do Azure AD.

* Existem várias restrições quanto ao formato do URL de redirecionamento permitido. Para mais informações sobre o URL de redirecionamento, consulte a próxima seção.

### <a name="restrictions-on-redirect-urls"></a>Restrições em URLs de redirecionamento

Os aplicativos registrados no Portal de registro de aplicativos estão restritos a um conjunto limitado de valores de URL de redirecionamento. O URL de redirecionamento para aplicativos e serviços da web deve começar com o esquema `https`, e todos os valores de URL de redirecionamento devem compartilhar um único domínio DNS. Por exemplo, você não pode registrar um aplicativo da web que tenha um desses URLs de redirecionamento:

* `https://login-east.contoso.com`  
* `https://login-west.contoso.com`

O sistema de registro compara todo o nome DNS do URL de redirecionamento existente com o nome DNS do URL de redirecionamento que você está adicionando. A solicitação para adicionar o nome DNS falhará se alguma das condições abaixo for verdadeira:  

* Todo o nome DNS do novo URL de redirecionamento não corresponde ao nome DNS do URL de redirecionamento existente.

* O nome DNS completo da URL de redirecionamento novo não for um subdomínio da URL de redirecionamento existente.

Por exemplo, se o aplicativo tem essa URL de redirecionamento:

`https://login.contoso.com`

Você pode adicionar a ele, desta forma:

`https://login.contoso.com/new`

Nesse caso, os nome DNS corresponde exatamente. Ou você pode fazer isto:

`https://new.login.contoso.com`

Nesse caso, você está se referindo a um subdomínio DNS logon.contoso.com. Se você quiser ter um aplicativo que tenha `login-east.contoso.com` e `login-west.contoso.com` como URLs de redirecionamento, adicione os URLs de redirecionamento nesta ordem:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Você pode adicionar os dois últimos porque são subdomínios do primeiro URL de redirecionamento, contoso.com. Essa limitação será removida em uma versão futura.

Observe também que você pode ter apenas 20 URLs de resposta para um aplicativo específico.

Para saber como registrar um aplicativo no Portal de Registro de Aplicativo, veja [Como registrar um aplicativo no ponto de extremidade v2.0](quickstart-v2-register-an-app.md).

### <a name="restrictions-on-libraries-and-sdks"></a>Restrição de bibliotecas e SDKs

Atualmente, o suporte de biblioteca para o ponto de extremidade v2.0 é limitado. Se deseja usar o ponto de extremidade v2.0 em um aplicativo de produção, você tem estas opções:

* Se estiver criando um aplicativo Web, você poderá usar com segurança nosso middleware do servidor disponível da Microsoft para realizar a entrada e a validação de token. Isso inclui o middleware OWIN Open ID Connect para ASP.NET e o plug-in Passport do Node.js. Para amostras de código que usam o middleware da Microsoft, consulte a seção [v2.0 introdução](v2-overview.md#getting-started).

* Se você estiver criando um aplicativo móvel ou de área de trabalho, poderá usar uma das MSAL (Bibliotecas de Autenticação da Microsoft) de versão prévia. Essas bibliotecas são uma versão prévia com suporte de produção; portanto, é seguro usá-las em aplicativos de produção. Leia mais sobre os termos da versão prévia e as bibliotecas disponíveis na [referência de bibliotecas de autenticação](reference-v2-libraries.md).

* Para outras plataformas não abrangidas pelas bibliotecas da Microsoft, é possível fazer a integração com o ponto de extremidade v2.0 enviando e recebendo diretamente mensagens de protocolo no código do aplicativo. Os protocolos OAuth e OpenID Connect v2.0 [foram explicitamente documentados](active-directory-v2-protocols.md) para ajudar você a executar essa integração.

* Por fim, você pode usar bibliotecas de software livre do Open ID Connect e do OAuth para fazer a integração com o ponto de extremidade v2.0. O protocolo v2.0 deve ser compatível com muitas bibliotecas de protocolo de software livre sem grandes alterações. A disponibilidade desses tipos de bibliotecas varia por idioma e plataforma. Os sites do [Open ID Connect](http://openid.net/connect/) e do [OAuth 2.0](http://oauth.net/2/) mantêm uma lista das implementações populares. Para saber mais, confira [Bibliotecas de autenticação e v2.0 do Azure Active Directory](reference-v2-libraries.md) e para obter a lista de bibliotecas de cliente de software livre e exemplos testados com o ponto de extremidade v2.0.

* Para referência, o ponto de extremidade `.well-known` para o ponto de extremidade comum v2.0 é `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` .  Substitua `common` pela ID do locatário para obter dados específicos para o locatário.  

### <a name="restrictions-on-protocols"></a>Restrições quanto a protocolos

O ponto de extremidade v2.0 não dá suporte ao SAML ou Web Services Federation; ele dá suporte apenas ao Open ID Connect e OAuth 2.0. Nem todos os recursos e funcionalidades dos protocolos OAuth foram incorporados ao ponto de extremidade v2.0.

No momento, os seguintes recursos e funcionalidades de protocolo *não estão disponíveis* ou *não têm suporte* no ponto de extremidade v2.0:

* A declaração `email` será retornada somente se uma declaração opcional estiver configurada e o escopo scope=email tiver sido especificado na solicitação. No entanto, esse comportamento deverá ser alterado conforme o ponto de extremidade v2.0 for atualizado para melhorar ainda mais a conformidade com os padrões Open ID Connect e OAuth2.0.

* O ponto de extremidade v2.0 não dá suporte à emissão de declarações de função ou de grupo em tokens de ID.

* O ponto de extremidade v2.0 não dá suporte à [Concessão de credenciais de senha de proprietário de recurso do OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.3).

Para entender melhor o escopo da funcionalidade de protocolo com suporte no ponto de extremidade v2.0, leia nossa [referência de protocolo do OpenID Connect e OAuth 2.0](active-directory-v2-protocols.md).

#### <a name="saml-restrictions"></a>Restrições de SAML

Se você usou a ADAL (Active Directory Authentication Library) em aplicativos Windows, poderá aproveitar a autenticação integrada do Windows, que usa a concessão de declaração SAML (Security Assertion Markup Language). Com essa concessão, os usuários de locatários do Azure AD federados se autenticam com suas instâncias do Active Directory local sem inserir as credenciais. Atualmente, a concessão de asserção SAML não tem suporte no ponto de extremidade v2.0.
