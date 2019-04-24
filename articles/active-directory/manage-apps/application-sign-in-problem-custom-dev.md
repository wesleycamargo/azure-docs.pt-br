---
title: Problemas para entrar em um aplicativo de desenvolvimento personalizado | Microsoft Docs
description: Erros comuns que podem impossibilitá-lo de entrar em um aplicativo desenvolvido por você com o Azure AD
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
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4365c87f0028fe3a9c4ba35f40599359eb1455f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60438771"
---
# <a name="problems-signing-in-to-an-custom-developed-application"></a>Problemas para entrar em um aplicativo de desenvolvimento personalizado

Há vários erros que podem estar causando a impossibilidade de entrar em um aplicativo. O principal motivo das pessoas se depararem com esse problema está relacionado com aplicativos configurados incorretamente.

## <a name="errors-related-to--misconfigured-apps"></a>Erros relacionados com aplicativos configurados incorretamente

* Verifique se as configurações do portal correspondem às que você tem no seu aplicativo. Especificamente, compare o ID do Aplicativo/Cliente, URLs de Resposta, Chaves/Segredos do Cliente e URI da ID do Aplicativo.

* Compare o recurso que você está solicitando acesso no código com as permissões configuradas na guia **Recursos Necessários** para certificar-se de que está solicitando somente os recursos que você configurou.

* Consulte [StackOverflow do Azure AD ](https://stackoverflow.com/questions/tagged/azure-active-directory) para quaisquer erros ou problemas semelhantes.

## <a name="next-steps"></a>Próximos passos

[Guia do Desenvolvedor do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Consentir e Integrar Aplicativos ao Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Consentimento e Permissão para Aplicativos Convergidos do Azure AD v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[StackOverflow do Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory>)
