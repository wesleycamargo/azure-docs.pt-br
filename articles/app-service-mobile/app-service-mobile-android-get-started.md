<properties
    pageTitle="Criar um aplicativo Android nos Aplicativos Móveis do Serviço de Aplicativo do Azure | Microsoft Azure"
    description="Siga este tutorial para começar a usar back-ends de aplicativos móveis do Azure para desenvolvimento do Android"
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="11/20/2015"
    ms.author="yuaxu"/>

#Criar um aplicativo Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Visão geral

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo Android móvel usando um back-end de aplicativo móvel do Azure. Você criará um novo back-end do aplicativo móvel e um aplicativo Android simples com _Lista de tarefas pendentes_ que armazena dados de aplicativo no Azure.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais do Android sobre como usar o recurso de Aplicativos Móveis no Serviço de Aplicativo do Azure.

## Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Uma [conta ativa do Azure](http://azure.microsoft.com/pricing/free-trial/).

* Um computador com o [Visual Studio Community 2013] ou mais recente

* [Ferramentas para desenvolvedores do Android](https://developer.android.com/sdk/index.html), que incluem o ambiente de desenvolvimento integrado do Android Studio e a plataforma Android mais recente.

* O SDK para Android do Azure Mobile, que é automaticamente referenciado como parte do projeto de início rápido baixado

## Criar um novo back-end de aplicativo móvel do Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Configurar o projeto de servidor

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Baixar e executar o aplicativo Android

[AZURE.INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_1203_2015-->