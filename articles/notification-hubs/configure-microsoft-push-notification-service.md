---
title: Configurar o serviço de notificação por Push da Microsoft nos Hubs de notificação do Azure | Microsoft Docs
description: Saiba como definir as configurações de serviço de notificação por Push da Microsoft para um hub de notificação do Azure.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 1c76b44438e6527439d0a370c92f4120424b8da5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60240308"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-for-a-notification-hub-in-the-azure-portal"></a>Definir configurações de serviço de notificação por Push da Microsoft (MPNS) para um hub de notificação no portal do Azure
Este artigo mostra como definir as configurações de serviço de notificação por Push da Microsoft (MPNS) para um hub de notificação do Azure usando o portal do Azure. 

## <a name="prerequisites"></a>Pré-requisitos
Se você ainda não criou um hub de notificação, crie um agora. Para obter mais informações, consulte [Criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Configurar o serviço de notificação por Push da Microsoft (MPNS)

O procedimento a seguir fornece as etapas para definir as configurações de serviço de notificação por Push da Microsoft (MPNS) para um hub de notificação: 

1. No portal do Azure, sobre o **Hub de notificação** página, selecione **Windows Phone (MPNS)** no menu à esquerda.
1. Habilitar notificações por push autenticadas ou não autenticadas:

    a. Para habilitar as notificações por push não autenticadas, selecione **Habilitar envio por push não autenticado** > **Salvar**.

      ![Captura de tela que mostra como habilitar as notificações por push](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Habilite as notificações por push autenticadas:
      * Na barra de ferramentas, selecione **Carregar Certificado**.
      * Selecione o ícone de arquivo e selecione o arquivo de certificado.
      * Insira a senha para o certificado.
      * Selecione **OK**.
      * Na página **Windows Phone (MPNS)**, selecione **Salvar**.

## <a name="next-steps"></a>Próximos passos
Para obter um tutorial com instruções passo a passo para enviar notificações para dispositivos Windows Phone usando os Hubs de notificação do Azure e o serviço de notificação por Push da Microsoft (MPNS), consulte [notificações por Push para aplicativos do Windows Phone por meio de notificação Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md).

