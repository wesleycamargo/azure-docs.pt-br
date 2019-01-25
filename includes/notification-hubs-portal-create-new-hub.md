---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: b68fa345d4772134c30ce8b8b559f98113a0496f
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54453080"
---
1. Entre no [Portal do Azure](https://portal.azure.com).

2. Selecione **Criar um recurso** > **Celular** > **Hub de Notificação**.

      ![Portal do Azure - criar um hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)

3. Na caixa **Hub de Notificação** , digite um nome exclusivo. Selecione sua **Região**, **Assinatura** e **Grupo de Recursos** (se você já tiver um).

      Se você ainda não tiver um namespace de barramento de serviço, você pode usar o nome padrão, que é criado com base no nome do hub (se o nome do namespace estiver disponível).

      Se você já tiver um namespace de barramento de serviço que deseja criar o hub, siga estas etapas

     a. Na área **Namespace**, selecione o link **Selecione Existente**.

    b. Selecione **Criar**.

    ![Portal do Azure - definir propriedades do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

4. Selecione **Notificações** (ícone de sino) e selecione **Acessar recurso**.

      ![Portal do Azure - notificações -> Acessar recurso](./media/notification-hubs-portal-create-new-hub/notification-go-to-resource.png)
5. Selecione **Políticas de Acesso** na lista. Observe as cadeias de caracteres de duas conexão que estão disponíveis para você. Você precisará delas para manipular notificações por push mais tarde.

      >[!IMPORTANT]
      >**NÃO** use a DefaultFullSharedAccessSignature em seu aplicativo. Ela é destinada a ser usada apenas em seu back-end.
      >

      ![Portal do Azure - cadeias de conexão do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
