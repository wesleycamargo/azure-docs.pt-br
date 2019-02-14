---
title: Saiba mais sobre os protocolos de autorização suportados pelo Azure AD v2.0 | Microsoft Docs
description: Um guia para protocolos com suporte no ponto de extremidade Azure AD v2.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed27830aa1f4212e4bc26af8da4febc1b61a76cc
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175075"
---
# <a name="v20-protocols---oauth-20-and-openid-connect"></a>Protocolos v2.0 - OAuth 2.0 e OpenID Connect

O ponto de extremidade v2.0 pode usar o Azure Active Directory (Azure AD) como identidade como um serviço com protocolos padrão da indústria, como o OAuth 2.0 e o OpenID Connect. Embora o serviço esteja em conformidade com o padrão, pode haver diferenças sutis entre duas implementações diferentes desses protocolos. As informações descritas aqui serão úteis se você optar por criar seu código enviando e tratando solicitações HTTP diretamente, ou usar uma biblioteca de software livre de terceiros ao invés de usar uma de nossas [bibliotecas de software livre](reference-v2-libraries.md).

> [!NOTE]
> Nem todos os recursos e cenários do Azure Active Directory têm suporte no ponto de extremidade v2.0. Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Noções básicas

Em quase todos os fluxos do OAuth 2.0 e do OpenID Connect, há quatro partes envolvidas na troca:

![Funções do OAuth 2.0](../../media/active-directory-v2-flows/protocols_roles.png)

* O **Servidor de Autorização** é o ponto de extremidade v2.0 e é responsável por garantir a identidade do usuário, conceder e revogar o acesso a recursos e emitir tokens. O servidor de autorização também é conhecido como provedor de identidade; ele trata com segurança tudo que estiver relacionado às informações do usuário, seu acesso e as relações de confiança entre as partes de um fluxo.
* O **Proprietário do Recurso** normalmente é o usuário final. Ele é a parte que possui os dados e tem o poder de permitir que terceiros acessem esses dados ou recurso.
* O **Cliente OAuth** é o seu aplicativo, identificado pela respectiva ID de Aplicativo. Geralmente é a parte com a qual usuário final interage e solicita tokens do servidor de autorização. O cliente deve receber permissão do proprietário do recurso para acessar o recurso.
* O **Servidor de Recurso** é o local no qual o recurso ou os dados residem. Ele confia no Servidor de Autorização para autenticar e autorizar o Cliente OAuth com segurança, além de usar access_tokens de portador para garantir que o acesso a um recurso possa ser concedido.

## <a name="app-registration"></a>Registro do Aplicativo
Cada aplicativo que usa o ponto de extremidade v2.0 deve estar registrado em [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ou por meio da nova experiência **Registros de aplicativo (versão prévia)** no [portal do Azure](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) para poder interagir usando OAuth ou OpenID Connect. O processo de registro de aplicativo coleta e atribui alguns valores a seu aplicativo:

* Uma **ID de Aplicativo** que identifica exclusivamente o aplicativo
* Um **URI de Redirecionamento** ou **Identificador de Pacote** que pode ser usado para direcionar as respostas de volta ao aplicativo
* Alguns outros valores específicos de cenário.

Para obter mais detalhes, saiba como [registrar um aplicativo](quickstart-v2-register-an-app.md).

## <a name="endpoints"></a>Pontos de extremidade

Depois de registrado, o aplicativo se comunica com o Azure AD enviando solicitações ao ponto de extremidade v2.0:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Em que o `{tenant}` pode ter um de quatro valores diferente:

| Valor | DESCRIÇÃO |
| --- | --- |
| `common` | Permite que os usuários com contas pessoais da Microsoft e contas corporativas/de estudante do Azure AD entrem no aplicativo. |
| `organizations` | Permite que somente usuários com contas corporativas/de estudante do Azure AD entrem no aplicativo. |
| `consumers` | Permite que somente os usuários com MSA (Contas Pessoais da Microsoft) para entrem no aplicativo. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` | Permite que somente os usuários com contas corporativas/de estudante de um locatário específico do Azure AD entrem no aplicativo. É possível usar o nome de domínio amigável do locatário do Azure AD ou o identificador GUID de locatário. |

Para saber mais sobre como interagir com esses pontos de extremidade, escolha um tipo específico de aplicativo na seção [Protocolos](#protocols) e siga os links.

## <a name="tokens"></a>Tokens

A implementação v2.0 do OAuth 2.0 e do OpenID Connect faz amplo uso de tokens de portador, incluindo aqueles representados como JWTs. Um token de portador é um token de segurança leve que concede ao "portador" acesso a um recurso protegido. Nesse sentido, o "portador" é qualquer parte que possa apresentar o token. Embora uma parte deva primeiro se autenticar no Azure AD para receber o token de portador, se as medidas necessárias não forem tomadas para proteger o token durante a transmissão e o armazenamento, ele pode ser interceptado e usado por uma parte não planejada. Embora alguns tokens de segurança tenham um mecanismo interno para impedir que partes não autorizadas os utilizem, tokens de portador não possuem esse mecanismo e devem ser transportados em um canal seguro, como segurança da camada de transporte (HTTPS). Se um token de portador for transmitido livremente, uma parte mal-intencionada pode usar um ataque "man-in-the-middle" para adquirir o token e usá-lo para um acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança se aplicam ao armazenar ou manter em cache tokens de portador para uso posterior. Sempre se certifique de que seu aplicativo transmita e armazene tokens de portador de maneira segura. Para obter mais considerações de segurança sobre tokens de portador, consulte [RFC 6750 seção 5](https://tools.ietf.org/html/rfc6750).

Mais detalhes sobre os diferentes tipos de token usados no ponto de extremidade v2.0 estão disponíveis na [referência do token do ponto de extremidade v2.0](v2-id-and-access-tokens.md).

## <a name="protocols"></a>Protocolos

Se você estiver pronto para ver alguns exemplos de solicitação, inicie com um dos tutoriais a seguir. Cada um corresponde a um cenário de autenticação específico. Se precisar de ajuda para determinar qual é o fluxo ideal para você, confira [os tipos de aplicativos que você pode compilar com a v2.0](v2-app-types.md).

* [Criar aplicativos nativos e móveis com o OAuth 2.0](v2-oauth2-auth-code-flow.md)
* [Criar aplicativos Web com o OpenID Connect](v2-protocols-oidc.md)
* [Criar aplicativos de página única com o fluxo implícito do OAuth 2.0](v2-oauth2-implicit-grant-flow.md)
* [Criar daemons ou processos do servidor com o fluxo de credenciais do cliente OAuth 2.0](v2-oauth2-client-creds-grant-flow.md)
* [Obter tokens em uma API Web com o OAuth 2.0 em nome do fluxo](v2-oauth2-on-behalf-of-flow.md)
