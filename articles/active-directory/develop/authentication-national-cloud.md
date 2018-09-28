---
title: Autenticação usando o Azure AD nas nuvens nacionais
description: Saiba mais sobre o registro do aplicativo e os pontos de extremidade de autenticação para nuvens nacionais.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: negoe
ms.reviewer: negoe,andret,saeeda
ms.custom: aaddev
ms.openlocfilehash: 866a86178d66b7b4af069d684e4eb56c12db47ca
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46981994"
---
# <a name="national-clouds"></a>Nuvens nacionais

Nuvens nacionais (também conhecidas como nuvens soberanas) são instâncias isoladas fisicamente do Azure. Essas regiões do Azure são projetadas para garantir que os requisitos de residência, de soberania e de conformidade de dados sejam respeitados dentro de limites geográficos.

Incluindo nuvem global, o Azure Active Directory é implantado nas seguintes nuvens nacionais:  

- Governo dos EUA para Azure
- Azure Alemanha
- Azure China 21Vianet

## <a name="app-registration-endpoints"></a>Pontos de extremidade de registro do aplicativo

A tabela a seguir lista as URLs base para os pontos de extremidade do Azure AD (Azure Active Directory) usados para registrar um aplicativo para cada nuvem nacional.

| Nuvem nacional | Ponto de extremidade do portal do Azure AD
| --- | --- |
| Azure AD for US Government |https://portal.azure.us
|Azure AD Alemanha |https://portal.microsoftazure.de
|Azure AD China operado pela 21Vianet |https://portal.azure.cn
|Azure AD (serviço global)|https://portal.azure.com

## <a name="azure-ad-authentication-endpoints"></a>Pontos de extremidade de autenticação do Azure AD

A tabela a seguir lista as URLs base para os pontos de extremidade do Azure AD (Azure Active Directory) usados para obter tokens para chamar o Microsoft Graph para cada nuvem nacional.

| Nuvem nacional | Ponto de extremidade de autenticação do Azure AD
| --- | --- |
| Azure AD for US Government |`https://login.microsoftonline.us`
|Azure AD Alemanha| `https://login.microsoftonline.de`
|Azure AD China operado pela 21Vianet | `https://login.chinacloudapi.cn`
|Azure AD (serviço global)|`https://login.microsoftonline.com`

As solicitações para os pontos de extremidade de token ou autorização do Azure AD podem ser formadas usando a URL base específica da região apropriada. Por exemplo, no caso da Alemanha:

- O ponto de extremidade de autorização comum é `https://login.microsoftonline.de/common/oauth2/authorize`
- O ponto de extremidade de token comum é `https://login.microsoftonline.de/common/oauth2/token` 

Para aplicativos de locatário único, substitua o comum nas URLs acima por sua ID de locatário ou nome, por exemplo, `https://login.microsoftonline.de/contoso.com`

>[!NOTE]
> A [autorização do Azure AD v2.0]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) e os pontos de extremidade de token só estão disponíveis para o serviço global. Ele ainda não tem suporte para implantações de nuvem nacional.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Azure Governamental](https://docs.microsoft.com/azure/azure-government/)
- Saiba mais sobre o [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- Saiba mais sobre o [Azure Alemanha](https://docs.microsoft.com/azure/germany/)
- Saiba sobre as [noções básicas de autenticação do Azure AD](authentication-scenarios.md)
- Saiba mais sobre a [implantação do Microsoft Graph na nuvem nacional](https://developer.microsoft.com/graph/docs/concepts/deployments)