<properties
    pageTitle="Criar um aplicativo Cordova nos Aplicativos Móveis do Serviço de Aplicativo do Azure | Microsoft Azure"
    description="Siga este tutorial para começar a usar back-ends de aplicativos móveis do Azure para desenvolvimento do Apache Cordova"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="ggailey777"
    editor=""
    tags=""
    keywords="cordova,javascript,móvel,cliente" />

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="hero-article"
    ms.date="02/09/2016"
    ms.author="adrianha"/>

#Criar um aplicativo Apache Cordova

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## Visão geral

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo Apache Cordova móvel usando um back-end de aplicativo móvel do Azure. Você criará um novo back-end do aplicativo móvel e um aplicativo Apache Cordova simples com _Lista de tarefas pendentes_ que armazena dados de aplicativo no Azure.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais do Apache Cordova sobre como usar o recurso de Aplicativos Móveis no Serviço de Aplicativo do Azure.

## Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Um computador com o [Visual Studio Community 2015] ou mais recente.
* [Ferramentas do Visual Studio para Apache Cordova].
* Uma [conta ativa do Azure](https://azure.microsoft.com/pricing/free-trial/).

Você também pode ignorar o Visual Studio e usar a linha de comando do Apache Cordova diretamente. Isso é útil ao concluir o tutorial em um computador Mac. Compilar aplicativos de cliente do Apache Cordova usando a linha de comando não é coberto por este tutorial.

## Criar um novo back-end de aplicativo móvel do Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Configurar o projeto de servidor

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Baixe e execute o aplicativo Apache Cordova

[AZURE.INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## Próximas etapas

Agora que você concluiu este tutorial de início rápido, passe para um dos tutoriais a seguir:

* [Adicionar autenticação] ao aplicativo Apache Cordova.
* [Adicionar Notificações por push] ao seu aplicativo Apache Cordova.

Saiba mais sobre os principais conceitos com o Serviço de Aplicativo do Azure.

* [Autenticação]
* [Notificações por Push]

Saiba como usar os SDKs.

* [SDK do Servidor ASP.NET]
* [SDK do Servidor Node.js]

<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Ferramentas do Visual Studio para Apache Cordova]: https://www.visualstudio.com/pt-BR/features/cordova-vs.aspx
[Adicionar autenticação]: app-service-mobile-cordova-get-started-users.md
[Adicionar Notificações por push]: app-service-mobile-cordova-get-started-push.md
[Autenticação]: app-service-mobile-auth.md
[Notificações por Push]: ../notification-hubs/notification-hubs-overview.md
[SDK do Servidor ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[SDK do Servidor Node.js]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0224_2016-->