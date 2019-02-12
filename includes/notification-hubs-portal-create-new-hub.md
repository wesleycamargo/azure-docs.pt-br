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
ms.openlocfilehash: 00b7ffcba876b6abea59cff170331c7413a61d39
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823092"
---
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no menu à esquerda e **Hubs de Notificação** na seção **Móvel**. Selecione a estrela (`*`) ao lado do nome do serviço para adicioná-la à seção **FAVORITOS** no menu à esquerda. Depois de adicionar os **Hubs de Notificação** a **FAVORITOS**, selecione-o no menu à esquerda. 

      ![Portal do Azure – selecionar Hubs de Notificação](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)
3. Na página **Hubs de Notificação**, selecione **Adicionar** na barra de ferramentas. 

      ![Hubs de Notificação – adicionar botão de barra de ferramentas](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)
4. Na página **Hub de Notificação**, execute as seguintes etapas: 
    1. Especifique um **nome** para o **hub** de notificação.  
    2. Especifique um **nome** para o **namespace**.
    3. Selecione uma **localização** na qual deseja criar o hub de notificação. 
    4. Selecione um grupo de recursos existente ou insira um nome para o novo **grupo de recursos**.
    5. Selecione **Criar**. 

        ![Portal do Azure - definir propriedades do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)
4. Selecione **Notificações** (ícone de sino) e selecione **Ir para o recurso** ou atualize a lista na página **Hubs de Notificação** e, em seguida, selecione o hub de notificação. 

      ![Portal do Azure - notificações -> Acessar recurso](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)
5. Selecione **Políticas de Acesso** na lista. Observe as cadeias de caracteres de duas conexão que estão disponíveis para você. Você precisará delas para manipular notificações por push mais tarde.

      >[!IMPORTANT]
      >**NÃO** use a DefaultFullSharedAccessSignature em seu aplicativo. Ela é destinada a ser usada apenas em seu back-end.
      >

      ![Portal do Azure - cadeias de conexão do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
