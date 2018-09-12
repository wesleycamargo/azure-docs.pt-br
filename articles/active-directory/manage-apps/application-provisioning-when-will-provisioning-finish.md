---
title: O provisionamento do usuário para um aplicativo da Galeria do Azure AD está levando horas ou mais | Microsoft Docs
description: Como descobrir por que o provisionamento para seu aplicativo está demorando mais do que o esperado
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 2b46fecc44803130e4f79667d0e083314c4419c2
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44354439"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>O provisionamento do usuário para um aplicativo da Galeria do Azure AD está levando horas ou mais

Ao habilitar pela primeira vez o provisionamento automático para um aplicativo, a sincronização inicial pode levar de 20 minutos até várias horas, dependendo do tamanho do diretório do Azure AD e do número de usuários no escopo para provisionamento. 

Sincronizações subsequentes após a sincronização inicial são mais rápidas, uma vez que o serviço de provisionamento armazena as marcas d'água que representam o estado dos dois sistemas após a sincronização inicial, melhorando o desempenho dessas sincronizações subsequentes.

## <a name="how-to-improve-provisioning-performance"></a>Como melhorar o desempenho do provisionamento

Se a sincronização inicial estiver demorando mais do que apenas algumas horas, há algo que você pode fazer para melhorar o desempenho:

-   **Filtros de escopo de usuário.** Filtros de escopo permitem ajustar os dados que o serviço de provisionamento extrai do Azure AD filtrando usuários com base em valores de atributo específicos. Para obter mais informações sobre filtros de escopo, consulte [Provisionamento de aplicativos com base em atributo com filtros de escopo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>Próximas etapas
[Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](user-provisioning.md)

