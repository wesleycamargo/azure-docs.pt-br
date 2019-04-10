---
title: Como alterar os padrões de tempo de vida do token para um aplicativo personalizado | Microsoft Docs
description: Como atualizar as políticas de tempo de vida do token para o aplicativo que você está desenvolvendo no Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: celested
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04abdedf5ac19be3d5a43e7502cbc97f8f5fee43
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59360298"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Como alterar os padrões de tempo de vida do token para um aplicativo personalizado

Este artigo mostra como usar o PowerShell do Azure AD para definir uma política de tempo de vida de token. O Azure AD Premium permite que desenvolvedores de aplicativos e administradores de locatários configurem o tempo de vida de tokens emitidos para clientes não confidenciais. As políticas de tempo de vida do token são definidas com base em todos os locatários ou nos recursos que estão sendo acessados.

1. Para definir uma política de tempo de vida do token, você precisa baixar o [Módulo PowerShell do Azure AD](https://www.powershellgallery.com/packages/AzureADPreview).
1. Execute o comando **Connect-AzureAD -Confirm**.

    Veja um exemplo de política que define o token de atualização de fator único de idade máxima. Crie a política:
  ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Próximas etapas

* Consulte [Tempos de vida de token configuráveis no Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) para saber como configurar tempos de vida de token emitidos pelo Azure AD, incluindo como definir tempos de vida de token para todos os aplicativos na organização, para um aplicativo de vários locatários ou para uma entidade de serviço específica na organização. 
* [Referência de Token do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

