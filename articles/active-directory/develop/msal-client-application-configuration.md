---
title: Configuração de aplicativo do cliente (biblioteca de autenticação da Microsoft) | Azure
description: Saiba mais sobre as opções de configuração de cliente público e cliente confidencial aplicativos na autenticação biblioteca MSAL (Microsoft).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a48eea9fedd2d82f44693d58b31ee0d5c8c288d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138543"
---
# <a name="application-configuration-options"></a>Opções de configuração de aplicativo

Em seu código, você inicializa um novo cliente público ou confidencial (ou o agente do usuário para a msal) o aplicativo para autenticar e obter tokens.  Há várias opções de configuração diferentes que podem ser definidas ao inicializar o aplicativo cliente no MSAL. Essas opções podem ser divididas em dois grupos:

- Opções de registro, incluindo:
    - [Autoridade](#authority) (composta de provedor de identidade [instância](#cloud-instance) e entre [público-alvo](#application-audience) para o aplicativo e, possivelmente, a ID do Locatário).
    - [ID do cliente](#client-id)
    - [URI de redirecionamento](#redirect-uri)
    - [segredo do cliente](#client-secret) (para aplicativos de cliente confidencial).
- [Opções de log](#logging), incluindo o nível de log, controle de dados pessoais e o nome do componente usando a biblioteca.

## <a name="authority"></a>Authority
A autoridade é uma URL que indica um diretório que o MSAL pode solicitar tokens do. Autoridades comuns são:

- https://login.microsoftonline.com/&lt; locatário&gt;/, onde &lt;locatário&gt; é a ID de locatário do locatário do Azure AD ou um domínio associado a este locatário do Azure AD.  Usado somente para entrada de usuários de uma organização específica.
- https://login.microsoftonline.com/common/. Usado para conectar os usuários com o trabalho e contas de estudante ou uma conta pessoal da Microsoft.
- https://login.microsoftonline.com/organizations/. Usado para conectar os usuários com contas corporativas e de estudante.
- https://login.microsoftonline.com/consumers/. Usado para conectar os usuários com apenas conta pessoal da Microsoft (live).

A configuração da autoridade deve ser consistente com o que é declarado no portal de registro de aplicativo.

A URL de autoridade é composta de instância e o público-alvo.

A autoridade pode ser:
- uma autoridade de nuvem do Azure Active directory
- uma autoridade do Azure AD B2C. Ver [especificidades do B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- uma autoridade do AD FS. Ver [dar suporte a ADFS](https://aka.ms/msal-net-adfs-support).

Autoridades de nuvem do Azure AD tem duas partes:
- o provedor de identidade **instância**
- na entrada **público** para o aplicativo

A instância e o público-alvo podem ser concatenados e fornecidos como a URL de autoridade. Nas versões do MSAL.NET antes da MSAL 3. x, você tinha que compõem a autoridade por conta própria, dependendo da nuvem que você quisesse o público entrar e de destino.  O diagrama a seguir mostra como a URL de autoridade é composta.

![Authority](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Instância de nuvem
O **instância** é usado para especificar se seu aplicativo está se conectando a usuários de nuvem pública do Microsoft Azure ou de nuvens nacionais. Usando a MSAL em seu código, a instância de nuvem do Azure pode ser definida usando uma enumeração ou passando a URL para o [instância de nuvem nacional](authentication-national-cloud.md#azure-ad-authentication-endpoints) como o `Instance` membro (se souber).

MSAL.NET lançará uma exceção explícita se os dois `Instance` e `AzureCloudInstance` são especificados. 

Se você não especificar uma instância, seu aplicativo se destinará a instância de nuvem pública do Azure (a instância do URL `https://login.onmicrosoftonline.com`).

## <a name="application-audience"></a>Público-alvo do aplicativo

O público entrar depende das necessidades de negócios para seu aplicativo:
- Se você for uma linha de desenvolvedor de negócios (LOB), você provavelmente vai produzir um aplicativo de locatário único, que será usado apenas em sua organização. Nesse caso, você precisará especificar o que é essa organização, por sua ID de locatário (ID do Azure Active Directory) ou um nome de domínio associado com do Active Directory do Azure.
- Se você for um ISV, você talvez queira conectar usuários com seu trabalho e contas de estudante em qualquer organização, ou algumas organizações (multilocatário), mas você talvez também queira que os usuários a entrar com sua conta pessoal da Microsoft.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Como especificar o público em seu código/configuração
Usando MSAL em seu código, você especificar o público-alvo, usando:
- o Azure AD autoridade público enumeração. 
- ou a ID do locatário, que pode ser:
  - um GUID (ID do Azure Active Directory), para aplicativos de locatário único
  - um nome de domínio associado com o Azure Active Directory (também para aplicativos de locatário único)
- ou um desses espaços reservados como uma ID de locatário no lugar da enumeração de público-alvo de autoridade do Azure AD:
    - `organizations` para um aplicativo de vários locatários
    - `consumers` para conectar usuários apenas com as respectivas contas pessoais
    - `common` para conectar os usuários com seu trabalho e conta de escola ou conta pessoal da Microsoft

MSAL lançará uma exceção significativa se você especificar o público-alvo de autoridade do Azure AD e a ID de locatário. 

Se você não especificar um público-alvo seu aplicativo se destinará do Azure AD e contas pessoais da Microsoft como um público-alvo (ou seja `common`).

### <a name="effective-audience"></a>Público-alvo efetiva
O público-alvo em vigor para o seu aplicativo será o mínimo (se houver uma interseção) público definido em seu aplicativo e o público-alvo especificado no registro do aplicativo. Na verdade, a experiência de registro de aplicativo ([registro de aplicativo](https://aka.ms/appregistrations)) permite que você especifique o público-alvo (tipos de conta com suporte) para o aplicativo. Confira [Início Rápido: Registrar um aplicativo com a plataforma de identidade Microsoft](quickstart-register-app.md) para obter mais informações.

Atualmente, a única maneira de obter um aplicativo para conectar usuários com apenas contas pessoais da Microsoft é definir:
- Defina o público de registro de aplicativo como "Trabalho e contas de estudante e contas pessoais" e,
- e, defina o público em seu código / configuração de `AadAuthorityAudience.PersonalMicrosoftAccount` (ou `TenantID `= "clientes")

## <a name="client-id"></a>ID do cliente
A ID do aplicativo exclusiva ao aplicativo (cliente) atribuída ao seu aplicativo pelo AD do Azure quando o aplicativo foi registrado.

## <a name="redirect-uri"></a>URI de redirecionamento
O URI de redirecionamento é o URI no qual o provedor de identidade enviará os tokens de segurança novamente. 

### <a name="redirect-uri-for-public-client-applications"></a>URI de redirecionamento para aplicativos de cliente público
Para desenvolvedores de aplicativos de cliente público usando MSAL:
- Você não precisa passar o ``RedirectUri`` conforme ele é calculado automaticamente pelo MSAL. Esse redirecionamento URI é definido como os valores a seguir dependendo da plataforma:

- ``urn:ietf:wg:oauth:2.0:oob`` para todas as plataformas do Windows
- ``msal{ClientId}://auth`` para Xamarin Android e iOS

No entanto, o redirecionamento de URI deve ser configurada na [registro de aplicativo](https://aka.ms/appregistrations).

![URI de redirecionamento no portal](media/msal-client-application-configuration/redirect-uri.png)

É possível substituir o URI de redirecionamento usando o `RedirectUri` propriedade, por exemplo se você usar agentes. Nesse caso, aqui estão alguns exemplos de URIs de redirecionamento:

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $"msauth.{Bundle.ID}://auth";

Para obter detalhes, consulte as especificações de Android e [informações específicas do iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)

### <a name="redirect-uri-for-confidential-client-applications"></a>URI de redirecionamento para aplicativos de cliente confidencial
Para aplicativos web, o redirecionamento de URI (ou URI de resposta), é o URI no qual o AD do Azure entrará em contato com novamente o aplicativo com o token. Isso pode ser a URL do aplicativo Web / API Web se o confidencial é um deles.  Esse URI de redirecionamento deve ser registrado no registro de aplicativo. Isso é especialmente importante quando você implanta um aplicativo que inicialmente testado localmente. Em seguida, você precisa adicionar a URL de resposta do aplicativo implantado no portal de registro de aplicativo.

Para aplicativos de daemon, você não precisa especificar um URI de redirecionamento.

## <a name="client-secret"></a>Segredo do cliente
O segredo do cliente para o aplicativo cliente confidencial. Esse segredo (senha de aplicativo) é fornecido pelo portal de registro do aplicativo ou fornecido para o Azure AD durante o registro de aplicativo com o PowerShell AzureAD, PowerShell AzureRM ou CLI do Azure.

## <a name="logging"></a>Registro em log
As outras opções de habilitar o registro em log e solução de problemas. Para obter mais informações, consulte o [registro em log](msal-logging.md) para obter mais detalhes sobre como usá-los.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [criando aplicativos de cliente usando MSAL.NET](msal-net-initializing-client-applications.md).

Saiba mais sobre [criando uma instância de aplicativos cliente usando a msal](msal-js-initializing-client-applications.md).
