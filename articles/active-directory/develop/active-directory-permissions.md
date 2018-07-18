---
title: Permissões no Microsoft Azure Active Directory | Microsoft docs
description: Saiba mais sobre escopos e permissões no Microsoft Azure Active Directory e como usá-las
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 6c0dc122-2cd8-4d70-be5a-3943459d308e
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 04/20/2017
ms.author: celested
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: 749253d6a082bcdc2b80c5984f20c4b8c4039ad0
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2018
---
# <a name="permissions-in-azure-ad"></a>Permissões no Microsoft Azure Active Directory
O Microsoft Azure Active Directory (Azure AD) faz uso extensivo de permissões para fluxos OAuth e OpenID Connect (OIDC). Quando seu aplicativo receber um token de acesso do Microsoft Active Directory, ele incluirá declarações que descrevem as permissões (também conhecido como escopos) que seu aplicativo tem em relação a um recurso específico. Isso facilita a autorização para o recurso porque só precisa verificar se seu token contém a permissão apropriada para qualquer chamada de API. 

## <a name="types-of-permissions"></a>Tipos de permissões
O Microsoft Active Directory do Azure define dois tipos de permissões: 
* **Permissões delegadas** - são usadas por aplicativos que têm um usuário conectado presente. Para esses aplicativos de usuário ou administrador consente as permissões que as solicitações de aplicativo e o aplicativo tem permissão delegada para agir como usuário atribuído ao realizar chamadas para uma API. Dependendo da API, o usuário não poderá obter autorização para consentir à API diretamente e seria em vez disso, [exigir um administrador para fornecer "consentimento do administrador".](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)
* **Permissões de aplicativo** - são usadas por aplicativos executados sem um usuário conectado presente; por exemplo, aplicativos executados como serviços em segundo plano ou daemons. As permissões de aplicativo só podem ser [consentidas por um administrador](/azure/active-directory/develop/active-directory-v2-scopes#requesting-consent-for-an-entire-tenant) porque eles são geralmente muito poderosos e permitem o acesso a dados entre limites de usuário, ou dados que seriam restritos aos administradores. 

Permissões efetivas são as permissões que seu aplicativo terá ao fazer solicitações para uma API. 

* Para obter permissões delegadas, as permissões efetivas do seu aplicativo será a interseção menos privilegiadas das permissões delegadas que tiver recebido o aplicativo (por meio de consentimento) e os privilégios do usuário conectado no momento. Seu aplicativo não pode ter mais privilégios que o usuário conectado. Dentro das organizações, os privilégios do usuário conectado podem ser determinados pela política ou por associação em uma ou mais funções de administrador. Para obter mais informações sobre as funções de usuário e administrador, consulte [Atribuindo funções de administrador no Azure AD](/azure/active-directory/active-directory-assign-admin-roles-azure-portal.md).
    Por exemplo, suponha que seu aplicativo tenha recebido a `User.ReadWrite.All` a permissão delegada no Microsoft Graph. Essa permissão concede uma permissão ao seu aplicativo para ler e atualizar o perfil de todos os usuários em uma organização. Se o usuário conectado for um administrador global, seu aplicativo poderá atualizar o perfil de todos os usuários na organização. No entanto, se o usuário conectado não estiver em uma função de administrador, seu aplicativo poderá atualizar apenas o perfil do usuário conectado. Não poderá atualizar os perfis de outros usuários na organização porque o que ele tem permissão para agir em nome de usuário não tem os privilégios.
* Para permissões de aplicativo, as permissões efetivas do seu aplicativo são o nível completo de privilégios indicado pela permissão. Por exemplo, um aplicativo que tenha o `User.ReadWrite.All` permissão de aplicativo pode atualizar o perfil de todos os usuários na organização. 

## <a name="permission-attributes"></a>Atributos de permissão
As permissões no AD do Azure têm um número de propriedades que ajudam, administradores ou desenvolvedores de aplicativo a tomarem decisões conscientes sobre o que a permissão concede acesso.  

> [!NOTE]
> Você pode exibir as permissões de um aplicativo do Azure AD ou a Entidade de Serviço expõe usando o portal do Azure ou o PowerShell. Tente este script para exibir as permissões expostas pelo Microsoft Graph.
> ```powershell
> Connect-AzureAD
> 
> # Get OAuth2 Permissions/delegated permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").OAuth2Permissions
> 
> # Get App roles/application permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").AppRoles
> ```

| Nome da propriedade | DESCRIÇÃO | Exemplo | 
| --- | --- | --- |
| ID | Este é um valor de GUID que identifica exclusivamente esta permissão. | 570282fd-fa5c-430d-a7fd-fc8dc98a9dca | 
| IsEnabled | Indica se esse escopo está disponível para uso. | verdadeiro | 
| type | Indica se essa permissão requer o consentimento do usuário ou consentimento do administrador. | Usuário | 
| AdminConsentDescription | Esta é uma descrição que é exibida para os administradores durante as experiências de consentimento do administrador | Permite que o aplicativo leia emails nas caixas de entrada dos usuários.  | 
| AdminConsentDisplayName | Este é um nome fácil para os administradores durante as experiências de consentimento do administrador | Ler email de usuário | 
| UserConsentDescription | Esta é uma descrição que é exibida aos usuários durante a experiência de consentimento do usuário. |  Permite que o aplicativo leia emails em sua caixa de email.  | 
| UserConsentDisplayName | Este é um nome fácil que é exibido aos usuários durante uma experiência de consentimento do usuário. | Leia seu email | 
| Valor | Isso é a cadeia de caracteres que é usada para identificar a permissão durante os fluxos de autorização OAuth 2.0. Isso também pode ser combinado com a cadeia de caracteres do URI da ID do aplicativo para formar um nome totalmente qualificado de permissão. | `Mail.Read` | 

## <a name="types-of-consent"></a>Tipos de consentimento
Os aplicativos no Microsoft Azure Active Directory dependem de autorização para acessar os recursos necessários ou APIs. Há um número de tipos de autorização que seu aplicativo precisa saber para ser bem-sucedido. Se você estiver definindo permissões, também precisará entender como seus usuários terá acesso ao seu aplicativo ou a API.

* **Consentimento do usuário estático** - ocorre automaticamente durante o [OAuth 2.0 autorizar fluxo](/azure/active-directory/develop/active-directory-protocols-oauth-code.md#request-an-authorization-code) quando você especifica o recurso que deseja que seu aplicativo interaja. No cenário de consentimento do usuário estático, seu aplicativo deve ter já especificado todas as permissões necessárias na configuração do aplicativo no portal do Azure. Se o usuário (ou um administrador, conforme apropriado) não concedeu permissão para este aplicativo, em seguida, o Microsoft Active Directory solicitará ao usuário para fornecer consentimento neste momento. 

    Saiba mais sobre como registrar um aplicativo do Microsoft Azure Active Directory que solicita acesso a um conjunto estático de APIs.
* **Consentimento do usuário dinâmico** - é um recurso do modelo de aplicativo do Microsoft Azure Active Directory e v2. Nesse cenário, o aplicativo solicita um conjunto de escopos que precisa de [OAuth 2.0 autorizar fluxo para aplicativos v2](/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent). Se o usuário não aceitou, serão solicitados a consentir neste momento. [Saiba mais sobre o consentimento dinâmico](/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent).

    > [!NOTE]
    > O consentimento dinâmico pode ser conveniente, mas apresenta um grande desafio para permissões que exigem o consentimento do administrador, desde que a experiência de consentimento do administrador não saiba sobre essas permissões no momento da autorização. Se você necessitar de escopos de administrador privilegiado, seu aplicativo deve registrá-los no Portal do Azure.
  
* **Consentimento do administrador** - é necessário quando seu aplicativo precisa ter acesso a determinadas permissões com alto privilégio. Isso garante que os administradores tenham alguns controles adicionais antes de autorizar a aplicativos ou usuários acessem altamente privilegiado dados da organização. [Saiba mais sobre como conceder consentimento do administrador](/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="best-practices"></a>Práticas recomendadas

### <a name="resource-best-practices"></a>Práticas recomendas de recurso
Recursos que expõem as APIs devem definir permissões que são específicas para os dados ou ações que estão protegendo. Isso ajuda a garantir que os clientes não encerrem permissão para acessar os dados que não é necessário e que os usuários sejam bem informados sobre os dados que está aceitando.

Recursos devem definir explicitamente `Read` e `ReadWrite` permissões separadamente. 

Recursos deverão marcar quaisquer permissões que permitam o acesso a dados entre limites de usuário como `Admin` permissões. 

Recursos devem seguir o padrão de nomenclatura a seguir `Subject.Permission[.Modifier]` onde `Subject` correspondem com o tipo de dados que estão disponíveis, `Permission` corresponde à ação que um usuário pode tomar dados, e `Modifier` é usado opcionalmente para descrever especializações de outra permissão. Por exemplo:  
* Mail.Read - permite que os usuários leiam o email. 
* Mail.Read - permite que os usuários leiam o email.
* Mail.ReadWrite.All - permite que um administrador ou usuário acesse todos os emails da organização.

### <a name="client-best-practices"></a>Práticas recomendadas de cliente
Apenas solicite permissão para os escopos que seu aplicativo precisa. Aplicativos com um número excessivo de permissões estão em risco de exposição de dados de usuário se eles forem comprometidos.

Os clientes não devem solicitar permissões de aplicativo e as permissões delegadas do mesmo aplicativo. Isso pode resultar na elevação de privilégio e permitir que um usuário obtenha acesso aos dados que normalmente não teriam permissão. 




