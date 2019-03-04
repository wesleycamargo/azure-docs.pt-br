---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 8146489a913ce863cee7534331231a248a3ea7ac
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890981"
---
1. Em seu Mac, visite o [portal do Azure]. Clique em **Todos os Serviços** > **Serviços de Aplicativos** > o back-end que você acabou de criar. Nas configurações de aplicativo móvel, escolha seu idioma preferencial:

    - Objective-C &ndash; **Início Rápido** > **iOS (Objective-C)**
    - Swift &ndash; **Início Rápido** > **iOS (Swift)**

    Em **3. Configurar seu aplicativo cliente**, clique em **Baixar**. Isso baixa um projeto completo do Xcode previamente configurado para conexão com o back-end. Abra o projeto usando Xcode.

1. Pressione o botão **Executar** para criar o projeto e iniciar o aplicativo no simulador de iOS.

1. No aplicativo, clique no ícone de adição (**+**), digite um texto significativo, como *Concluir o tutorial* e, em seguida, clique no botão Salvar. Isso envia uma solicitação POST para o back-end do Azure implantado anteriormente. O back-end insere dados da solicitação na tabela SQL TodoItem e retorna informações sobre os itens armazenados recentemente no aplicativo móvel. O aplicativo móvel exibe esses dados na lista.

   ![Aplicativo de início rápido em execução no iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Portal do Azure]: https://portal.azure.com/
