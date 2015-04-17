<properties
	pageTitle="Introdução aos Aplicativos Móveis no Xamarin iOS"
	description="Começar a usar o Xamarin iOS para compilar um Aplicativo Móvel do Azure com o Serviço de Aplicativo do Azure."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/24/2015"
	ms.author="chrande"/>


# <a name="getting-started"> </a>Crie um aplicativo Xamarin iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../includes/app-service-mobile-selector-get-started-preview.md)]

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo Xamarin iOS com o Aplicativo Móvel do Azure.  Neste tutorial, você criará um novo serviço .NET e um aplicativo simples _To do list_ que armazene dados do aplicativo no back-end .NET.

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure.  Se você não tiver uma conta, pode se inscrever para uma avaliação do Azure e obter até 10 aplicativos móveis gratuitos que pode continuar usando mesmo depois do fim de sua avaliação.  Para obter detalhes, consulte [Avaliação Gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE] Se desejar começar com o serviço de aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Testa Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), em que você pode criar imediatamente um aplicativo móvel de curta duração iniciante no aplicativo de serviço.  Não é necessário nenhum cartão de crédito; não há compromissos.

## Criar um novo back-end do aplicativo móvel

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Criar um novo aplicativo Xamarin iOS

Depois de criar seu back-end do aplicativo móvel, você pode seguir um guia de início rápido fácil no Portal do Azure para criar um novo aplicativo ou modificar um aplicativo existente para se conectar ao back-end do aplicativo móvel.

Nesta seção você baixará um novo aplicativo Xamarin iOS e um projeto de serviço para o back-end do aplicativo móvel.

1. No Portal do Azure, clique em **Aplicativo Móvel** e, em seguida, clique em back-end do aplicativo móvel que acabou de criar.

2. Na parte superior da folha, clique em **Adicionar Cliente** e expanda **Xamarin iOS**.

	![][6]

	Isso exibe as três etapas fáceis de criar um aplicativo Xamarin iOS conectado ao seu back-end do aplicativo móvel.

3. Se você ainda não tiver feito isso, baixe e instale <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> no computador local ou na máquina virtual.  

4. Baixe e instale o [Xcode] v4.4 ou uma versão posterior e o [Xamarin Studio].  Você também pode usar Xamarin para Visual Studio.

5. Em **Baixar e publicar o serviço na nuvem**, clique em **Baixar**.

 Isso baixa a solução que contém projetos para o back-end de aplicativo móvel e para o aplicativo de exemplo _To do list_ conectado ao back-end de aplicativo móvel.  Salve o arquivo de projeto compactado em seu computador local e anote onde ele foi salvo.

6. Baixe seu perfil de publicação, salve o arquivo baixado no computador local e anote o local onde o salvou.

## Testar o back-end do aplicativo móvel

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Publicar seu back-end do aplicativo móvel

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Executar o aplicativo Xamarin iOS

O estágio final deste tutorial é compilar e executar seu novo aplicativo.

1. Navegue para o projeto cliente na solução de back-end do aplicativo móvel no Visual Studio ou Xamarin Studio.

	![][8]

	![][9]

2. Pressione o botão **Executar** para compilar o projeto cliente e iniciar o aplicativo no emulador do iPhone.

3. No aplicativo, digite um texto significativo, como _Concluir o tutorial_, e clique no ícone de adição (**+**).

	![][10]

	Isso envia uma solicitação POST para o novo back-end do aplicativo móvel hospedado no Azure.  Os dados da solicitação são inseridos na tabela TodoItem.  Itens armazenados na tabela são retornados pelo back-end do aplicativo móvel e os dados são exibidos na lista.

>[AZURE.NOTE]Você pode examinar o código que acessa o back-end do aplicativo móvel para consultar e inserir dados no arquivo QSTodoService.cs C#.


<!-- Anchors. -->
[Introdução a back-ends de aplicativo móvel]:#getting-started
[Criar um novo back-end do aplicativo móvel]:#create-new-service
[Próximas etapas]:#next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Introdução à sincronização de dados offline]: app-service-mobile-xamarin-ios-get-started-offline-data-preview.md
[Introdução à autenticação]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-users.md
[Introdução às notificações por push]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[SDK do aplicativo móvel]: http://go.microsoft.com/fwlink/?LinkId=257545
[Portal do Azure]: https://portal.azure.com/
[Versão de back-end do JavaScript]: partner-xamarin-mobile-services-ios-get-started.md
[Introdução a dados nos serviços de aplicativos usando o Visual Studio 2012]: app-service-mobile-windows-store-dotnet-get-started-data-vs2012-preview.md
[Solucionar problemas de um back-end do aplicativo móvel .NET]: app-service-mobile-dotnet-backend-how-to-troubleshoot-preview.md


[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin para Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409

<!--HONumber=49-->