---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 1d1d593b7305e0cd9899f4ec388cb441ced90b10
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134333"
---
1. Em seu Mac, visite o [portal do Azure]. Clique em **Todos os Serviços** > **Serviços de Aplicativos** > o back-end que você acabou de criar. Nas configurações de aplicativo móvel, escolha seu idioma preferencial:

    - Objective-C &ndash; **Início Rápido** > **iOS (Objective-C)**
    - Swift &ndash; **Início Rápido** > **iOS (Swift)**

    Em **3. Configurar seu aplicativo cliente**, clique em **Baixar**. Isso baixa um projeto completo do Xcode previamente configurado para conexão com o back-end. Abra o projeto usando Xcode.

1. Pressione o botão **Executar** para criar o projeto e iniciar o aplicativo no simulador de iOS.

1. No aplicativo, digite um texto significativo, como *Concluir o tutorial* e clique no ícone de adição (**+**). Isso envia uma solicitação POST para o back-end do Azure implantado anteriormente. Os back-end insere dados da solicitação na tabela TodoItem SQL e retorna informações sobre os itens armazenados recentemente no aplicativo móvel. O aplicativo móvel exibe esses dados na lista.

   ![Aplicativo de início rápido em execução no iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Portal do Azure]: https://portal.azure.com/
