---
title: "Adicionar notificações por push ao aplicativo iOS com aplicativos móveis do Azure"
description: "Saiba como usar aplicativos móveis do Azure para enviar notificações por push para seu aplicativo iOS."
services: app-service\mobile
documentationcenter: ios
manager: crdun
editor: 
author: conceptdev
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/10/2016
ms.author: crdun
ms.openlocfilehash: 1fd90df3b6935d35834e1f571e80b945716b55ff
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="add-push-notifications-to-your-ios-app"></a>Adicionar as notificações por push ao seu aplicativo iOS
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Visão geral
Neste tutorial, você adicionará notificações por push ao projeto de [Início rápido do iOS] de forma que sempre que um registro for inserido, uma notificação por push seja enviada.

Se você não usar o projeto do servidor de início rápido baixado, precisará do pacote de extensão da notificação por push. Para saber mais, veja o guia [Trabalhar com o SDK do servidor de back-end do .NET para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

O [simulador do iOS não dá suporte a notificações por push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Você precisa de um dispositivo iOS físico e uma [Associação de programa para desenvolvedores da Apple](https://developer.apple.com/programs/ios/).

## <a name="configure-hub"></a>Configurar Hub de Notificações
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Registre o aplicativo para obter notificações por push
[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configurar o Azure para enviar notificações por push
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a id="update-server"></a>Atualizar back-end para enviar notificações por push
[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Adicionar notificações por push ao aplicativo
[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Testar notificações por push
[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a id="more"></a>Mais
* Os modelos fornecem a flexibilidade para enviar pushes de plataforma cruzada e pushes localizados. [Como usar a biblioteca de cliente do iOS para aplicativos móveis do Azure](app-service-mobile-ios-how-to-use-client-library.md#templates) mostra como registrar os modelos.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[Início rápido do iOS]: app-service-mobile-ios-get-started.md
