---
title: "Noções básicas sobre o manifesto de aplicativo do Azure Active Directory | Microsoft Docs"
description: "Cobertura detalhada do manifesto do aplicativo do Azure Active Directory, que representa a configuração de identidade de um aplicativo em um locatário do AD do Azure, e é usado para facilitar a autorização do OAuth, experiência de consentimento e muito mais."
services: active-directory
documentationcenter: 
author: sureshja
manager: mtillman
editor: 
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: sureshja
ms.custom: aaddev
ms.reviewer: elisol
ms.openlocfilehash: f3284d4cbb15f21522549c678410815b54344744
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2017
---
# <a name="azure-active-directory-application-manifest"></a>Manifesto do aplicativo do Azure Active Directory
Aplicativos que se integram com o Azure AD devem ser registrados com um locatário do Azure AD. Este aplicativo pode ser configurado usando o manifesto do aplicativo (na folha do Azure AD) no [portal do Azure](https://portal.azure.com).

## <a name="manifest-reference"></a>Referência do manifesto

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]
|Chave  |Tipo de valor |Exemplo de valor  |DESCRIÇÃO  |
|---------|---------|---------|---------|
|appID     |  Cadeia de caracteres do identificador       |""|  O identificador exclusivo para o aplicativo que está atribuído a um aplicativo do Azure AD.|
|appRoles     |    Tipo de matriz     |[{<br>&emsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"Usuário"<br>&emsp;],<br>&emsp;"Descrição": "Acesso de somente leitura às informações do dispositivo",<br>&emsp;"displayName": "Somente leitura",<br>&emsp;"id": guid,<br>&emsp;"isEnabled": true,<br>&emsp;"valor": "ReadOnly"<br>}]|A coleção de funções que um aplicativo pode declarar. Essas funções podem ser atribuídas a usuários, grupos ou entidades de serviço.|
|availableToOtherTenants|booleano|verdadeiro|Se esse valor é definido como true, o aplicativo está disponível para outros locatários.  Se definido como false, o aplicativo está apenas disponível para o locatário no qual está registrado.  Para mais informações, consulte: [Como conectar qualquer usuário do Azure Active Directory (AD) usando o padrão de aplicativo multilocatário](active-directory-devhowto-multi-tenant-overview.md). |
|displayName     |string         |MyRegisteredApp         |O nome de exibição do aplicativo. |
|errorURL     |string         |http:<i></i>//MyRegisteredAppError         |A URL dos erros encontrados em um aplicativo. |
|GroupMembershipClaims     |    string     |    1     |   Um bitmask que configura a declaração "groups" emitida em um usuário ou um token de acesso OAuth 2.0 que o aplicativo espera. Os valores de bitmask são: 0: nenhum, 1: grupos de segurança e funções do Azure AD, 2: reservado e 4: reservado. Definir o bitmask para 7 obterá todos os grupos de segurança, grupos de distribuição e funções de diretório do Azure AD dos quais o usuário conectado é membro.      |
|optionalClaims     |  string       |     nulo    |    As declarações opcionais retornadas no token pelo serviço de token de segurança para este aplicativo específico.     |
|acceptMappedClaims    |      booleano   | verdadeiro        |    Se este valor é definido como true, ele permite que um aplicativo use o mapeamento de declarações sem especificar uma chave de autenticação personalizada.|
|homepage     |  string       |http:<i></i>//MyRegistererdApp         |    A URL da página inicial do aplicativo.     |
|identifierUris     |  Matriz de cadeia de caracteres       | http:<i></i>//MyRegistererdApp        |   As URIs definidas pelo usuário que identificam exclusivamente um aplicativo da Web em seu locatário do Azure AD, ou em um domínio personalizado verificado se o aplicativo for multilocatário.      |
|keyCredentials     |   Tipo de matriz      |   [{<br>&nbsp;"customKeyIdentifier": null,<br>"endDate":"2018-09-13T00:00:00Z",<br>"keyId":"\<guid>",<br>"startDate":"2017-09-12T00:00:00Z",<br>"type":"AsymmetricX509Cert",<br>"usage":"Verify",<br>"value":null<br>}]      |   Esta propriedade contém referências a credenciais de aplicativo atribuídas, segredos compartilhados com base em cadeia de caracteres e certificados x. 509.  Essas credenciais entram em jogo ao solicitar tokens de acesso (quando o aplicativo está agindo como um cliente em vez de um recurso).     |
|knownClientApplications     |     Tipo de matriz    |    [guid]     |     O valor é usado para agrupamento de consentimento se você tiver uma solução que contém duas partes, um aplicativo cliente e um aplicativo de API da web personalizado. Se você inserir a appID do aplicativo cliente nesse valor, o usuário terá somente terá que consentir uma vez com o aplicativo cliente. O Azure AD saberá que o consentimento com o cliente significa consentir implicitamente com a API da web e provisionará automaticamente entidades de serviço para o cliente e a API da web ao mesmo tempo (o cliente e o aplicativo da API da web devem ser registrados no mesmo Locatário).|
|logoutUrl     |   string      |     http:<i></i>//MyRegisteredAppLogout    |   A URL de logout do aplicativo.      |
|oauth2AllowImplicitFlow     |   booleano      |  falso       |       Especifica se este aplicativo web pode solicitar tokens de fluxo implícitos OAuth 2.0. O padrão é false. Isso é usado para aplicativos baseados em navegador, como os aplicativos de página única Javascript. |
|oauth2AllowUrlPathMatching     |   booleano      |  falso       |   Especifica se, como parte das solicitações de token OAuth 2.0, do Azure AD permitirá a correspondência de caminho do URI de redirecionamento em relação aos replyUrls do aplicativo. O padrão é false.      |
|oauth2Permissions     | Tipo de matriz         |      [{<br>"adminConsentDescription": "Permitir que o aplicativo acesse recursos em nome do usuário conectado.",<br>"adminConsentDisplayName": "Acesso a resource1",<br>"id": "\<guid >",<br>"isEnabled":true,<br>"tipo": "Usuário",<br>"userConsentDescription":"Permitir que o aplicativo acesse resouce1 em seu nome",<br>"userConsentDisplayName": "Acesso a recursos"<br>"valor": "user_impersonation"<br>}]   |  A coleção de escopos de permissões OAuth 2.0 que expõe o aplicativo de API da web (recurso) para aplicativos cliente. Os escopos de permissões podem ser concedidos a aplicativos cliente durante o consentimento. |
|oauth2RequiredPostResponse     | booleano        |    falso     |      Especifica se, como parte das solicitações de token OAuth 2.0, o Azure AD permitirá solicitações POST, em vez de solicitações GET. O padrão é false, que especifica que somente as solicitações GET serão permitidas.   
|ID do objeto     | Cadeia de caracteres do identificador        |     ""    |    Um identificador exclusivo do aplicativo no diretório.  Isso não é o identificador usado para identificar o aplicativo em qualquer transação de protocolo.  Ele é o usuário para a referência ao objeto em consultas de diretório.|
|passwordCredentials     | Tipo de matriz        |   [{<br>"customKeyIdentifier": null,<br>"endDate":"2018-10-19T17:59:59.6521653Z",<br>"keyId":"\<guid>",<br>"startDate":"2016-10-19T17:59:59.6521653Z",<br>"value":null<br>}]      |    Consulte a descrição da propriedade keyCredentials.     |
|publicClient     |  booleano       |      falso   | Especifica se um aplicativo é um cliente público (como um aplicativo instalado em execução em um dispositivo móvel). O padrão é falso.        |
|supportsConvergence     |  booleano       |   falso      | Essa propriedade não deve ser editada.  Aceite o valor padrão.        |
|replyUrls     |  Matriz de cadeia de caracteres       |   http:<i></i>//localhost     |  Esta propriedade de vários valores contém a lista de valores redirect_uri registrados que o Azure AD aceitará como destinos quando retornar tokens. |
|requiredResourceAccess     |     Tipo de matriz    |    [{<br>"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>"resourceAccess":[{<br>&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;"tipo":"Escopo"<br>&nbsp;&nbsp;}]<br>}]     |   Especifica os recursos para os quais este aplicativo requer acesso e o conjunto de escopos de permissão OAuth e funções de aplicativo que ele precisa em cada um desses recursos. Esta configuração prévia de acesso aos recursos necessários conduz a experiência de consentimento.|
|resourceAppId     |    Cadeia de caracteres do identificador     |  ""      |   O identificador exclusivo para o recurso que o aplicativo requer acesso. Este valor deve ser igual à appId declarada no aplicativo de recurso de destino.     |
|resourceAccess     |  Tipo de matriz       | Consulte o valor de exemplo da propriedade requiredResourceAccess.        |   A lista de escopos de permissão OAuth 2.0 e funções de aplicativo que o aplicativo requer do recurso especificado (contém os valores de ID e o tipo de recursos especificados)        |
|samlMetadataUrl|string|http:<i></i>//MyRegisteredAppSAMLMetadata|A URL para os metadados SAML do aplicativo.| 

## <a name="next-steps"></a>Próximas etapas
* Para obter mais informações sobre o relacionamento entre os objetos de Aplicativo e de Entidade de Serviço do aplicativo, consulte [Objetos de aplicativo e de entidade de serviço no Azure AD][AAD-APP-OBJECTS].
* Veja as definições do [Glossário de desenvolvedor do Azure AD][AAD-DEVELOPER-GLOSSARY] de alguns dos conceitos de desenvolvedor do Azure Active Directory (AD).

Use a seção de comentários a seguir para dar sua opinião e nos ajudar a aprimorar e adaptar nosso conteúdo.

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

