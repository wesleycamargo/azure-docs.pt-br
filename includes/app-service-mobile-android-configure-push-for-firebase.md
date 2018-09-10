---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: bb03e4b5b04a0272d8fa9b032da5adb50878b620
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42809873"
---
1. No [portal do Azure](https://portal.azure.com/), clique em **Procurar Tudo** > **Serviços de Aplicativos** e clique em seu back-end de Aplicativos Móveis. Em **Configurações**, clique em **Push do Serviço de Aplicativo** e clique no nome do hub de notificação.
2. Vá para **Google (GCM)**, insira o valor **Chave do Servidor** obtido do Firebase no procedimento anterior e clique em **Salvar**.

    ![Definir a chave de API no portal](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

O back-end de Aplicativos Móveis agora está configurado para usar o Firebase Cloud Messaging. Isso permite que você envie notificações por push para seu aplicativo em execução em um dispositivo Android usando o hub de notificação.
