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
	ms.date="02/04/2016"
	ms.author="krisragh"/>

#Criar um aplicativo do iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## Visão geral

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem ([Aplicativos Móveis do Azure](app-service-mobile-value-prop.md)) a um aplicativo móvel iOS. Você criará um novo back-end móvel e usará um aplicativo iOS simples de _Lista de tarefas pendentes_ que armazena dados de aplicativo no Azure.

## Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Uma [conta ativa do Azure](https://azure.microsoft.com/pricing/free-trial/).

* Um computador com o [Visual Studio Community 2013] ou mais recente

* Um Mac com Xcode v7.0 ou mais recente

* [Estrutura do iOS do Azure Mobile](https://go.microsoft.com/fwLink/?LinkID=529823), que é automaticamente incluído como parte do projeto de início rápido baixado por você

## Criar um novo back-end de aplicativo móvel do Azure

Siga estas etapas para criar um novo back-end de aplicativo móvel.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Você acabou de provisionar um back-end do aplicativo móvel do Azure que pode ser usado pelos aplicativos móveis clientes. Em seguida, você baixará um projeto do servidor para um back-end simples da "lista de tarefas" e o publicará no Azure.

## Baixar o projeto de servidor

1. Em seu computador, visite o [portal do Azure]. Clique em **Procurar Tudo** > **Aplicativos Móveis** e clique no back-end de aplicativo móvel que você acabou de criar.

2. Na folha Aplicativo Móvel, clique em **Configurações** e, em **Aplicativo Móvel**, clique em **Início Rápido** > **iOS (Objective-C)**. Se você preferir o Swift, clique em **Início Rápido** > **iOS (Swift)** em vez disso.

## Configurar o projeto de servidor

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]


## Baixe e execute o aplicativo iOS

[AZURE.INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[portal do Azure]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_0211_2016-->