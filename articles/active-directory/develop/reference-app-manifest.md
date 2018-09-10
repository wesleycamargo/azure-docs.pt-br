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
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/27/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja, justhu
ms.openlocfilehash: f336771da334ffd964ecd032654b8549b7a5e847
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127013"
---
# <a name="azure-active-directory-app-manifest"></a>Manifesto do aplicativo do Azure Active Directory

Os aplicativos que se integram ao Azure AD (Azure Active Directory) precisam ser registrados em um locatário do Azure AD. Você pode configurar o aplicativo no [portal do Azure](https://portal.azure.com) selecionando **Azure Active Directory**, escolhendo o aplicativo que deseja configurar e, em seguida, selecionando **Manifesto**.

## <a name="manifest-reference"></a>Referência do manifesto

> [!NOTE]
> Se você não puder ver as descrições, maximize a janela do navegador ou role/passar o dedo para ver as descrições.

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]

| Chave  | Tipo de valor | Valor de exemplo | DESCRIÇÃO  |
|---------|---------|---------|---------|
| `appID` | Cadeia de caracteres do identificador | `"601790de-b632-4f57-9523-ee7cb6ceba95"` | Especifica o identificador exclusivo do aplicativo que é atribuído a um aplicativo pelo Azure AD. |
| `appRoles` | Tipo de matriz | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code> | Especifica a coleção de funções que um aplicativo pode declarar. Essas funções podem ser atribuídas a usuários, grupos ou entidades de serviço. |
| `availableToOtherTenants`| booleano | `true` | Se esse valor for definido como true, o aplicativo ficará disponível para outros locatários. Se definido como false, o aplicativo está apenas disponível para o locatário no qual está registrado. Para obter mais informações, confira [Como conectar qualquer usuário do Azure AD usando o padrão de aplicativo multilocatário](howto-convert-app-to-be-multi-tenant.md). |
| `displayName` | string | `MyRegisteredApp` | O nome de exibição do aplicativo. |
| `errorURL` | string | `http://MyRegisteredAppError` | A URL dos erros encontrados em um aplicativo. |
| `groupMembershipClaims` | string | `1` | Um bitmask que configura a declaração `groups` emitida em um usuário ou um token de acesso OAuth 2.0 que o aplicativo espera. Os valores de bitmask são:<br>0: nenhum<br>1: grupos de segurança e funções do Azure AD<br>2: reservado<br>4: reservado<br>Definir o bitmask para 7 obterá todos os grupos de segurança, grupos de distribuição e funções de diretório do Azure AD dos quais o usuário conectado é membro. |
| `optionalClaims` | string | `null` | As declarações opcionais retornadas no token pelo serviço de token de segurança para este aplicativo específico. Para obter mais informações, confira [declarações opcionais](active-directory-optional-claims.md). |
| `acceptMappedClaims` | booleano | `true` | Se esse valor for definido como `true`, ele permitirá que o aplicativo use o mapeamento de declarações sem especificar uma chave de autenticação personalizada. |
| `homepage` | string | `http://MyRegisteredApp` | Especifica a URL da home page do aplicativo. |
| `informationalUrls` | string | <code>{<br>&nbsp;&nbsp;&nbsp;"privacy":"http://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"http://MyRegisteredApp/termsofservice"<br>}</code> | Especifica os links para os termos de serviço e a política de privacidade do aplicativo. Os termos de serviço e a declaração de privacidade são revelados aos usuários por meio da experiência de consentimento do usuário. Para obter mais informações, confira [Como adicionar termos de serviço e política de privacidade para aplicativos do Azure AD registrados](howto-add-terms-of-service-privacy-statement.md). |
| `identifierUris` | Matriz de cadeia de caracteres | `http://MyRegistererdApp` | Os URIs definidos pelo usuário que identificam exclusivamente um aplicativo Web em seu locatário do Azure AD ou em um domínio personalizado verificado, quando o aplicativo é multilocatário. |
| `keyCredentials` | Tipo de matriz | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> | Contém referências a credenciais, segredos compartilhados com base em cadeia de caracteres e certificados X.509 atribuídos ao aplicativo. Essas credenciais são usadas ao solicitar tokens de acesso (quando o aplicativo está agindo como um cliente e não como um recurso). |
| `knownClientApplications` | Tipo de matriz | `[GUID]` | Usado para agrupamento de consentimento no caso de uma solução que contenha duas partes: um aplicativo cliente e um aplicativo de API Web personalizado. Se você inserir a appID do aplicativo cliente nesse valor, o usuário precisará consentir somente uma vez no aplicativo cliente. O Azure Active Directory saberá que consentir com o cliente significa concordar implicitamente com a API da Web e provisionará automaticamente entidades de serviço tanto para o cliente como para a API da Web ao mesmo tempo. O cliente e o aplicativo de API Web precisam ser registrados no mesmo locatário. |
| `logoutUrl` | string | `http://MyRegisteredAppLogout` | A URL para fazer logoff do aplicativo. |
| `oauth2AllowImplicitFlow` | booleano | `false` | Especifica se este aplicativo Web pode solicitar tokens de fluxo implícitos OAuth 2.0. O padrão é false. Esse sinalizador é usado para aplicativos baseados em navegador, como os aplicativos de página única Javascript. |
| `oauth2AllowUrlPathMatching` | booleano | `false` | Especifica se, como parte das solicitações de token OAuth 2.0, o Azure AD permitirá a correspondência de caminho do URI de redirecionamento com as replyUrls do aplicativo. O padrão é false. |
| `oauth2Permissions` | Tipo de matriz | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>]</code> | Especifica a coleção de escopos de permissão do OAuth 2.0 que o aplicativo de API Web (recurso) expõe aos aplicativos clientes. Esses escopos de permissões podem ser concedidos a aplicativos clientes durante o consentimento. |
| `oauth2RequiredPostResponse` | booleano | `false` | Especifica se, como parte das solicitações de token OAuth 2.0, o Azure AD permitirá solicitações POST, em vez de solicitações GET. O padrão é false, que especifica que somente as solicitações GET serão permitidas. |
| `objectId` | Cadeia de caracteres do identificador | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` | O identificador exclusivo do aplicativo no diretório. Essa ID não é o identificador usado para identificar o aplicativo em qualquer transação de protocolo. Ele é usado para referenciar o objeto em consultas de diretório. |
| `parentalControlSettings` | string | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> | `countriesBlockedForMinors` especifica os países em que o aplicativo está bloqueado para menores.<br>`legalAgeGroupRule` especifica a regra de grupo de faixa etária que se aplica a usuários do aplicativo. Pode ser definido como `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids` ou `BlockMinors`.  |
| `passwordCredentials` | Tipo de matriz | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> | Confira a descrição da propriedade `keyCredentials`. |
| `publicClient` | booleano | `false` | Especifica se um aplicativo é um cliente público, como um aplicativo instalado em execução em um dispositivo móvel. O valor padrão é falso. |
| `replyUrls` | Matriz de cadeia de caracteres | `"http://localhost"` | Essa propriedade de vários valores contém a lista de valores redirect_uri registrados que o Azure AD aceitará como destinos quando retornar tokens. |
| `requiredResourceAccess` | Tipo de matriz | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;]<br>&nbsp;}<br>] </code> | Com o consentimento dinâmico, o `requiredResourceAccess` gera a experiência de consentimento do administrador e a experiência de consentimento do usuário para usuários que estão usando o consentimento estático. No entanto, isso não gera a experiência de consentimento do usuário para o caso geral.<br>`resourceAppId` é o identificador exclusivo do recurso ao qual o aplicativo requer acesso. Esse valor deve ser igual à appId declarada no aplicativo do recurso de destino.<br>`resourceAccess` é uma matriz que lista os escopos de permissão do OAuth2.0 e as funções de aplicativo que o aplicativo exige do recurso especificado. Contém os valores `id` e `type` dos recursos especificados. |
| `samlMetadataUrl` | string | `http://MyRegisteredAppSAMLMetadata` | A URL dos metadados SAML do aplicativo. |

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

