---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 90ffebf5f3375e94545f82a95f5c240ad845bd94
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2019
ms.locfileid: "57908241"
---
1. Faça logon no [console do Firebase](https://firebase.google.com/console/). Crie um novo projeto do Firebase se você ainda não tiver um.
2. Depois de criar seu projeto, selecione **Adicionar Firebase ao seu aplicativo Android**. 

    ![Adicione o Firebase ao seu aplicativo Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Na página **Adicionar Firebase ao seu aplicativo Android**, siga estas etapas: 
    1. Para **nome do pacote Android**, insira um nome para o pacote. Por exemplo: `tutorials.tutoria1.xamarinfcmapp`. 

        ![Especificar o nome do pacote](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Selecione **Registrar aplicativo**. 
4. Selecione **Baixar google-services.json**, salve o arquivo na pasta **app** do projeto e, em seguida, selecione **Avançar**. 

    ![Baixar google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
6. Selecione **Avançar** na página. 
7. Selecione **Ignorar esta etapa** na página. 

    ![Ignorar a última etapa](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. No console do Firebase, selecione a engrenagem para seu projeto. Em seguida, selecione **Configurações do Projeto**.

    ![Selecione Configurações do Projeto](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Se você ainda não baixou o arquivo **google-services.json**, poderá fazer isso nesta página. 
5. Mude para a guia **Mensagens de Nuvem** na parte superior. 
6. Copie e salve a **Chave do servidor** para uso posterior. Você usará esse valor para configurar o hub de notificação.
