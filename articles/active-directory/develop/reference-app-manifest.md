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
ms.date: 04/13/2019
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18ff5c4c54cdfe03eca572e2aa42f2330597c94d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918765"
---
# <a name="azure-active-directory-app-manifest"></a>Manifesto do aplicativo do Azure Active Directory

O manifesto do aplicativo contém uma definição de todos os atributos de um objeto de aplicativo na plataforma de identidade da Microsoft. Ele também serve como um mecanismo para atualizar o objeto do aplicativo. Para obter mais informações sobre a entidade de aplicativo e seu esquema, consulte o [documentação da entidade de aplicativo de API do Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity).

Você pode configurar atributos de um aplicativo por meio do portal do Azure ou utilize programaticamente [API REST](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) ou [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications). No entanto, existem alguns cenários em que você precisará editar o manifesto do aplicativo para configurar o atributo de um aplicativo. Esses cenários incluem:

* Se você registrou o aplicativo como contas da Microsoft pessoais e de vários locatários do Azure AD, é possível alterar as contas da Microsoft com suporte na interface do usuário. Em vez disso, você deve usar o editor de manifesto do aplicativo para alterar o tipo de conta suportado.
* Se você precisa definir permissões e funções que seu aplicativo suporta, você deve modificar o manifesto do aplicativo.

## <a name="configure-the-app-manifest"></a>Configurar o manifesto do aplicativo

Para configurar o manifesto do aplicativo:

1. Entrar a [portal do Azure](https://portal.azure.com).
1. Selecione o **Azure Active Directory** de serviço e, em seguida, selecione **registros de aplicativo**.
1. Selecione o aplicativo que você deseja configurar.
1. Na página **Visão Geral** do aplicativo, selecione a seção **Manifesto**. Um editor de manifesto baseado na Web é aberto, permitindo que você edite o manifesto no portal. Opcionalmente, você pode selecionar **Download** para editar o manifesto localmente e usar **Upload** para reaplicá-lo ao seu aplicativo.

## <a name="manifest-reference"></a>Referência do manifesto

> [!NOTE]
> Se você não conseguir vir a coluna **Valor de exemplo** após a **Descrição**, maximize a janela do navegador e role/passe o dedo até ver a coluna **Valor de exemplo**.

| Chave  | Tipo de valor | DESCRIÇÃO  | Valor de exemplo |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | Int32 anulável | Especifica a versão do token de acesso esperada pelo recurso. Isso altera a versão e o formato do JWT produzido, independentemente do ponto de extremidade ou do cliente usado para solicitar o token de acesso.<br/><br/>O ponto de extremidade usado, v1.0 ou v2.0, é escolhido pelo cliente e só afeta a versão do id_tokens. Os recursos precisam configurar explicitamente `accesstokenAcceptedVersion` para indicar o formato do token de acesso com suporte.<br/><br/>Os valores possíveis para `accesstokenAcceptedVersion` são 1, 2 ou nulo. Se o valor for nulo, será usado o padrão de 1, que corresponde ao ponto de extremidade v1.0. | `2` |
| `addIns` | Coleção | Define o comportamento personalizado que um serviço consumidor pode usar para chamar um aplicativo em contextos específicos. Por exemplo, aplicativos que podem renderizar fluxos de arquivos podem definir a propriedade de suplementos para funcionalidade de "FileHandler". Isso permitirá que serviços como o Office 365 chamem o aplicativo no contexto de um documento que o usuário está trabalhando. | <code>{<br>&nbsp;&nbsp;&nbsp;"id":"968A844F-7A47-430C-9163-07AE7C31D407"<br>&nbsp;&nbsp;&nbsp;"type": "FileHandler",<br>&nbsp;&nbsp;&nbsp;"properties": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{"key": "version", "value": "2" }<br>&nbsp;&nbsp;&nbsp;]<br>}</code>|
| `allowPublicClient` | Boolean | Especifica o tipo de aplicativo de fallback. O Azure AD infere o tipo de aplicativo do replyUrlsWithType por padrão. Há alguns cenários nos quais o Azure AD não consegue determinar o tipo de aplicativo do cliente (por exemplo, o fluxo [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3), no qual a solicitação HTTP acontece sem um redirecionamento de URL). Nesses casos, o Azure AD interpretará o tipo de aplicativo com base no valor dessa propriedade. Se esse valor for definido como true, o tipo de aplicativo de fallback será definido como cliente público, como um aplicativo instalado em execução em um dispositivo móvel. O valor padrão é false, que significa que o tipo de aplicativo de fallback é cliente confidencial, como o aplicativo Web. | `false` |
| `availableToOtherTenants` | Boolean | True se o aplicativo for compartilhado com outros locatários; Caso contrário, false. <br><br> _Observação: Isso está disponível apenas na experiência de registros (herdado) do aplicativo. Substituído por `signInAudience` no [registros do aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência._ | |
| `appId` | Cadeia de caracteres | Especifica o identificador exclusivo do aplicativo que é atribuído a um aplicativo pelo Azure AD. | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | Coleção | Especifica a coleção de funções que um aplicativo pode declarar. Essas funções podem ser atribuídas a usuários, grupos ou entidades de serviço. Para ver exemplos e mais informações, consulte: [Adicionar funções de aplicativo ao seu aplicativo e recebê-las no token](howto-add-app-roles-in-azure-ad-apps.md) | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `displayName` | Cadeia de caracteres | O nome de exibição do aplicativo. <br><br> _Observação: Isso está disponível apenas na experiência de registros (herdado) do aplicativo. Substituído por `name` no [registros do aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência._ | `"MyRegisteredApp"` |
| `errorUrl` | Cadeia de caracteres | Sem suporte. | |
| `groupMembershipClaims` | Cadeia de caracteres | Configura a declaração `groups` emitida em um usuário ou o token de acesso OAuth 2.0 que o aplicativo espera. Para definir esse atributo, use um dos seguintes valores válidos da cadeia de caracteres:<br/><br/>- `"None"`<br/>- `"SecurityGroup"` (para grupos de segurança e funções do Azure AD)<br/>- `"All"` (isso obterá todos os grupos de segurança, grupos de distribuição e funções do diretório do Azure AD dos quais o usuário conectado é membro. | `"SecurityGroup"` |
| `homepage` | Cadeia de caracteres | A URL da home page do aplicativo. <br><br> _Observação: Isso está disponível apenas na experiência de registros (herdado) do aplicativo. Substituído por `signInUrl` no [registros do aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência._ | `"https://MyRegisteredApp"` |
| `objectId` | Cadeia de caracteres | O identificador exclusivo do aplicativo no diretório. <br><br> _Observação: Isso está disponível apenas na experiência de registros (herdado) do aplicativo. Substituído por `id` no [registros do aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência._ | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `optionalClaims` | Cadeia de caracteres | As declarações opcionais retornadas no token pelo serviço de token de segurança para este aplicativo específico.<br>No momento, os aplicativos que oferecem suporte a contas pessoais e ao Azure AD (registrados por meio do portal de registro de aplicativos) não podem usar declarações opcionais. No entanto, os aplicativos registrados apenas para o Azure AD usando o ponto de extremidade v2.0 podem obter as declarações opcionais solicitadas no manifesto. Para obter mais informações, confira [declarações opcionais](active-directory-optional-claims.md). | `null` |
| `id` | Cadeia de caracteres | O identificador exclusivo do aplicativo no diretório. Essa ID não é o identificador usado para identificar o aplicativo em qualquer transação de protocolo. Ele é usado para referenciar o objeto em consultas de diretório. | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | String Array | Os URIs definidos pelo usuário que identificam exclusivamente um aplicativo Web em seu locatário do Azure AD ou em um domínio personalizado verificado, quando o aplicativo é multilocatário. | <code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> |
| `informationalUrls` | Cadeia de caracteres | Especifica os links para os termos de serviço e a política de privacidade do aplicativo. Os termos de serviço e a declaração de privacidade são revelados aos usuários por meio da experiência de consentimento do usuário. Para obter mais informações, consulte [Como: Adicionar termos de serviço e declaração de privacidade para aplicativos registrados do Azure AD](howto-add-terms-of-service-privacy-statement.md). | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | Coleção | Contém referências a credenciais, segredos compartilhados com base em cadeia de caracteres e certificados X.509 atribuídos ao aplicativo. Essas credenciais são usadas ao solicitar tokens de acesso (quando o aplicativo está agindo como um cliente e não como um recurso). | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | String Array | Usado para agrupamento de consentimento no caso de uma solução que contenha duas partes: um aplicativo cliente e um aplicativo de API Web personalizado. Se você inserir a appID do aplicativo cliente nesse valor, o usuário precisará consentir somente uma vez no aplicativo cliente. O Azure Active Directory saberá que consentir com o cliente significa concordar implicitamente com a API da Web e provisionará automaticamente entidades de serviço tanto para o cliente como para a API da Web ao mesmo tempo. O cliente e o aplicativo de API Web precisam ser registrados no mesmo locatário. | `["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"]` |
| `logoUrl` | Cadeia de caracteres | Leia apenas o valor que aponta para a URL da CDN para o logotipo que foi carregado no portal. | `"https://MyRegisteredAppLogo"` |
| `logoutUrl` | Cadeia de caracteres | A URL para fazer logoff do aplicativo. | `"https://MyRegisteredAppLogout"` |
| `name` | Cadeia de caracteres | O nome de exibição do aplicativo. | `"MyRegisteredApp"` |
| `oauth2AllowImplicitFlow` | Boolean | Especifica se este aplicativo Web pode solicitar tokens de acesso de fluxo implícitos OAuth 2.0. O padrão é false. Esse sinalizador é usado para aplicativos baseados em navegador, como os aplicativos de página única JavaScript. Para saber mais, digite `OAuth 2.0 implicit grant flow` no sumário e consulte os tópicos sobre fluxo implícito. | `false` |
| `oauth2AllowIdTokenImplicitFlow` | Boolean | Especifica se este aplicativo Web pode solicitar os tokens de ID de fluxo implícitos OAuth 2.0. O padrão é false. Esse sinalizador é usado para aplicativos baseados em navegador, como os aplicativos de página única JavaScript. | `false` |
| `oauth2Permissions` | Coleção | Especifica a coleção de escopos de permissão do OAuth 2.0 que o aplicativo de API Web (recurso) expõe aos aplicativos clientes. Esses escopos de permissões podem ser concedidos a aplicativos clientes durante o consentimento. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>] </code>|
| `oauth2RequiredPostResponse` | Boolean | Especifica se, como parte das solicitações de token OAuth 2.0, o Azure AD permitirá solicitações POST, em vez de solicitações GET. O padrão é false, que especifica que somente as solicitações GET serão permitidas. | `false` |
| `parentalControlSettings` | Cadeia de caracteres | `countriesBlockedForMinors` especifica os países em que o aplicativo está bloqueado para menores.<br>`legalAgeGroupRule` especifica a regra de grupo de faixa etária que se aplica a usuários do aplicativo. Pode ser definido como `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids` ou `BlockMinors`.  | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> |
| `passwordCredentials` | Coleção | Confira a descrição da propriedade `keyCredentials`. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> |
| `preAuthorizedApplications` | Coleção | Lista os aplicativos e as permissões solicitadas para consentimento implícito. Exige que um administrador tenha fornecido o consentimento para o aplicativo. preAuthorizedApplications não exigem que o usuário consinta com as permissões solicitadas. As permissões listadas em preAuthorizedApplications não exigem o consentimento do usuário. No entanto, quaisquer permissões solicitadas adicionais não listadas no preAuthorizedApplications exigem o consentimento do usuário. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `publicClient` | Boolean | Especifica se este aplicativo é um cliente público (por exemplo, um aplicativo instalado em execução em um dispositivo móvel). <br><br> _Observação: Isso está disponível apenas na experiência de registros (herdado) do aplicativo. Substituído por `allowPublicClient` no [registros do aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência._ | |
| `publisherDomain` | Cadeia de caracteres | O domínio verificado do publicador para o aplicativo. Somente leitura. | https://www.contoso.com |
| `replyUrls` | Matriz de cadeia de caracteres | Essa propriedade de vários valores contém a lista de valores redirect_uri registrados que o Azure AD aceitará como destinos quando retornar tokens. <br><br> _Observação: Isso está disponível apenas na experiência de registros (herdado) do aplicativo. Substituído por `replyUrlsWithType` no [registros do aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência._ | |
| `replyUrlsWithType` | Coleção | Essa propriedade de vários valores contém a lista de valores redirect_uri registrados que o Azure AD aceitará como destinos quando retornar tokens. Cada valor de URI deve conter um valor de tipo de aplicativo associado. Os valores de tipo com suporte são: `Web`, `InstalledClient`. | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | Coleção | Com o consentimento dinâmico, o `requiredResourceAccess` gera a experiência de consentimento do administrador e a experiência de consentimento do usuário para usuários que estão usando o consentimento estático. No entanto, isso não gera a experiência de consentimento do usuário para o caso geral.<br>`resourceAppId` é o identificador exclusivo do recurso ao qual o aplicativo requer acesso. Esse valor deve ser igual à appId declarada no aplicativo do recurso de destino.<br>`resourceAccess` é uma matriz que lista os escopos de permissão do OAuth2.0 e as funções de aplicativo que o aplicativo exige do recurso especificado. Contém os valores `id` e `type` dos recursos especificados. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> |
| `samlMetadataUrl` | Cadeia de caracteres | A URL dos metadados SAML do aplicativo. | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | Cadeia de caracteres | Especifica a URL da home page do aplicativo. | `https://MyRegisteredApp` |
| `signInAudience` | Cadeia de caracteres | Especifica quais contas da Microsoft são suportadas para o aplicativo atual. Os valores para os quais há suporte são:<ul><li>**AzureADMyOrg** – usuários com uma conta corporativa ou de estudante da Microsoft no locatário do Azure AD da minha organização (ou seja, locatário único)</li><li>**AzureADMultipleOrgs** – usuários com uma conta corporativa ou de estudante da Microsoft no locatário do Azure AD de qualquer organização (ou seja, multilocatário)</li> <li>**AzureADandPersonalMicrosoftAccount** – usuários com uma conta Microsoft pessoal ou uma conta corporativa ou de estudante no locatário do Azure AD de qualquer organização</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | String Array | Cadeias de caracteres personalizadas que podem ser usadas para categorizar e identificar o aplicativo. | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |

## <a name="common-issues"></a>Problemas comuns

### <a name="manifest-limits"></a>Limites de manifesto

Um manifesto de aplicativo tem vários atributos que são chamados de coleções; Por exemplo, approles, keycredentials, knownClientApplications, identifierUris, rediretUris, requiredResourceAccess e oauth2Permissions. No manifesto do aplicativo completo para qualquer aplicativo, o número total de entradas em todas as coleções combinadas tem sido limitado a 1200. Se você já tiver 100 redirecionar URIs especificados no manifesto do aplicativo, você está apenas esquerda com 1100 entradas restantes para usar em todas as outras coleções combinadas que compõem o manifesto.

> [!NOTE]
> No caso de você tenta adicionar entradas de mais de 1200 no manifesto do aplicativo, você poderá ver um erro **"Falha ao atualizar o aplicativo xxxxxx. Detalhes do erro: O tamanho do manifesto excedeu seu limite. Reduzir o número de valores e repita a solicitação."**

### <a name="unsupported-attributes"></a>Atributos sem suporte

O manifesto de aplicativo representa o esquema do modelo de aplicativo subjacente no Azure AD. À medida que o esquema subjacente evolui, o editor de manifesto será atualizado para refletir o novo esquema de tempos em tempos. Como resultado, você pode perceber novos atributos aparecendo no manifesto do aplicativo. Em raras ocasiões, você pode observar uma alteração sintática ou semântica em um dos atributos existentes ou você pode encontrar um atributo que existia anteriormente não são tem mais suporte. Por exemplo, você verá novos atributos na [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) que são conhecidos com um nome diferente na experiência de registros (herdado) do aplicativo.


| Registros de aplicativo (herdado)| Registros de aplicativo           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Para obter descrições para esses atributos, consulte o [referência do manifesto](#manifest-reference) seção.

Quando você tenta carregar um manifesto baixado anteriormente, você poderá ver um dos seguintes erros. Isso provavelmente ocorre porque o editor de manifesto agora dá suporte a uma versão mais recente do esquema, que não coincide com aquele que você está tentando carregar.

- "**Falha ao atualizar o aplicativo xxxxxx. Detalhe do erro: Identificador de objeto inválido 'undefined'. [].** "
- "**Falha ao atualizar o aplicativo xxxxxx. Detalhe do erro: Um ou mais valores de propriedade especificados são inválidos. [].** "
- "**Falha ao atualizar o aplicativo xxxxxx. Detalhe do erro: Não é permitido definir availableToOtherTenants nesta versão de api para atualização. [].** "
- "**Falha ao atualizar o aplicativo xxxxxx. Detalhe do erro: Atualizações para a propriedade 'replyUrls' não é permitido para este aplicativo. Use a propriedade 'replyUrlsWithType' em vez disso. [].** "
- "**Falha ao atualizar o aplicativo xxxxxx. Detalhe do erro: Um valor sem um nome de tipo foi encontrado e nenhum tipo esperado está disponível. Quando o modelo for especificado, cada valor na carga deve ter um tipo que pode ser especificado explicitamente na carga, pelo chamador ou implicitamente inferido do valor pai. []**"

Quando você vir um desses erros, recomendamos o seguinte:

1. Edite os atributos individualmente no editor de manifesto em vez de carregar um manifesto baixado anteriormente. Use o [referência do manifesto](#manifest-reference) tabela para entender a sintaxe e semântica antigos e novos atributos para que você possa editar com êxito os atributos que você está interessado. 
1. Se seu fluxo de trabalho exige que você salve os manifestos em seu repositório de origem para uso posterior, sugerimos que os manifestos salvos em seu repositório com aquele que você vê na troca de base de **registros de aplicativo** experiência.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a relação entre objetos de entidade de serviço e aplicativo de um aplicativo, consulte [aplicativo e objetos de entidade de serviço no Azure AD](app-objects-and-service-principals.md).
* Consulte a [Glossário de desenvolvedor do Microsoft identity platform](developer-glossary.md) para definições de parte da Microsoft identity platform conceitos de desenvolvedor.

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
