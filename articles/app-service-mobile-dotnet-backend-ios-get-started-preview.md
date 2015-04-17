<properties
	pageTitle="Introdução aos aplicativos iOS móveis do Serviço de Aplicativo do Azure"
	description="Siga este tutorial para começar a usar o Serviço de Aplicativo do Azure para desenvolvimento do iOS."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="yuaxu"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="02/20/2015"
	ms.author="yuaxu"/>

# <a name="getting-started"> </a>Criar um aplicativo do iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../includes/app-service-mobile-selector-get-started-preview.md)]

Este tutorial mostra como criar um aplicativo iOS com aplicativos móveis do Serviço de Aplicativo do Azure.  Você criará um novo back-end do aplicativo móvel e um aplicativo _To do list_ simples que armazene dados de aplicativo.  O tutorial usa o .NET e o Visual Studio para a lógica do lado do servidor.

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure.  Se você não tiver uma conta, pode se inscrever para uma avaliação do Azure e obter até 10 aplicativos móveis gratuitos que pode continuar usando mesmo depois do fim de sua avaliação.  Para obter detalhes, consulte [Avaliação Gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE] Se desejar começar com o serviço de aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Testa Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), em que você pode criar imediatamente um aplicativo móvel de curta duração iniciante no aplicativo de serviço.  Não é necessário nenhum cartão de crédito; não há compromissos.

## <a name="create-new-service"> </a>Criar um novo back-end do aplicativo móvel

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Criar um novo aplicativo iOS

Agora que você criou seu back-end móvel, pode seguir uma introdução rápida e fácil no Portal de visualização do Azure para criar um novo aplicativo ou modificar um aplicativo existente para se conectar ao seu back-end de aplicativo móvel.

1. No Portal do Azure, clique em **Aplicativo Móvel** e, em seguida, clique em back-end do aplicativo móvel que acabou de criar.

2. Na parte superior da folha, clique em Adicionar Cliente e expanda iOS.

	![][6]

	Isso exibe as etapas para criar um aplicativo iOS conectado ao seu back-end de aplicativo móvel.

3. Se você ainda não o fez, baixe e instale o <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> no computador local ou na máquina virtual. 

4. Baixe e instale o [Xcode] v4.4 ou uma versão posterior e o [Xamarin Studio].  Você também pode usar Xamarin para Visual Studio.

5. Em **Baixar e publicar o serviço na nuvem**, clique em **Baixar**.

 Isso baixa a solução que contém projetos para o back-end de aplicativo móvel e para o aplicativo de exemplo _To do list_ conectado ao back-end de aplicativo móvel.  Salve o arquivo de projeto compactado em seu computador local e anote onde ele foi salvo.

6. Baixe seu perfil de publicação, salve o arquivo baixado no computador local e anote o local onde o salvou.

## Testar o aplicativo móvel

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Publicar o aplicativo móvel

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Executar o aplicativo iOS

[AZURE.INCLUDE [app-service-mobile-ios-run-app-preview](../includes/app-service-mobile-ios-run-app-preview.md)]



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/app-service-mobile-dotnet-backend-ios-get-started-preview/ios-quickstart.png

[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png

[Portal de Gerenciamento]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532

<!--HONumber=49-->