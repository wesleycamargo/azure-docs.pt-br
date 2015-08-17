<properties
	pageTitle="Introdução aos Aplicativos Móveis do Azure para aplicativos Xamarin Android - Aplicativo Móvel do Azure"
	description="Siga este tutorial para começar a usar os Aplicativos Móveis do Azure para desenvolvimento Android Xamarin"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="chrisanderson"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="05/01/2015"
	ms.author="chrande"/>

# <a name="getting-started"> </a>Criar um aplicativo Xamarin Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo Xamarin Android com os Aplicativos Móveis do Azure. Neste tutorial, você criará um novo serviço .NET e um aplicativo simples de _Lista de tarefas pendentes_, que armazena dados do aplicativo no novo back-end do .NET.

Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Aplicativos Móveis do Azure para aplicativos Xamarin Android.

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 aplicativos móveis gratuitos que você pode continuar a usar mesmo após o fim do seu período de avaliação. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE]Se você deseja começar com o serviço de aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá até [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), onde você pode criar imediatamente um aplicativo móvel inicial de curta duração no serviço de aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## Criar um novo back-end de aplicativo móvel

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Criar um novo aplicativo Xamarin Android

Depois de criar seu back-end de aplicativo móvel, você poderá seguir um quickstart fácil no [Portal do Azure] para criar um novo aplicativo ou modificar um aplicativo existente para conectar-se ao back-end de aplicativo móvel.

Neste tutorial, você baixará um novo aplicativo Xamarin Android e um projeto de serviço de back-end .NET para seu aplicativo móvel.

1. No Portal do Azure, clique em **Procurar**, depois em **Aplicativos móveis** e, em seguida, clique no aplicativo móvel que você acabou de criar.

2. Na parte superior da folha, clique em **Adicionar Cliente** e expanda **Xamarin.Android**.

    ![][6]

    Isso exibe as três etapas fáceis para criar um aplicativo Xamarin Android conectado a seu back-end de aplicativo móvel.


3. Se ainda não o fez, baixe e instale o <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> no seu computador local ou na máquina virtual.

4. Se ainda não o fez, baixe e instale ou o [Xamarin Studio]. Você também pode usar Xamarin para Visual Studio.

5. Em **Baixar e publicar seu serviço na nuvem**, clique em **Baixar**.

  	Isso baixa uma solução que contém projetos para o back-end de aplicativo móvel e para o aplicativo cliente de exemplo de _Lista de tarefas pendentes_ que está conectado ao seu back-end de aplicativo móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

6. Baixe seu perfil de publicação, salve o arquivo baixado no computador local e anote o local onde o salvou.

## Testar o back-end de aplicativo móvel

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Publicar seu back-end de aplicativo móvel

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Executar o aplicativo Xamarin Android

O estágio final deste tutorial é compilar e executar seu novo aplicativo.

1. Navegue até o projeto cliente dentro da solução de aplicativo móvel, no Visual Studio ou Xamarin Studio.

	![][8]

	![][9]

2. Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo. Você será solicitado a selecionar um emulador ou um dispositivo USB conectado.

	> [AZURE.NOTE]Para que seja possível executar o projeto no emulador do Android, você deve definir no mínimo um AVD (Dispositivo Virtual Android). Use o Gerenciador do AVD para criar e gerenciar esses dispositivos.

3. No aplicativo, digite um texto significativo, como _Concluir o tutorial_ e clique no ícone de adição (**+**).

	![][10]

	Isso envia uma solicitação de POST para o novo back-end de aplicativo móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Itens armazenados na tabela são retornados pelo back-end do aplicativo móvel e os dados são exibidos na lista.

	> [AZURE.NOTE]Você pode examinar o código que acessa o back-end do aplicativo móvel para consultar e inserir dados que estão localizados no arquivo ToDoActivity.cs C#.



<!-- Images. -->
[0]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Portal do Azure]: https://azure.portal.com/
[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
 

<!---HONumber=August15_HO6-->