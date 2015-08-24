<properties
	pageTitle="Introdução aos back-ends de aplicativo móvel para aplicativos da Windows Store | Microsoft Azure"
	description="Siga este tutorial para começar a usar os back-ends de aplicativo móvel do Azure para desenvolvimento da Windows Store em C#, VB ou JavaScript."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="06/16/2015"
	ms.author="chrande"/>


# <a name="getting-started"> </a>Criar um aplicativo do Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo universal do Windows usando um back-end de aplicativo móvel do Azure. Soluções de aplicativos universais do Windows incluem projetos para a Windows Store 8.1 e a Loja do Windows Phone 8.1, e um projeto compartilhado comum.

[AZURE.INCLUDE [app-service-mobile-windows-universal-get-started-preview](../../includes/app-service-mobile-windows-universal-get-started-preview.md)]

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 aplicativos móveis gratuitos que você pode continuar a usar mesmo após o fim do seu período de avaliação. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE]Se você deseja começar com o serviço de aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá até [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), onde você pode criar imediatamente um aplicativo móvel inicial de curta duração no serviço de aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## <a name="create-new-service"> </a>Criar um novo back-end de aplicativo móvel

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Criar um novo aplicativo universal do Windows

Depois de criar seu back-end de aplicativo móvel, você poderá seguir um quickstart fácil no Portal do Azure para criar um novo aplicativo ou modificar um aplicativo existente para conectar-se ao back-end de aplicativo móvel.

Nesta seção, você criará um novo aplicativo universal do Windows conectado ao seu back-end de aplicativo móvel.

1. No Portal do Azure, clique em **Aplicativo móvel** e, em seguida, clique no aplicativo móvel que você acabou de criar.

2. Na parte superior da folha, clique em **Adicionar Cliente** e expanda **Windows (C#)**.

    ![Etapas de início rápido do aplicativo móvel](./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/windows-quickstart.png)

    Isso exibe as três etapas fáceis para criar um aplicativo da Windows Store conectado ao back-end de aplicativo móvel.

3. Se ainda não o fez, baixe e instale o <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> no seu computador local ou na máquina virtual.

4. Em **Baixar e executar seu aplicativo e serviço localmente**, selecione um idioma para seu aplicativo da Windows Store e clique em **Baixar**.

    Isso baixa uma solução que contém projetos para o back-end de aplicativo móvel e para o aplicativo de exemplo _Lista de tarefas pendentes_ que está conectado ao seu back-end de aplicativo móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

## Testar o aplicativo móvel

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Publicar seu back-end de aplicativo móvel

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Executar o aplicativo do Windows

Agora que o back-end de aplicativo móvel está publicado e o cliente está conectado a um back-end de aplicativo móvel hospedado no Azure, podemos executar o aplicativo usando o Azure para armazenar itens.

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-app-preview](../../includes/app-service-mobile-windows-universal-test-app-preview.md)]

<!-- Anchors. -->

[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Define the mobile app backend instance]: #define-mobile-app-backend-instance
[Next Steps]: #next-steps

<!-- Images. -->



<!-- URLs. -->
[Get started with authentication]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-users-preview.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/
 

<!---HONumber=August15_HO7-->