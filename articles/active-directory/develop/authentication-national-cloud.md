---
title: Autenticação usando o Azure AD nas nuvens nacionais
description: Saiba mais sobre o registro do aplicativo e os pontos de extremidade de autenticação para nuvens nacionais.
services: active-directory
documentationcenter: ''
author: negoe
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/20/2018
ms.author: negoe
ms.reviewer: negoe,andret,saeeda,CelesteDG
ms.custom: aaddev
ms.openlocfilehash: 2130cc4d51965a087ccdbaeb922eda5059ee1c82
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55091573"
---
# <a name="national-clouds"></a>Nuvens nacionais

Nuvens nacionais (também conhecidas como nuvens soberanas) são instâncias isoladas fisicamente do Azure. Essas regiões do Azure são projetadas para garantir que os requisitos de residência, de soberania e de conformidade de dados sejam respeitados dentro de limites geográficos.

Incluindo nuvem global, o Azure Active Directory é implantado nas seguintes nuvens nacionais:  

- Governo dos EUA para Azure
- Azure Alemanha
- Azure China 21Vianet

Nuvens nacionais são ambientes exclusivos e diferentes do Azure global. Portanto, é importante estar ciente de algumas das principais diferenças ao desenvolver seu aplicativo para esses ambientes, como registrar aplicativos, adquirir tokens e configurar pontos de extremidade.

## <a name="app-registration-endpoints"></a>Pontos de extremidade de registro do aplicativo

Há um portal do Azure separado para cada uma das nuvens nacionais. Para integrar aplicativos com a plataforma de identidade da Microsoft em uma nuvem nacional, você deve registrar seu aplicativo separadamente em cada um portal do Azure específico do ambiente.

A tabela a seguir lista as URLs base para os pontos de extremidade do Azure AD (Azure Active Directory) usados para registrar um aplicativo para cada nuvem nacional.

| Nuvem nacional | Ponto de extremidade do portal do Azure AD
| --- | --- |
| Azure AD for US Government |https://portal.azure.us
|Azure AD Alemanha |https://portal.microsoftazure.de
|Azure AD China operado pela 21Vianet |https://portal.azure.cn
|Azure AD (serviço global)|https://portal.azure.com 

## <a name="azure-ad-authentication-endpoints"></a>Pontos de extremidade de autenticação do Azure AD

Todas as nuvens nacionais autenticam usuários separadamente em cada ambiente e têm pontos de extremidade de autenticação separados.

A tabela a seguir lista as URLs base para os pontos de extremidade do Azure AD (Azure Active Directory) usados para obter tokens para chamar o Microsoft Graph para cada nuvem nacional.

| Nuvem nacional | Ponto de extremidade de autenticação do Azure AD
| --- | --- |
| Azure AD for US Government |`https://login.microsoftonline.us`
|Azure AD Alemanha| `https://login.microsoftonline.de`
|Azure AD China operado pela 21Vianet | `https://login.chinacloudapi.cn`
|Azure AD (serviço global)|`https://login.microsoftonline.com`

- As solicitações para os pontos de extremidade de token ou autorização do Azure AD podem ser formadas usando a URL base específica da região apropriada. Por exemplo, para o Azure Alemanha:

  - O ponto de extremidade de autorização comum é `https://login.microsoftonline.de/common/oauth2/authorize`.
  - O ponto de extremidade de token comum é `https://login.microsoftonline.de/common/oauth2/token`.

- Para aplicativos de locatário único, substitua o comum nas URLs anteriores por sua ID de locatário ou nome, por exemplo, `https://login.microsoftonline.de/contoso.com`.

>[!NOTE]
> A [autorização do Azure AD v2.0]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) e os pontos de extremidade de token só estão disponíveis para o serviço global. Ele ainda não tem suporte para implantações de nuvem nacional.

## <a name="microsoft-graph-api"></a>API do Microsoft Graph

Para saber como chamar as APIs do Microsoft Graph no ambiente de Nuvem Nacional, acesse [Microsoft Graph na nuvem nacional](https://developer.microsoft.com/graph/docs/concepts/deployments).



>[!IMPORTANT]
Determinados serviços e recursos que estão em regiões específicas do serviço global podem não estar disponíveis em todas as nuvens Nacionais. Para descobrir quais serviços estão disponíveis, acesse [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Azure Governamental](https://docs.microsoft.com/azure/azure-government/).
- Saiba mais sobre o [Azure China 21Vianet](https://docs.microsoft.com/azure/china/).
- Saiba mais sobre o [Azure Alemanha](https://docs.microsoft.com/azure/germany/).
- Conheça as [noções básicas de autenticação do Azure AD](authentication-scenarios.md).
