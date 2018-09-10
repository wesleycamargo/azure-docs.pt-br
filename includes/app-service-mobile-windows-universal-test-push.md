---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: c86210208b9f747cbef1d9231528fa6cedbdb5d2
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42814919"
---
1. Clique com o botão direito do mouse no projeto do Windows Store, clique em **Definir como Projeto de Inicialização**e pressione a tecla F5 para executar o aplicativo do Windows Store.

    Depois que o aplicativo é iniciado, o dispositivo está registrado para notificações por push.
2. Pare o aplicativo da Windows Store e repita a etapa anterior para o aplicativo Loja do Windows Phone.

    Nesse ponto, ambos os dispositivos são registrados para receber as notificações por push.

3. Execute o aplicativo do Windows Store novamente, digite o texto em **Inserir um TodoItem** e clique em **Salvar**.

    Observe que, após a inserção, tanto os aplicativos do Windows Store como do Windows Phone recebem uma notificação por push dos WNS. A notificação é exibida no Windows Phone, mesmo quando o aplicativo não está em execução.

    ![](./media/app-service-mobile-windows-universal-test-push/mobile-quickstart-push5-wp8.png)
