<properties
	pageTitle="Criar um aplicativo iOS nos Aplicativos Móveis do Serviço de Aplicativo do Azure | Microsoft Azure"
	description="Siga este tutorial para começar a usar os back-ends de aplicativo móvel do Azure para desenvolvimento de iOS em Objective-C ou Swift."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="10/28/2015"
	ms.author="krisragh"/>

#Criar um aplicativo do iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Visão geral

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo iOS móvel usando um back-end de aplicativo móvel do Azure. Você criará um novo back-end do aplicativo móvel e um aplicativo iOS simples com _Lista de tarefas pendentes_ que armazena dados de aplicativo no Azure.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre como usar o recurso Aplicativos Móveis no Serviço de Aplicativo do Azure.

## Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Uma [conta ativa do Azure](http://azure.microsoft.com/pricing/free-trial/).

* Um computador com o [Visual Studio Community 2013] ou mais recente

* Um Mac com Xcode v7.0 ou mais recente

* [Estrutura do iOS do Azure Mobile](https://go.microsoft.com/fwLink/?LinkID=529823), que é automaticamente incluído como parte do projeto de início rápido baixado por você

## Criar um novo back-end de aplicativo móvel do Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Baixar o projeto de servidor

1. Em seu computador, visite o [portal do Azure]. Clique em **Procurar Tudo** > **Aplicativos Móveis** e clique no back-end de aplicativo móvel que você acabou de criar.

2. Na folha Aplicativo Móvel, clique em **Configurações** e, em **Aplicativo Móvel**, clique em **Início rápido** > **iOS (Objective-C)**. Se você preferir o Swift, clique em **Início Rápido** > **iOS (Swift)** em vez disso.

3. Em **Baixar e executar seu projeto de servidor**, clique em **Baixar**. Extraia os arquivos compactados do projeto em seu computador e abra a solução no Visual Studio.

## Publicar o projeto de servidor no Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

## Baixe e execute o aplicativo iOS

[AZURE.INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[portal do Azure]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=Nov15_HO2-->