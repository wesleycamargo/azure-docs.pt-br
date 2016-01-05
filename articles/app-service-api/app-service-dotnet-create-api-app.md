<properties
	pageTitle="Criar um aplicativo de API do ASP.NET no Serviço de Aplicativo do Azure | Microsoft Azure"
	description="Saiba como criar um aplicativo de API do ASP.NET no Serviço de Aplicativo do Azure, usando o Visual Studio 2013."
	services="app-service\api"
	documentationCenter=".net"
	authors="bradygaster"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/08/2015"
	ms.author="tdykstra"/>

# Criar um aplicativo de API ASP.NET no Serviço de Aplicativo do Azure

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## Visão geral

Este tutorial mostra como criar um projeto de API Web ASP.NET configurado para implantação na nuvem como um [aplicativo de API no Serviço de Aplicativo do Azure](app-service-api-apps-why-best-platform.md). Para obter informações sobre como configurar um projeto de API da Web existente para implantação como um aplicativo de API, consulte [Configurar um projeto de API Web como um aplicativo de API](app-service-dotnet-create-api-app-visual-studio.md).

Este é um tutorial rápido e simples que mostra como criar um projeto do Visual Studio usando um modelo. É o primeiro de uma série que também mostra como [implantar](app-service-dotnet-deploy-api-app.md) e [depurar](../app-service-dotnet-remotely-debug-api-app.md) o projeto de aplicativo de API que você cria neste tutorial. Para obter informações mais detalhadas sobre como trabalhar com aplicativos de API, veja a seção [Próximas etapas](#next-steps) no fim do tutorial.

[AZURE.INCLUDE [install-sdk-2015-2013](../../includes/install-sdk-2015-2013.md)]

Este tutorial requer a versão 2.6 ou posterior do SDK do Azure para .NET.

## Criar um pacote de aplicativos de API

Quando as instruções solicitarem que você insira um nome para o projeto, insira **ContactsList**.

[AZURE.INCLUDE [app-service-api-create](../../includes/app-service-api-create.md)]

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## Próximas etapas

Seu aplicativo de API está pronto para ser implantado, e você pode seguir o tutorial [Implantar um Aplicativo de API](app-service-dotnet-deploy-api-app.md) para fazer isso.

Para saber mais sobre como usar o código gerado automaticamente pelo cliente para chamar aplicativos de API, veja [Consumir um aplicativo de API de um cliente .NET](app-service-api-dotnet-consume.md).

Para saber mais sobre como personalizar os metadados do Swagger gerados automaticamente para um aplicativo de API, veja [Personalizar definições de API geradas pelo Swashbuckle](app-service-api-dotnet-swashbuckle-customize.md).

Para saber mais sobre como criar, excluir e configurar aplicativos de API no portal de visualização do Azure, veja [Gerenciar aplicativos de API](app-service-api-manage-in-portal.md).

Para saber mais sobre a autenticação de usuários de aplicativos de API, veja [Autenticação para aplicativos de API e aplicativos móveis no Serviço de Aplicativo do Azure](../app-service/app-service-authentication-overview.md).

Para saber mais sobre os recursos de aplicativos de API, veja [O que são aplicativos de API?](app-service-api-apps-why-best-platform.md).

<!---HONumber=AcomDC_1203_2015-->