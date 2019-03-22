---
title: Azure MFA Server ou Serviço, no Local ou na nuvem?
description: Como Administrador do Azure AD, eu preciso entender qual versão da MFA devo implantar?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: edfc26355933db549b735c4baf9744744f46734c
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56870458"
---
# <a name="which-version-of-azure-mfa-is-right-for-my-organization"></a>Qual versão do Azure MFA é ideal para minha organização?

Antes de decidir onde e como implantar a MFA (Autenticação Multifator do Microsoft Azure), é necessário responder a três perguntas básicas.

* [O que estou tentando proteger](#what-am-i-trying-to-secure)
* [Onde os usuários estão localizados](#where-are-the-users-located)
* [De quais recursos preciso?](#what-features-do-i-need)

Cada uma das seções a seguir fornece detalhes para ajudá-lo a responder as perguntas anteriores.

## <a name="what-am-i-trying-to-secure"></a>O que estou tentando proteger?

Para determinar a solução de verificação em duas etapas correta, primeiro é necessário responder à pergunta sobre o que você está tentando proteger com um fator de autenticação adicional. É um aplicativo no Azure? Ou um sistema de acesso remoto? Ao determinar o que você está tentando proteger, será possível responder à pergunta sobre onde a Autenticação Multifator deve ser habilitada.

| O que você está tentando proteger | MFA na nuvem | Servidor MFA |
| --- |:---:|:---:|
| Aplicativos primários da Microsoft |● |● |
| Aplicativos SaaS da Galeria de Aplicativos |● |  |
| Aplicativos Web publicados por meio da Proxy de Aplicativo do Azure AD |● |  |
| Aplicativos IIS não publicados por meio da Proxy de aplicativo do Azure AD | |● |
| Acesso remoto, como VPN, RDG usando a extensão do NPS ou um servidor existente do NPS | ● | ● |

## <a name="where-are-the-users-located"></a>Onde os usuários estão localizados

Em seguida, determine onde estão localizados os usuários da sua organização para determinar a solução correta a ser usada, seja na nuvem ou no local, usando o Servidor MFA.

| Local do usuário | MFA na nuvem | Servidor MFA |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| Azure AD e AD local usando federação com AD FS |● |● |
| Azure AD e AD local usando o DirSync, o Azure AD Connect - sem sincronização de hash de senha ou autenticação de passagem |● |● |
| Azure AD e AD local usando o Azure AD Connect - sem sincronização de hash de senha ou autenticação de passagem |● | |
| Active Directory local | |● |

## <a name="what-features-do-i-need"></a>De quais recursos preciso?

A tabela a seguir compara os recursos disponíveis com a Autenticação Multifator na nuvem e o Servidor de Autenticação Multifator.

| Recurso | MFA na nuvem | Servidor MFA |
| --- |:---:|:---:|
| Notificação de aplicativo móvel como um segundo fator | ● | ● |
| Código de verificação de aplicativo móvel como um segundo fator | ● | ● |
| Chamada telefônica como um segundo fator | ● | ● |
| SMS unidirecional como segundo fator | ● | ● |
| Tokens de hardware como segundo fator | ● (Versão prévia pública) | ● |
| Senhas de aplicativos para clientes do Office 365 que não oferecem suporte a MFA | ● | |
| Controle do administrador sobre métodos de autenticação | ● | ● |
| Modo PIN | | ● |
| Alerta de fraude | ● | ● |
| Relatórios de MFA | ● | ● |
| Desvio único | | ● |
| Saudações personalizadas para chamadas telefônicas | ● | ● |
| ID do chamador personalizável para chamadas telefônicas | ● | ● |
| IPs confiáveis | ● | ● |
| Lembrar MFA para dispositivos confiáveis | ● | |
| Acesso condicional | ● | ● |
| Cache |  | ● |

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe a diferença entre a Autenticação Multifator do Microsoft Azure na nuvem ou no Servidor MFA local, é hora de configurar e usar a Autenticação Multifator do Microsoft Azure. **Selecione o ícone que representa seu cenário.**

<center>

[![MFA na nuvem](./media/concept-mfa-whichversion/cloud2.png)](howto-mfa-getstarted.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[![Servidor MFA ](./media/concept-mfa-whichversion/server2.png)](howto-mfaserver-deploy.md)&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;</center>
