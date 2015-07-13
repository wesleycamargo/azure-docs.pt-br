<properties
	pageTitle="Criar um aplicativo de API ASP.NET no Serviço de Aplicativo do Azure"
	description="Saiba como criar um aplicativo de API ASP.NET no Serviço de Aplicativo do Azure, usando o Visual Studio 2013"
	services="app-service\api"
	documentationCenter=".net"
	authors="bradygaster"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="05/19/2015"
	ms.author="bradyg;tarcher"/>

# Criar um aplicativo de API ASP.NET no Serviço de Aplicativo do Azure

> [AZURE.SELECTOR]
- [Visual Studio 2013](app-service-dotnet-create-api-app.md)
- [Visual Studio 2015 RC](app-service-dotnet-create-api-app-vs2015.md)
- [Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)

## Visão geral

Este tutorial mostra como criar um projeto de API Web ASP.NET usando um modelo Visual Studio 2013 que configura o projeto para implantação na nuvem como um [aplicativo de API](app-service-api-apps-why-best-platform.md) no [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md). Para obter informações sobre como configurar um projeto de API da Web existente para implantação como um aplicativo de API, consulte [Configurar um projeto de API Web como um aplicativo de API](app-service-dotnet-create-api-app-visual-studio.md).

Tutoriais subsequentes na série mostram como [implantar](app-service-dotnet-deploy-api-app.md) e [depurar](../app-service-dotnet-remotely-debug-api-app.md) o projeto de aplicativo de API que você cria neste tutorial.

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

Este tutorial requer a versão 2.5.1 ou posterior do SDK do Azure para .NET.

## Criar um pacote de aplicativos de API

1. Abra o Visual Studio 2013.

2. Selecione **Arquivo > Novo Projeto**.

3. Selecione o modelo **Aplicativo Web ASP .NET**.

4. Verifique se a caixa de seleção **Adicionar o Application Insights ao Projeto** está desmarcada.

4. Nomeie o projeto *ContactsList*

	![](./media/app-service-dotnet-create-api-app/01-filenew-v3.png)

5. Clique em **OK**.

6. Na caixa de diálogo **Novo Projeto ASP.NET**, selecione o modelo de projeto **Aplicativo de API do Azure**.

	![](./media/app-service-dotnet-create-api-app/02-api-app-template-v3.png)

7. Clique em **OK** para criar o projeto.

O Visual Studio cria um projeto de API Web configurado para implantação como um aplicativo de API.

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## Próximas etapas

Seu aplicativo de API está pronto para ser implantado, e você pode seguir o tutorial [Implantar um Aplicativo de API](app-service-dotnet-deploy-api-app.md) para fazer isso.
 

<!---HONumber=62-->