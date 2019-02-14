---
title: Como selecionar permissões para uma API | Microsoft Docs
description: Como localizar os pontos de extremidade de autenticação para um aplicativo personalizado que você está desenvolvendo ou registrando com o Azure AD.
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
ms.date: 09/11/2018
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a0962e5381a6e0a8da96bc7379c38aae73ee400
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56207017"
---
# <a name="how-to-configure-endpoints"></a>Como configurar pontos de extremidade

Você pode encontrar os pontos de extremidade de autenticação para seu aplicativo no [Portal do Azure](https://portal.azure.com).

-   Navegue até o [Portal do Azure](https://portal.azure.com).

-   No painel de navegação esquerdo, clique em **Azure Active Directory**.

-   Clique em **Registros do Aplicativo** e escolha **Pontos de Extremidade**.

-   Isso abre a página **Pontos de Extremidade**, que lista todos os pontos de extremidade de autenticação de seu locatário.

-   Use o ponto de extremidade específico ao protocolo de autenticação que você está usando, em conjunto com a ID do aplicativo, para criar a solicitação de autenticação específica ao aplicativo.

## <a name="next-steps"></a>Próximas etapas
[Guia do desenvolvedor do Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)
