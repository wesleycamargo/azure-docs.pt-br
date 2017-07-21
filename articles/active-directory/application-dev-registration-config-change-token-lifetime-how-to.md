---
title: "Como alterar os padrões do tempo de vida do token para um aplicativo personalizado | Microsoft Docs"
description: "Como atualizar as políticas do tempo de vida do Token para o aplicativo que você está desenvolvendo no Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 54c30123bb582f515dfb0324cdfd897a6c4af5c0
ms.contentlocale: pt-br
ms.lasthandoff: 04/14/2017

---


<a id="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application" class="xliff"></a>

# Como alterar os padrões do tempo de vida do token para um aplicativo personalizado

O Azure AD Premium permite que os desenvolvedores de aplicativos e administradores de locatários configurem o tempo de vida dos tokens emitidos para clientes não confidenciais. As políticas do tempo de vida do token são definidas com base em todos os locatários ou recursos que estão sendo acessados.

 * Para definir uma política do tempo de vida do token, você precisa baixar o [Módulo PowerShell do Azure AD](https://www.powershellgallery.com/packages/AzureADPreview).

 * Execute o comando **Connect-AzureAD -Confirm**.

 * Aqui está um exemplo de política que define o token de atualização de fator único de idade máxima. Criar a política: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

 * Confira o documento [Tempo de vida do token de configuração](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-configurable-token-lifetimes) para saber como criar outro personalizado.

<a id="next-steps" class="xliff"></a>

## Próximas etapas
[Configurando o tempo de vida do token](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-configurable-token-lifetimes)<br>

[Referência de token do Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-token-and-claims)


