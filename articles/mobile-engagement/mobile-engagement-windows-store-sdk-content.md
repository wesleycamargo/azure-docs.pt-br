---
title: Conteúdo do SDK de aplicativos do Windows Universal
description: Saiba mais sobre o conteúdo do SDK dos aplicativos do Windows Universal para o Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 8fa1b701-1c2b-4aec-940c-06c974ef5405
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 7b520adcc6af24f7b092580ea82a787a120668bf
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-apps-sdk-content"></a>Conteúdo do SDK de aplicativos do Windows Universal
> [!IMPORTANT]
> O Azure Mobile Engagement será desativado em 31/03/2018. Esta página será excluída logo em seguida.
> 

Este documento lista e descreve o conteúdo implantado pelo SDK em seu aplicativo.

## <a name="the-resources-folder"></a>A pasta `/Resources`
Essa pasta contém todos os recursos que o Mobile Engagement precisa. Você também pode personalizá-los para se adequarem ao seu aplicativo.

* `EngagementConfiguration.xml` : Arquivo de configuração do Mobile Engagement, é onde você pode personalizar configurações do Mobile Engagement (cadeia de conexão do Mobile Engagement, relatar falhas, etc.).

### <a name="html-folder"></a>/pasta html
* `EngagementNotification.html` : O design HTLM do modo de exibição Web `Notification` para faixas no aplicativo.
* `EngagementAnnouncement.html` : O design HTLM do modo de exibição Web `Announcement` para exibições intersticiais no aplicativo.

### <a name="images-folder"></a>/pasta imagens
* `EngagementIconNotification.png` : O ícone de marca exibido à esquerda de uma notificação, substitua-o pelo ícone da sua marca.
* `EngagementIconOk.png` : O ícone `Ok` das páginas de conteúdo do reach para o botão de ação ou de validação.
* `EngagementIconNOK.png` : O ícone `NOK` usado quando o botão de validação das páginas de conteúdo do reach é desabilitado.
* `EngagementIconClose.png` : O ícone `Close` de notificações e conteúdos do reach para o botão descartar.

### <a name="overlay-folder"></a>/pasta sobreposição
* `EngagementPageOverlay.cs` : A página de sobreposição responsável por adicionar a interface do usuário no aplicativo de alcance do Engagement ao seu filho.

