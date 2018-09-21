---
title: Como alterar os padrões de tempo de vida do token para um aplicativo personalizado | Microsoft Docs
description: Como atualizar as políticas de tempo de vida do token para o aplicativo que você está desenvolvendo no Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: 059920c710b202e22a22f8431c536c5dfa19f2b9
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44723629"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Como alterar os padrões de tempo de vida do token para um aplicativo personalizado

O Azure AD Premium permite que desenvolvedores de aplicativos e administradores de locatários configurem o tempo de vida de tokens emitidos para clientes não confidenciais. As políticas de tempo de vida do token são definidas com base em todos os locatários ou nos recursos que estão sendo acessados.

 * Para definir uma política de tempo de vida do token, você precisa baixar o [Módulo PowerShell do Azure AD](https://www.powershellgallery.com/packages/AzureADPreview).

 * Execute o comando **Connect-AzureAD -Confirm**.

 * Veja um exemplo de política que define o token de atualização de fator único de idade máxima. Crie a política: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

 * Veja o documento [Configurando tempo de vida do token](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) para saber como criar outro personalizado.

## <a name="next-steps"></a>Próximas etapas
[Configurando o tempo de vida do token](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)<br>

[Referência de token do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

