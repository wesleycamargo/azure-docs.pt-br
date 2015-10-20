<properties
	pageTitle="Introdução aos Aplicativos Móveis usando o Xamarin.Forms"
	description="Siga este tutorial para começar a usar os Aplicativos Móveis do Azure para desenvolvimento do Xamarin.Forms"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="08/12/2015"
	ms.author="normesta"/>

#Criar um aplicativo Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

##Visão geral

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo móvel Xamarin.Forms usando um back-end de Aplicativo Móvel do Azure. Você criará um novo back-end do Aplicativo Móvel e um aplicativo Xamarin.Forms simples _Todo list_ que armazena dados do aplicativo no Azure.

A conclusão desse tutorial é um pré-requisito para todos os outros tutoriais de Aplicativos Móveis para o Xamarin.Forms.

##Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 aplicativos móveis gratuitos que você pode continuar a usar mesmo após o fim do seu período de avaliação. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).
 
* [Visual Studio Community 2013] ou posterior. Se você instalar o Visual Studio Community 2013, instale o [Xamarin] separadamente. Você pode instalar as ferramentas Xamarin quando você instala o Visual Studio de 2015.

* Um Mac com [Xcode] v7.0 ou posterior e [Xamarin Studio] instalados.
 
     >[AZURE.NOTE]Se você planeja criar seu aplicativo em um computador com Windows usando o Visual Studio, você ainda precisará acessar um Mac em rede para fazê-lo.
 
>[AZURE.NOTE]Se você deseja começar com o serviço de aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá até [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), onde você pode criar imediatamente um aplicativo móvel inicial de curta duração no serviço de aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## Criar um novo back-end de aplicativo móvel do Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Baixar o projeto de servidor

1. Em seu PC, visite o [Portal do Azure]. Clique em **Procurar Tudo** > **Aplicativos Móveis** e, em seguida, clique no back-end de Aplicativo Móvel que você acabou de criar.

2. Na folha do aplicativo móvel, clique em **Configurações** e, em **Aplicativo Móvel**, clique **Início rápido** > **Xamarin.Forms**.
 
3. Em **Baixar e executar seu projeto de servidor**, clique em **Baixar**. Extraia os arquivos compactados do projeto em seu computador e abra a solução no Visual Studio.
 
## Teste seu projeto de back-end localmente

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Publicar o projeto de servidor no Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

##Baixar e executar a solução de Xamarin.Forms

Aqui, você tem algumas opções. Você pode baixar a solução em um Mac e abri-la no Xamarin Studio, ou você pode baixar a solução para um computador com Windows e abri-la no Visual Studio. Você também pode usar ambos os ambientes e alternar entre eles. Considere estas coisas:

* É mais fácil de executar o projeto do iOS de sua solução em um Mac. Você pode usar o Visual Studio no computador com o Windows se quiser, mas é um pouco mais complicado porque você precisa se conectar a um Mac em rede. Se você estiver interessado em fazer isso, consulte [Instalando Xamarin.iOS no Windows].
* Você pode executar o projeto Android em seu Mac ou seu computador Windows.
* Você pode executar os projetos do Windows somente usando o Visual Studio em um computador Windows.

Com isso em mente, vamos continuar.

 1. Em seu Mac ou em seu computador Windows, abra o [Portal do Azure] em uma janela do navegador.
 2. Em **Baixe e execute seu projeto Xamarin.Forms**, clique no botão **baixar**.

    Isso baixa um projeto que contém um aplicativo de cliente que está conectado ao seu aplicativo móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

 3. Extraia o projeto que você baixou e abra-o no Xamarin Studio ou no Visual Studio.

	![][9]

	![][8]

###Executar o projeto iOS

####No Xamarin Studio

1. Com o botão direito do projeto do iOS e, em seguida, clique em **Definir Como Projeto de Inicialização**.
2. Pressione o botão **Executar**, clique em **Iniciar Depuração** para compilar o projeto e iniciar o aplicativo no emulador do iPhone.

####No Visual Studio
1. Clique com o botão direito no projeto do iOS e, em seguida, clique em **Set as StartUp Project**.
2. No menu **Compilar** clique em **Gerenciador de Configurações**. 
3. Na caixa de diálogo **Gerenciador de Configurações**, selecione o **Criar** e **Implantar** caixas de seleção do projeto iOS.
4. Pressione a chave **F5** para compilar o projeto e iniciar o aplicativo no emulador do iPhone.

No aplicativo, digite um texto significativo, como _Aprenda sobre o Xamarin_ e, em seguida, clique no botão **+**.

![][10]

Isso envia uma solicitação de POST para o novo back-end de aplicativo móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Itens armazenados na tabela são retornados pelo back-end do aplicativo móvel e os dados são exibidos na lista.

> [AZURE.NOTE]Você encontrará o código que acessa o back-end do aplicativo móvel no arquivo ToDoActivity.cs c# do projeto de biblioteca de classes portátil da sua solução.

###Execute o projeto Android

####No Xamarin Studio

1. Com o botão direito do projeto Android e, em seguida, clique em **Definir Como Projeto de Inicialização**.
2. Pressione o botão **Executar**, clique em **Iniciar Depuração** para compilar o projeto e iniciar o aplicativo no emulador do Android.

####No Visual Studio
1. Clique com o botão direito do projeto Android e, em seguida, clique em **Definir Como Projeto de Inicialização**.
4. No menu **Compilar**, clique em **Gerenciador de Configurações**. 
5. Na caixa de diálogo **Gerenciador de Configurações**, selecione o **Criar** e **Implantar** caixas de seleção do projeto Android.
6. Pressione a chave **F5** para compilar o projeto e iniciar o aplicativo no emulador Android.

No aplicativo, digite um texto significativo, como _Aprenda sobre o Xamarin_ e, em seguida, clique no botão **+**.

![][11]

Isso envia uma solicitação de POST para o novo back-end de aplicativo móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Itens armazenados na tabela são retornados pelo back-end do aplicativo móvel e os dados são exibidos na lista.

> [AZURE.NOTE]Você encontrará o código que acessa o back-end do aplicativo móvel no arquivo ToDoActivity.cs c# do projeto de biblioteca de classes portátil da sua solução.


###Execute o projeto do Windows

####No Visual Studio
1. Clique em qualquer um dos projetos do Windows e, em seguida, clique em **Definir como Projeto de Inicialização**.
4. No menu **Compilar**, clique em **Gerenciador de Configurações**. 
5. Na caixa de diálogo **Gerenciador de Configurações**, selecione as caixas de seleção **Criar** e **Implantar** de projeto do Windows que você escolheu.
6. Pressione a chave **F5** para compilar o projeto e iniciar o aplicativo no emulador do Windows.

No aplicativo, digite um texto significativo, como _Aprenda sobre o Xamarin_ e, em seguida, clique no botão **+**.
	
Isso envia uma solicitação de POST para o novo back-end de aplicativo móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Itens armazenados na tabela são retornados pelo back-end do aplicativo móvel e os dados são exibidos na lista.

![][12]
	
> [AZURE.NOTE]Você encontrará o código que acessa o back-end do aplicativo móvel no arquivo ToDoActivity.cs c# do projeto de biblioteca de classes portátil da sua solução.

<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Get started with offline data sync]: app-service-mobile-xamarin-ios-get-started-offline-data-preview.md
[Get started with authentication]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-users.md
[Get started with push notifications]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Portal do Azure]: https://portal.azure.com/
[JavaScript backend version]: ../partner-xamarin-mobile-services-ios-get-started.md
[Get started with data in app services using Visual Studio 2012]: ../app-service-mobile-windows-store-dotnet-get-started-data-vs2012-preview.md
[Troubleshoot a mobile app .NET backend]: ../app-service-mobile-dotnet-backend-how-to-troubleshoot-preview.md


[Xamarin Studio]: http://xamarin.com/download
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
[Instalando Xamarin.iOS no Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
 

<!----HONumber=Oct15_HO3-->