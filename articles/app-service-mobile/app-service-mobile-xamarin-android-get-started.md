<properties
	pageTitle="Introdução aos aplicativos móveis do Azure para aplicativos Xamarin Android"
	description="Siga este tutorial para começar a usar os Aplicativos Móveis do Azure para desenvolvimento Android Xamarin"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="ggailey777"
	manager="erikre"
	editor="" />

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/11/2016"
	ms.author="glenga" />

#Criar um Aplicativo Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##Visão geral

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo Xamarin Android. Para saber mais, confira [O que são Aplicativos Móveis](app-service-mobile-value-prop.md).

Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Aplicativos Móveis para aplicativos Xamarin.Android.

##Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 aplicativos móveis gratuitos que você pode continuar a usar mesmo após o fim do seu período de avaliação. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio com Xamarin. Confira [Configuração e instalação para Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) para obter instruções.
 
>[AZURE.NOTE] Se você desejar uma introdução ao Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](https://tryappservice.azure.com/?appServiceName=mobile), onde poderá criar imediatamente um Aplicativo Móvel inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## Criar um novo back-end de aplicativo móvel do Azure

Siga estas etapas para criar um novo back-end de aplicativo móvel.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Você acabou de provisionar um back-end do aplicativo móvel do Azure que pode ser usado pelos aplicativos móveis clientes. Em seguida, você baixará um projeto do servidor para um back-end simples da "lista de tarefas" e o publicará no Azure.

## Configurar o projeto de servidor

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Baixar e executar o aplicativo Xamarin.Android

1. Em **Baixar e executar seu projeto Xamarin.Android**, clique no botão **Baixar**.

  	Isso baixa um projeto que contém uma aplicação de cliente que está conectada ao seu aplicativo móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

2. Pressione a tecla **F5** para compilar o projeto e iniciar o aplicativo.

3. No aplicativo, digite um texto significativo, como _Concluir o tutorial_ e clique no botão **Adicionar**.

	![][10]

	Isso envia uma solicitação de POST para o novo back-end de aplicativo móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Itens armazenados na tabela são retornados pelo back-end do aplicativo móvel e os dados aparecem na lista.

	> [AZURE.NOTE] Você pode examinar o código que acessa o back-end do aplicativo móvel para consultar e inserir dados que estão localizados no arquivo ToDoActivity.cs C#.

##Próximas etapas

* [Adicionar autenticação ao seu aplicativo](app-service-mobile-xamarin-android-get-started-users.md) Saiba como autenticar os usuários de seu aplicativo com um provedor de identidade.
* [Adicionar notificações por push ao seu aplicativo Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md) Saiba como adicionar notificações por push ao seu aplicativo.
* [Como usar o cliente gerenciado para os Aplicativos Móveis do Azure](app-service-mobile-dotnet-how-to-use-client-library.md) Saiba como trabalhar com o SDK do cliente gerenciado em seu aplicativo Xamarin.


<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure Portal]: https://azure.portal.com/
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_0817_2016-->