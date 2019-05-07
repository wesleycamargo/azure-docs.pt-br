---
title: Autenticação usando o Azure Active Directory nas nuvens nacionais
description: Saiba mais sobre o registro do aplicativo e os pontos de extremidade de autenticação para nuvens nacionais.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: negoe,CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0d4586df23548854f4acbfefd32081a36906097
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067912"
---
# <a name="national-clouds"></a>Nuvens nacionais

Nuvens nacionais são instâncias isoladas fisicamente do Azure. Essas regiões do Azure são projetadas para garantir que os requisitos de residência, de soberania e de conformidade de dados sejam respeitados dentro de limites geográficos.

Incluindo nuvem global, o Azure Active Directory é implantado nas seguintes nuvens nacionais:  

- Governo dos EUA para Azure
- Azure Alemanha
- Azure China 21Vianet

Nuvens nacionais são ambientes exclusivos e diferentes do Azure global. Portanto, é importante estar ciente de algumas das principais diferenças ao desenvolver seu aplicativo para esses ambientes, como registrar aplicativos, adquirir tokens e configurar pontos de extremidade.

## <a name="app-registration-endpoints"></a>Pontos de extremidade de registro do aplicativo

Há um portal do Azure separado para cada uma das nuvens nacionais. Para integrar aplicativos com a plataforma de identidade da Microsoft em uma nuvem nacional, você deve registrar seu aplicativo separadamente em cada um portal do Azure específico do ambiente.

A tabela a seguir lista as URLs base para os pontos de extremidade do Azure AD (Azure Active Directory) usados para registrar um aplicativo para cada nuvem nacional.

| Nuvem nacional | Ponto de extremidade do portal do Azure AD |
|----------------|--------------------------|
| Azure AD for US Government | `https://portal.azure.us` |
| Azure AD Alemanha | `https://portal.microsoftazure.de` |
| Azure AD China operado pela 21Vianet | `https://portal.azure.cn` |
| Azure AD (serviço global) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Pontos de extremidade de autenticação do Azure AD

Todas as nuvens nacionais autenticam usuários separadamente em cada ambiente e têm pontos de extremidade de autenticação separados.

A tabela a seguir lista as URLs base para os pontos de extremidade do Azure AD (Azure Active Directory) usados para obter tokens para chamar o Microsoft Graph para cada nuvem nacional.

| Nuvem nacional | Ponto de extremidade de autenticação do Azure AD |
|----------------|-------------------------|
| Azure AD for US Government | `https://login.microsoftonline.us` |
| Azure AD Alemanha| `https://login.microsoftonline.de` |
| Azure AD China operado pela 21Vianet | `https://login.chinacloudapi.cn` |
| Azure AD (serviço global)| `https://login.microsoftonline.com` |

- As solicitações para os pontos de extremidade de token ou autorização do Azure AD podem ser formadas usando a URL base específica da região apropriada. Por exemplo, para o Azure Alemanha:

  - O ponto de extremidade de autorização comum é `https://login.microsoftonline.de/common/oauth2/authorize`.
  - O ponto de extremidade de token comum é `https://login.microsoftonline.de/common/oauth2/token`.

- Para aplicativos de locatário único, substitua o comum nas URLs anteriores por sua ID de locatário ou nome, por exemplo, `https://login.microsoftonline.de/contoso.com`.

> [!NOTE]
> A [autorização do Azure AD v2.0]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) e os pontos de extremidade de token só estão disponíveis para o serviço global. Ele ainda não tem suporte para implantações de nuvem nacional.

## <a name="microsoft-graph-api"></a>API do Microsoft Graph

Para saber como chamar as APIs do Microsoft Graph no ambiente de Nuvem Nacional, acesse [Microsoft Graph na nuvem nacional](https://developer.microsoft.com/graph/docs/concepts/deployments).

> [!IMPORTANT]
> Determinados serviços e recursos que estão em regiões específicas do serviço global podem não estar disponíveis em todas as nuvens Nacionais. Para descobrir quais serviços estão disponíveis, acesse [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Siga esse [tutorial de biblioteca de autenticação da Microsoft (MSAL)](msal-national-cloud.md) para aprender a criar um aplicativo usando a plataforma de identidade da Microsoft. Especificamente, este aplicativo irá entrar em um usuário, obter um token de acesso para chamar a API do Microsoft Graph.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre:

- [Azure Governamental](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Alemanha](https://docs.microsoft.com/azure/germany/)
- [Noções básicas de autenticação do AD do Azure](authentication-scenarios.md)
