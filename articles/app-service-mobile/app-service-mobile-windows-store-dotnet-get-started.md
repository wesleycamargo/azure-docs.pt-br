---
title: Criar um UWP (Plataforma Universal do Windows) que usa os Aplicativos Móveis do Azure | Microsoft Docs
description: Siga este tutorial para começar a usar os back-ends de aplicativo móvel do Azure para desenvolvimento de aplicativos UWP (Plataforma Universal do Windows) em C#, Visual Basic ou JavaScript.
services: app-service\mobile
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/17/2018
ms.author: crdun
ms.openlocfilehash: 00b635853d0dfad73a258ddc8ef9b168559fc5df
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56098418"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>Criar um aplicativo do Windows com um back-end do Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Visão geral

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a um aplicativo UWP (Plataforma Universal do Windows). Para saber mais, confira [O que são Aplicativos Móveis](app-service-mobile-value-prop.md). A seguir, há capturas de tela do aplicativo concluído:

![Aplicativo de área de trabalho concluído](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

A conclusão desse tutorial é um pré-requisito para todos os outros tutoriais de Aplicativos Móveis para aplicativos UWP.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 aplicativos móveis gratuitos que você pode continuar a usar mesmo após o fim do seu período de avaliação. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Windows 10.
* [Visual Studio Community 2017].
* Familiaridade com o desenvolvimento de aplicativos UWP. Visite a [documentação do UWP](https://docs.microsoft.com/windows/uwp/) para saber como [se preparar](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) para criar aplicativos UWP.

## <a name="create-a-new-azure-mobile-app-backend"></a>Criar um novo back-end de aplicativo móvel do Azure

Siga estas etapas para criar um novo back-end de aplicativo móvel.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Você acabou de provisionar um back-end do aplicativo móvel do Azure que pode ser usado pelos aplicativos móveis clientes. Em seguida, você baixará um projeto do servidor para um back-end simples da "lista de tarefas" e o publicará no Azure.

## <a name="configure-the-server-project"></a>Configurar o projeto de servidor

[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-client-project"></a>Baixe e execute o projeto do cliente

Quando você tiver configurado o back-end do Aplicativo Móvel, poderá criar um novo aplicativo cliente ou modificar um aplicativo existente para se conectar ao Azure. Nesta seção, você faz o download de um exemplo de projeto de aplicativo UWP personalizado para se conectar ao back-end do Aplicativo Móvel.

1. De volta à folha **Início rápido** do back-end do Aplicativo Móvel, clique em **Criar um novo aplicativo** > **Baixar** e extraia os arquivos de projeto compactados para o computador local.

    ![Baixar o projeto de início rápido do Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-app-windows-quickstart.png)

2. Abra o projeto UWP e pressione a tecla F5 para implantar e executar o aplicativo.
3. No aplicativo, digite um texto significativo, como *Concluir o tutorial*, na caixa de texto **Inserir um TodoItem** e clique em **Salvar**.

    ![Área de trabalho completa do início rápido do Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Isso envia uma solicitação POST para o novo back-end de aplicativo móvel hospedado no Azure.

> [!TIP]
> Adicione o projeto de aplicativo UWP à mesma solução que o projeto de servidor, se você estiver usando o back-end .NET. Isso torna mais fácil depurar e testar o aplicativo e o back-end na mesma solução do Visual Studio. Para adicionar um projeto de aplicativo UWP à solução de back-end, você deve estar usando o Visual Studio 2017.

## <a name="next-steps"></a>Próximas etapas

* [Adicionar autenticação ao seu aplicativo](app-service-mobile-windows-store-dotnet-get-started-users.md)  
   Saiba como autenticar usuários de seu aplicativo com um provedor de identidade.
* [Adicionar notificações por push ao aplicativo](app-service-mobile-windows-store-dotnet-get-started-push.md)  
   Saiba como adicionar suporte a notificações por push ao aplicativo e configurar o back-end do Aplicativo Móvel para usar os Hubs de Notificação do Azure para enviar notificações por push.
* [Habilitar sincronização offline para seu aplicativo](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Saiba como adicionar suporte offline ao seu aplicativo usando um back-end de Aplicativo Móvel. Sincronização offline permite que os usuários finais interajam com um aplicativo móvel, &mdash;exibindo, adicionando ou modificando dados&mdash;, mesmo quando não há conexão de rede.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Mobile App SDK]: https://go.microsoft.com/fwlink/?LinkId=257545
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2017]: https://go.microsoft.com/fwLink/p/?LinkID=534203
