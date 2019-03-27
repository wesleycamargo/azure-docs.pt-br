---
title: Configurar Apple Push Notification Service nos Hubs de notificação do Azure | Microsoft Docs
description: Saiba como configurar um hub de notificação do Azure com as configurações do serviço de notificação por Push da Apple (APNS).
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9a9db9f05895569b050e56cdeec1ee2ee25af0df
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488186"
---
# <a name="configure-apple-push-notification-service-apns-settings-for-a-notification-hub-in-the-azure-portal"></a>Definir configurações de serviço de notificação por Push da Apple (APNS) para um hub de notificação no portal do Azure
Este artigo mostra como definir as configurações de serviço de notificação por Push da Apple (APNS) para um hub de notificação do Azure usando o portal do Azure. 

## <a name="prerequisites"></a>Pré-requisitos
Se você ainda não criou um hub de notificação, crie um agora. Para obter mais informações, consulte [criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="configure-apple-push-notification-service"></a>Configurar o serviço de notificação por Push da Apple

O procedimento a seguir fornece as etapas para definir as configurações de serviço de notificação por Push da Apple (APNS) para um hub de notificação:

1. No portal do Azure, sobre o **Hub de notificação** página, selecione **Apple (APNS)** no menu à esquerda.

1. Para **modo de autenticação**, selecione **certificado** ou **Token**.

    a. Se você selecionar **certificado**:
   * Selecione o ícone de arquivo e, em seguida, selecione a *. p12* arquivo que você deseja carregar.
   * Digite uma senha.
   * Selecione o modo **Sandbox**. Ou, para enviar notificações por push para usuários que adquiriram seu aplicativo da loja, selecione **produção** modo.

     ![Captura de tela de um APNS certificado configuração no portal do Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Se você selecionar **Token**:

   * Insira os valores para **ID da chave**, **ID do pacote**, **ID da equipe**, e **Token**.
   * Selecione o modo **Sandbox**. Ou, para enviar notificações por push para usuários que adquiriram seu aplicativo da loja, selecione **produção** modo.

     ![Captura de tela de um APNS token configuração no portal do Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Próximas etapas
Para obter um tutorial com instruções passo a passo para enviar notificações para dispositivos iOS, consulte o artigo a seguir: [Notificações por push para dispositivos iOS usando os Hubs de notificação e APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
