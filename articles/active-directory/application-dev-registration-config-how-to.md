---
title: "Como selecionar permissões para determinada API | Microsoft Docs"
description: "Como localizar pontos de extremidade de autenticação para um aplicativo personalizado que você está desenvolvendo ou registrando com o Azure AD."
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
ms.openlocfilehash: 3578684ac8c92ff195b19740b28ef9adebdc7309
ms.contentlocale: pt-br
ms.lasthandoff: 04/14/2017

---

<a id="how-to-select-permissions-for-a-given-api" class="xliff"></a>

# Como selecionar permissões para determinada API

Você pode encontrar os pontos de extremidade de autenticação para seu aplicativo no [portal do Azure](https://portal.azure.com).

-   Navegue até o [Portal do Azure](https://portal.azure.com).

-   No painel de navegação à esquerda, clique em **Azure Active Directory**.

-   Clique em **Registros do Aplicativo** e escolha **Pontos de Extremidade**.

-   Isso abre a página **Pontos de Extremidade**, que lista todos os pontos de extremidade de autenticação para seu locatário.

-   Use o ponto de extremidade específico ao protocolo de autenticação que você está usando, em conjunto com a ID do aplicativo, para criar a autenticação de solicitação específicas ao aplicativo.

<a id="next-steps" class="xliff"></a>

## Próximas etapas
[Guia do desenvolvedor do Active Directory do Azure](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-developers-guide#authentication-and-authorization-protocols)

