---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 22c1d24042072de5d57d41da379a5fad18180de7
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972496"
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

