---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/05/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 4dc67175f2ccc569ab6dfe5338607fa6bebe3882
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
1. Faça logon no [console do Firebase](https://firebase.google.com/console/). Crie um novo projeto do Firebase se você ainda não tiver um.
2. Depois de criar seu projeto, selecione **Adicionar Firebase ao seu aplicativo Android**. Em seguida, siga as instruções fornecidas. Baixe o arquivo **services.json google**. 

    ![Adicione o Firebase ao seu aplicativo Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. No console do Firebase, selecione a engrenagem para seu projeto. Em seguida, selecione **Configurações do Projeto**.

    ![Selecione Configurações do Projeto](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Selecione a guia **Geral** nas configurações do projeto. Em seguida, baixe o arquivo **google-services.json** que contém a chave de API de Servidor e a ID do Cliente.
5. Selecione a guia **Cloud Messaging** nas configurações do projeto e copie o valor da **Chave do servidor herdado**. Você usa esse valor para configurar a política de acesso do hub de notificação.
