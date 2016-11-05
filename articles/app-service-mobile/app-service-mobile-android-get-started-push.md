---
title: Adicionar notificações por push ao aplicativo Android com aplicativos móveis do Azure
description: Saiba como usar aplicativos móveis do Azure para enviar notificações por push para seu aplicativo Android.
services: app-service\mobile
documentationcenter: android
manager: erikre
editor: ''
author: RickSaling

ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 07/21/2016
ms.author: ricksal

---
# Adicionar notificações de push ao seu aplicativo Android
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## Visão geral
Neste tutorial, você adicionará notificações por push ao projeto de [início rápido do Android] para que, sempre que um registro for inserido, uma notificação por push seja enviada. Este tutorial baseia-se no tutorial de [início rápido do Android], que você deve concluir primeiro. Se você não usar o projeto baixado do início rápido do servidor, você deve adicionar o pacote de extensão de notificação por push ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## Pré-requisitos
Para concluir este tutorial, você precisará do seguinte:

* [Conta do Google](http://go.microsoft.com/fwlink/p/?LinkId=268302) com um endereço de email verificado.
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) - não é necessário para um projeto de back-end Node.js.
* Conclua o [tutorial de início rápido](app-service-mobile-android-get-started.md).

## <a name="create-hub"></a>Criar um Hub de notificação
[!INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

## Habilitar o sistema de mensagens em nuvem do Google
[!INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

## Configurar seu back-end de Aplicativo Móvel para enviar solicitações por push
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

## <a id="update-service"></a>Atualizar o projeto de servidor para enviar notificações por push
[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## Adicionar notificações de push para seu aplicativo
Você deve certificar-se de que seu projeto de aplicativo do Android está pronto para lidar com notificações por push.

### Verificar Versão de SDK do Android
[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

A próxima etapa é instalar os serviços do Google Play. A mensagens de nuvem do Google tem alguns requisitos mínimos de nível do API para desenvolvimento e teste, o que a propriedade **minSdkVersion** no manifesto deve estar de acordo.

Se estiver testando com um dispositivo mais antigo, consulte [Configurar o SDK do Google Play Services] para determinar o mínimo que pode ser definido para esse valor e defina-o de maneira apropriada.

### Inclua o Google Play Services no projeto
[!INCLUDE [Adicionar Play Services](../../includes/app-service-mobile-add-google-play-services.md)]

### Incluir código
[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## Testar o aplicativo no serviço móvel publicado
Você pode testar o aplicativo anexando um telefone Android com um cabo USB diretamente ou usando um dispositivo virtual no emulador.

## <a id="more"></a>Mais
* As marcas permitem que você tenha como alvo clientes segmentados com pushes. [Trabalhar com o SDK do servidor de back-end do .NET para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) mostra como adicionar marcas à instalação de um dispositivo.

<!-- URLs -->
[início rápido do Android]: app-service-mobile-android-get-started.md

[Configurar o SDK do Google Play Services]: https://developers.google.com/android/guides/setup

<!---HONumber=AcomDC_0727_2016-->