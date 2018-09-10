---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 0a74079677a084b2d4e8221cf5a74b356126811d
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42816205"
---
O recurso Aplicativos Móveis do Serviço de Aplicativo do Azure usa [Hubs de Notificação do Azure] para enviar por push e, portanto, você deve configurar um hub de notificação para seu aplicativo móvel.

1. No [Portal do Azure], vá para **Serviços de Aplicativos** e, em seguida, selecione o back-end do aplicativo. Em **Configurações**, selecione **Push**.
2. Para adicionar um recurso do hub de notificação ao aplicativo, selecione **Conectar**. Você pode criar um hub ou conectar-se a um existente.

    ![Configurar um hub](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Agora você se conectou a um hub de notificação para o projeto de back-end dos Aplicativos Móveis. Posteriormente, você configura esse hub de notificação para conectar um PNS (Sistema de Notificação de Plataforma) a fim de enviar por push para dispositivos.

[Portal do Azure]: https://portal.azure.com/
[Hubs de Notificação do Azure]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
