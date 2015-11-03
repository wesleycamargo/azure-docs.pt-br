<properties
	pageTitle="Introdução aos Aplicativos Móveis do Serviço de Aplicativo do Azure para aplicativos Xamarin.iOS | Microsoft Azure"
	description="Siga este tutorial para começar a usar os Aplicativos Móveis para desenvolvimento do Xamarin.iOS."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/12/2015"
	ms.author="normesta"/>


#Criar um aplicativo Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Visão geral

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo móvel Xamarin.iOS usando um back-end de Aplicativo Móvel do Azure. Você criará um novo back-end do aplicativo móvel e um aplicativo Xamarin.iOS _Todo list_ simples que armazena dados de aplicativo no Azure.

Concluir este tutorial é um pré-requisito para todos os outros tutoriais do Xamarin.iOS sobre como usar o recurso de Aplicativos Móveis no Serviço de Aplicativo do Azure.

##Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 aplicativos móveis gratuitos que você pode continuar a usar mesmo após o fim do seu período de avaliação. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2013] ou posterior. Se você instalar o Visual Studio Community 2013, instale o [Xamarin] separadamente. Você pode instalar as ferramentas Xamarin quando você instala o Visual Studio de 2015.

* Um Mac com [Xcode] versão 7.0 ou posterior e [Xamarin Studio] instalados.

     >[AZURE.NOTE]Se você planeja criar seu aplicativo em um computador com Windows usando o Visual Studio, você ainda precisará ter acesso a um MAC em rede para fazê-lo.

>[AZURE.NOTE]Se você deseja começar com o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile). Lá, você pode criar imediatamente um aplicativo móvel de curta duração inicial no Serviço de Aplicativo – sem cartão de crédito e sem compromissos.

## Criar um novo back-end de aplicativo móvel do Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Baixar o projeto de servidor

1. Em seu computador, visite o [Portal do Azure]. Clique em **Procurar Tudo** > **Aplicativos Móveis** e, em seguida, clique no back-end de aplicativo móvel que você acabou de criar.

2. Na folha do Aplicativo móvel, clique em **Configurações** e, em **Aplicativo Móvel**, clique **Início rápido** > **Xamarin.iOS**.

3. Em **Baixar e executar seu projeto de servidor**, clique em **Baixar**. Extraia os arquivos compactados do projeto em seu computador e abra a solução no Visual Studio.

## Teste seu projeto de back-end localmente

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service](../../includes/app-service-mobile-dotnet-backend-test-local-service.md)]

## Publicar o projeto de servidor no Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

## Baixar e executar o aplicativo Xamarin.iOS

1. Em seu Mac, abra o [Portal do Azure] em uma janela do navegador.

>[AZURE.NOTE]É mais fácil de executar seu aplicativo Xamarin.iOS em um Mac. Você pode executar o aplicativo Xamarin.iOS usando o Visual Studio no computador com o Windows se quiser, mas é um pouco mais complicado porque você precisa se conectar a um MAC em rede. Se você estiver interessado em fazer isso, consulte [Installing Xamarin.iOS on Windows].

2. Em **Baixe e execute seu projeto Xamarin.iOS**, clique no botão **Baixar**.

  	Isso baixa um projeto que contém uma aplicação de cliente que está conectada ao seu aplicativo móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

3. Extraia o projeto que você baixou e abra-o no Xamarin Studio (ou no Visual Studio).

	![][9]

	![][8]

4. Pressione a tecla F5 para compilar o projeto e iniciar o aplicativo no emulador do iPhone.

5. No aplicativo, digite um texto significativo, como _Aprenda sobre o Xamarin_ e, em seguida, clique no botão **+**.

	![][10]

	Isso envia uma solicitação de POST para o novo back-end de aplicativo móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo back-end de aplicativo móvel, e os dados são exibidos na lista.

>[AZURE.NOTE]Você pode examinar o código que acessa seu back-end de aplicativo móvel para consultar e inserir dados no arquivo C# QSTodoService.cs.

##Próximas etapas

* [Adicionar autenticação ao seu aplicativo](app-service-mobile-xamarin-ios-get-started-users.md) <br/>Saiba como autenticar usuários de seu aplicativo por meio de um provedor de identidade.

* [Adicionar notificações por push ao seu aplicativo](app-service-mobile-xamarin-ios-get-started-push.md) <br/>Saiba como enviar uma notificação por push bem básica ao seu aplicativo.

<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Portal do Azure]: https://portal.azure.com/


[Xamarin Studio]: http://xamarin.com/download
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
[Installing Xamarin.iOS on Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/

<!---HONumber=Nov15_HO1-->