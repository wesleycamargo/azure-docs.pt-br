---
title: Entendendo o manifesto do aplicativo do Azure Active Directory | Microsoft Docs
description: A cobertura detalhada do manifesto do aplicativo do Azure Active Directory, que representa a configuração de identidade de um aplicativo em um locatário do Azure AD, e é usado para facilitar a autorização do OAuth, a experiência de consentimento e muito mais.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/18/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e07e371afaa239ca423f4266557cd2f55aa3a55
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495251"
---
# <a name="azure-active-directory-app-manifest"></a>Manifesto do aplicativo do Azure Active Directory

O manifesto do aplicativo contém uma definição de todos os atributos de um objeto de aplicativo na plataforma de identidade da Microsoft. Ele também serve como um mecanismo para atualizar o objeto do aplicativo. Para obter mais informações sobre a entidade Aplicativo e seu esquema, confira a [Documentação da entidade do Aplicativo da API do Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity).

Você pode configurar os atributos de um aplicativo por meio do portal do Azure ou programaticamente usando o Microsoft Graph. No entanto, existem alguns cenários em que você precisará editar o manifesto do aplicativo para configurar o atributo de um aplicativo. Esses cenários incluem:

* Se você registrou o aplicativo como multi locação do Azure AD e contas pessoais da Microsoft, não é possível alterar as contas da Microsoft com suporte na interface do usuário. Em vez disso, você deve usar o editor de manifesto do aplicativo para alterar o tipo de conta suportado.
* Se você precisa definir permissões e funções que seu aplicativo suporta, você deve modificar o manifesto do aplicativo.

## <a name="configure-the-app-manifest"></a>Configurar o manifesto do aplicativo

Para configurar o manifesto do aplicativo:

1. Faça logon na [portal do Azure](https://portal.azure.com).
1. Selecione o serviço do **Active Directory do Azure** e, em seguida, selecione **Registros do aplicativo** ou **Registros do aplicativo (Visualização)**.
1. Selecione o aplicativo que você deseja configurar.
1. Na página **Visão Geral** do aplicativo, selecione a seção **Manifesto**. Um editor de manifesto baseado na Web é aberto, permitindo que você edite o manifesto no portal. Opcionalmente, você pode selecionar **Download** para editar o manifesto localmente e usar **Upload** para reaplicá-lo ao seu aplicativo.

## <a name="manifest-reference"></a>Referência do manifesto

> [!NOTE]
> Se você não conseguir vir a coluna **Valor de exemplo** após a **Descrição**, maximize a janela do navegador e role/passe o dedo até ver a coluna **Valor de exemplo**.

| Chave  | Tipo de valor | DESCRIÇÃO  | Valor de exemplo |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | Int32 anulável | Especifica a versão do token de acesso esperada pelo recurso. Isso altera a versão e o formato do JWT produzido, independentemente do ponto de extremidade ou do cliente usado para solicitar o token de acesso.<br/><br/>O ponto de extremidade usado, v1.0 ou v2.0, é escolhido pelo cliente e só afeta a versão do id_tokens. Os recursos precisam configurar explicitamente `accesstokenAcceptedVersion` para indicar o formato do token de acesso com suporte.<br/><br/>Os valores possíveis para `accesstokenAcceptedVersion` são 1, 2 ou nulo. Se o valor for nulo, será usado o padrão de 1, que corresponde ao ponto de extremidade v1.0. <br/><br/>Se `signInAudience` é `AzureADandPersonalMicrosoftAccount`, o valor deve ser `2` | `2` |
| `allowPublicClient` | booleano | Especifica o tipo de aplicativo de fallback. O Azure AD infere o tipo de aplicativo do replyUrlsWithType por padrão. Há alguns cenários nos quais o Azure AD não consegue determinar o tipo de aplicativo do cliente (por exemplo, o fluxo [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3), no qual a solicitação HTTP acontece sem um redirecionamento de URL). Nesses casos, o Azure AD interpretará o tipo de aplicativo com base no valor dessa propriedade. Se esse valor for definido como true, o tipo de aplicativo de fallback será definido como cliente público, como um aplicativo instalado em execução em um dispositivo móvel. O valor padrão é false, que significa que o tipo de aplicativo de fallback é cliente confidencial, como o aplicativo Web. | `false` |
| `appId` | Cadeia de caracteres do identificador | Especifica o identificador exclusivo do aplicativo que é atribuído a um aplicativo pelo Azure AD. | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | Tipo de matriz | Especifica a coleção de funções que um aplicativo pode declarar. Essas funções podem ser atribuídas a usuários, grupos ou entidades de serviço. Para ver exemplos e mais informações, consulte: [Adicionar funções de aplicativo ao seu aplicativo e recebê-las no token](howto-add-app-roles-in-azure-ad-apps.md) | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `groupMembershipClaims` | cadeia de caracteres | Configura a declaração `groups` emitida em um usuário ou o token de acesso OAuth 2.0 que o aplicativo espera. Para definir esse atributo, use um dos seguintes valores válidos da cadeia de caracteres:<br/><br/>- `"None"`<br/>- `"SecurityGroup"` (para grupos de segurança e funções do Azure AD)<br/>- `"All"` (isso obterá todos os grupos de segurança, grupos de distribuição e funções do diretório do Azure AD dos quais o usuário conectado é membro. | `"SecurityGroup"` |
| `optionalClaims` | cadeia de caracteres | As declarações opcionais retornadas no token pelo serviço de token de segurança para este aplicativo específico.<br>No momento, os aplicativos que oferecem suporte a contas pessoais e ao Azure AD (registrados por meio do portal de registro de aplicativos) não podem usar declarações opcionais. No entanto, os aplicativos registrados apenas para o Azure AD usando o ponto de extremidade v2.0 podem obter as declarações opcionais solicitadas no manifesto. Para obter mais informações, confira [declarações opcionais](active-directory-optional-claims.md). | `null` |
| `id` | Cadeia de caracteres do identificador | O identificador exclusivo do aplicativo no diretório. Essa ID não é o identificador usado para identificar o aplicativo em qualquer transação de protocolo. Ele é usado para referenciar o objeto em consultas de diretório. | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | Matriz de cadeia de caracteres | Os URIs definidos pelo usuário que identificam exclusivamente um aplicativo Web em seu locatário do Azure AD ou em um domínio personalizado verificado, quando o aplicativo é multilocatário. | <code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> |
| `informationalUrls` | cadeia de caracteres | Especifica os links para os termos de serviço e a política de privacidade do aplicativo. Os termos de serviço e a declaração de privacidade são revelados aos usuários por meio da experiência de consentimento do usuário. Para obter mais informações, consulte [Como: Adicionar termos de serviço e declaração de privacidade para aplicativos registrados do Azure AD](howto-add-terms-of-service-privacy-statement.md). | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | Tipo de matriz | Contém referências a credenciais, segredos compartilhados com base em cadeia de caracteres e certificados X.509 atribuídos ao aplicativo. Essas credenciais são usadas ao solicitar tokens de acesso (quando o aplicativo está agindo como um cliente e não como um recurso). | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | Tipo de matriz | Usado para agrupamento de consentimento no caso de uma solução que contenha duas partes: um aplicativo cliente e um aplicativo de API Web personalizado. Se você inserir a appID do aplicativo cliente nesse valor, o usuário precisará consentir somente uma vez no aplicativo cliente. O Azure Active Directory saberá que consentir com o cliente significa concordar implicitamente com a API da Web e provisionará automaticamente entidades de serviço tanto para o cliente como para a API da Web ao mesmo tempo. O cliente e o aplicativo de API Web precisam ser registrados no mesmo locatário. | `[GUID]` |
| `logoUrl` | cadeia de caracteres | Leia apenas o valor que aponta para a URL da CDN para o logotipo que foi carregado no portal. | `https://MyRegisteredAppLogo` |
| `logoutUrl` | cadeia de caracteres | A URL para fazer logoff do aplicativo. | `https://MyRegisteredAppLogout` |
| `name` | cadeia de caracteres | O nome de exibição do aplicativo. | `MyRegisteredApp` |
| `oauth2AllowImplicitFlow` | booleano | Especifica se este aplicativo Web pode solicitar tokens de acesso de fluxo implícitos OAuth 2.0. O padrão é false. Esse sinalizador é usado para aplicativos baseados em navegador, como os aplicativos de página única JavaScript. Para saber mais, digite `OAuth 2.0 implicit grant flow` no sumário e consulte os tópicos sobre fluxo implícito. | `false` |
| `oauth2AllowIdTokenImplicitFlow` | booleano | Especifica se este aplicativo Web pode solicitar os tokens de ID de fluxo implícitos OAuth 2.0. O padrão é false. Esse sinalizador é usado para aplicativos baseados em navegador, como os aplicativos de página única JavaScript. | `false` |
| `oauth2Permissions` | Tipo de matriz | Especifica a coleção de escopos de permissão do OAuth 2.0 que o aplicativo de API Web (recurso) expõe aos aplicativos clientes. Esses escopos de permissões podem ser concedidos a aplicativos clientes durante o consentimento. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>] </code>|
| `oauth2RequiredPostResponse` | booleano | Especifica se, como parte das solicitações de token OAuth 2.0, o Azure AD permitirá solicitações POST, em vez de solicitações GET. O padrão é false, que especifica que somente as solicitações GET serão permitidas. | `false` |
| `parentalControlSettings` | cadeia de caracteres | `countriesBlockedForMinors` Especifica os países em que o aplicativo está bloqueado para os menores.<br>`legalAgeGroupRule` Especifica a regra de grupo etária que se aplica a usuários do aplicativo. Pode ser definido como `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids` ou `BlockMinors`.  | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> |
| `passwordCredentials` | Tipo de matriz | Confira a descrição da propriedade `keyCredentials`. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> |
| `preAuthorizedApplications` | Tipo de matriz | Lista os aplicativos e as permissões solicitadas para consentimento implícito. Exige que um administrador tenha fornecido o consentimento para o aplicativo. preAuthorizedApplications não exigem que o usuário consinta com as permissões solicitadas. As permissões listadas em preAuthorizedApplications não exigem o consentimento do usuário. No entanto, quaisquer permissões solicitadas adicionais não listadas no preAuthorizedApplications exigem o consentimento do usuário. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `replyUrlsWithType` | Matriz de cadeia de caracteres | Essa propriedade de vários valores contém a lista de valores redirect_uri registrados que o Azure AD aceitará como destinos quando retornar tokens. Cada valor de URI deve conter um valor de tipo de aplicativo associado. Os valores de tipo com suporte são: `Web`, `InstalledClient`. | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | Tipo de matriz | Com o consentimento dinâmico, o `requiredResourceAccess` gera a experiência de consentimento do administrador e a experiência de consentimento do usuário para usuários que estão usando o consentimento estático. No entanto, isso não gera a experiência de consentimento do usuário para o caso geral.<br>`resourceAppId` é o identificador exclusivo para o que o aplicativo requer acesso ao recurso. Esse valor deve ser igual à appId declarada no aplicativo do recurso de destino.<br>`resourceAccess` é uma matriz que lista os escopos de permissão OAuth2.0 e funções de aplicativo que o aplicativo requer do recurso especificado. Contém os valores `id` e `type` dos recursos especificados. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> |
| `samlMetadataUrl` | cadeia de caracteres | A URL dos metadados SAML do aplicativo. | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | cadeia de caracteres | Especifica a URL da home page do aplicativo. | `https://MyRegisteredApp` |
| `signInAudience` | cadeia de caracteres | Especifica quais contas da Microsoft são suportadas para o aplicativo atual. Os valores para os quais há suporte são:<ul><li>**AzureADMyOrg** – usuários com uma conta corporativa ou de estudante da Microsoft no locatário do Azure AD da minha organização (ou seja, locatário único)</li><li>**AzureADMultipleOrgs** – usuários com uma conta corporativa ou de estudante da Microsoft no locatário do Azure AD de qualquer organização (ou seja, multilocatário)</li> <li>**AzureADandPersonalMicrosoftAccount** – usuários com uma conta Microsoft pessoal ou uma conta corporativa ou de estudante no locatário do Azure AD de qualquer organização</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | Matriz de cadeia de caracteres | Cadeias de caracteres personalizadas que podem ser usadas para categorizar e identificar o aplicativo. | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |


## <a name="manifest-limits"></a>Limites de manifesto
Um manifesto de aplicativo tem vários atributos que são conhecidos como coleções, por exemplo, approles, keycredentials, knownClientApplications, identifierUris, rediretUris, requiredResourceAccess, oauth2Permissions etc. No manifesto do aplicativo completo para qualquer aplicativo, o número total de entradas em todas as coleções combinadas tem sido limitado a 1200. Se você já tiver 100 URI de redirecionamento especificado no manifesto do aplicativo, você estará apenas esquerda com o restante de 1100 entradas a serem usadas em todas as outras coleções combinados que compõem o manifesto.

> [!NOTE]
> No caso de você tentar adicionar entradas de mais de 1200 no manifesto do aplicativo. Você pode receber um erro **"Falha ao atualizar o aplicativo xxxxxx. Detalhes do erro: O tamanho do manifesto excedeu seu limite. Por favor, reduzir o número de valores e repita a solicitação.** "


## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a relação entre os objetos Aplicativo e Entidade de Serviço do aplicativo, confira [Objetos de aplicativo e de entidade de serviço no Azure AD](app-objects-and-service-principals.md).
* Veja as definições do [Glossário de desenvolvedor do Azure AD](developer-glossary.md) de alguns dos conceitos de desenvolvedor do Azure Active Directory (AD).

Use a seção de comentários a seguir para dar sua opinião e nos ajudar a aprimorar e adaptar nosso conteúdo.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
