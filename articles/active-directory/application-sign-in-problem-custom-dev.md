---
title: Problemas para entrar em um aplicativo de desenvolvimento personalizado | Microsoft Docs
description: Erros comuns que podem impossibilitá-lo de entrar em um aplicativo desenvolvido por você com o Azure AD
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
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 3cbc9f981ab528efe2d739022c674cef48dfeb51
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "36330879"
---
# <a name="problems-signing-in-to-an-custom-developed-application"></a>Problemas para entrar em um aplicativo de desenvolvimento personalizado

Há vários erros que podem estar causando a impossibilidade de entrar em um aplicativo. O principal motivo das pessoas se depararem com esse problema está relacionado com aplicativos configurados incorretamente.

## <a name="errors-related-to--misconfigured-apps"></a>Erros relacionados com aplicativos configurados incorretamente

* Verifique se as configurações do portal correspondem às que você tem no seu aplicativo. Especificamente, compare o ID do Aplicativo/Cliente, URLs de Resposta, Chaves/Segredos do Cliente e URI da ID do Aplicativo.

* Compare o recurso que você está solicitando acesso no código com as permissões configuradas na guia **Recursos Necessários** para certificar-se de que está solicitando somente os recursos que você configurou.

* Consulte [StackOverflow do Azure AD ](http://stackoverflow.com/questions/tagged/azure-active-directory) para quaisquer erros ou problemas semelhantes.

## <a name="next-steps"></a>Próximas etapas

[Guia do Desenvolvedor do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Consentir e Integrar Aplicativos ao Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications>)<br>

[Consentimento e Permissão para Aplicativos Convergidos do Azure AD v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[StackOverflow do Azure AD](http://stackoverflow.com/questions/tagged/azure-active-directory>)
